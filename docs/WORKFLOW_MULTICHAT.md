# Workflow Multi-Chat (cómo construir contadurIA con varios chats sin choques)

Este documento define cómo dividir el trabajo en múltiples chats (o agentes) y cómo sincronizarlo
para que todo avance coherente.

---

## 1) Regla de oro: los chats NO son la fuente de verdad

- La fuente de verdad son los archivos en `/docs` y `/docs/adr`.
- Cada chat debe producir entregables que se “versionan” en el repo.
- Si hay conflicto entre chats:
  - gana el último ADR aceptado
  - o se crea un ADR nuevo para resolver.

---

## 2) Estructura recomendada: 8 chats (equilibrio ideal)

### Chat A — HQ (Organización & Docs) ✅ (este chat)

Responsable de:

- mantener `/docs` y ADRs coherentes
- coordinar roadmap/backlog
- decidir “qué va primero”
  Salida:
- documentos + ADRs + reglas

### Chat B — Arquitectura & Data Model

Responsable de:

- refinar `docs/05` modelo de datos
- definir invariantes por módulo
- diseñar event log, jobs, offline, integraciones
  Salida:
- actualizaciones a `docs/05`, `docs/10`, ADRs

### Chat C — UX/UI Premium & Design System

Responsable de:

- guiar layout, componentes, patrones
- definir estándares de tablas, forms, flows
- revisar que todo sea premium
  Salida:
- `STYLEGUIDE_UI_PREMIUM.md` mejoras
- especificaciones de pantallas por módulo

### Chat D — Ventas + POS + Clientes

Responsable de:

- flujos ventas, devoluciones, cobros, POS por industria
- numeración interna y envío por email (DIAN-ready)
  Salida:
- specs de pantallas, edge cases, eventos

### Chat E — Inventario + Recetas/BOM + Costos

Responsable de:

- recetas, consumo parcial, merma, producción
- costeo y reportes de margen
  Salida:
- mejoras `docs/07`, historias MVP

### Chat F — Bancos + Conciliación + Caja + Deudas

Responsable de:

- importación extractos, clasificación, conciliación
- flujo de caja y deudas
  Salida:
- specs + reglas + IA aplicada

### Chat G — Contabilidad (Simple/Avanzado) + Reportes contables

Responsable de:

- motor contable, mapeo, posteo, reversos
- reportes contables vs simples
- cierres (v1)
  Salida:
- mejoras `docs/06`, historias y validaciones

### Chat H — Impuestos Colombia + DIAN-ready (sin integración aún)

Responsable de:

- diseñar configuración fiscal
- flujos impuestos y retenciones
- preparar fase DIAN (normativa, anexos, campos)
  Salida:
- mejoras `docs/08` y ADRs (si aplica)

> Si quieres hacerlo más compacto: fusiona (C+D), (F+IA), y quedan 5 chats.

---

## 3) Qué produce cada chat (entregables obligatorios)

Cada chat debe producir al menos uno:

1. Actualización de documentos en `/docs`
2. Un ADR en `/docs/adr`
3. Épicas/historias para el backlog en `docs/12...`
4. Checklist QA
5. Especificación de pantallas (con:
   - campos,
   - validaciones,
   - permisos,
   - eventos auditable,
   - edge cases)

---

## 4) Protocolo de trabajo (para no duplicar esfuerzos)

### 4.1 Antes de empezar una tarea

- Revisar:
  - sitemap (docs/03)
  - modelo de datos (docs/05)
  - principios (docs/02)
  - arquitectura (docs/10)
- Ver si existe ADR relacionado.

### 4.2 Durante

- Si se detecta una decisión crítica:
  - crear ADR (no solo “opinar en chat”).
- Si cambia el modelo:
  - actualizar `docs/05` y registrar impactos.

### 4.3 Después

- Entregar:
  - lista de cambios en docs
  - historias de backlog (si aplica)
  - checklist de pruebas

---

## 5) Reglas para evitar choques entre chats

1. **No tocar lo que no es tu módulo**
   - Si un cambio afecta a otro módulo, se propone y se abre ADR o issue.
2. **Modelo de datos manda**
   - No se inventan entidades nuevas sin actualizar `docs/05`.
3. **Eventos auditable siempre**
   - Si tu módulo crea/edita/anula, define eventos.
4. **Simple vs Avanzado no se rompe**
   - Cualquier módulo debe operar en Simple.
   - Avanzado agrega controles, no cambia la verdad.
5. **Configuración por industria**
   - Nada de hardcode. Se documenta como regla/plantilla.

---

## 6) Plantilla estándar para especificar una pantalla (usar siempre)

### Screen Spec Template

- Ruta:
- Objetivo:
- Roles que acceden:
- Modo (Simple/Avanzado):
- Componentes:
- Datos requeridos:
- Acciones:
- Validaciones:
- Automatización (manual/asistida/auto):
- Eventos auditable:
- Edge cases (offline/anulación/duplicados):
- KPIs impactados:
- Criterios de aceptación:

---

## 7) Cómo sincronizar con GitHub (trabajo real)

Recomendación:

- cada módulo trabaja en una rama:
  - `feature/docs-ventas`
  - `feature/docs-inventario`
  - etc.
- PRs pequeños y frecuentes.
- Los docs se actualizan con cada decisión.

---

## 8) Propuesta de “cadencia” semanal (1 dev)

- Lunes: plan de semana (docs/12 actualizado)
- Mar–Jue: construir módulos (MVP)
- Viernes: QA + refactor + actualizar docs + preparar demo

---

## 9) Checklist final antes de programar un módulo

- [ ] Está en sitemap (docs/03)
- [ ] Tiene entidades en modelo (docs/05)
- [ ] Tiene permisos (docs/11)
- [ ] Tiene eventos auditable (docs/05 + docs/11)
- [ ] Tiene reverso/anulación definido
- [ ] Tiene segmentación por industria/rol
- [ ] Tiene criterios de aceptación (docs/12)
