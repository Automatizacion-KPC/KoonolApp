# 🚀 KoonolApp | Repositorio Central

Bienvenido al repositorio raíz de **KoonolApp**, una Intranet/ERP desarrollada a la medida. Este sistema funciona como un ecosistema satélite diseñado para digitalizar, automatizar y optimizar los flujos operativos internos y los formatos físicos de la organización que no son absorbidos por el sistema central SAP Business One.

---

## 🏗️ 1. Arquitectura del Repositorio

El proyecto está estructurado bajo un enfoque de **Monorepo Desacoplado**, lo que permite centralizar todos los componentes del sistema en un único repositorio físico, manteniendo la independencia de construcción, configuración y despliegue de cada capa:

```text
KoonolApp/
├── 📁 backend/       # API Restful en Node.js y TypeScript
├── 📁 frontend/      # Aplicación Web en React y Next.js
├── 📁 mobile/        # Aplicación Móvil en React Native
├── 📁 docs/          # Documentacion General del Sistema: Reglas de negocio, diagramas de flujo e historias de usuario
│   ├── 📁 database/  # Diseño relacional (Esquema DBML)
│   └── 📁 modules/   # Especificaciones funcionales (core.md, social.md, productividad.md, etc.)
├── 📄 .gitignore     # Exclusiones globales del sistema de control de versiones
└── 📄 README.md      # Este documento (Mapa de navegación técnica y desarrollo)
```

---

# 🛠️ 2. Stack Tecnológico Principal

El ecosistema de **KoonolApp** utiliza herramientas modernas y estandarizadas en el sector de la ingeniería de software para asegurar un entorno de desarrollo homogéneo, seguro y de alto rendimiento.

| Componente    | Tecnología Base         | Herramientas y Librerías Clave | Propósito Técnico                                                                        |
| ------------- | ----------------------- | ------------------------------ | ---------------------------------------------------------------------------------------- |
| Backend       | 🟢 Node.js / TypeScript | Express (NestJS) / Swagger     | API REST, enrutamiento, lógica del servidor y documentación automatizada de endpoints.   |
| Frontend Web  | ⚛️ React.js             | Next.js                        | Panel administrativo, dashboard ejecutivo de reportes y herramientas comunes.            |
| Mobile App    | 📱 React Native         | Componentes Nativos            | Aplicación multiplataforma para flujos operativos y bitácoras en terreno.                |
| Base de Datos | 🐘 PostgreSQL           | Esquema SQL / UUID             | Persistencia relacional, llaves primarias basadas en UUID y registros de auditoría.      |
| Seguridad     | 🔒 JWT & Bcrypt         | Algoritmos de encriptación     | Autenticación robusta basada en tokens y hash seguro para la protección de credenciales. |
| Integración   | ⚡ SAP Service Layer    | Webhooks en tiempo real        | Sincronización reactiva de catálogos maestros externos.                                  |

---

# 📦 3. Requisitos Previos y Entorno Local

Dado que el proyecto se encuentra en su fase inicial de construcción, la gestión de servicios e infraestructura se ejecutará de forma nativa.

Antes de inicializar los entornos de desarrollo, asegúrese de contar con las siguientes herramientas instaladas y activas en su estación de trabajo:

- **Node.js:** Versión LTS más reciente recomendada como entorno de ejecución global.
- **Gestor de Paquetes:** `npm` (incluido nativamente con la instalación de Node.js).
- **Servidor PostgreSQL:** Instancia local activa o base de datos de desarrollo accesible con los privilegios requeridos.

> 🐋 **Nota de Arquitectura**
>
> La contenedorización y orquestación automatizada de los servicios mediante Docker y Docker Compose se encuentra planificada para etapas posteriores del ciclo de vida del proyecto. Por el momento, la instalación, enlace y ejecución se realizarán directamente a través de comandos nativos de `npm`.

---

# 🚀 4. Guía de Inicio Rápido (Desarrollo Local)

Para inicializar el entorno de desarrollo integrado por primera vez, ejecute la siguiente secuencia de comandos en su terminal.

## Paso 1: Clonar el Repositorio

```bash
git clone https://github.com/Automatizacion-KPC/KoonolApp.git
cd KoonolApp
```

## Paso 2: Configuración de Variables de Entorno

Cada subcarpeta operativa (`/backend`, `/frontend`, `/mobile`) cuenta con su propio archivo `.env.example`.

Deberá crear una copia de estos archivos dentro de cada directorio, renombrarlos a `.env` y configurar:

- Credenciales locales de acceso a PostgreSQL.
- Puertos de escucha.
- Claves secretas para la firma de tokens JWT.

## Paso 3: Instalación y Arranque por Componente

Al tratarse de un monorepo desacoplado, cada capa genera de forma aislada sus dependencias.

Diríjase a los archivos `README.md` específicos de cada directorio para conocer los comandos exactos de inicio y migración.

- ⚙️ **Servidor y APIs:** Instrucciones de Backend.
- 💻 **Interfaz Web:** Instrucciones de Frontend.
- 📱 **Aplicación Celular:** Instrucciones de Mobile.

---

# 🌿 5. Política de Ramas y Flujo de Trabajo (Git Workflow)

Para garantizar la estabilidad del código y optimizar la integración continua dentro del monorepo, el proyecto implementa una estrategia de desarrollo basada en características (**Feature-Driven Development**).

Esto faculta a los desarrolladores a abordar las distintas capas del ecosistema (**Backend, Frontend y Mobile**) involucradas en una tarea, ya sea mediante una sola rama integral o a través de ramas coordinadas por capa.

Cada bloque de trabajo debe ejecutarse en una rama temporal aislada antes de integrarse a la rama principal de desarrollo.

## 5.1 Ramas Permanentes e Institucionales

### 🚀 `main` (Producción)

Contiene exclusivamente código **100% estable y verificado**.

Es la rama utilizada para los despliegues oficiales en el entorno de producción en vivo.

> **Queda estrictamente prohibido realizar commits directos sobre esta rama.**

### 🧪 `develop` (Integración y Desarrollo)

Es la rama central de trabajo donde se consolidan todas las nuevas funciones, correcciones y actualizaciones de documentación.

Actúa como el entorno espejo para:

- Pruebas funcionales.
- Control de calidad (Staging/QA).
- Inspección de integraciones antes de una liberación oficial.

## 5.2 Conceptos Base: Módulos y Capas

Para mantener una nomenclatura uniforme en ramas y mensajes de commit, el proyecto se estructura en **Módulos** (áreas funcionales) y **Capas** (directorios del monorepo):

### 🧩 Catálogo de Módulos (Siglas Oficiales)

| Sigla        | Módulo / Área                                       | Sigla     | Módulo / Área                  |
| ------------ | --------------------------------------------------- | --------- | ------------------------------ |
| **`usr`**    | Core / Usuarios                                     | **`veh`** | Vehículos                      |
| **`soc`**    | Social / Muro                                       | **`gtc`** | Caseta / Accesos               |
| **`prd`**    | Productividad / Tareas                              | **`wem`** | Equipo de Almacén              |
| **`srm`**    | Recepción de Muestras                               | **`vhi`** | Calidad - Inspección Vehicular |
| **`rdr`**    | Solicitudes I+D                                     | **`gbp`** | Calidad - Vidrio y Plástico    |
| **`hrm`**    | Recursos Humanos                                    | **`cpr`** | Calidad - Devoluciones         |
| **`qlr`**    | Calidad/Logística - Recolección                     | **`qnc`** | Calidad - No Conformidades     |
| **`qwr`**    | Calidad - Recepción Almacén                         | **`ntf`** | Notificaciones                 |
| **`global`** | Utilidades o configuraciones generales del proyecto |           |                                |

### 🏗️ Capas del Monorepo (`<capa>`)

Representan las subdivisiones técnicas o directorios dentro del proyecto:

- **`backend`**: Código del servidor y APIs REST (`/backend`).
- **`frontend`**: Aplicación web (`/frontend`).
- **`mobile`**: Aplicación móvil (`/mobile`).
- **`database`**: Esquemas, entidades (TypeORM), DBML y migraciones (`/database`).
- **`docs`**: Archivos Markdown, diagramas y especificaciones funcionales (`/docs`).

## 5.3 Nomenclatura de Ramas Temporales

Cualquier desarrollo, corrección o mejora deberá ejecutarse en una rama temporal creada exclusivamente a partir de `develop` (a excepción de los `hotfix/`).

La nomenclatura de estas ramas debe definirse en minúsculas, utilizando guiones y respetando las siguientes convenciones:

### 📖 `docs/` (Documentación)

Para cambios cuyo único entregable sean archivos de lectura (`.md`), diagramas o especificaciones en `/docs/`.

- **Sintaxis:** `docs/<módulo>-<nombre-corto>`
- **Ejemplos:**
  ```text
  docs/vhi-especificacion-funcional
  docs/qnc-diagramas-mermaid
  ```

### 📦 `feature/` (Nuevas Características)

Utilizada para el desarrollo de historias de usuario o digitalización de nuevos procesos.

Para el desarrollo de un incremento funcional, una misma rama `feature/` permite modificar simultáneamente las capas necesarias (`backend`, `frontend` y/o `mobile`) para completar dicha funcionalidad.

- **Sintaxis:** `feature/<módulo>-<nombre-corto>`
- **Ejemplos:**
  ```text
  feature/vhi-precarga
  feature/hrm-limite-vacaciones
  ```

### 🐛 `bugfix/` (Corrección de Errores)

Destinada a resolver fallos operativos o comportamientos inesperados detectados durante la etapa de pruebas en el entorno de desarrollo.

- **Sintaxis:** `bugfix/<módulo>-<nombre-corto>`
- **Ejemplo:**
  ```text
  bugfix/vhi-render-checklist
  ```

### 🛠️ `chore/` (Mantenimiento e Infraestructura)

Para tareas técnicas sin impacto directo en la experiencia de usuario (configuración de dependencias, scripts de despliegue, ajustes de base de datos o utilidades).

- **Sintaxis:** `chore/<módulo>-<capa>-<nombre-corto>`
- **Ejemplos:**
  ```text
  chore/global-backend-init
  chore/gtc-database-indexes
  ```

### 🔥 `hotfix/` (Correcciones Críticas en Producción)

Ramas de extrema urgencia creadas directamente desde `main` para solucionar un error crítico que afecte la operación en vivo.

Una vez verificado el cambio, se fusiona de inmediato tanto en `main` como en `develop`.

- **Sintaxis:** `hotfix/<módulo>-<nombre-corto>`
- **Ejemplo:**
  ```text
  hotfix/usr-bloqueo-sesion
  ```

## 5.4 Estrategias de Colaboración en Paralelo (Trabajo Multicapa)

Cuando múltiples desarrolladores o equipos (Backend, Frontend, Mobile) trabajen de forma simultánea sobre una misma funcionalidad, la integración en el monorepo se gestionará bajo un enfoque **API-First** (Desarrollo Basado en Contrato) mediante dos esquemas permitidos:

### 1. Esquema Predeterminado: Ramas Independientes por Capa

Cada desarrollador abre una rama corta sufijada por la capa en la que colabora. Dado que los archivos modificados residen en carpetas separadas (`/backend`, `/frontend`, `/mobile`), no existen conflictos de Git entre desarrolladores.

- **Desarrollador Backend:** Crea `feature/<módulo>-<nombre>-backend` $\rightarrow$ Desarrolla endpoints/DB $\rightarrow$ PR a `develop`.
- **Desarrollador Frontend/Mobile**: Crea `feature/<módulo>-<nombre>-frontend` $\rightarrow$ Desarrolla interfaz con mocks del contrato de la API $\rightarrow$ PR a `develop`.

```text
develop ───────────────────────────────────────────────────────────► (Integración)
   │                                                         ▲
   ├──► feature/vhi-precarga-backend (Dev Backend) ──────────┤ (PR Backend)
   │                                                         │
   └──► feature/vhi-precarga-frontend (Dev Frontend) ────────┘ (PR Frontend)
```

### 2. Esquema para Funcionalidades Complejas: Rama de Integración Conjunta

Si la función requiere un acoplamiento estricto y pruebas combinadas antes de llegar a `develop`:

1. Se crea una rama base del incremento desde `develop`:
   `feature/<módulo>-<nombre>`.
2. Los desarrolladores abren sus ramas individuales tomando como base esa rama:

- `feature/<módulo>-<nombre>-backend`
- `feature/<módulo>-<nombre>-frontend`

3. Cada desarrollador hace Pull Request hacia la rama `feature/<módulo>-<nombre>`.
4. Una vez integradas y verificadas ambas partes, se abre un único Pull Request desde `feature/<módulo>-<nombre>` hacia `develop`.

## 5.5 Ciclo de Vida de un Cambio y Pull Requests (PR)

### 1. Creación de Rama

El desarrollador actualiza su rama `develop` local y crea la rama temporal correspondiente:

```bash
git checkout develop
git pull origin develop
git checkout -b feature/vhi-precarga
```

### 2. Desarrollo Integral

Se realizan las modificaciones en las capas del monorepo involucradas (`/backend`, `/frontend`, `/mobile`, etc.) bajo la misma rama aislada y realizando commits bajo la convención oficial.

### 3. Sincronización Local

Antes de solicitar la integración, el desarrollador debe sincronizar su rama local con el estado actual del servidor remoto en la rama `develop`.

```bash
git pull origin develop
```

Esto permite resolver de forma anticipada cualquier conflicto en su entorno de trabajo.

### 4. Pull Request (PR)

Se abre un PR con destino a la rama `develop`.

### 5. Revisión y Merge

El código deberá ser revisado, auditado y aprobado por un administrador. Al fusionar (_merge_) el PR hacia `develop`, la rama temporal se elimina para mantener limpio el repositorio.

## 5.6 Convención y Política de Mensajes de Commit (Conventional Commits)

Para mantener la trazabilidad entre el código y la documentación funcional del sistema (`docs/README.md`), todos los mensajes de commit deben seguir la especificación de **Conventional Commits**.

### 📜 Estructura del Mensaje

```text
<tipo>(<módulo>/<capa>): <descripción imperativa> [<REFERENCIA_DOCUMENTACIÓN>]
```

#### 1. Tipos Válidos de Commit (`<tipo>`)

- **`feat`**: Nueva funcionalidad para el usuario final.
- **`fix`**: Corrección de un error o bug en el código.
- **`docs`**: Cambios exclusivamente en la documentación (`/docs` o `README.md`).
- **`refactor`**: Reestructuración de código sin alterar su comportamiento externo o reglas de negocio.
- **`style`**: Formateo de código, espacios, puntos y comas (sin cambios en lógica).
- **`test`**: Añadir o modificar pruebas unitarias/integración.
- **`chore`**: Tareas auxiliares de configuración, dependencias o infraestructura (scripts de build, `.gitignore`).

#### 2. Alcance (`<módulo>/<capa>`)

El alcance debe combinar la sigla oficial del módulo en minúsculas y la capa o directorio afectado del monorepo:

- _(**Ejemplos:** `vhi/backend`, `soc/frontend`, `qwr/database`, `global/docs`)_

#### 3. Referencia a Documentación Funcional (`[US-XXX-YY]` / `[BR-XXX-YY]`)

Al final del mensaje de commit (entre corchetes), es obligatorio hacer referencia al identificador técnico documentado en `docs/modules/`:

- **Nuevas Funcionalidades:** Incluir el ID de la historia `[US-SIGLAS-XX]`.
- **Ajustes por Reglas de Negocio:** Incluir el ID de la regla `[BR-SIGLAS-XX]`.
- **Mantenimiento/Chore o Tareas Generales:** Si el commit no corresponde a una US/BR directa, en el corchete final se coloca `[chore]`.

#### 📝 Ejemplos Correctos por Escenario:

##### 🚀 Desarrollo de Historias de Usuario (`feat`)

```bash
git commit -m "feat(vhi/backend): crear endpoint para registro de precarga [US-VHI-01]"
git commit -m "feat(soc/frontend): implementar componente de reacciones en muro [US-SOC-02]"
git commit -m "feat(qwr/mobile): agregar captura de fotografías en recepción [US-QWR-03]"
```

##### 🛠️ Aplicación de Reglas de Negocio o Correcciones (fix / refactor)

```bash
git commit -m "fix(hrm/backend): validar límite de días de vacaciones según antigüedad [BR-HRM-02]"
git commit -m "refactor(gtc/database): aplicar soft delete en bitácora de accesos [BR-GTC-01]"
```

##### 📖 Documentación y Mantenimiento (docs / chore)

```bash
git commit -m "docs(rdr/docs): actualizar diagrama Mermaid de solicitudes I+D [chore]"
git commit -m "chore(global/backend): actualizar versión de TypeORM y drivers de postgres [chore]"
```

---

# 📖 6. Documentación Funcional y de Negocio

Si requiere comprender la lógica de:

- Roles de acceso.
- Encapsulamiento por departamentos.
- Políticas globales de **Soft Deletes** (borrado lógico).
- Directrices de base de datos en inglés.
- Especificaciones funcionales de los módulos **Core**, **Social**, **Productividad**, etc.

Consulte el índice maestro de diseño en:

> 👉 📘 [**La Documentación General del Sistema**](./docs/README.md)
