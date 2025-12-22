# INDEX — ContadurIA Docs (Fuente de verdad)

Este índice es la **entrada oficial** a la documentación del repo.
Regla: si algo importante no está documentado aquí o en un ADR, **no existe**.

---

## 1) Cómo leer estos docs (orden recomendado)

### 1) Start

- `00_START_HERE.md` — visión rápida, reglas de uso de docs y cómo contribuir
- `01_VISION_Y_ESTRATEGIA.md` — visión, ICP, diferenciación, enfoque Colombia-first

### 2) Producto y navegación

- `02_PRINCIPIOS_DE_PRODUCTO.md` — principios no negociables (simple vs avanzado, auditoría, etc.)
- `03_SITEMAP_MAESTRO.md` — navegación completa (segmentable por rol/industria)
- `04_PLANTILLAS_POR_INDUSTRIA.md` — plantillas de onboarding y módulos por industria

### 3) Dominio y core contable

- `05_DOMINIO_Y_MODELO_DE_DATOS.md` — entidades, relaciones, eventos y trazabilidad
- `06_MOTOR_CONTABLE_SIMPLE_VS_AVANZADO.md` — doble partida + mapeo simple↔PUC
- `07_INVENTARIO_Y_RECETAS_BOM.md` — BOM, consumo parcial, merma, costeo

### 4) Impuestos + IA (sin humo)

- `08_IMPUESTOS_COLOMBIA_DIAGNOSTICO.md` — DIAN-ready (sin integración aún)
- `09_IA_SIN_HUMO.md` — features IA auditables + guardrails

### 5) Plataforma y seguridad

- `10_ARQUITECTURA_Y_NO_FUNCIONALES.md` — multi-tenant, jobs, offline, integraciones
- `11_SEGURIDAD_RBAC_AUDITORIA.md` — roles/permisos, acciones sensibles, auditoría
- `STYLEGUIDE_UI_PREMIUM.md` — reglas de UI premium (obligatorio)

### 6) Ejecución

- `12_ROADMAP_BACKLOG_Y_CRITERIOS.md` — MVP→v1→v2, DoD, prioridades
- `SCREEN_SPECS_LIBRARY_MVP.md` — specs de pantallas MVP
- `QA_TESTING_STRATEGY.md` — estrategia QA + smoke tests
- `LAUNCH_PILOT_PLAN.md` — plan de piloto con el cliente inicial
- `MASTER_CHECKLIST_BEFORE_CODING.md` — checklist final antes de programar

### 7) Workflow y conexión

- `WORKFLOW_MULTICHAT.md` — cómo trabajar con varios chats sin conflictos
- `CONNECT_GITHUB_CHATGPT.md` — conectar repo a ChatGPT y rutina de trabajo
- `ADR_TEMPLATE.md` — plantilla para decisiones críticas

---

## 2) ADRs (Decisiones arquitectónicas)

Ubicación: `docs/adr/`

- `0002_multi_tenant_strategy.md` — definición tenant/branch y estrategia multi-tenant
- `0003_document_numbering_strategy.md` — numeración, offline y DIAN-ready
- `0004_offline_sync_strategy.md` — offline parcial, cola de eventos, idempotencia

Regla: cualquier decisión que afecte **datos / contabilidad / seguridad / offline / integraciones**
debe tener ADR.

---

## 3) Reglas rápidas (TL;DR)

- **Docs mandan**: la fuente de verdad es `/docs` y `/docs/adr`.
- **No borrar**: todo se anula/reversa y se audita.
- **Una contabilidad, dos vistas**: Simple/Avanzado comparten la misma verdad contable.
- **Configuración por industria**: evitar hardcode; usar plantillas/reglas.
- **Multi-tenant primero**: todas las tablas llevan `tenant_id` (y casi siempre `branch_id`).
- **Offline parcial MVP**: POS y gastos; bancos e impuestos avanzados no.

---

## 4) Dónde registrar cambios

- Cambios de reglas/decisiones → nuevo ADR o update a doc relevante
- Nuevas pantallas → agregar spec en `SCREEN_SPECS_LIBRARY_MVP.md`
- Nuevas historias → `12_ROADMAP_BACKLOG_Y_CRITERIOS.md` o issues en GitHub
