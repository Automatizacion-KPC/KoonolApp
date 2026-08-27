# 🔍 Módulo: Calidad - Devoluciones y Rechazos de Producto por Cliente (Customer Product Rejection - CPR)

El módulo de **Devoluciones y Rechazos de Producto por Cliente** gestiona el flujo operativo, investigación técnica, dictamen y resolución ante no conformidades reportadas por el cliente. Abarca tanto reclamaciones posteriores a la entrega con mercancía en poder del cliente (**Con Posesión - CAL-FOR-01**) como rechazos inmediatos en ruta de reparto (**Sin Posesión - CAL-FOR-02**). Garantiza la trazabilidad de lotes/pesos, la ejecución de planes de acción correctiva y la aprobación administrativa/técnica interdepartamental; apoyándose de los subsistemas de recolección logística, recepción en almacén y registro central de no conformidades (QNC).

---

---

## 💼 Reglas de Negocio (Business Rules)

### BR-CPR-01: Tipificación de Formatos, Folios Distintivos e Integración con el QNC

- **Descripción:** Las solicitudes de devolución y rechazo deben clasificarse según la posesión física de la mercancía.
- **Comportamiento Global:**
  - **`CAL-FOR-01` (Con Posesión):** Aplica cuando el cliente recibió formalmente el pedido y posteriormente reporta un reclamo. Genera folios con el prefijo `NCC-CP-YY-#####`.
  - **`CAL-FOR-02` (Sin Posesión):** Aplica cuando el cliente rechaza la entrega de forma inmediata en la unidad de transporte. Genera folios con el prefijo `NCC-SP-YY-#####`.
  - `YY` corresponde a los dos últimos dígitos del año en curso y `#####` a un consecutivo de 5 dígitos reiniciado anualmente.
- **Integración QNC:** Al cambiar el estado del folio a `CERRADO`, el backend actualiza automáticamente el expediente vinculado en el sistema central de no conformidades (`quality_non_conformities`) con `source_type = 'RECLAMO_CLIENTE'`.

### BR-CPR-02: Flujo Asincrónico y Secuencia de Estados

- **Descripción:** El ciclo de vida de un folio se rige por la secuencia de estados de acuerdo con el tipo de formato.
- **Comportamiento por Ruta:**
  - **Ruta `CAL-FOR-01` (Con Posesión):**
    `ABIERTO (Ventas)` $\rightarrow$ `DICTAMINADO (Calidad)` $\rightarrow$ `AUTORIZADO / RECHAZADO (Administración)` $\rightarrow$ `RECOLECTADO (Evento Backend por Chofer)` $\rightarrow$ `RECIBIDO_ALMACEN (Evento Backend por Almacén)` $\rightarrow$ `CERRADO (Manual por Calidad)`
  - **Ruta `CAL-FOR-02` (Sin Posesión):**
    Recepción física previa en rampa $\rightarrow$ `RECIBIDO_ALMACEN (Calidad)` $\rightarrow$ `DICTAMINADO (Calidad)` $\rightarrow$ `AUTORIZADO / RECHAZADO (Administración)` $\rightarrow$ `CERRADO (Manual por Calidad)`
  - **Transiciones Automáticas:** Las transiciones a los estados `RECOLECTADO` y `RECIBIDO_ALMACEN` en `quality_customer_complaints` son ejecutadas automáticamente por eventos del backend al cambiar el estatus en los módulos de Recolección y Recepción respectivamente.
  - Las solicitudes `CAL-FOR-02` omiten por completo la orden de recolección y el estado `RECOLECTADO`.

### BR-CPR-03: Registro de Queja Con Posesión (CAL-FOR-01)

- **Descripción:** El flujo para producto en posesión del cliente inicia formalmente desde la fuerza de ventas.
- **Comportamiento Global:**
  - El **Ejecutivo de Ventas** registra el formulario `CAL-FOR-01` ingresando cliente, factura/remisión (`invoice_reference`), partidas afectadas y evidencia fotográfica.
  - El folio se crea con estado inicial `ABIERTO`.

### BR-CPR-04: Registro de Rechazo Inmediato Sin Posesión (CAL-FOR-02)

- **Descripción:** Los rechazos inmediatos en ruta de reparto se originan a partir del reingreso físico de la unidad de transporte a rampa.
- **Comportamiento Global:**
  - La mercancía es recepcionada en primera instancia por personal de rampa/almacén en la tabla `quality_warehouse_receptions`.
  - El **MANAGER de Calidad** crea formalmente el expediente `CAL-FOR-02` en `quality_customer_complaints` asociando obligatoriamente el ID de la recepción física previa (`id_quality_warehouse_reception`). El sistema autocompletará en el formulario los campos id_client, id_sales_executive e invoice_reference con la información capturada por Almacén, sin embargo, mantendrá la facultad de editar manualmente estos tres campos antes de guardar.

### BR-CPR-05: Exclusividad Mutua en Desviaciones Logísticas Globales (CAL-FOR-02)

- **Descripción:** En solicitudes `CAL-FOR-02`, el motivo de rechazo en ruta debe delimitarse a nivel de encabezado.
- **Comportamiento Global:** Las banderas de desviación global (`is_dev_not_found`, `is_dev_outside_hours`, `is_dev_payment_missing`, `is_dev_customer_rejected`) son mutuamente excluyentes. El sistema permite marcar únicamente una causa global por folio de rechazo inmediato.

### BR-CPR-06: Obligatoriedad Condicional de Evidencia Fotográfica

- **Descripción:** La exigencia de evidencia visual para respaldar el dictamen varía según el origen de la reclamación.
- **Comportamiento Global:**
  - Para **`CAL-FOR-01` (Con Posesión):** El campo `has_photo_evidence` debe ser `true` y la carga de al menos una imagen en `photos_url` (`JSONB`) es obligatoria a nivel de partida.
  - Para **`CAL-FOR-02` (Sin Posesión):** La adjunción de imágenes es opcional.

### BR-CPR-07: Dictamen Técnico y Plan de Acción Mínimo

- **Descripción:** Para que un folio pueda avanzar al estado `DICTAMINADO`, el departamento de Calidad debe documentar el análisis técnico y los compromisos de solución.
- **Comportamiento Global:** El backend impide la transición a `DICTAMINADO` si no se ha capturado el análisis de causa raíz (`root_cause_analysis`), la solución final (`final_solution`), la definición del flag `requires_recollection` (booleano) y al menos un registro activo en la tabla `quality_complaint_action_plans`.Si `form_type = 'CAL-FOR-02'`, el backend forzará automáticamente `requires_recollection = false` e inhabilitará su edición en la interfaz.

### BR-CPR-08: Matriz de Firmas y Doble Autorización

- **Descripción:** La aprobación de un reclamo exige la validación formal conjunta del departamento de Calidad y la Gerencia de Administración.
- **Comportamiento Global:**
  - El **MANAGER de Calidad** firma digitalmente registrando `id_quality_reviewer` y `quality_signature_at` al momento de dictaminar.
  - El **MANAGER de Administración** revisa el dictamen y aprueba o rechaza registrando `id_admin_authorizer` y `admin_signature_at`.
  - La presencia de ambas firmas es prerrequisito para que el estado cambie a `AUTORIZADO` o `RECHAZADO`.

### BR-CPR-09: Generación Automática de Orden de Recolección (Solo CAL-FOR-01)

- **Descripción:** Cuando una queja para producto en posesión del cliente es autorizada y requiere el retorno del material, se automatiza el despacho logístico.
- **Comportamiento Global:** Si `form_type = 'CAL-FOR-01'`, el estado es `AUTORIZADO` y `requires_recollection = true`, el backend genera automáticamente un registro en `quality_recollection_authorizations` (estado `PENDIENTE`) y desglose en `quality_recollection_authorization_details`.

### BR-CPR-10: Cierre Manual del Expediente

- **Descripción:** La conclusión definitiva de un folio de queja/devolución es una acción manual explícita y exclusiva ejecutada por el **MANAGER de Calidad**. El seguimiento al cumplimiento de los compromisos en `quality_complaint_action_plans` es una responsabilidad estrictamente operativa del personal a cargo, fuera del alcance del sistema.
- **Comportamiento Global:**
  - **Para CAL-FOR-01 con recolección (`requires_recollection = true`):** El sistema permitirá el cierre únicamente si el estado actual es `RECIBIDO_ALMACEN` (o `RECHAZADO`).
  - **Para CAL-FOR-01 sin recoleccion (`requires_recollection = false`):** El sistema permitirá el cierre directamente cuando el estado se encuentre en `AUTORIZADO` (o `RECHAZADO`).
  - **Para CAL-FOR-02:** El sistema permitirá el cierre únicamente si el estado se encuentra en `AUTORIZADO` (o `RECHAZADO`), habiendo iniciado previamente desde `RECIBIDO_ALMACEN`.

---

---

## 👥 Historias de Usuario (User Stories)

### US-CPR-01: Levantamiento de Queja por Producto Con Posesión (CAL-FOR-01)

- **Como:** Ejecutivo de Ventas.
- **Quiero:** Registrar el reporte de queja presentado por un cliente adjuntando evidencia fotográfica.
- **Para:** Iniciar el proceso de evaluación técnica y dictamen por parte del departamento de Calidad.
- **Criterios de Aceptación:**
  - **C.A. 1.1:** La UI solicita datos del cliente, factura/remisión (`invoice_reference`), detalle de productos, lotes, empaques, piezas, peso en KG y la relatoría del problema (`problem_description`).
  - **C.A. 1.2:** El sistema exige la marca `has_photo_evidence = true` y la carga obligatoria de al menos una URL de imagen en `photos_url` (**BR-CPR-06**).
  - **C.A. 1.3:** Al guardar, se genera el folio `NCC-CP-YY-#####` en estado inicial `ABIERTO` (**BR-CPR-01**, **BR-CPR-02**).
  - **C.A. 1.4:** Cada partida permite desglosar el producto (`id_product`), lote, fecha de caducidad, empaque, piezas devueltas, peso en KG y el checklist de desviaciones de inocuidad o calidad.

### US-CPR-02: Registro de Rechazo Inmediato Sin Posesión (CAL-FOR-02)

- **Como:** Gerente (`MANAGER`) de Calidad.
- **Quiero:** Dar de alta un rechazo de producto retornado en ruta de reparto vinculándolo a su recepción física en rampa.
- **Para:** Documentar la causa del rechazo inmediato y pasar el folio al proceso de dictamen y plan de acción.
- **Criterios de Aceptación:**
  - **C.A. 2.1:** El registro exige seleccionar una recepción en rampa (`id_quality_warehouse_reception`). Al elegirla, la UI precargará automáticamente `id_client`, `id_sales_executive` e `invoice_reference`, permitiendo al **MANAGER de Calidad** la edición libre de estos campos en caso de requerir corrección (**BR-CPR-04**).
  - **C.A. 2.2:** Permite seleccionar únicamente una causa global de rechazo en ruta (`is_dev_*`) (**BR-CPR-05**) y desglosar las partidas afectadas.
  - **C.A. 2.3:** La carga de imágenes fotográficas es opcional (**BR-CPR-06**).
  - **C.A. 2.4:** Al guardar, se genera el folio `NCC-SP-YY-#####` en estado inicial `RECIBIDO_ALMACEN` (**BR-CPR-01**, **BR-CPR-02**).

### US-CPR-03: Dictamen Técnico, Análisis de Causa Raíz y Plan de Acción

- **Como:** Gerente (`MANAGER`) de Calidad.
- **Quiero:** Registrar el análisis de causa raíz, la solución propuesta, el plan de acción y definir si se requiere recolección física.
- **Para:** Firmar el dictamen técnico y solicitar la aprobación administrativa.
- **Criterios de Aceptación:**
  - **C.A. 3.1:** Para avanzar al estado `DICTAMINADO`, la UI exige capturar `root_cause_analysis`, `final_solution`, establecer el flag `requires_recollection` (`true`/`false`) y agregar al menos un compromiso asignado en `quality_complaint_action_plans` (**BR-CPR-07**).
  - **C.A. 3.2:** El `MANAGER` de Calidad aplica su firma digital, registrando `id_quality_reviewer` y `quality_signature_at` (**BR-CPR-08**).
  - **C.A. 3.3:** El estado del folio actualiza a `DICTAMINADO`.

### US-CPR-04: Autorización Administrativa, Disparo Logístico y Cierre del Folio

- **Como:** Gerente de Administración (`MANAGER`) y Gerente de Calidad (`MANAGER`).
- **Quiero:** Revisar y firmar la aprobación del dictamen, detonar automáticamente la orden de recolección (si aplica) y cerrar el expediente.
- **Para:** Validar la resolución financiera/comercial, asegurar el retorno logístico del material.
- **Criterios de Aceptación:**
  - **C.A. 4.1:** El `MANAGER` de Administración revisa el folio dictaminado y emite su firma (`id_admin_authorizer`, `admin_signature_at`), aprobando o rechazando el plan de acción. El estado cambia a `AUTORIZADO` o `RECHAZADO` (**BR-CPR-08**).
  - **C.A. 4.2:** Si el folio es `CAL-FOR-01`, su estado es `AUTORIZADO` y `requires_recollection = true`, el sistema crea en automático la orden de recolección en `quality_recollection_authorizations` con estado `PENDIENTE` (**BR-CPR-09**).
  - **C.A. 4.3:** En solicitudes `CAL-FOR-01`, el estado de la queja actualizará automáticamente a `RECOLECTADO` cuando el chofer confirme en ruta, y a `RECIBIDO_ALMACEN` cuando se registre el reingreso en rampa (**BR-CPR-02**, **BR-QLR-04**, **BR-QLR-07**).
  - **C.A. 4.4:** El botón de **"Cerrar Queja"** se habilitará para el **MANAGER de Calidad** cuando se cumpla alguna de las siguientes condiciones:
    - Folio `CAL-FOR-01` en estado `RECIBIDO_ALMACEN` o `RECHAZADO`.
    - Folio `CAL-FOR-01` con `requires_recollection = false` en estado `AUTORIZADO`.
    - Folio `CAL-FOR-02` en estado `AUTORIZADO` o `RECHAZADO`. Al ejecutar la acción, el estado pasará a `CERRADO`.

---

---

## 🔄 Diagramas de Flujo

### 1. Flujo Operativo CAL-FOR-01: Devolución Posterior (Con Posesión del Cliente)

```mermaid
graph TD
    A[Inicio: Cliente reporta falla a Ventas] --> B[Ejecutivo de Ventas captura CAL-FOR-01]
    B --> C{¿Adjunta evidencia fotográfica? \nhas_photo_evidence = true}
    C -- No --> B
    C -- Sí --> D[Sistema genera folio NCC-CP-YY-##### en estado ABIERTO]
    D --> E[MANAGER de Calidad evalúa, registra causa raíz, solución final y plan de acción]
    E --> F[MANAGER de Calidad firma dictamen y define si requires_recollection]
    F --> G[Estado cambia a DICTAMINADO]
    G --> H[MANAGER de Administración revisa dictamen y aplica firma autorizadora]
    H --> I{¿Aprobado por Administración?}
    I -- No --> J[Estado cambia a RECHAZADO]
    J --> K[MANAGER de Calidad realiza Cierre Manual]
    I -- Sí --> L[Estado cambia a AUTORIZADO]
    L --> M{¿requires_recollection = true?}
    M -- No --> K
    M -- Sí --> N[Backend genera automáticamente Orden de Recolección en PENDIENTE]
    N --> O[Chofer confirma recolección en ruta]
    O --> P[Evento Backend: Estado cambia a RECOLECTADO]
    P --> Q[Almacén/Rampa registra reingreso de mercancía]
    Q --> R[Evento Backend: Estado cambia a RECIBIDO_ALMACEN]
    R --> K
    K --> S[Estado cambia a CERRADO]
    S --> T[Backend actualiza QNC quality_non_conformities con source_type = RECLAMO_CLIENTE]
    T --> U[Fin del Proceso]
```

#### Referencias

- Reglas de Negocio (BR):
  - **[BR-CPR-01]:** Tipificación de Formatos, Folios Distintivos (NCC-CP-YY-#####) e Integración con QNC al Cierre.
  - **[BR-CPR-02]:** Flujo Asincrónico, Secuencia de Estados (Ruta CAL-FOR-01) y Transiciones Automáticas del Backend.
  - **[BR-CPR-03]:** Registro de Queja Con Posesión (CAL-FOR-01) desde Ventas en estado ABIERTO.
  - **[BR-CPR-06]:** Obligatoriedad Condicional de Evidencia Fotográfica (has_photo_evidence = true y URLs obligatorias en partidas).
  - **[BR-CPR-07]:** Dictamen Técnico, Causa Raíz, Solución Final y Plan de Acción Mínimo.
  - **[BR-CPR-08]:** Matriz de Firmas y Doble Autorización (Calidad y Administración).
  - **[BR-CPR-09]:** Generación Automática de Orden de Recolección (Solo CAL-FOR-01 en AUTORIZADO y requires_recollection = true).
  - **[BR-CPR-10]:** Cierre Manual Exclusivo por MANAGER de Calidad desde RECIBIDO_ALMACEN, AUTORIZADO (sin recolección) o RECHAZADO.
- Historias de Usuario (US):
  - **[US-CPR-01]:** Levantamiento de Queja por Producto Con Posesión (CAL-FOR-01).
  - **[US-CPR-03]:** Dictamen Técnico, Análisis de Causa Raíz y Plan de Acción.
  - **[US-CPR-04]:** Autorización Administrativa, Disparo Logístico y Cierre del Folio.
- Criterios de Aceptación (C.A):
  - **[C.A 1.1]:** Captura de datos del cliente, factura/remisión, productos, lotes, peso en KG y descripción del problema.
  - **[C.A 1.2]:** Carga obligatoria de al menos una foto en photos_url.
  - **[C.A 1.3]:** Generación de folio NCC-CP-YY-##### en estado inicial ABIERTO.
  - **[C.A 3.1]:** Exigencia de causa raíz, solución final, flag de recolección y plan de acción para dictaminar.
  - **[C.A 3.2]:** Firma digital del MANAGER de Calidad (id_quality_reviewer).
  - **[C.A 4.1]:** Firma del MANAGER de Administración (id_admin_authorizer) para pasar a AUTORIZADO o RECHAZADO.
  - **[C.A 4.2]:** Disparo automático de orden en quality_recollection_authorizations (PENDIENTE).
  - **[C.A 4.3]:** Actualización automática por eventos backend a RECOLECTADO y RECIBIDO_ALMACEN.
  - **[C.A 4.4]:** Habilitación del botón "Cerrar Queja" y transición manual a CERRADO.

### 2. Flujo Operativo CAL-FOR-02: Rechazo Inmediato (Sin Posesión del Cliente)

```mermaid
graph TD
    A[Inicio: Unidad de reparto regresa a rampa con material rechazado] --> B[Almacén/Rampa registra reingreso en quality_warehouse_receptions]
    B --> C[MANAGER de Calidad crea expediente CAL-FOR-02 asociando id_quality_warehouse_reception]
    C --> D[Sistema precarga cliente, ejecutivo y factura de la recepción]
    D --> E[MANAGER de Calidad valida/edita datos y desglosa partidas]
    E --> F{¿Selecciona únicamente una causa global is_dev_*?}
    F -- No --> E
    F -- Sí --> G[Sistema genera folio NCC-SP-YY-##### en estado inicial RECIBIDO_ALMACEN]
    G --> H[Backend forzará automáticamente requires_recollection = false]
    H --> I[MANAGER de Calidad captura causa raíz, solución final y plan de acción]
    I --> J[MANAGER de Calidad firma dictamen técnico]
    J --> K[Estado cambia a DICTAMINADO]
    K --> L[MANAGER de Administración revisa dictamen y aplica firma autorizadora]
    L --> M{¿Aprobado por Administración?}
    M -- No --> N[Estado cambia a RECHAZADO]
    M -- Sí --> O[Estado cambia a AUTORIZADO]
    N --> P[MANAGER de Calidad realiza Cierre Manual]
    O --> P
    P --> Q[Estado cambia a CERRADO]
    Q --> R[Backend actualiza QNC quality_non_conformities con source_type = RECLAMO_CLIENTE]
    R --> S[Fin del Proceso]
```

#### Referencias

- Reglas de Negocio (BR):
  - **[BR-CPR-01]:** Tipificación de Formatos, Folios Distintivos (NCC-SP-YY-#####) e Integración QNC al Cierre.
  - **[BR-CPR-02]:** Flujo Asincrónico y Secuencia de Estados (Ruta CAL-FOR-02, inicia en RECIBIDO_ALMACEN y omite recolección).
  - **[BR-CPR-04]:** Registro de Rechazo Inmediato Sin Posesión (CAL-FOR-02) con precargado y facultad de edición manual.
  - **[BR-CPR-05]:** Exclusividad Mutua en Desviaciones Logísticas Globales (is*dev*\*).
  - **[BR-CPR-06]:** Evidencia fotográfica opcional en CAL-FOR-02.
  - **[BR-CPR-07]:** Dictamen Técnico, Plan de Acción Mínimo y Forzado Automático de requires_recollection = false.
  - **[BR-CPR-08]:** Matriz de Firmas y Doble Autorización (Calidad y Administración).
  - **[BR-CPR-10]:** Cierre Manual Exclusivo por MANAGER de Calidad desde AUTORIZADO o RECHAZADO.
- Historias de Usuario (US):
  - **[US-CPR-02]:** Registro de Rechazo Inmediato Sin Posesión (CAL-FOR-02).
  - **[US-CPR-03]:** Dictamen Técnico, Análisis de Causa Raíz y Plan de Acción.
  - **[US-CPR-04]:** Autorización Administrativa y Cierre del Folio.
- Criterios de Aceptación (C.A):
  - **[C.A 2.1]:** Selección obligatoria de id_quality_warehouse_reception, autocompletado y edición manual habilitada.
  - **[C.A 2.2]:** Selección exclusiva de una sola causa global de rechazo (is*dev*\*).
  - **[C.A 2.3]:** Carga opcional de evidencia fotográfica.
  - **[C.A 2.4]:** Generación de folio NCC-SP-YY-##### en estado inicial RECIBIDO_ALMACEN.
  - **[C.A 3.1]:** Captura de causa raíz, solución final y plan de acción para dictaminar.
  - **[C.A 3.2]:** Firma digital del MANAGER de Calidad (id_quality_reviewer).
  - **[C.A 4.1]:** Firma del MANAGER de Administración (id_admin_authorizer) para pasar a AUTORIZADO o RECHAZADO.
  - **[C.A 4.4]:** Cierre explícito por el MANAGER de Calidad a CERRADO.

### 3. Diagrama de Transición de Estados y Disparadores del Folio

```mermaid
graph TD
    subgraph RUTA_CAL_FOR_01 [Ruta CAL-FOR-01: Con Posesión - NCC-CP-YY-#####]
        A1[ABIERTO] -->|Firma Dictamen Calidad| B1[DICTAMINADO]
        B1 -->|Firma Admin| C1{¿Dictamen Admin?}
        C1 -- Rechazado --> D1[RECHAZADO]
        C1 -- Aprobado + requires_recollection=true --> E1[AUTORIZADO]
        C1 -- Aprobado + requires_recollection=false --> E1_1[AUTORIZADO]
        E1 -->|Evento Backend: Chofer Recolecta| F1[RECOLECTADO]
        F1 -->|Evento Backend: Reingreso Rampa| G1[RECIBIDO_ALMACEN]
        G1 -->|Cierre Manual Calidad| H1[CERRADO]
        E1_1 -->|Cierre Manual Calidad| H1
        D1 -->|Cierre Manual Calidad| H1
    end

    subgraph RUTA_CAL_FOR_02 [Ruta CAL-FOR-02: Sin Posesión - NCC-SP-YY-#####]
        A2[Recepción Rampa Previa] -->|Registro Calidad + Vincular ID| B2[RECIBIDO_ALMACEN]
        B2 -->|Firma Dictamen Calidad| C2[DICTAMINADO]
        C2 -->|Firma Admin| D2{¿Dictamen Admin?}
        D2 -- Aprobado --> E2[AUTORIZADO]
        D2 -- Rechazado --> F2[RECHAZADO]
        E2 -->|Cierre Manual Calidad| G2[CERRADO]
        F2 -->|Cierre Manual Calidad| G2
    end

    H1 -->|Evento Backend: Sync QNC| I1[quality_non_conformities: source_type=RECLAMO_CLIENTE]
    G2 -->|Evento Backend: Sync QNC| I1
    I1 --> J1[FIN DEL CICLO DE VIDA]
```

#### Referencias

- Reglas de Negocio (BR):
  - **[BR-CPR-01]:** Prefijos de folios (NCC-CP / NCC-SP) e integración automática con QNC al llegar a CERRADO.
  - **[BR-CPR-02]:** Flujo Asincrónico y Secuencia de Estados por tipo de formato (CAL-FOR-01 inicia en ABIERTO; CAL-FOR-02 inicia en RECIBIDO_ALMACEN).
  - **[BR-CPR-08]:** Requisito de doble firma (Calidad y Administración) para mover a AUTORIZADO o RECHAZADO.
  - **[BR-CPR-09]:** Generación automática de orden logística y eventos asincrónicos para transiciones a RECOLECTADO y RECIBIDO_ALMACEN.
  - **[BR-CPR-10]:** Cierre manual exclusivo por MANAGER de Calidad diferenciado por tipo de formato (RECIBIDO_ALMACEN / AUTORIZADO sin recolección para CAL-FOR-01; AUTORIZADO para CAL-FOR-02).
- Historias de Usuario (US):
  - **[US-CPR-01]:** Inicio en ABIERTO para CAL-FOR-01.
  - **[US-CPR-02]:** Precondición de recepción previa en rampa (id_quality_warehouse_reception) e inicio en RECIBIDO_ALMACEN para CAL-FOR-02.
  - **[US-CPR-03]:** Transición al estado DICTAMINADO.
  - **[US-CPR-04]:** Transiciones a AUTORIZADO, RECHAZADO, RECOLECTADO, RECIBIDO_ALMACEN y CERRADO.
- Criterios de Aceptación (C.A):
  - **[C.A 1.3]:** Estado inicial ABIERTO en CAL-FOR-01.
  - **[C.A 2.4]:** Estado inicial RECIBIDO_ALMACEN en CAL-FOR-02.
  - **[C.A 3.3]:** Transición a DICTAMINADO.
  - **[C.A 4.1]:** Transición a AUTORIZADO o RECHAZADO.
  - **[C.A 4.2]:** Disparo automático de orden de recolección en backend.
  - **[C.A 4.3]:** Avance operativo a RECOLECTADO y RECIBIDO_ALMACEN mediante eventos backend.
  - **[C.A 4.4]:** Habilitación condicional del botón de cierre y transición final a CERRADO.

---

---

- ⬆️ [Volver arriba](#)
- 📖 [Ir al Índice](../README.md#-5-índice-de-módulos-funcionales)
