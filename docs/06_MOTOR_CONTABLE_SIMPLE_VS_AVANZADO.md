# 06 — Motor contable: Modo Simple vs Modo Avanzado (PUC opcional)

Este documento define cómo contadurIA garantiza contabilidad real (doble partida) para todos,
mientras ofrece dos experiencias: Simple (amigable) y Avanzado (profesional).

---

## 1) Objetivo y alcance

### Objetivo

- Permitir que un usuario no contable opere (ventas/gastos/bancos/inventario) sin fricción.
- Mantener datos contables correctos (doble partida) desde el día 1.
- Permitir que un contador habilite PUC y controles avanzados en cualquier momento.

### Alcance MVP

- Doble partida interna para ventas, compras/gastos, pagos, bancos, inventario (básico).
- Categorías simples + mapeo configurable a cuentas contables.
- Reportes básicos (resultado simple, caja/bancos, margen).

### Alcance v1/v2 (referencia)

- Cierres contables por periodos, bloqueos, reapertura con permisos.
- Centros de costo completos, terceros obligatorios por regla.
- Documentos contables completos (comprobantes).
- Ajustes, provisiones, depreciaciones, etc.

---

## 2) Principio central (no negociable)

> **Una sola contabilidad. Dos vistas.**

- Los documentos operativos son los “eventos de negocio”.
- La contabilidad se genera como “posteo” de esos documentos.
- Modo Simple oculta complejidad, pero no altera la verdad contable.

---

## 3) ¿Qué es el “Motor Contable”?

El motor contable es el conjunto de:

1. **Reglas de contabilización** (Posting Rules)
2. **Mapeo Simple → Cuenta** (CategoryAccountMap)
3. **Generación de asientos** (JournalEntry)
4. **Validaciones** (balanceado, periodos, impuestos)
5. **Trazabilidad** (origen, actor, reversos, auditoría)

---

## 4) Modelo conceptual mínimo

### 4.1 Entidades (alto nivel)

- SimpleCategory: categorías amigables (Modo Simple)
- Account: cuenta contable (PUC si Avanzado)
- CategoryAccountMap: mapeo de categoría → cuenta (por tenant/industria)
- PostingRule: regla para decidir cuentas y tratamiento según evento
- JournalEntry: asiento (cabecera)
- JournalLine: línea deb/cred
- ClosePeriod: periodos contables (v1+)

### 4.2 Orígenes de asientos (source)

Cada asiento debe guardar `source_type` y `source_id`:

- sale_document
- purchase_document
- bank_transaction
- inventory_movement
- manual (avanzado)

---

## 5) Modo Simple (experiencia y datos)

### 5.1 Qué ve el usuario

- Categorías “humanas” para ingresos/gastos
- Reportes claros (sin PUC)
- Reglas sugeridas (asistido)
- “Contabilidad” como:
  - “Resumen” y “Salud del negocio”
  - “Explicaciones” en lenguaje simple

### 5.2 Qué se registra por debajo

- Se registra JournalEntry y JournalLines igualmente.
- La diferencia es que:
  - las cuentas no se presentan como PUC,
  - pero existen como “cuentas internas base” (o PUC mínimo oculto).

> Recomendación: tener un **Chart of Accounts mínimo interno** desde el MVP,
> aunque el usuario no lo vea.

### 5.3 Categorías simples (v0 sugerida)

**Ingresos**

- Ventas (producto/servicio)
- Otros ingresos

**Costos**

- Costo de ventas (si inventario/recetas)
- Compras de inventario (si se maneja como activo)

**Gastos**

- Arriendo
- Servicios públicos
- Nómina (simple)
- Comisiones y pasarelas
- Transporte/envíos
- Publicidad/marketing
- Mantenimiento
- Impuestos y tasas (simple)
- Otros gastos

**Finanzas**

- Préstamos / intereses
- Pagos de deuda
- Transferencias internas

> Estas categorías se ajustan por plantilla de industria.

---

## 6) Modo Avanzado (PUC y contabilidad profunda)

### 6.1 Qué habilita

- PUC completo (o import de PUC)
- Asientos manuales y plantillas
- Terceros contables (NIT/DV) obligatorios en ciertas transacciones
- Centros de costo / proyectos
- Cierres contables por periodo (v1)
- Auditoría ampliada

### 6.2 Reglas adicionales

- Validación de periodos cerrados (no editar)
- Validación de cuentas (naturaleza deb/cred)
- Controles de integridad (balance, terceros, centros)

---

## 7) Capa de Mapeo (Simple ↔ Avanzado)

### 7.1 Qué es

Una tabla editable (por rol admin/contador) que define:

- `simple_category_id` → `account_id` (cuenta PUC o interna)
- opcional: cuenta alternativa por sucursal, canal o línea de negocio
- vigencia: desde/hasta (v1)

### 7.2 Reglas de consistencia del mapeo

1. Si existen asientos en periodos cerrados:
   - no permitir cambiar mapeo retroactivo sin reapertura.
2. Si se cambia mapeo:
   - aplica a nuevos documentos (por defecto).
3. Debe registrarse un evento auditable:
   - antes/después + quién cambió + motivo.

### 7.3 Wizard de cambio de mapeo (UX requerida)

Al editar el mapeo:

- Mostrar “afecta solo futuro” (default)
- Opción avanzada: “recalcular desde fecha X” (v1+) con advertencia fuerte
- Mostrar impacto estimado:
  - cuántos documentos serían recalculados
  - qué periodos tocaría
- Confirmación y auditoría

---

## 8) Posteo contable (Posting) — cómo se generan asientos

### 8.1 Estados de un documento

Cada documento operativo debe tener:

- draft (borrador)
- issued/posted (emitido)
- voided (anulado)
- reversed (si aplica)

### 8.2 Momento de posteo

Por configuración por módulo:

- Manual: el usuario decide cuándo postear
- Asistido: el sistema sugiere y el usuario confirma
- Automático: se postea al emitir

### 8.3 Reverso / anulación (regla general)

- No se elimina un asiento.
- Se crea un **asiento reverso** con líneas invertidas (deb↔cred).
- Se linkea:
  - `reversal_of_entry_id`
  - `reason`

---

## 9) Ejemplos concretos (muy importantes)

> Estos ejemplos deben guiar implementación y pruebas.

### 9.1 Venta en efectivo (sin inventario)

Evento: sale_document issued

- Debe:
  - aumentar caja (activo)
  - reconocer ingreso (ingreso)

Asiento:

- Débito: Caja / Banco
- Crédito: Ingresos por ventas

### 9.2 Venta a crédito (CxC)

- Débito: Cuentas por cobrar (cliente)
- Crédito: Ingresos por ventas

Cuando paga:

- Débito: Caja / Banco
- Crédito: Cuentas por cobrar (cliente)

### 9.3 Compra de inventario

- Débito: Inventario
- Crédito: Cuentas por pagar (proveedor) o Caja/Banco si contado

### 9.4 Gasto operativo (sin inventario)

- Débito: Gasto (categoría simple → cuenta)
- Crédito: Caja/Banco o CxP

### 9.5 Consumo de inventario por receta (costo de venta)

Cuando se vende producto preparado:

1. Salida de inventario de insumos
2. Reconocer costo de venta

Asiento:

- Débito: Costo de ventas
- Crédito: Inventario

> Esto requiere costeo (promedio MVP).

### 9.6 Merma / ajuste

- Débito: Gasto por merma (o Costo de ventas, según política)
- Crédito: Inventario

---

## 10) Reportes contables y “reportes simples”

### 10.1 Reportes simples (para no contables)

- Ingresos del periodo
- Gastos fijos vs variables
- Flujo de caja simplificado
- Margen estimado (si inventario/recetas)

### 10.2 Reportes avanzados

- Libro diario y mayor
- Balance de prueba
- Estado de resultados contable
- Balance general
- Auxiliares por tercero
- Reportes por centro de costo

> Los reportes avanzados deben ser derivables de JournalLines + dimensiones.

---

## 11) Validaciones mínimas del motor (MVP)

- Un JournalEntry debe estar balanceado (sum deb = sum cred)
- Las cuentas deben existir y ser del tenant
- Fechas contables deben respetar periodo (si v1)
- No permitir posteo si:
  - falta mapeo para una categoría usada
  - hay inconsistencias de impuestos configurados (si aplica)

---

## 12) Auditoría contable (qué se registra siempre)

Por cada JournalEntry:

- origen (documento)
- actor (user/system/ai)
- timestamp creación
- fecha contable
- motivo (si manual o ajuste)
- reversos y referencias

---

## 13) Checklist para implementación del motor contable

- [ ] Definir Chart of Accounts mínimo interno (MVP)
- [ ] Definir SimpleCategories base por industria
- [ ] Crear CategoryAccountMap inicial por plantilla
- [ ] Implementar creación de JournalEntry desde:
  - ventas
  - compras/gastos
  - pagos
  - inventario (consumos/ajustes)
- [ ] Implementar reversos (anulación)
- [ ] Implementar validación de balance
- [ ] Implementar auditoría para mapeo y posteos
