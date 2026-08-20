# 🎯 KoonolApp | Tablero de Desarrollo y Gestión SCRUM

Bienvenido al espacio de trabajo central de **KoonolApp** en GitHub Projects. Este tablero orquesta el ciclo de vida de desarrollo ágil para el ecosistema Intranet/ERP satélite de la organización, diseñado para complementar a **SAP Business One** digitalizando y automatizando flujos operativos internos y formatos físicos.

---

## 🧭 1. Visión del Proyecto y Alcance Operativo

- **Propósito del Sistema:** Digitalizar, automatizar y auditar procesos operativos y formatos físicos (Calidad, Almacén, Vehículos, RH, etc.) no cubiertos por el ERP central.
- **Arquitectura Monorepo Desacoplado:** Centralizado en 4 capas operativas (`/backend`, `/frontend`, `/mobile`, `/docs`).
- **Regla de Oro de Alcance:** Este desarrollo **NO** gestiona transacciones comerciales o financieras directas (compras, ventas, divisa, impuestos), las cuales son facultad y responsabilidad exclusiva de SAP Business One.
- **Integración:** Sincronización reactiva de catálogos maestros mediante SAP Service Layer y Webhooks en tiempo real.

---

## 🔄 2. Marco de Trabajo SCRUM y Flujo Operativo

El desarrollo de KoonolApp se rige por la metodología **SCRUM** adaptada a nuestro enfoque de desarrollo basado en características (_Feature-Driven Development_).

### ⏱️ Ciclo del Sprint y Ceremonias

El desarrollo se organiza en ciclos de tiempo fijos (Sprints). Cada Sprint sigue una secuencia de 4 etapas para transformar ideas en código probado y desplegado:

1. **📑 Backlog Refinement (Aclaración y Diseño Funcional):**
   - **Propósito:** Definir el "qué" y el "cómo" antes de tocar código.
   - **Acción:** Se redactan las Historias de Usuario (`US`), se fijan las Reglas de Negocio (`BR`) y se elaboran los diagramas o especificaciones en `/docs/modules/`.

2. **🎯 Sprint Planning (Compromiso del Sprint):**
   - **Propósito:** Seleccionar las tareas que se van a construir en el ciclo actual.
   - **Acción:** Se verifica que las tarjetas cumplan con el **Definition of Ready (DoR)** (requisitos de entrada claros), los desarrolladores estiman el esfuerzo y se mueven del _Product Backlog_ al _Sprint Backlog_.

3. **☀️ Daily SCRUM (Sincronización Diaria):**
   - **Propósito:** Mantener al equipo alineado y resolver bloqueos a tiempo.
   - **Acción:** Reunión rápida de 10-15 min para revisar avances del día anterior, plan del día actual y trabas técnicas.

4. **🧪 Sprint Review & DoD Check (Demostración y Aprobación):**
   - **Propósito:** Validar el software funcionando y dar por terminada la tarea.
   - **Acción:** Demostración funcional en el entorno de Staging/QA y auditoría estricta contra el **Definition of Done (DoD)** (código, commits, BD y documentación sin pendientes) antes de mover la tarjeta a **Done**.

---

## 📊 3. Estados del Tablero (Workflow States)

| Estado en Tablero             | ¿Por qué está aquí? (Significado / Origen)                                                                                 | ¿Qué se hace en este estado? (Acción Requerida)                                                                                         | Regla o Límite de Trabajo                                                |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **📥 Product Backlog**        | Es una idea, requisito, regla de negocio, documento o fallo recién detectado que aún no se va a programar.                 | Se analiza y refina en las reuniones. Se redactan sus detalles, Criterios de Aceptación (C.A.) y diagramas hasta dejarla clara.         | Priorizado estrictamente de arriba (mayor urgencia) a abajo.             |
| **🎯 Sprint Backlog (Ready)** | La tarjeta ya se refinó, cumple al 100% el DoR (está definida y estimada) y se seleccionó para el Sprint activo.           | Espera en fila a que un desarrollador se desocupe para tomarla e iniciar su construcción.                                               | Representa el compromiso de trabajo para el ciclo de 1 a 2 semanas.      |
| **🛠️ In Progress**            | Un desarrollador la tomó del Sprint Backlog, creó su rama temporal (`feature/`, `bugfix/` o `docs/`) y comenzó a trabajar. | Se escribe el código, la documentación o los diagramas. Se realizan pruebas locales y commits convencionales.                           | **Límite WIP:** Máximo 2 tarjetas simultáneas por desarrollador.         |
| **🔍 In Code Review / PR**    | El desarrollador terminó su trabajo local, sincronizó con `develop` y abrió un Pull Request (PR) en GitHub.                | Un revisor (Lead/Admin) audita el código, verifica convenciones de Git, solicita cambios si es necesario y aprueba la fusión (_merge_). | **Prohibido autoaprobarse el PR.** Requiere revisión de un tercero.      |
| **🧪 QA / Staging**           | El Pull Request fue aprobado y fusionado en `develop`. El código ya está desplegado en el entorno de pruebas.              | Se prueban los Criterios de Aceptación en la aplicación en vivo para confirmar que funciona correctamente y no rompió nada.             | Si la prueba falla, la tarjeta regresa a _In Progress_ o se abre un Bug. |
| **✅ Done**                   | La tarjeta fue probada exitosamente en QA y cumple al 100% con el DoD (código, commits, BD y `/docs` al día).              | Ninguna. La tarea se da por oficialmente concluida y lista para pasar a producción en la siguiente liberación.                          | Estado final e irreversible de la tarjeta en el Sprint.                  |

### 💡 Resumen del Flujo Visual de una Tarjeta

```text
[ 📥 Product Backlog ]   ->  Idea o requisito sin pulir
        │  (Refinamiento)
        ▼
[ 🎯 Sprint Backlog ]    ->  Listo para programar (DoR cumplido)
        │  (Desarrollador inicia)
        ▼
[ 🛠️ In Progress ]       ->  Programando/documentando en rama local
        │  (Abre Pull Request)
        ▼
[ 🔍 Code Review ]       ->  Revisión y aprobación de código
        │  (Merge a develop)
        ▼
[ 🧪 QA / Staging ]      ->  Pruebas funcionales en servidor de pruebas
        │  (Criterios validados)
        ▼
[ ✅ Done ]              ->  Entregado y completado (DoD cumplido)
```

---

## 🏷️ 4. Estándar de Nomenclatura para Tarjetas e Issues

Para garantizar la trazabilidad punta a punta entre el tablero de Projects, la documentación funcional (`/docs/modules/`), el modelo de datos y las ramas de Git, toda tarjeta debe clasificarse e identificarse formalmente.

### 4.1 Tipología y Jerarquía de Tarjetas

El proyecto clasifica el trabajo en 6 tipos de tarjetas organizadas bajo una jerarquía clara:

```text
📦 EPIC (Módulo o Proyecto Completo)
  ├── 👥 US (Funcionalidad Completa / End-to-End)
  ├── 💼 BR (Regla o Restricción Estricta de Negocio)
  ├── 📖 DOCS (Documentación Técnica y Diagramas)
  ├── 🐛 BUG (Corrección de Fallos)
  └── 🛠️ CHORE (Mantenimiento e Infraestructura)
```

#### 1. 📦 Epic (Épica) — Contenedor de Módulo

- **¿Qué es?** Una vertical operativa o módulo completo del sistema.
- **¿Cuándo se usa?** Para agrupar bajo un mismo "paraguas" todas las tarjetas de un módulo.
- **Regla Clave:** Un Epic **NUNCA** se asigna a un desarrollador ni se programa en un Sprint. Es solo una categoría contenedora.
- **Ejemplo:** `EPIC-VHI: Módulo Completo de Inspección Vehicular`

#### 2. 👥 User Story (US) — Funcionalidad Completa (End-to-End)

- **¿Qué es?** Una función o pantalla entregable que le aporta un valor directo al usuario final.
- **Aclaración de Capa:** **NO** es solo Frontend. Una US abarca toda la solución técnica (Pantalla + API Backend + Base de Datos) necesaria para que la función sirva.
- **¿Cuándo se usa?** Al construir un nuevo flujo, pantalla o herramienta interactiva.
- **Ejemplo:** `US-VHI-01: Permitir al inspector registrar el checklist de precarga vehicular` _(Incluye la pantalla en la app móvil, el endpoint en el servidor y la tabla en PostgreSQL)_.

#### 3. 💼 Business Rule (BR) — Regla de Negocio o Validación Estricta

- **¿Qué es?** Una condición, cálculo, candado o restricción obligatoria que la empresa exige y el sistema debe hacer cumplir sin excepciones.
- **Aclaración de Capa:** **NO** es solo Backend. Representa la lógica de negocio, sin importar si se valida en el cliente, servidor o base de datos.
- **¿Cuándo se usa?** Cuando la lógica de un proceso es tan compleja o crítica que conviene separarla en su propia tarjeta para probarla rigurosamente.
- **Ejemplo:** `BR-HRM-02: Bloquear solicitudes de vacaciones si el empleado tiene menos de 1 año de antigüedad o supera su saldo disponible`

#### 4. 📖 Docs (Documentación Técnica)

- **¿Qué es?** Tareas cuyo resultado final son exclusivamente archivos de lectura (`.md`), diagramas de flujo (Mermaid) o diseño de base de datos (DBML) en la carpeta `/docs`.
- **¿Cuándo se usa?** Para diseñar la arquitectura, escribir la especificación funcional o actualizar manuales antes o durante el desarrollo.
- **Ejemplo:** `DOCS-RDR: Elaborar el diagrama de secuencia para el flujo de solicitudes a I+D`

#### 5. 🐛 Bug (Corrección de Error)

- **¿Qué es?** La reparación de un comportamiento incorrecto o fallo en una funcionalidad que ya había sido construida.
- **¿Cuándo se usa?** Cuando algo falla durante las pruebas en QA/Staging o en el entorno de Producción.
- **Ejemplo:** `BUG-VHI: El checklist de inspección no guarda las fotografías si la conexión a internet es inestable`

#### 6. 🛠️ Chore / Tech Task (Tarea Técnica)

- **¿Qué es?** Trabajo de infraestructura, configuración o mantenimiento interno del código que no añade pantallas ni reglas de negocio visibles para el usuario.
- **¿Cuándo se usa?** Para actualizar librerías, configurar variables de entorno, refactorizar código interno o crear índices en la base de datos.
- **Ejemplo:** `CHORE-GLOBAL/BACKEND: Configurar TypeORM y optimizar el pool de conexiones de PostgreSQL`

### 4.2 Catálogo Oficial de Módulos (Siglas Oficiales)

| Sigla        | Módulo Funcional                | Dominio Operativo                                        |
| ------------ | ------------------------------- | -------------------------------------------------------- |
| **`usr`**    | Core / Usuarios                 | Accesos, Roles, Sesiones y Departamentos                 |
| **`soc`**    | Social                          | Muro institucional y comunicación interna                |
| **`prd`**    | Productividad                   | Gestión de tareas y notas personales/asignadas           |
| **`srm`**    | Recepción de Muestras           | Muestras enviadas por Proveedores                        |
| **`rdr`**    | Solicitudes a I+D               | Muestras, Desarrollos y Visitas técnicas                 |
| **`hrm`**    | Recursos Humanos                | Control de Vacaciones, Autoservicio y Préstamos          |
| **`veh`**    | Vehículos                       | Control vehicular, Mantenimientos, Seguros y Combustible |
| **`gtc`**    | Caseta                          | Bitácoras de acceso de personal y vehículos              |
| **`wem`**    | Equipo de Almacén               | Mantenimiento de Montacargas y Patines                   |
| **`vhi`**    | Calidad - Inspección Vehicular  | Precarga, Postlavado y Recepción Vehicular               |
| **`gbp`**    | Calidad - Vidrio y Plástico     | Control de materiales quebradizos                        |
| **`cpr`**    | Calidad - Devoluciones          | Registro de devoluciones y rechazos                      |
| **`qlr`**    | Calidad/Logística - Recolección | Recolección de mercancías devueltas                      |
| **`qwr`**    | Calidad - Recepción Almacén     | Inspección y recepción física en Almacén                 |
| **`qnc`**    | Calidad - No Conformidades      | Control General de No Conformidades                      |
| **`ntf`**    | Notificaciones                  | Registro General de Notificaciones Internas              |
| **`global`** | Transversal / Infraestructura   | Cambios en configuración global o librerías del monorepo |

### 4.3 Estructura de Títulos en las Tarjetas

- **Historias de Usuario:** `US-[SIGLAS]-[NÚMERO]: [Descripción corta de la funcionalidad]`  
  _Ejemplo:_ `US-SOC-02: Publicar anuncios con imágenes en el muro`
- **Reglas de Negocio:** `BR-[SIGLAS]-[NÚMERO]: [Descripción corta de la regla/validación]`  
  _Ejemplo:_ `BR-HRM-02: Validar límite de días de vacaciones según antigüedad`
- **Documentación:** `DOCS-[SIGLAS]: [Descripción del entregable de documentación]`  
  _Ejemplo:_ `DOCS-RDR: Actualizar diagrama Mermaid de flujo de solicitudes I+D`
- **Corrección de Errores (Bugs):** `BUG-[SIGLAS]: [Descripción breve del fallo]`  
  _Ejemplo:_ `BUG-VHI: Error de renderizado en checklist de postlavado`
- **Tareas Técnicas:** `CHORE-[CAPA/MÓDULO]: [Descripción del mantenimiento]`  
  _Ejemplo:_ `CHORE-GLOBAL/BACKEND: Actualizar versión de TypeORM y drivers PostgreSQL`

---

## 🚦 5. Criterios de Calidad: DoR (Entrada) y DoD (Salida)

Para mantener la calidad del proyecto y evitar que se trabaje sobre especificaciones incompletas o se entreguen tareas a medias, el tablero utiliza dos puertas de control:

### 📋 5.1 Definition of Ready (DoR) — ¿Está la tarjeta lista para programarse?

Una tarjeta **NO** puede pasar al _"Sprint Backlog"_ ni asignarse a un desarrollador si no cumple con este checklist previo:

- [ ] **Identificador Oficial:** Tiene su código de módulo y tipo asignado (Ej: `US-VHI-01`, `BR-VHI-02` o `DOCS-VHI`).
- [ ] **Redacción Clarificada:** Está escrita en formato estándar de Historia de Usuario (_Como [rol] quiero [acción] para [beneficio]_) o especificación técnica.
- [ ] **Criterios de Aceptación (C.A.):** Explica claramente qué condiciones específicas se deben cumplir para dar por buena la tarjeta.
- [ ] **Reglas de Negocio Vinculadas:** Cita la regla de negocio (BR) o diagramas de flujo de `/docs/modules/` si modifica lógica o base de datos.
- [ ] **Capas Identificadas:** Define claramente qué partes del monorepo se van a tocar (`/backend`, `/frontend`, `/mobile`, `/database` o `/docs`).
- [ ] **Estimada y Asignada:** Se discutió en la planeación del Sprint y se le asignó un nivel de esfuerzo.

### ✅ 5.2 Definition of Done (DoD) — ¿Está la tarjeta completamente terminada?

Una tarjeta **NO** puede moverse a _"Done"_ solo porque el código "ya funciona". Debe cumplir al 100% con los estándares de calidad del repositorio:

#### 1. Control de Versiones y Código (Git & TypeScript)

- [ ] Se trabajó en una rama temporal nombrada según la convención (Ej: `feature/vhi-precarga`, `docs/vhi-diagramas` o `bugfix/soc-muro-render`).
- [ ] Los mensajes de commit usan Conventional Commits y citan la documentación (`feat(vhi/backend): endpoint precarga [US-VHI-01]`).
- [ ] Se hizo `git pull origin develop` previo y no existen conflictos de fusión.
- [ ] Pull Request revisado, probado y aprobado por un administrador.
- [ ] El código compila sin errores de TypeScript ni advertencias de linter.

#### 2. Estándar de Base de Datos (PostgreSQL)

- [ ] Tablas y columnas creadas en inglés y formato `snake_case`.
- [ ] Plurales/singulares aplicados correctamente (Contables en plural: `vehicle_daily_inspections`; incontables en singular: `warehouse_equipment`).
- [ ] Clave primaria configurada como `id` (UUID) y llaves foráneas con prefijo `id_` + singular (`id_user`).
- [ ] **Campos de Auditoría:** Incluye `created_at`, `updated_at` e `id_user` en tablas mutables.
- [ ] **Soft Delete:** Implementa `deleted_at` para bajas de información. **Prohibido el uso de SQL `DELETE`**.
- [ ] Nombres de estados (`status`) guardados en español, mayúsculas y `SCREAMING_SNAKE_CASE` (Ej: `EN_PROCESO`).

#### 3. Validación Operativa y Documentación

- [ ] Se probaron y validaron todos los Criterios de Aceptación (C.A.) en el entorno de Staging/QA.
- [ ] Si la tarea agregó tablas, campos o cambió un flujo, se actualizó el archivo del módulo en `/docs/modules/` o el esquema DBML.

---

## 🌿 6. Guía Rápida de Git Flow & Convención de Commits

### 6.1 Nomenclatura de Ramas Temporales

Toda rama temporal debe crearse a partir de la versión más reciente de `develop`:

```bash
# Para una Historia de Usuario (Feature)
git checkout -b feature/<módulo>-<nombre-corto>

# Para una rama por capa (Trabajo en Paralelo Backend/Frontend)
git checkout -b feature/<módulo>-<nombre-corto>-backend
git checkout -b feature/<módulo>-<nombre-corto>-frontend

# Para Documentación
git checkout -b docs/<módulo>-<nombre-corto>

# Para Corrección de Errores (Bugfix)
git checkout -b bugfix/<módulo>-<nombre-error>
```

### 6.2 Sintaxis Oficial de Commits

Todos los commits deben cumplir con la estructura de Conventional Commits estandarizada para el monorepo:

```text
<tipo>(<módulo>/<capa>): <descripción imperativa> [<REFERENCIA_DOCUMENTACIÓN>]
```

#### Ejemplos Correctos

```bash
# Desarrollo de Funcionalidad (feat)
git commit -m "feat(vhi/backend): crear endpoint para registro de precarga [US-VHI-01]"
git commit -m "feat(soc/frontend): implementar componente de reacciones en muro [US-SOC-02]"

# Aplicación de Reglas de Negocio / Correcciones (fix / refactor)
git commit -m "fix(hrm/backend): validar límite de días de vacaciones según antigüedad [BR-HRM-02]"
git commit -m "refactor(gtc/database): aplicar soft delete en bitácora de accesos [BR-GTC-01]"

# Documentación y Mantenimiento (docs / chore)
git commit -m "docs(rdr/docs): actualizar diagrama Mermaid de solicitudes I+D"
git commit -m "chore(global/backend): actualizar versión de TypeORM y drivers de postgres"
```
