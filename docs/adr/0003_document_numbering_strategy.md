# ADR-0003 — Estrategia de numeración de documentos (DIAN-ready + offline)

**Estado:** Aceptado (para MVP; DIAN se integra en v2)  
**Fecha:** 2025-12-22  
**Decisores:** Owner (Daniel) + Arquitectura contadurIA

---

## 1) Contexto

contadurIA debe:

- generar documentos de venta “internos” (sin DIAN por ahora),
- enviar comprobantes por email (PDF),
- manejar múltiples sucursales,
- soportar operación offline parcial,
- y quedar listo para incorporar DIAN en v2 sin reconstruir todo.

La numeración debe ser:

- coherente (sin choques),
- auditable,
- configurable (serie/prefijo),
- y compatible con escenarios futuros.

---

## 2) Decisión

### 2.1 Cada tipo de documento tiene numeración propia

Por ejemplo:

- Venta (invoice)
- Nota crédito / devolución
- Compra
- Gasto
- Ajuste inventario
- Transferencia inventario
- Recibo de caja / egreso

Cada tipo tendrá:

- `document_type`
- `series_code` (ej: "POS", "WEB", "S1")
- `consecutive_number`
- `full_number` (ej: "POS-00001234")
- `issue_timestamp`
- `status` (draft/issued/voided)

---

## 3) Alcance de la secuencia (por sucursal, recomendado)

### 3.1 Regla

**La secuencia de ventas se lleva por sucursal** (branch) y por serie.

Ejemplo:

- Sucursal 1: POS-00000001, POS-00000002…
- Sucursal 2: POS-00000001, POS-00000002…

**Ventajas:**

- cada sucursal opera independiente
- reduce choque en offline
- facilita reportes y operación física

**Contras:**

- consolidación requiere mostrar sucursal + número (pero es normal)

### 3.2 Excepción

Si el negocio pide numeración única global, se habilita como opción (v1) con más restricciones para offline.

---

## 4) Reservas de numeración y concurrencia

### 4.1 Online

- Al emitir (issued), el servidor asigna el consecutivo con bloqueo transaccional.
- Se guarda el “hueco” si falla el email/PDF; el documento sigue emitido.

### 4.2 Offline (MVP: estrategia segura)

Cuando el usuario emite offline:

- se asigna un `offline_number` temporal:
  - formato: `OFF-{branch}-{device}-{timestamp}-{rand}`
- y un `offline_series_code` (ej: "OFF")

Cuando vuelve online:

- el servidor:
  1. valida idempotencia (no duplicar venta)
  2. asigna el número final `series_code + consecutive_number`
  3. registra evento auditable de “renumeración”

**Regla:** el PDF “final” se regenera con el número definitivo (y opcionalmente se conserva el PDF offline como “borrador emitido offline”).

---

## 5) DIAN-ready (sin DIAN aún)

Aunque no integremos DIAN en MVP, guardaremos campos para futuro:

- `dian_status` (none/pending/accepted/rejected) — por ahora "none"
- `dian_uuid` (CUFE/UUID futuro)
- `dian_payload_hash` (futuro)
- `customer_email` (para envío)
- `tax_breakdown` por línea y totales

---

## 6) Auditoría obligatoria

Se registran eventos:

- `document.number.assigned` (issued)
- `document.number.renumbered` (offline → online)
- `document.voided` (con motivo)
- `document.pdf.generated` (opcional)
- `document.email.sent` (opcional)

Cada evento incluye:

- tenant_id, branch_id
- actor (user/system)
- before/after (en renumeración)
- motivo (si aplica)
- metadata (device_id en offline)

---

## 7) Consecuencias

### Pros

- Funciona muy bien para sucursales + offline
- No bloquea DIAN futuro
- Es auditable y consistente

### Contras

- Puede haber “saltos” (huecos) si un documento se emite y luego se anula
  - esto es normal y aceptable; se audita

---

## 8) Pendientes (por validar en v1/v2)

- Numeración DIAN real (rangos autorizados, resolución DIAN)
- Series múltiples por canal (POS vs Web vs E-commerce)
- Reglas para cierres y bloqueo de numeración por periodo
