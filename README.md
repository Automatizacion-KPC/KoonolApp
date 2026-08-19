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

Esto faculta a los desarrolladores a unificar los cambios de todas las capas del ecosistema (**Backend, Frontend y Mobile**) relacionados con una misma tarea en una sola rama de trabajo.

## 5.1 Ramas Permanentes e Institucionales

### 🚀 `main` (Producción)

Contiene exclusivamente código **100% estable y verificado**.

Es la rama utilizada para los despliegues oficiales en el entorno de producción en vivo.

> **Queda estrictamente prohibido realizar commits directos sobre esta rama.**

### 🧪 `develop` (Integración y Desarrollo)

Es la rama central de trabajo donde se consolidan todas las nuevas funciones.

Actúa como el entorno espejo para:

- Pruebas funcionales.
- Control de calidad (Staging/QA).
- Inspección de integraciones antes de una liberación oficial.

---

## 5.2 Ramas Temporales (De Trabajo)

Cualquier desarrollo, corrección o mejora deberá ejecutarse en una rama temporal creada exclusivamente a partir de `develop`.

La nomenclatura de estas ramas debe definirse en minúsculas, utilizando guiones y respetando las siguientes convenciones.

### 📦 `feature/` (Nuevas Características)

Utilizada para el desarrollo de historias de usuario o digitalización de nuevos procesos.

Debe incluir:

- Las siglas del módulo de destino.
- El identificador de la tarea.

**Ejemplos**

```text
feature/core-us-01-login
feature/productividad-us-12-inspeccion-vehiculo
```

### 🐛 `bugfix/` (Corrección de Errores)

Destinada a resolver fallos operativos o comportamientos inesperados detectados durante la etapa de pruebas en el entorno de desarrollo.

**Ejemplo**

```text
bugfix/social-muro-render
```

### 🔥 `hotfix/` (Correcciones Críticas en Producción)

Ramas de extrema urgencia creadas directamente desde `main` para solucionar un error crítico que afecte la operación en vivo.

Una vez verificado el cambio, se fusiona de inmediato tanto en `main` como en `develop`.

---

## 5.3 Ciclo de Vida de un Cambio y Pull Requests (PR)

### 1. Creación

El desarrollador crea la rama correspondiente desde la versión más actualizada de `develop`.

### 2. Desarrollo Integral

Se realizan las modificaciones en los directorios implicados (`/backend`, `/frontend` y/o `/mobile`) bajo la misma rama aislada.

### 3. Sincronización Local

Antes de solicitar la integración, el desarrollador debe sincronizar su rama local con el estado actual del servidor remoto.

```bash
git pull origin develop
```

Esto permite resolver de forma anticipada cualquier conflicto en su entorno de trabajo.

### 4. Pull Request (PR)

Se abre un PR con destino a la rama `develop`.

El código deberá ser revisado, auditado y aprobado por los roles con facultades globales de administración (**ADMIN** o **GOD**) antes de su fusión definitiva.

## 5.4 Convención y Política de Mensajes de Commit

Para mantener la trazabilidad entre el código y la documentación funcional del sistema (`docs/README.md`), todos los mensajes de commit deben seguir la especificación de **Conventional Commits** enriquecida con la nomenclatura oficial de **Módulos**, **Historias de Usuario (US)** y **Reglas de Negocio (BR)** del proyecto.

### 📜 Estructura del Mensaje

```text
<tipo>(<módulo>/<capa>): <descripción imperativa> [<REFERENCIA_DOCUMENTACIÓN>]
```

#### 1. Tipos de Commit (`<tipo>`)

- **`feat`**: Nueva funcionalidad para el usuario final (ligada a una US).
- **`fix`**: Corrección de un error o bug en el código.
- **`docs`**: Cambios exclusivamente en la documentación (`/docs` o `README.md`).
- **`refactor`**: Reestructuración de código sin alterar su comportamiento externo o reglas de negocio.
- **`style`**: Formateo de código, espacios, puntos y comas (sin cambios en lógica).
- **`test`**: Añadir o modificar pruebas unitarias/integración.
- **`chore`**: Tareas auxiliares (configuraciones, dependencias, scripts de build, `.gitignore`).

#### 2. Alcance (`<módulo>/<capa>`)

El alcance debe combinar la sigla oficial del módulo en minúsculas y la capa o directorio afectado del monorepo:

##### Módulos Válidos (Siglas Oficiales):

- **`usr`**: Core / Usuarios
- **`soc`**: Social / Muro
- **`prd`**: Productividad / Tareas
- **`srm`**: Recepción de Muestras
- **`rdr`**: Solicitudes I+D
- **`hrm`**: Recursos Humanos
- **`veh`**: Vehículos
- **`gtc`**: Caseta / Accesos
- **`wem`**: Equipo de Almacén
- **`vhi`**: Calidad - Inspección Vehicular
- **`gbp`**: Calidad - Vidrio y Plástico
- **`cpr`**: Calidad - Devoluciones
- **`qlr`**: Calidad/Logística - Recolección
- **`qwr`**: Calidad - Recepción Almacén
- **`global`**: Afecta a todo el ecosistema o utilidades comunes

##### Capas Válidas:

- `backend`
- `frontend`
- `mobile`
- `database`
- `docs`

##### Sintaxis del Alcance:

`módulo/capa`  
_(Ejemplos: `vhi/backend`, `soc/frontend`, `qwr/database`, `global/docs`)_

#### 3. Referencia a Documentación Funcional (`[US-XXX-YY]` / `[BR-XXX-YY]`)

Al final del mensaje de commit (o en corchetes), es obligatorio hacer referencia al identificador técnico documentado en `docs/modules/`:

- **Nuevas Funcionalidades:** Incluir el ID de la historia `[US-SIGLAS-XX]`.
- **Ajustes por Reglas de Negocio:** Incluir el ID de la regla `[BR-SIGLAS-XX]`.
- **Mantenimiento/Chore:** Si el commit no corresponde a una US/BR directa, se omite el corchete final o se usa `[chore]`.

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
git commit -m "docs(rdr/docs): actualizar diagrama Mermaid de solicitudes I+D"
git commit -m "chore(global/backend): actualizar versión de TypeORM y drivers de postgres"
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
