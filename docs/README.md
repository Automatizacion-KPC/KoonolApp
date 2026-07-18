# 📘 Documentación General del Sistema | KoonolApp

Bienvenido al repositorio central de documentación de **KoonolApp**. Este espacio constituye la única fuente de verdad (_Single Source of Truth_) para las reglas de negocio, especificaciones funcionales, historias de usuario y diagramas de flujo que gobiernan la plataforma.

---

## 🎯 1. Objetivo y Alcance del Sistema

KoonolApp es una solución interna (Intranet/ERP) desarrollada con el objetivo primordial de **digitalizar y automatizar los procesos operativos y formatos físicos** de la organización que no son absorbidos por el sistema central SAP Business One.

El alcance del sistema está estrictamente limitado a la eficiencia operativa e interna (por ejemplo, gestión de incidencias, solicitudes de vacaciones, inspección de vehículos, y flujos de control de muestras o aseguramientos de calidad). En consecuencia:

- 🚫 **No se gestionan transacciones comerciales directas:** El sistema no contempla módulos de compraventa, gestión de divisas ni cálculo de impuestos.
- 🤝 **Complementariedad:** Funciona como un ecosistema satélite que extiende las capacidades operativas de la empresa sin duplicar las facultades financieras del ERP central.

---

## 🛡️ 2. Políticas de Seguridad y Control de Acceso

El sistema implementa un modelo de seguridad basado en la asignación de departamentos y una jerarquía estricta de roles.

### 🏢 2.1 Encapsulamiento por Departamento

A excepción de los roles globales de administración, **el acceso a los módulos está estrictamente encapsulado por departamento**. Los usuarios solo podrán interactuar con las interfaces y datos correspondientes a su área asignada.

### 👥 2.2 Jerarquía de Roles y Facultades

| Nivel | Rol en Sistema  | Alcance y Facultades Operativas                                                                                                                                              |
| :---: | :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **6** | `GOD`           | Acceso total, directo e irrestricto a la base de datos y entornos de backend.                                                                                                |
| **5** | `ADMIN`         | Administración funcional global: gestión de usuarios, configuración de catálogos maestros y supervisión de integraciones.                                                    |
| **4** | `COMPANY_OWNER` | Acceso exclusivo a un panel ejecutivo de reportes y métricas globales. Cuenta con acceso a módulos comunes (Muro, Notas, Tareas) y facultad de asignación directa de tareas. |
| **3** | `MANAGER`       | Gerencia de departamento. Control total de su dominio, reportes de área y facultad de asignación directa de tareas a subordinados.                                           |
| **2** | `SUPERVISOR`    | Supervisión operativa dentro de su departamento. Facultades de aprobación y revisión.                                                                                        |
| **1** | `LEADER`        | Coordinación operativa de tareas asignadas.                                                                                                                                  |
| **0** | `USER`          | Colaborador estándar. Registro básico de datos y ejecución de flujos operativos propios.                                                                                     |

### 🗂️ 2.3 Departamentos Contemplados

- 🏗️ Producción
- 🧪 I+D (Investigación y Desarrollo)
- 🔍 Calidad
- 📈 Mejora Continua
- 📦 Almacén
- 🚛 Logística
- 📊 Datos
- 💻 TI (Tecnologías de la Información)
- 🛍️ Compras
- 🤝 Ventas
- 👥 RH (Recursos Humanos)
- 💼 Administración

### 🌐 2.4 Módulos Transversales Comunes

Todos los usuarios del sistema, independientemente de su nivel o departamento, comparten acceso a las siguientes herramientas colaborativas:

- 📢 **Muro:** Espacio de comunicación interna y avisos institucionales.
- 📝 **Notas:** Gestión de anotaciones personales.
- 📋 **Tareas:** Flujo de asignación y seguimiento de actividades. Los roles de mando (`LEADER`, `SUPERVISOR`, `MANAGER` y superiores) poseen la facultad explícita de delegar tareas a usuarios específicos.

---

## 💾 3. Arquitectura de Datos y Políticas Globales

### 🔄 3.1 Sincronización de Catálogos Maestros

Para optimizar la integridad de los datos, las entidades compartidas (Empleados, Clientes y Proveedores) operan bajo el siguiente esquema:

1. 📥 **Carga Inicial:** Se realiza una carga masiva única en la base de datos de KoonolApp.
2. ⚡ **Sincronización Automatizada vía Webhooks:** El backend se conecta directamente a SAP Business One mediante la capa de servicios (_Service Layer_). El intercambio de datos se gestiona en tiempo real a través de webhooks disparados ante eventos de creación o modificación, manteniendo actualizados los registros maestros de forma reactiva.

### 📜 3.2 Política de Auditoría de Datos

Con el fin de garantizar el rastreo de operaciones y la seguridad de la información, el esquema de auditoría interna se aplicará de forma diferenciada según la naturaleza y el ciclo de vida de los datos:

- 🔄 **Tablas Modificables:** Para aquellos registros sujetos a actualizaciones constantes o cambios de estado a lo largo del tiempo, es obligatoria la inclusión del ciclo de vida completo:
  - `created_at` (Fecha y hora exacta de la creación del registro).
  - `updated_at` (Fecha y hora exacta de la última modificación realizada).
  - `id_user` (Identificador del usuario responsable de la inserción o modificación).

- 🔒 **Tablas Inmutables (Bitácoras, Logs y Registros Históricos):** Para aquellas entidades que funcionan estrictamente como un histórico de control operativo y que, por regla de negocio, **no admiten modificaciones ni eliminaciones** una vez guardadas (por ejemplo: bitácoras de acceso de vehículos/personal o registros de inspecciones de calidad), se omitirá el campo de actualización. Estas tablas requerirán únicamente el registro de origen:
  - `created_at` (Fecha y hora exacta del asentamiento del registro).
  - `id_user` (Identificador del usuario u operario que generó el registro).

### 🏷️ 3.3 Estándares de Nomenclatura y Convenciones de Base de Datos

Con el objetivo de garantizar la consistencia del esquema relacional, optimizar la escritura de consultas SQL y estandarizar el mapeo de objetos en el Backend, se establecen las siguientes directrices obligatorias de diseño técnico:

- 🔤 **Idioma y Formato (Casing):** Todos los nombres de tablas, columnas, restricciones (constraints) y variables dentro de la base de datos deben ser escritos estrictamente en **idioma inglés** y bajo el formato **snake_case** (letras minúsculas separadas por guiones bajos; por ejemplo: `gate_logs`, `vehicle_inspections`, `first_name`).
- 🔑 **Llaves Primarias (Primary Keys):** Toda tabla dentro del ecosistema debe poseer una llave primaria única **(uuid)** nombrada categóricamente como `id`. Queda estrictamente desestimado el uso de nombres compuestos para identificar la clave primaria de la propia tabla (por ejemplo, evitar estructurar la tabla de usuarios con una PK llamada `id_user` o `user_id`).
- 🔗 **Llaves Foráneas (Foreign Keys):** Toda columna que actúe como una llave foránea para relacionar entidades deberá iniciar obligatoriamente con el prefijo `id_`, seguido del nombre de la entidad referenciada en singular y en idioma inglés (por ejemplo: `id_user`, `id_department`, `id_vehicle`).

### 🗑️ 3.4 Política de Eliminación de Datos (Soft Deletes)

Con el propósito de salvaguardar la integridad histórica de la información y garantizar el cumplimiento de las auditorías internas, se restringe la eliminación física (_hard delete_) de registros mutables en el sistema mediante sentencias SQL `DELETE`.

- 🔄 **Borrado Lógico (_Soft Delete_):** Toda entidad susceptible a ser dada de baja operativa por el usuario (por ejemplo: desactivación de un empleado, anulación de un folio o baja de un vehículo) deberá implementar una columna obligatoria llamada `deleted_at` (de tipo _timestamp_ y con valor por defecto `NULL`).
- 📊 **Persistencia de Datos:** Si el campo `deleted_at` se encuentra en estado `NULL`, el registro se considerará activo y visible para la operación regular. Al ejecutarse una baja, el backend asentará la fecha y hora exacta de la acción en dicha columna. El registro se ocultará de las interfaces de usuario (Frontend/Mobile), pero permanecerá intacto en la base de datos para fines analíticos e históricos.

### 🚦 3.5 Gestión y Flexibilidad de Estados Operativos

Debido a la naturaleza diversa de los formatos físicos y flujos operativos digitalizados en KoonolApp, los estados lógicos variarán significativamente según el proceso de cada departamento. Para garantizar la flexibilidad operativa sin perder la uniformidad técnica, se establecen las siguientes directrices:

- 📝 **Nomenclatura Uniforme:** Cualquier columna destinada al control de fases, autorizaciones o ciclos de vida dentro de una tabla deberá nombrarse categóricamente como `status` o, en caso de requerir una entidad relacional, mediante `id_status`.
- 🔤 **Formato de Valores:** Los estados lógicos almacenados como cadenas de texto (_strings_) deben definirse estrictamente en **idioma español**, en **letras mayúsculas** y bajo el formato **SCREAMING_SNAKE_CASE** (por ejemplo: `PENDIENTE`, `EN_PROCESO`, `ACEPTADO_CLIENTE`, `ARCHIVADO`).
- 📂 **Documentación Descentralizada:** Los diagramas de transición de estados y el significado de cada estatus no se centralizan de forma global; estos deberán quedar explícitamente detallados e ilustrados mediante diagramas Mermaid dentro del archivo técnico de su respectivo módulo operativo en la carpeta `/modules`.

---

## 📂 4. Índice de Módulos Funcionales

La documentación detallada de cada vertical operativa se encuentra segmentada en los siguientes documentos técnicos, los cuales unifican reglas de negocio, historias de usuario y diagramas de flujo en formato `Mermaid.js`:

- 📦 [Módulo: Core/Acceso](./modules/core.md) - _Accesos, Roles y Departamentos._
- 🛒 [Módulo: Social](./modules/social.md) - _Comunicación comunal intra e inter departamental.._
- 🧪 [Módulo: Productividad](./modules/productividad.md) - _Gestión de tareas intra e inter departamental._

---

## 🛠️ 5. Guía para la Creación de Nuevos Módulos

KoonolApp está diseñada bajo un enfoque modular y descentralizado. Si la operación requiere digitalizar un nuevo proceso que dé origen a un nuevo módulo, se deberá seguir estrictamente el estándar de nomenclatura y la estructura formal ya establecida en el repositorio.

### 🔄 5.1 Procedimiento de Documentación

1.  **Creación del Archivo Técnico:** Generar un nuevo archivo Markdown dentro de la ruta `docs/modules/nombre-modulo.md`.
2.  **Estructura Interna del Módulo:** El nuevo archivo deberá contener, de manera obligatoria, las secciones: _Reglas de Negocio_, _Historias de Usuario (con criterios de aceptación)_ y _Diagramas de Flujo (en Mermaid.js)_.
3.  **Indexación Global:** Registrar el nuevo módulo en la **Sección 4** de este documento (`docs/README.md`) mediante un enlace relativo para mantener el índice actualizado.

### 🏷️ 5.2 Estándares de Nomenclatura (IDs únicos)

Para asegurar la trazabilidad del código y el orden de las pruebas, cada regla de negocio, historia de usuario y criterio de aceptación debe usar la estructura de identificadores lógicos del sistema:

#### A) Reglas de Negocio (Business Rules)

- **Formato:** `BR-[SIGLAS_MÓDULO]-[NÚMERO_DOS_DÍGITOS]`
- **Uso:** Restricciones técnicas e interceptaciones lógicas obligatorias en el backend.
- **Ejemplo:** `BR-SC-01` (Donde `SC` corresponde al módulo Social/Muro).

#### B) Historias de Usuario (User Stories)

- **Formato:** `US-[NÚMERO_DOS_DÍGITOS]`
- **Uso:** Funcionalidades entregables descritas desde la perspectiva del colaborador.
- **Ejemplo:** `US-02`.

#### C) Criterios de Aceptación (C.A.)

- **Formato:** `C.A. [NÚMERO_US].[NÚMERO_CRITERIO]`
- **Uso:** Validaciones específicas de interfaz, base de datos o lógica JSONB que determinan el éxito de la historia.
- **Ejemplo:** `C.A. 2.1` (Primer criterio vinculado a la `US-02`).

---

### 📝 5.3 Estructura Estándar del Archivo Técnico

Cada nuevo archivo Markdown generado en la ruta `docs/modules/nombre-modulo.md` debe replicar exactamente la siguiente plantilla estructural:

````markdown
# 👥 Módulo: [Nombre del Módulo]

[Breve descripción institucional del propósito del módulo y los procesos que digitaliza].

## 💼 Reglas de Negocio (Business Rules)

### BR-[SIGLAS]-[NÚMERO]: [Título de la Regla]

- **Descripción:** [Explicación detallada de la lógica o validación técnica].
- **Comportamiento Global:** [Impacto o comportamiento de la regla a nivel base de datos/backend].

---

## 👥 Historias de Usuario (User Stories)

### US-[NÚMERO]: [Título de la Historia]

- **Como:** [Rol del colaborador en KoonolApp],
- **Quiero:** [Acción o requerimiento funcional específico],
- **Para:** [Beneficio operativo o valor de negocio obtenido].
- **Criterios de Aceptación:**
  - **C.A. [US].[REF]:** [Validación o restricción de UI/UX o Backend].
  - **C.A. [US].[REF]:** [Comportamiento esperado ligado a las tablas o campos].

---

## 🔄 Diagramas de Flujo

### [NÚMERO]. [Título del Diagrama Operativo o de Transición]

\```mermaid
graph TD
// Código estructurado de Mermaid.js
\```
````

---

Para consultar la definición de términos específicos del negocio o tecnicismos del sistema, refiérase al 📖 [Glosario de Términos](./glosario.md).

---
