# 09 — IA “sin humo” (features útiles + validación + auditoría + arquitectura)

Este documento define IA en contadurIA como:

- automatización segura,
- asistencia verificable,
- explicaciones claras,
- y trazabilidad completa.

**La IA no “hace magia” ni reemplaza contabilidad.**
La IA reduce trabajo manual y errores, con control humano.

---

## 1) Principios de IA (reglas no negociables)

1. IA sugiere, humano decide (por defecto).
2. Todo output de IA debe ser:
   - trazable (qué datos usó)
   - auditable (quién aceptó)
   - reversible (undo)
3. IA no inventa datos:
   - si falta información, debe pedirla o marcar “por validar”.
4. Privacidad multi-tenant:
   - IA no puede mezclar datos entre negocios.
5. Seguridad:
   - no ejecutar acciones destructivas sin confirmación explícita.

---

## 2) Niveles de automatización con IA

- **Manual**: IA desactivada; solo guías.
- **Asistido (default MVP)**: IA sugiere; usuario confirma.
- **Automático (opt-in)**: IA ejecuta si:
  - existe regla aprobada
  - existe umbral de confianza
  - queda evento auditable

---

## 3) Tabla de features IA (mínimo 12, accionables)

> Cada feature debe registrar:
>
> - input usado
> - output generado
> - confianza/razón
> - confirmación usuario (si aplica)
> - evento auditado

| #   | Feature                                | Input                               | Output                            | Valor                  | Riesgo               | Validación                           | Auditoría (evento)                         |
| --- | -------------------------------------- | ----------------------------------- | --------------------------------- | ---------------------- | -------------------- | ------------------------------------ | ------------------------------------------ |
| 1   | Clasificación bancaria                 | Extracto (CSV), histórico etiquetas | Categoría + tercero sugerido      | Ahorra tiempo          | mala clasificación   | mostrar “por qué”, permitir corregir | bank.tx.classified (ai_suggested/accepted) |
| 2   | Conciliación automática                | Movimientos banco + pagos/ventas    | Matches sugeridos                 | reduce descuadres      | match incorrecto     | umbral + confirmación                | reconciliation.match.suggested/accepted    |
| 3   | Generación de reglas                   | Correcciones del usuario            | Regla “si contiene X → Y”         | mejora con uso         | reglas peligrosas    | sandbox + preview impacto            | rule.created.from_ai                       |
| 4   | Detección de anomalías gastos          | Gastos históricos + bancos          | Alertas “atípico”                 | evita fugas            | falsos positivos     | threshold + explicación              | anomaly.detected                           |
| 5   | Detección de merma anómala             | Recetas + consumo + ventas          | alerta desviación teórico vs real | control inventario     | datos incompletos    | requerir conteo/confirmación         | inventory.anomaly.detected                 |
| 6   | OCR soportes (v1)                      | Imagen/PDF soporte                  | campos extraídos                  | menos digitación       | OCR errado           | highlight + confirmación             | support.ocr.extracted/accepted             |
| 7   | Asistente cierre mensual               | Checklist + contabilidad            | lista de pendientes               | cierre ordenado        | sugerencias erradas  | cada paso confirmable                | close.assistant.run                        |
| 8   | Explicación de reportes                | Reportes/KPIs                       | narrativa “por qué subió/bajó”    | claridad               | interpretación       | citar datos usados                   | report.explained                           |
| 9   | Sugerencia de precios                  | costos + margen objetivo            | precio sugerido                   | rentabilidad           | mercado externo      | confirmación + simulación            | pricing.suggested                          |
| 10  | Limpieza de terceros duplicados        | terceros + coincidencias            | merge sugerido                    | datos limpios          | merges errados       | preview + confirmación               | thirdparty.merge.suggested/accepted        |
| 11  | Predicción flujo caja (v1)             | recurrencias + CxC/CxP              | proyección 30-60 días             | planeación             | alta incertidumbre   | mostrar rango/escenario              | cashflow.predicted                         |
| 12  | Asistente de onboarding                | industria + respuestas wizard       | config inicial sugerida           | arranque rápido        | sugerencias erradas  | “por validar” + editable             | onboarding.ai.suggested                    |
| 13  | Recomendación de impuestos (cautelosa) | industria + config                  | “posible aplica IVA/INC”          | guía                   | riesgo legal         | “no asesoría” + confirmación         | tax.suggestion.generated                   |
| 14  | Autocompletado contable (avanzado v1)  | documento + reglas                  | propuesta asiento                 | ahorra tiempo contador | asientos incorrectos | balance + preview                    | journal.ai.proposed/accepted               |

---

## 4) Guardrails (controles obligatorios)

### 4.1 Controles de ejecución

- IA no puede:
  - anular documentos,
  - reabrir periodos,
  - cambiar mapeo contable,
  - mover dinero,
    sin confirmación explícita y rol adecuado.

### 4.2 Controles de confianza

- Cada sugerencia tiene:
  - `confidence_score` (0–1)
  - `reasons[]` (por qué lo sugiere)
- Si score < umbral, solo sugerir, no ejecutar.

### 4.3 Controles de datos

- Contexto solo del tenant.
- PII mínima.
- Logs sin exponer documentos sensibles completos cuando no sea necesario.

---

## 5) Arquitectura IA recomendada (práctica y escalable)

### 5.1 Componentes

1. **Feature layer (por caso de uso)**
   - clasificación bancos
   - conciliación
   - anomalías
   - asistente
2. **RAG (cuando aplique)**
   - base de conocimiento del negocio:
     - categorías, productos, reglas, histórico de clasificaciones
     - políticas internas (si el usuario crea)
3. **Policy/Guardrails**
   - validación de acciones permitidas por rol
   - umbrales de confianza
   - confirmaciones obligatorias
4. **Audit logger**
   - todo output y aceptación/rechazo
5. **Evaluation**
   - medir precisión por feature:
     - % aceptado sin cambios
     - tasa de corrección
     - tasa de falsos positivos

### 5.2 Datos de entrada IA (definidos)

- Bancos: concepto, monto, fecha, cuenta, contrapartida si existe
- Documentos: tipo, tercero, líneas, impuestos, totales
- Inventario: consumos, recetas, compras, ajustes
- Usuario: correcciones anteriores (feedback loop)

### 5.3 “Aprendizaje” sin entrenar modelos propios (MVP)

- No entrenar modelos.
- Aprender por:
  - reglas derivadas del usuario
  - diccionario de equivalencias (merchant → categoría)
  - embeddings para similitud (opcional v1)

---

## 6) Diseño de UI para IA (para que sea premium)

### 6.1 Reglas de presentación

- Mostrar:
  - sugerencia
  - razón (explicación corta)
  - botón “Aceptar”
  - botón “Editar”
  - botón “Siempre hacer esto” (crea regla)
- Mostrar “impacto” antes de aplicar:
  - “esto afectará 12 movimientos similares” (si aplica)

### 6.2 Auditoría visible (pro)

En pantallas críticas (bancos/conciliación):

- “Historial de sugerencias IA”
- “Cambios hechos por usuario”
- “Reglas activas que se aplicaron”

---

## 7) Eventos de auditoría IA (estándar)

Todo output IA debe generar eventos:

- ai.suggestion.created
- ai.suggestion.accepted
- ai.suggestion.rejected
- ai.rule.created
- ai.action.executed (solo si auto + permitido)
  Campos mínimos:
- tenant_id, actor_id
- feature_name
- inputs_hash (o referencia)
- output_payload
- confidence_score
- user_feedback (accepted/edited/rejected)

---

## 8) Checklist de implementación IA (por fases)

### MVP (lo mínimo que sí aporta valor)

- [ ] Clasificación bancaria asistida
- [ ] Reglas “si contiene X → Y”
- [ ] Conciliación sugerida (match simple)
- [ ] Anomalías básicas (umbral simple)
- [ ] UI de sugerencias + confirmación + auditoría

### v1

- [ ] OCR soportes
- [ ] cierre asistido
- [ ] embeddings para similitud (merchant, categorías)
- [ ] explicaciones de reportes con trazabilidad

### v2

- [ ] recomendaciones más avanzadas
- [ ] proyección caja por escenarios
- [ ] automatizaciones por rol y aprobaciones
