# 05 — Dominio y modelo de datos (Entidades, relaciones e invariantes)

Este documento define el “lenguaje del sistema” y sus entidades núcleo.
Antes de construir pantallas grandes, este documento debe estar estable.

---

## 1) Invariantes (reglas del sistema que no se rompen)

1. **Multi-tenant real**
   - Ningún usuario puede acceder a datos de otro tenant.
2. **No borrar**
   - Se anula o se revierte, siempre con auditoría.
3. **Event log para todo lo importante**
   - Toda acción que cambie números o configuración debe quedar registrada.
4. **Simple y Avanzado comparten datos**
   - No existen dos sistemas separados.
5. **Operación → Contabilidad**
   - Documentos operativos deben poder generar asientos (auto/asistido/manual).
6. **Trazabilidad**
   - Toda cifra debe poder explicarse: de reporte → asiento → documento → evento.

---

## 2) Conceptos base del dominio

### 2.1 Tenant, Sucursal, Usuario

- Tenant contiene:
  - configuración fiscal
  - plantilla de industria
  - reglas
  - datos operativos y contables
- Sucursal:
  - dimensión operativa (ventas, inventario, caja)
- Usuario:
  - pertenece a tenant
  - tiene rol/permisos
  - puede estar asociado a una o varias sucursales (opcional)

### 2.2 Dimensiones transversales

Estas dimensiones aparecen en casi todo:

- sucursal_id
- tercero_id (cliente/proveedor)
- centro_costo_id (avanzado)
- moneda (multi-país futuro)
- canal_venta_id

---

## 3) Entidades núcleo (MVP)

> Nota: esto no es un esquema SQL final; es la lista de entidades y relaciones mínimas.

### 3.1 Plataforma y seguridad

- Tenant
- Branch (Sucursal)
- User
- Role
- Permission
- UserRole
- AuditEvent (Event Log)

### 3.2 Terceros

- ThirdParty (tercero base)
  - tipo: cliente / proveedor / ambos
  - identificación: NIT/CC, DV, etc.
- Contact (contactos por tercero)

### 3.3 Catálogo

- Product
  - type: inventory_item / service / prepared_item
  - unidad base (unidad/ml/g/porción)
  - impuestos aplicables (configurable)
- ProductVariant (si aplica)
- PriceList (por canal o cliente)
- Unit (sistema de unidades)
- TaxCategory (referencias internas; DIAN-ready futuro)

### 3.4 Ventas

- SaleDocument
  - estado: draft/issued/voided
  - numeración interna (serie + consecutivo)
  - totals (subtotal, taxes, total)
- SaleLine
  - product_id / service
  - qty + unit
  - price + discount
- Payment
  - método: cash/card/transfer/other
  - link a caja/banco si aplica
- AccountsReceivable (CxC)
  - saldo y vencimientos
  - abonos (PaymentAllocation)

### 3.5 Compras y gastos

- PurchaseDocument
- PurchaseLine
- Expense (gasto rápido) o usar PurchaseDocument con tipo = expense
- AccountsPayable (CxP)
- VendorPayment

> Recomendación: unificar compras y gastos con un documento genérico “Document” con tipos,
> pero si estás empezando, separar puede ser más simple. Se decide en ADR.

### 3.6 Bancos y caja

- Cashbox
- BankAccount
- BankTransaction
- BankImportBatch
- ReconciliationSession
- ReconciliationMatch

### 3.7 Inventario

- InventoryItem (puede ser Product type=inventory)
- InventoryMovement
  - tipo: purchase_in / sale_out / adjustment / transfer / production_consume / production_output
- Warehouse (bodega)
- StockSnapshot (opcional, para performance)

### 3.8 Recetas / BOM y producción

- Recipe
  - product_id (producto preparado)
  - version
  - yield (porciones)
- RecipeLine
  - ingredient_product_id
  - qty + unit
  - waste (merma)
- ProductionOrder (opcional MVP; puede ser v1)
- ProductionEvent (consumo/producción)

### 3.9 Contabilidad (doble partida)

- ChartOfAccounts (PUC)
- Account
- JournalEntry (asiento)
- JournalLine (líneas deb/cred)
- PostingRule (reglas de contabilización)
- SimpleCategory (categorías simples)
- CategoryAccountMap (mapeo simple ↔ PUC)

### 3.10 Reportes (derivados)

- ReportSnapshot (opcional, para cache)
- KPIConfig (qué se muestra en dashboard)

---

## 4) Relaciones clave (vista conceptual)

- Tenant 1—N Branch
- Tenant 1—N User
- Tenant 1—N ThirdParty
- Tenant 1—N Product
- SaleDocument 1—N SaleLine
- SaleDocument 1—N Payment (o pagos separados)
- SaleDocument 0..N JournalEntry (asientos generados)
- PurchaseDocument 0..N JournalEntry
- InventoryMovement puede originarse en:
  - SaleLine (si producto preparado o inventario)
  - PurchaseLine
  - Adjustment
  - Transfer
  - ProductionEvent
- CategoryAccountMap depende de:
  - plantilla (industria)
  - tenant (customización)
  - vigencia por fecha (opcional v1)

---

## 5) Event Log (Auditoría) — especificación mínima

### 5.1 Qué debe guardar

AuditEvent:

- id
- tenant_id
- branch_id (si aplica)
- actor_type: user/system/ai
- actor_id (si user)
- event_type (string)
- entity_type (sale, product, inventory_movement, journal_entry, etc.)
- entity_id
- timestamp
- payload_min (diff o snapshot)
- metadata (ip, user-agent, origen: import/manual)

### 5.2 Tipos de eventos mínimos

- tenant.created / updated
- template.applied / changed
- rule.created / updated / toggled
- sale.created / issued / voided
- purchase.created / posted / voided
- expense.created / voided
- inventory.movement.created / reversed
- bank.imported / classified
- reconciliation.matched / resolved
- journal.entry.created / reversed / edited (si permitido)
- close.period / reopen.period
- permissions.changed

---

## 6) Multi-tenant (por decidir en ADR)

Opciones:

1. Row-level (tenant_id en todas las tablas) + RLS en Postgres
2. Schema por tenant
3. Database por tenant

**Recomendación para solo dev / SaaS**:

- Row-level + RLS (más simple de operar y escalar)
- Se formaliza en ADR.

---

## 7) Qué falta para v1 (no MVP)

- Lotes y vencimientos
- Seriales
- Aprobaciones
- Centros de costo obligatorios por regla
- Cierres contables formales
- Multi-moneda (multi-país)
- API pública y webhooks

---

## 8) Checklist de “modelo listo para construir”

- [ ] Definido tenant y sucursal (ADR)
- [ ] Lista de entidades MVP cerrada
- [ ] Event types mínimos definidos
- [ ] Definido mapeo simple ↔ PUC
- [ ] Definidos estados de documentos (draft/issued/voided)
- [ ] Definidos orígenes (manual/import/ai/system)
