# 👥 Módulo: Recepción de Devoluciones en Almacén (Quality Warehouse Receptions - QWR)

El módulo de **Recepción de Devoluciones en Almacén** digitaliza y estandariza el proceso operativo de entrada a ciegas de mercancías rechazadas directamente en ruta o procedentes de recolecciones autorizadas. Al operar como una bitácora de control interno independiente de SAP Business One, garantiza la trazabilidad física de los productos que reingresan a la planta, asegurando la captura imparcial de lotes, caducidades y pesajes por parte del personal de inspección, e integrándose de manera posterior con las resoluciones del Manager de Calidad y la gestión global de quejas de clientes.

---

---

## 💼 Reglas de Negocio (Business Rules)

### BR-QCR-01: Inmutabilidad del Registro de Recepción (Bitácora de Control)

- **Descripción:** Las recepciones registradas en la tabla `quality_warehouse_receptions` actúan como registros inmutables de bitácora. No se permite el borrado físico ni lógico (`deleted_at` inexistente) de las recepciones ni de sus renglones de detalle.
- **Comportamiento Global:** Una vez insertada una recepción por el Inspector de Calidad, la estructura de la cabecera y los renglones capturados no pueden modificarse ni eliminarse. Los campos de auditoría de actualización (`updated_at` e `id_updated_by`) están reservados exclusivamente para las interacciones posteriores del Manager de Calidad.

### BR-QCR-02: Exclusividad Mutua en Motivos de Devolución

- **Descripción:** El formulario de recepción exige la selección de exactamente un motivo de devolución entre las banderas booleanas disponibles (`is_mot_*`).
- **Comportamiento Global:** A nivel de validación en backend/UI, únicamente uno de los campos de motivo puede enviarse con valor `true`. Si se selecciona un motivo general no listado o personalizado, debe activarse la bandera correspondiente y requerirse obligatoriamente la justificación textual.

### BR-QCR-03: Justificación Obligatoria para Motivo "Otro"

- **Descripción:** Cuando se especifica un motivo de devolución que no entra en las categorías predefinidas de la inspección, el sistema exige la captura explícita del detalle.
- **Comportamiento Global:** Si el indicador de motivo "Otro" se marca como `true`, el campo `mot_other_specify` debe contener un texto explicativo válido (no nulo ni vacío). En caso contrario, la transacción se rechaza.

### BR-QCR-04: Exclusividad Mutua y Rol Exclusivo en Acciones a Realizar

- **Descripción:** Las banderas booleanas de acción a realizar (`act_product_change`, `act_reschedule_delivery`, `act_order_cancellation`) representan la resolución administrativa de la devolución y son mutuamente excluyentes entre sí.
- **Comportamiento Global:** La selección o actualización de estos campos es nula al momento del registro inicial por parte del Inspector de Calidad. Únicamente los usuarios con rol **MANAGER** tienen permisos de escritura sobre estas banderas, pudiendo activar como máximo una sola acción por recepción. Cada actualización registra la fecha en `updated_at` y el identificador en `id_updated_by`.

### BR-QCR-05: Captura Ciega de Detalles de Producto y Valores Positivos

- **Descripción:** El registro físico de los productos devueltos se realiza a ciegas, sin validación o restricción respecto al documento de origen o facturación. Cada renglón capturado debe representar valores cuantitativos reales y válidos.
- **Comportamiento Global:** Los campos `total_weight_kg` y `pieces_quantity` en la tabla `quality_warehouse_reception_details` deben ser estrictamente mayores a cero ($> 0$). El empaque unitario (`unit_package`) debe seleccionarse obligatoriamente de la lista predefinida del sistema (ej. `'SCO'`, `'BOL'`, `'BID'`).

### BR-QCR-06: Vinculación Automática por Coincidencia de Factura

- **Descripción:** Cuando se registra una recepción con una referencia de factura existente en las autorizaciones de recolección previas, el sistema asocia de forma autogestionada el origen del flujo.
- **Comportamiento Global:** Durante el evento de inserción de la recepción (`created_at`), un evento en backend evalúa el valor de `invoice_reference`. Si este coincide con el número de factura de un registro en `quality_recollection_authorizations`, el campo `id_recollection_authorization` se puebla automáticamente con la llave primaria correspondiente.

---

---

## 👥 Historias de Usuario (User Stories)

### US-QCR-01: Registro e Inspección Ciega de Recepción en Rampa

- **Como:** Inspector de Calidad / Personal de Almacén (**INSPECTOR**)
- **Quiero:** Registrar en el sistema la entrada física de mercancía devuelta especificando los datos del cliente, factura, motivo y el desglose a ciegas de lotes, empaques, piezas y pesos
- **Para:** Tener una bitácora inmutable de los productos que ingresan a la planta y permitir su trazabilidad sin sesgos por la facturación original.

**Criterios de Aceptación:**

- **C.A. 1.1:** El formulario solicita obligatoriamente: fecha/hora de recepción, cliente, ejecutivo de ventas, tipo de devolución (**PARCIAL** o **TOTAL**), referencia de factura y exactamente un motivo de devolución (`is_mot_*` = `true`).
- **C.A. 1.2:** Si el motivo seleccionado requiere especificación ("Otro"), el sistema habilita y exige la captura de `mot_other_specify`.
- **C.A. 1.3:** En la sección de detalle, el usuario puede agregar múltiples renglones por producto (`id_product`). Un mismo producto puede registrarse en más de un renglón si presenta diferente número de lote (`lot_number_received`) o fecha de caducidad (`expiration_date_received`).
- **C.A. 1.4:** Para cada renglón de detalle, el sistema valida que `total_weight_kg > 0`, `pieces_quantity > 0` y que `unit_package` pertenezca al catálogo cerrado configurado en el frontend.
- **C.A. 1.5:** Al guardar el registro, la recepción se almacena inmutablemente asignando el folio con formato `'ALM-AA-#####'` e identificando al usuario en `id_inspector_user`. El backend ejecuta la verificación de `invoice_reference` para enlazar `id_recollection_authorization` si existiese coincidencia.

### US-QCR-02: Definición de Acción a Realizar por Dirección de Calidad

- **Como:** Manager de Calidad (**MANAGER**)
- **Quiero:** Revisar las recepciones en bitácora y asignar la acción operativa correspondiente (cambio de producto, reprogramación de entrega o cancelación de pedido)
- **Para:** Dictaminar el tratamiento logístico y comercial que se le dará a la mercancía ingresada.

**Criterios de Aceptación:**

- **C.A. 2.1:** El sistema permite al usuario con rol **MANAGER** consultar las recepciones registradas y editar únicamente la sección de "Acción a Realizar".
- **C.A. 2.2:** La interfaz permite seleccionar como máximo una acción (`act_product_change`, `act_reschedule_delivery` o `act_order_cancellation`). Si se selecciona una nueva, desmarca automáticamente las demás.
- **C.A. 2.3:** Al guardar la actualización, el sistema guarda el ID del manager en `id_updated_by` y la fecha/hora actual en `updated_at`, manteniendo intactos los datos originales capturados por el inspector.
