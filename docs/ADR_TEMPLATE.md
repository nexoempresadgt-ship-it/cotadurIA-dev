# ADR Template — Architecture Decision Record

> Usa este template para cualquier decisión crítica.
> Regla: si una decisión afecta datos, seguridad, offline, contabilidad o integraciones, debe tener ADR.

---

## ADR-XXXX — Título corto y claro

**Estado:** Propuesto | Aceptado | Deprecado  
**Fecha:** YYYY-MM-DD  
**Decisores:** Owner (Daniel) + (si aplica)  
**Módulos afectados:** (ventas / bancos / inventario / contabilidad / impuestos / plataforma)

---

## 1) Contexto

Describe el problema y por qué se debe decidir ahora.

- Qué se está construyendo
- Qué restricciones existen
- Qué riesgos hay si no se decide

---

## 2) Decisión

Explica exactamente qué se eligió.

- Estrategia/solución:
- Reglas principales:
- Límites del alcance:

---

## 3) Alternativas consideradas

Lista 2–4 alternativas reales.

### A) Opción 1

**Pros**

- …
  **Contras**
- …

### B) Opción 2

**Pros**

- …
  **Contras**
- …

---

## 4) Consecuencias

Qué cambia a partir de esta decisión.

- Impactos en modelo de datos
- Impactos en UX
- Impactos en performance
- Implicaciones operativas (migraciones, backups, soporte)

---

## 5) Mitigaciones y guardrails

Si la decisión tiene riesgos, define controles obligatorios.

- Tests
- Auditoría
- Validaciones
- Rollback plan (si aplica)

---

## 6) Plan de implementación (MVP / v1 / v2)

- MVP:
- v1:
- v2:

---

## 7) Referencias

Links o docs internos relevantes.

- docs/xx...
- ADR-000y...
