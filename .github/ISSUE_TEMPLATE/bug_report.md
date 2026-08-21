---
name: "🐛 Bug Report"
about: Reporte de fallos o errores detectados en desarrollo, QA o producción
title: "BUG-[SIGLAS]: "
labels: "bug"
assignees: ""
---

## 🐛 Comportamiento Actual

[Descripción clara y concisa del error detectado]

## 🎯 Comportamiento Esperado

[Cómo debería operar el sistema según la documentación de /docs/modules/]

## 🐾 Pasos para Reproducir

1. Ir a '...'
2. Hacer clic en '....'
3. Ver el error en pantalla/consola

## 📸 Capturas o Logs

```text
[Pegar trazabilidad de errores, consola del navegador o logs de Node.js]
```

## 🛠️ Entorno, Capa Afectada y Criticidad

- **Capa:**
  - [ ] `/backend`
  - [ ] `/frontend`
  - [ ] `/mobile`
  - [ ] `/database`
  - [ ] `/docs`
  - [ ] `global`

- **Entorno:**
  - [ ] `Staging/QA`
  - [ ] `Desarrollo Local`
  - [ ] `Producción`

- **Clasificación de Despliegue:**
  - [ ] Bug Estándar: Corregir en ciclo de Sprint normal (Rama `bugfix/` hacia develop)
  - [ ] HOTFIX Crítico: Parche directo a producción (Rama `hotfix/` desde main)

## 🧪 Definition of Done (DoD - Salida)

- [ ] Rama temporal creada (`bugfix/[módulo]-[nombre-error]` o `hotfix/[módulo]-[nombre-error]`)
- [ ] Commit formateado (`fix(módulo/capa): descripción [BUG-SIGLAS]`)
- [ ] Fallo corregido sin romper otras funcionalidades
- [ ] PR fusionado y verificado en Staging/QA (o en `main` si fue un Hotfix)
