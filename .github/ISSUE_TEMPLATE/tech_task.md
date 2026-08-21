---
name: "🛠️ Tech Task / Chore / Docs"
about: Mantenimiento, infraestructura, refactorización o documentación
title: "CHORE-[MÓDULO/CAPA]: "
labels: "maintenance"
assignees: ""
---

## 🎯 Objetivo Técnico

[Descripción clara de lo que se construirá, refactorizará o documentará]

---

## 🛠️ Tareas / Lista de Trabajo

- [ ] [Paso o tarea técnica 1]
- [ ] [Paso o tarea técnica 2]

---

## 🏗️ Capa Monorepo Afectada

- [ ] `/backend`
- [ ] `/frontend`
- [ ] `/mobile`
- [ ] `/database`
- [ ] `/docs`
- [ ] `global`

---

## 📋 Definition of Ready (DoR - Entrada)

- [ ] Identificador alineado al estándar (`CHORE-[MÓDULO]/[CAPA]:` o `DOCS-[MÓDULO]:`)
- [ ] Objetivo técnico y alcance delimitados
- [ ] Tarea estimada y asignada

---

## 🧪 Definition of Done (DoD - Salida)

- [ ] Rama temporal según convención (`chore/`, `docs/`)
- [ ] Commits en formato Conventional Commits (`chore(módulo/capa): ...` o `docs(módulo/capa): ...`)
- [ ] Sin advertencias de linter ni errores de compilación en TypeScript
- [ ] Sin sentencias SQL `DELETE` directas (uso estricto de soft delete)
- [ ] PR fusionado en `develop` y cambios verificados en Staging/QA
