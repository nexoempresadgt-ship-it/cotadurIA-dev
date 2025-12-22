# ADR-0004 — Estrategia Offline + Sync (event-queue, idempotencia y conflictos)

**Estado:** Aceptado (MVP: offline parcial)  
**Fecha:** 2025-12-22  
**Decisores:** Owner (Daniel) + Arquitectura contadurIA

---

## 1) Contexto

contadurIA debe funcionar **principalmente online**, pero el cliente inicial (coctelería/fast food) puede necesitar operar con:

- internet inestable,
- cortes temporales,
- y necesidad de registrar ventas/gastos sin perder datos.

El offline total es complejo por:

- contabilidad (doble partida),
- inventario y recetas,
- numeración (y futuro DIAN),
- conciliación bancaria.

Se requiere una estrategia que:

- sea realista para MVP,
- minimice riesgos de datos,
- sea auditable,
- y escale a v1/v2 sin reescribir el core.

---

## 2) Decisión

### 2.1 Offline parcial en MVP

Se soporta offline para:

- Ventas (modo POS simple)
- Gastos rápidos
- Movimientos de inventario limitados (opcional MVP; recomendado v1)
- Captura de soportes (foto/pdf) en cola

Se NO soporta offline en MVP para:

- Importación y conciliación bancaria
- Cambios de configuración (reglas, impuestos, mapeo)
- Posteo contable manual (avanzado)
- Cierres/reaperturas

### 2.2 Patrón: Cola local de eventos + sincronización idempotente

- La app (PWA) guarda acciones offline como **eventos** en IndexedDB.
- Al volver online:
  - envía eventos al servidor en orden
  - el servidor aplica con idempotencia
  - devuelve resultados (ids definitivos, numeración final, etc.)
- Cada evento genera **AuditEvent** del lado servidor.

---

## 3) Diseño del “Offline Event Queue”

### 3.1 Estructura de evento local

Campos mínimos:

- `event_id` (UUID generado en cliente)
- `tenant_id` (siempre)
- `branch_id` (si aplica)
- `actor_user_id`
- `device_id` (UUID persistente)
- `event_type` (ej: sale.create, sale.issue, expense.create)
- `created_at_client` (timestamp)
- `payload` (datos necesarios)
- `idempotency_key` (derivado de event_id + device_id)
- `status` (pending/sent/acked/failed)
- `retry_count`

### 3.2 Eventos permitidos offline (MVP)

- `sale.create_draft`
- `sale.issue_offline`
- `expense.create`
- `support.capture` (adjunto local)
- (opcional v1) `inventory.adjust_simple`

---

## 4) Idempotencia (regla crítica)

### 4.1 Regla

El servidor debe tratar cada evento como **idempotente**:

- Si llega el mismo `idempotency_key` dos veces, NO duplica.
- Devuelve el mismo resultado (ids y estados) que la primera vez.

### 4.2 Implementación conceptual (server)

- Tabla `idempotency_records`:
  - key
  - tenant_id
  - status (success/fail)
  - response_payload (resultado)
  - created_at
- Cada endpoint de sync:
  - consulta si existe key → devuelve respuesta
  - si no existe → procesa transacción → guarda record

---

## 5) Numeración offline → online (con ADR-0003)

### 5.1 Regla

Offline emite con:

- `offline_number` (temporal)
- `offline_series_code = "OFF"` (por defecto)

Online asigna:

- `series_code` real (ej: POS/S1)
- `consecutive_number` real
- `full_number` final

Se registra:

- `document.number.renumbered` con before/after y device_id.

---

## 6) Conflictos (cómo se resuelven sin romper contabilidad)

### 6.1 Tipos de conflicto esperados

1. **Stock insuficiente** al sincronizar (offline vendió más de lo disponible)
2. **Precio/catálogo cambió** mientras estaba offline
3. **Documento duplicado** (usuario repite acción)
4. **Usuario sin permisos** (rol cambiado mientras estaba offline)
5. **Sucursal cambiada** o acceso revocado

### 6.2 Política MVP de resolución

- Nunca “adivinar” silenciosamente si afecta números.
- Resolver así:

**Caso A: Conflicto no crítico (precio)**

- El servidor acepta, pero:
  - marca warning en el documento
  - notifica al admin
  - permite ajuste posterior (auditado)

**Caso B: Conflicto crítico (stock insuficiente)**

- El servidor:
  - acepta la venta como documento emitido (para no perder operación),
  - genera **flag de inconsistencia**:
    - `inventory_conflict = true`
  - crea tarea: “Resolver inventario: faltante X”
  - no permite cierre hasta resolver (v1)

**Caso C: Permisos revocados**

- El evento falla:
  - se marca como `failed` y queda en cola con razón
  - el admin decide si re-emite con otro usuario

**Caso D: Duplicados**

- Idempotencia evita duplicar.
- Si el usuario generó 2 ventas “parecidas” pero distintas:
  - se aceptan ambas y se muestran con device_id y timestamps.

---

## 7) Auditoría (obligatoria)

### 7.1 Eventos de auditoría requeridos

- `offline.event.received`
- `offline.event.applied`
- `offline.event.rejected` (con razón)
- `document.number.renumbered`
- `inventory.conflict.flagged`
- `support.upload.synced`

Cada uno debe guardar:

- tenant_id, branch_id
- actor_user_id
- device_id
- event_id
- payload hash (para integridad)

---

## 8) UX offline (premium y claro)

### 8.1 Indicador de estado

Siempre visible:

- Online ✅ / Offline ⚠️
- “X acciones pendientes de sincronizar”

### 8.2 Centro de sincronización

Pantalla:

- `/app/offline-sync`
  Lista:
- eventos pendientes
- fallidos con razón
- botón “reintentar”
- botón “exportar respaldo” (v1)

### 8.3 Modo POS offline

- Flujo ultrarrápido
- Reduce campos obligatorios
- Adjuntos y detalles quedan opcionales

---

## 9) Consecuencias

### Pros

- Offline realista sin reescribir toda la app
- Idempotencia reduce errores
- Escala a v1/v2 (más eventos, mejor resolución)

### Contras

- No hay “offline total”
- Conflictos de inventario requieren tareas de resolución
- Requiere disciplina: event log + idempotency siempre

---

## 10) Pendientes (v1/v2)

- Sync de inventario más robusto (conteos, transferencias)
- Resolución de conflictos avanzada (workflows)
- Firma/validación de eventos (seguridad extra)
- Sincronización de adjuntos con compresión y reintentos inteligentes
