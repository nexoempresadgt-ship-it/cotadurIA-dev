# Decision Log — ContadurIA

Este archivo registra **decisiones pequeñas** que no ameritan ADR completo.
Regla:

- Si una decisión impacta **datos, seguridad, contabilidad, offline, integraciones**, se convierte en ADR.
- Si una decisión es táctica/menor, se registra aquí.

Formato:

- Fecha
- Decisión
- Motivo
- Impacto
- Dueño
- Link (si aplica)

---

## 2025-12-22

### Decisión: Repo privado por defecto

- Motivo: proteger estrategia, pricing, diferenciadores y lógica de negocio.
- Impacto: se habilita acceso a ChatGPT vía GitHub Connected App solo al repo requerido.
- Dueño: Daniel
- Link: docs/CONNECT_GITHUB_CHATGPT.md

### Decisión: Web-first (desktop) antes de móvil

- Motivo: ICP opera principalmente en computadores; MVP requiere velocidad y tablas pro.
- Impacto: PWA/offline parcial; móvil en v2.
- Dueño: Daniel
- Link: docs/10_ARQUITECTURA_Y_NO_FUNCIONALES.md

### Decisión: DIAN se integra después, pero DIAN-ready desde ya

- Motivo: reducir complejidad inicial sin bloquear la fase DIAN.
- Impacto: guardar campos mínimos y trazabilidad; numeración y tax breakdown por línea.
- Dueño: Daniel
- Link: docs/08_IMPUESTOS_COLOMBIA_DIAGNOSTICO.md, ADR-0003

### Decisión: Sidebar corta por defecto (segmentable)

- Motivo: UI premium y baja fricción; no saturar al usuario.
- Impacto: navegación por rol/industria; “More”/submenús.
- Dueño: Daniel
- Link: docs/03_SITEMAP_MAESTRO.md, docs/STYLEGUIDE_UI_PREMIUM.md

### Decisión: IA por defecto en modo asistido (no automático)

- Motivo: evitar errores y riesgo legal; construir confianza.
- Impacto: confirmación humana + auditoría de sugerencias.
- Dueño: Daniel
- Link: docs/09_IA_SIN_HUMO.md

---

## (Plantilla para nuevas entradas)

## YYYY-MM-DD

### Decisión: ...

- Motivo:
- Impacto:
- Dueño:
- Link:
