---
name: "👥 User Story / Business Rule"
about: Plantilla para Historias de Usuario (US) y Reglas de Negocio (BR)
title: "US-[SIGLAS]-XX: "
labels: "enhancement"
assignees: ""
---

## 🎯 Descripción de la Historia

- **Como:** [Rol de usuario]
- **Quiero:** [Acción o requerimiento específico]
- **Para:** [Beneficio de negocio]

---

## 💼 Reglas de Negocio Vinculadas (BR)

- [ ] Ref: `BR-[SIGLAS]-XX` — _Escribe aquí la regla definida en /docs/modules/_

---

## 🏗️ Capa Monorepo Impactada

- [ ] `/backend`
- [ ] `/frontend`
- [ ] `/mobile`
- [ ] `/database`
- [ ] `/docs`
- [ ] `global`

---

## ✅ Criterios de Aceptación (C.A.)

- [ ] **C.A 1.1:** [Condición técnica o funcional a cumplir]
- [ ] **C.A 1.2:** [Segundo criterio de aceptación]

---

## 📋 Definition of Ready (DoR - Entrada)

- [ ] Identificador oficial en título (`US-[SIGLAS]-XX` o `BR-[SIGLAS]-XX`)
- [ ] Redacción en formato US y Criterios de Aceptación definidos
- [ ] Capa del monorepo identificada
- [ ] Tarea estimada y asignada al Sprint

---

## 🧪 Definition of Done (DoD - Salida)

- [ ] Rama según convención (`feature/[módulo]-[nombre]`)
- [ ] Commits en formato Conventional Commits (`feat(módulo/capa): descripción [US-XXX-YY]`)
- [ ] Estructura de BD en `snake_case`, PK `id` (UUID), auditoría (`created_at`, `updated_at`, `id_user`) y soft delete (`deleted_at`)
- [ ] Pull Request fusionado en `develop` y Criterios de Aceptación validados en Staging/QA
- [ ] Documentación funcional actualizada en `/docs/modules/` o esquema DBML
