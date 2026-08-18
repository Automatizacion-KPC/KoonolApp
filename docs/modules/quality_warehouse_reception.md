# 👥 Módulo: Recepción de Devoluciones en Almacén (Quality Warehouse Receptions - QWR)

El módulo de **Recepción de Devoluciones en Almacén** digitaliza y estandariza la bitácora operativa de entrada en rampa de mercancías devueltas, procedentes tanto de rechazos inmediatos en ruta de reparto (Sin Posesión - CAL-FOR-02) como de recolecciones autorizadas previamente (Con Posesión - CAL-FOR-01). Garantiza la captura imparcial de lotes, caducidades y pesajes por parte del personal de inspección en rampa, detonando de forma coordinada las transiciones de estado en el flujo global y alimentando el módulo de quejas de clientes.

---

---

## 💼 Reglas de Negocio (Business Rules)

### BR-QWR-01: Inmutabilidad del Registro de Recepción (Bitácora de Control)

- **Descripción:** Las recepciones registradas en la tabla `quality_warehouse_receptions` actúan como registros inmutables de bitácora. No se permite el borrado físico ni lógico (`deleted_at` inexistente) de las recepciones ni de sus renglones de detalle.
- **Comportamiento Global:** Una vez insertada una recepción por el Inspector de Calidad, la estructura de la cabecera y los renglones capturados no pueden modificarse ni eliminarse. Los campos de auditoría de actualización (`updated_at` e `id_updated_by`) están reservados exclusivamente para las interacciones posteriores del Manager de Calidad.

### BR-QWR-02: Exclusividad Mutua en Motivos de Devolución y justificación Obligatoria para Motivo "Otro"

- **Descripción:** El formulario de recepción exige la selección de exactamente un motivo de devolución entre las banderas booleanas disponibles (`is_mot_*`).
- **Comportamiento Global:**
  - A nivel de validación en backend/UI, únicamente uno de los campos de motivo puede enviarse con valor `true`.
  - Si se selecciona el motivo "Otro" (`is_mot_other = true`) el campo `mot_other_specify` requeriere obligatoriamente la justificación textual.

### BR-QWR-03: Determinación del Tipo de Devolución por Documento Físico

- **Descripción:** La clasificación de `return_type` (`'PARCIAL'` o `'TOTAL'`) se realiza en rampa tomando como referencia la copia física de la factura o remisión presentada por el chofer al reingresar.
- **Comportamiento Global:** El Inspector de Calidad coteja la mercancía física contra el documento impreso:
  - `'TOTAL'`: Si reingresa la totalidad del pedido/partidas amparadas en el documento físico.
  - `'PARCIAL'`: Si reingresa únicamente una fracción del volumen o ciertas partidas del documento físico.

### BR-QWR-04: Captura Ciega de Detalles de Producto y Notificación de Discrepancias

- **Descripción:** La captura de lotes, piezas y kilogramos en `quality_warehouse_reception_details` se realiza por pesado y conteo físico directo en rampa.
- **Comportamiento Global:**
  - Los campos `total_weight_kg > 0` y `pieces_quantity > 0` y `unit_package` (por ejemplo: `'SCO'`, `'BOL'`, `'BID'`) son estrictamente obligatorios.
  - **Control de Discrepancias:** Cuando la recepción esté vinculada a una recolección previa (`id_recollection_authorization`), el backend comparará el peso recibido (`total_weight_kg`) contra el peso recolectado reportado en la orden. Si la diferencia es mayor al 1%, el sistema registrará una alerta de discrepancia en la bitácora y notificará al Manager de Calidad para su auditoría en el Módulo de Quejas.

### BR-QWR-05: Carácter Informativo de la Acción a Realizar

- **Descripción:** Las banderas booleanas de acción a realizar en rampa (`act_product_change`, `act_reschedule_delivery`, `act_order_cancellation`) son estrictamente informativas y operativas para la logística de rampa, representan la resolución administrativa de la devolución y son mutuamente excluyentes entre sí.
- **Comportamiento Global:** Los valores de estos campos es nula al momento del registro inicial por parte del Inspector de Calidad. Únicamente los usuarios con rol **MANAGER** tienen permisos de escritura sobre estas banderas, pudiendo activar como máximo una sola acción por recepción. Cada actualización registra la fecha en `updated_at` y el identificador en `id_updated_by`. No sustituyen la resolución técnica ni los dictámenes financieros/comerciales documentados formalmente en `quality_customer_complaints`.

### BR-QWR-06: Vinculación de Origen, Llaves Foráneas y Eventos Síncronos

- **Descripción:** Al insertar la recepción en rampa, el backend vincula de forma autogestionada las llaves de origen y ejecuta las transiciones de estado hacia los módulos CPR y QLR.
- **Comportamiento Global:**
  - **Para Devoluciones Con Posesión (`CAL-FOR-01`):** El backend busca `invoice_reference` en `quality_recollection_authorizations` con `status IN ('PROGRAMADO', 'RECOLECTADO')`. Al encontrar coincidencia:
    1. Puebla `quality_warehouse_receptions.id_recollection_authorization`.
    2. Hereda el ID de la queja en `quality_warehouse_receptions.id_complaint`.
    3. **Trigger Síncrono:** Actualiza `quality_recollection_authorizations.status` $\rightarrow$ `'ENTREGADO_ALMACEN'`.
    4. **Trigger Síncrono:** Actualiza `quality_customer_complaints.status` $\rightarrow$ `'RECIBIDO_ALMACEN'`.
  - **Para Rechazos Sin Posesión (`CAL-FOR-02`):** La recepción en rampa se crea sin orden de recolección (`id_recollection_authorization = NULL`) e `id_complaint` queda nulo momentáneamente. Cuando el Manager de Calidad crea la queja `CAL-FOR-02` asignando el ID de esta recepción, el backend actualiza `quality_warehouse_receptions.id_complaint` con la queja resultante.

### BR-QWR-07: Asistencia de Mapeo y Responsabilidad Manual del Dictamen (CAL-FOR-02)

- **Descripción:** Al iniciar la creación de una queja sin posesión (**CAL-FOR-02**) vinculando una recepción de rampa, el sistema ofrecerá una sugerencia previa de clasificación basada en el motivo capturado en almacén. Sin embargo, el dictamen final, la selección de la causa global y el checklist de desviaciones son responsabilidad exclusiva y manual del **MANAGER de Calidad**.
- **Comportamiento Global:**
  - El **MANAGER de Calidad** conservará en todo momento la facultad y la obligación de confirmar, cambiar o modificar manualmente la causa global (`quality_customer_complaints.is_dev_*`) o las desviaciones por partida (`quality_complaint_items.is_dev_*`) según la investigación técnica antes de proceder con el guardado o dictamen.
  - El sistema mapeará el motivo de rampa (`quality_warehouse_receptions.is_mot_*`) únicamente como una sugerencia de precarga en la UI del siguiente modo:

| Motivo en Rampa (`quality_warehouse_receptions`)                                     | Causa Global / Checklist en Queja (`quality_customer_complaints`) |
| :----------------------------------------------------------------------------------- | :---------------------------------------------------------------- |
| `is_mot_not_found`, `is_mot_wrong_location`                                          | `is_dev_not_found = true` (Cliente no se encontraba)              |
| `is_mot_delayed_delivery`, `is_mot_wait_time`, `is_mot_excess_customers`             | `is_dev_outside_hours = true` (Fuera de horario)                  |
| `is_mot_no_cash_total`, `is_mot_no_cash_cod`                                         | `is_dev_payment_missing = true` (Falta de pago)                   |
| `is_mot_customer_error`, `is_mot_sales_error`, `is_mot_driver_error`, `is_mot_other` | `is_dev_customer_rejected = true` (El cliente no lo quiso)        |
| `is_mot_out_of_spec`                                                                 | `is_quality_deviation = true` (Detalle por partida)               |
| `is_mot_safety_issue`                                                                | `is_food_safety_deviation = true` (Detalle por partida)           |
| `is_mot_incomplete_weight`, `is_mot_incomplete_order`                                | `is_dev_incomplete_weight = true` (Detalle por partida)           |
| `is_mot_picking_error`, `is_mot_documentation_error`                                 | `is_dev_wrong_product = true` (Detalle por partida)               |

---

---

## 👥 Historias de Usuario (User Stories)

### US-QWR-01: Registro e Inspección en Rampa y Disparo de Eventos

- **Como:** Inspector de Calidad / Personal de Almacén (**INSPECTOR**)
- **Quiero:** Registrar la entrada física de mercancía devuelta especificando cliente, factura, tipo de devolución, motivo y el desglose de lotes/pesos.
- **Para:** Alimentar la bitácora inmutable de rampa y detonar las transiciones del flujo en los módulos de recolección y quejas.
- **Criterios de Aceptación:**
  - **C.A. 1.1:** El formulario solicita obligatoriamente: fecha/hora de recepción, cliente (`id_client`), ejecutivo (`id_sales_executive`), tipo de devolución (`return_type` = `'PARCIAL'` o `'TOTAL'` determinado mediante cotejo contra el documento físico), referencia de factura (`invoice_reference`) y exactamente un motivo (`is_mot_*` = `true`).
  - **C.A. 1.2:** Si se selecciona `is_mot_other` = `true`, la UI exige la captura de `mot_other_specify`.
  - **C.A. 1.3:** Permite capturar renglones en `quality_warehouse_reception_details` ingresando `id_product`, `lot_number_received`, `expiration_date_received`, `unit_package`, `pieces_quantity` ($> 0$) y `total_weight_kg` ($> 0$). Un mismo producto puede repetirse si proviene de distintos lotes o caducidades.
  - **C.A. 1.4:** Al guardar, se genera el folio `ALM-AA-#####` e `id_inspector_user`. El backend evalúa `invoice_reference`:
    - Si la factura existe en una orden de recolección activa, asocia `id_recollection_authorization` e `id_complaint`, e invoca síncronamente la actualización de la recolección a `ENTREGADO_ALMACEN` y de la queja padre a `RECIBIDO_ALMACEN` (**BR-QWR-06**).
    - Evalúa el peso recibido contra el peso de la orden de recolección. Si la variación es mayor al $1\%$, genera una alerta de discrepancia para auditoría de Calidad (**BR-QWR-04**).

### US-QWR-02: Asignación Informativa de Acción a Realizar por Dirección de Calidad

- **Como:** Manager de Calidad (**MANAGER**).
- **Quiero:** Consultar las recepciones registradas en bitácora y marcar la acción informativa de manejo en rampa (cambio de producto, reprogramación o cancelación).
- **Para:** Dar visibilidad operativa a los auxiliares de almacén sobre el destino visual del lote en rampa.
- **Criterios de Aceptación:**
  - **C.A. 2.1:** La UI permite al usuario con rol **MANAGER** consultar el listado de recepciones y editar únicamente la sección "Acción a Realizar" (`act_*`) de un folio guardado (**BR-QWR-05**).
  - **C.A. 2.2:** La UI permite seleccionar como máximo una acción (`act_product_change`, `act_reschedule_delivery` o `act_order_cancellation`). Al seleccionar una, desmarca automáticamente las demás (**BR-QWR-05**).
  - **C.A. 2.3:** Al guardar, el backend asienta `id_updated_by` y `updated_at`, manteniendo intacta la información original registrada por el inspector. El sistema despliega un aviso recordando que la resolución comercial y contable formal debe asentarse en el Módulo de Quejas (**BR-QWR-05**).
