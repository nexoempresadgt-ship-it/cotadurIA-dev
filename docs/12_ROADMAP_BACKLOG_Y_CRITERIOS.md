# 12 — Roadmap, Backlog y Criterios (MVP → v1 → v2)

Este documento define:

- el orden de construcción,
- qué entra en MVP/v1/v2,
- criterios de éxito,
- Definition of Done (DoD),
- y cómo priorizar sin perder el foco.

---

## 1) Filosofía de roadmap (para no ahogarse)

### 1.1 Regla: ganar con un ICP primero

El MVP debe enamorar a un tipo de negocio (cliente inicial) y crear una base sólida:

- coctelería/heladería/granizados + comidas rápidas (recetas + inventario + caja + bancos)

### 1.2 Regla: core antes de features

Antes de sumar módulos “bonitos”:

- multi-tenant definido
- RBAC mínimo
- event log (auditoría)
- base contable (doble partida)
- inventario + BOM (tu diferencial)

### 1.3 Regla: configurabilidad incremental

Hiperconfigurable no significa “1000 opciones al inicio”.
MVP trae:

- plantilla + defaults + niveles de automatización por módulo
  Luego se expande el motor de reglas.

---

## 2) Definición de fases

### Fase 0 — Base del sistema (infra + UX shell) (previo al MVP)

**Meta:** que el proyecto sea construible y no se rompa.

Entregables:

- Estructura de docs + ADR
- Navegación base (sidebar segmentable por rol/industria)
- Auth + sesión
- Multi-tenant (decisión ADR)
- RBAC mínimo
- Event log mínimo
- Sistema UI premium (componentes base + layout)

Criterio de éxito:

- Puedo crear un tenant y entrar a la app con un rol.
- Veo el shell premium con navegación correcta por rol.

---

## 3) MVP (cliente inicial) — “Operación + control real”

### 3.1 Objetivo del MVP

Que el negocio pueda operar diariamente y obtener control:

- ventas, caja, bancos, inventario y recetas
- gastos fijos/variables
- reportes simples que le sirvan al dueño
- IA mínima que ahorra tiempo (bancos)

### 3.2 Módulos MVP (must-have)

#### A) Onboarding por industria (plantilla)

- Selección industria (coctelería/heladería/fast food)
- Modo Simple/Avanzado (por defecto simple)
- Configuración inicial:
  - sucursales
  - unidades (ml, g, unidad)
  - productos base
  - cuentas/cajas
  - reglas iniciales

**Criterio MVP:**

- onboarding termina en < 10 min con datos mínimos.

#### B) Ventas + POS básico (según plantilla)

- Crear venta rápida
- Métodos de pago
- Envío de comprobante por email (PDF interno, DIAN-ready)
- Anulación/reverso

**Criterio MVP:**

- registrar una venta en < 20s en modo rápido.

#### C) Inventario + Recetas/BOM (diferencial)

- Insumos con unidades parciales (ml, g)
- Producto preparado con receta versionada
- Venta → consumo de insumos (asistido por defecto)
- Merma (registro rápido)
- Ajustes / conteo simple
- Kardex básico por producto

**Criterio MVP:**

- El dueño puede ver cuánto insumo consumió por ventas vs merma.

#### D) Compras + Gastos (fijos/variables)

- Compra inventario
- Gasto rápido con categoría simple
- Gastos fijos recurrentes (arriendo, servicios)
- Adjuntar soporte (sin OCR MVP)
- CxP básico (vencimientos simples)

**Criterio MVP:**

- gastos fijos proyectan impacto en flujo de caja.

#### E) Bancos (import CSV) + clasificación + conciliación simple

- Importación de extractos
- Clasificación asistida + reglas “si contiene X”
- Conciliación con pagos/ventas (match simple)
- Caja chica básica

**Criterio MVP:**

- al menos 70% de movimientos bancarios quedan clasificados con reglas/IA + confirmación.

#### F) Contabilidad “interna” + reportes simples

- Doble partida debajo
- Reportes simples:
  - ingresos vs gastos
  - caja/bancos
  - margen por receta (teórico vs real estimado)
  - resumen de inventario

**Criterio MVP:**

- reportes son consistentes con documentos y event log (trazabilidad).

#### G) IA mínima útil (sin humo)

- Clasificación bancaria asistida
- Generador de reglas a partir de correcciones
- Alertas/anomalías básicas (umbral simple)

**Criterio MVP:**

- el usuario siente ahorro de tiempo y control (no “chat bonito”).

---

## 4) v1 (control profesional y escalabilidad)

### 4.1 Objetivo v1

Que contadurIA sea usable para negocios con contador y más volumen.
Agregar:

- contabilidad avanzada activable
- cierres y periodos
- aprobaciones
- reportes robustos
- mejoras de inventario (lotes/seriales por industria)

### 4.2 Módulos v1 (high priority)

- Contabilidad avanzada:
  - PUC + terceros + centros de costo
  - asientos manuales + plantillas
  - cierre mensual y bloqueo
  - auditoría visible
- Aprobaciones:
  - gastos/compras
- Inventario avanzado según industria:
  - vencimientos/lotes (droguería)
  - seriales (electrónica)
  - transferencias robustas multi-bodega
- Reportes avanzados:
  - libro mayor/diario
  - balance de prueba
  - auxiliares por tercero
- IA v1:
  - OCR soportes
  - conciliación más inteligente
  - cierre asistido

---

## 5) v2 (ecosistema, integraciones y expansión)

### 5.1 Objetivo v2

Convertir contadurIA en plataforma:

- integraciones (e-commerce, bancos directos)
- DIAN real
- app móvil
- multi-país

### 5.2 Módulos v2 (selectivos)

- DIAN:
  - facturación electrónica + notas
  - documento soporte electrónico
  - nómina electrónica (si se implementa)
- Integraciones:
  - Shopify/WooCommerce (por país)
  - bancos (open banking cuando aplique)
- Móvil:
  - ventas rápidas
  - gastos con foto
  - dashboard
- Multi-país:
  - capa fiscal por país
  - moneda y calendarios

---

## 6) Backlog priorizado (MVP) — épicas y user stories

### 6.1 Épicas MVP (orden)

1. Shell premium + navegación segmentable
2. Auth + tenant + RBAC mínimo
3. Event log mínimo
4. Catálogo (productos, unidades)
5. Ventas + POS básico
6. Inventario + movimientos
7. Recetas/BOM + consumo parcial
8. Compras/gastos + recurrencias
9. Bancos import + clasificación + reglas
10. Reportes simples + dashboard
11. IA asistida (bancos + reglas)
12. QA + ajustes + piloto con cliente

### 6.2 Formato de historia (plantilla)

- Como [rol], quiero [acción], para [beneficio].
- Criterios de aceptación:
  - …
- Eventos auditable esperados:
  - …
- Permisos:
  - …
- Estados:
  - draft/issued/voided…
- Edge cases:
  - offline, anulación, duplicados…

---

## 7) Definition of Done (DoD) global

Una funcionalidad está “done” si:

1. UI premium completa (empty + loading + errores)
2. Validaciones (frontend y backend)
3. RBAC aplicado
4. Evento auditable registrado
5. Reverso/anulación definido y probado
6. Trazabilidad a reportes (si impacta números)
7. Actualiza docs si cambió reglas/modelo
8. Pruebas mínimas (smoke) o checklist QA

---

## 8) Métricas de éxito por fase

### MVP (piloto)

- uso diario: ventas/gastos registradas
- conciliación: % clasificado
- inventario: reducción de descuadres percibidos
- tiempo de operación: venta < 20s (modo rápido)

### v1

- cierre mensual ejecutable sin caos
- control por centros de costo y terceros
- auditoría confiable

### v2

- adopción de integraciones
- expansión por país

---

## 9) Riesgos y cómo priorizar

### Riesgos

- scope creep (querer “ganarle a todos” en 2 semanas)
- complejidad fiscal temprana
- offline total demasiado pronto
- IA excesiva sin data

### Regla de priorización

Si una feature no mejora:

- operación diaria
- control de caja/bancos
- inventario/costos
- o diferenciación (recetas)
  Entonces se va a v1/v2.
