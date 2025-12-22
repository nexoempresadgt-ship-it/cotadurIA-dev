# ADR-0002 — Estrategia Multi-tenant (Colombia-first, SaaS escalable)

**Estado:** Aceptado (propuesto por defecto; se puede revisar antes de producción)  
**Fecha:** 2025-12-22  
**Decisores:** Owner (Daniel) + Arquitectura contadurIA

---

## 1) Contexto

contadurIA es un SaaS multi-tenant. Debe garantizar:

- aislamiento de datos entre negocios (tenants),
- rendimiento con tablas grandes (ventas, movimientos banco, inventario),
- facilidad operativa (backups, migraciones, observabilidad),
- escalabilidad a múltiples sucursales por negocio,
- posibilidad de crecer a multi-país.

Además:

- el proyecto inicia con 1 dev,
- se busca velocidad de desarrollo sin sacrificar seguridad.

---

## 2) Decisión

### 2.1 Estrategia elegida

**Row-level multi-tenancy**:

- Todas las tablas “de negocio” incluyen `tenant_id`.
- Casi todas incluyen `branch_id` (sucursal) como dimensión.
- Se aplica **Row Level Security (RLS)** en Postgres (cuando sea viable) y/o filtrado estricto por tenant en todas las queries.

### 2.2 Definición del tenant

- **Tenant = Negocio (empresa/marca)**.
- **Sucursal = Branch** dentro del tenant.
- Suscripción se cobra por:
  - tenant base (negocio) + addons por sucursal/usuarios según plan.

> Esto permite que un negocio con varias sedes comparta:
>
> - catálogo,
> - reglas/plantillas,
> - contabilidad consolidada,
>   y aún así reporte por sede.

---

## 3) Alternativas consideradas

### A) Schema por tenant

**Pros**

- aislamiento fuerte (a nivel de schema)
- reduce riesgo de fuga por query

**Contras**

- migraciones más complejas
- miles de schemas al crecer (operación pesada)
- reporting cross-tenant más difícil

### B) Database por tenant

**Pros**

- aislamiento máximo
- cumplimiento fuerte

**Contras**

- operación compleja (backups, migraciones, costos)
- difícil para 1 dev en etapas tempranas

### C) Row-level sin RLS (solo en app)

**Pros**

- más simple al inicio
- rápido para MVP

**Contras**

- depende de disciplina perfecta en código
- riesgo mayor si alguna query se olvida filtrar

---

## 4) Consecuencias (Trade-offs)

### Ventajas

- Simple de operar y escalar
- Migraciones únicas para toda la base
- Buen fit para SaaS
- Permite consolidación por negocio y análisis por sucursal

### Riesgos

- Error humano: una query sin `tenant_id` puede filtrar datos cruzados
- RLS requiere disciplina para usar conexiones/roles bien configurados

### Mitigaciones (obligatorias)

1. **Convención de repositorio/queries**
   - Todas las queries pasan por una capa “scoped” que exige tenant_id.
2. **Testing**
   - tests básicos que validen que endpoints siempre filtran por tenant.
3. **RLS (fase v1)**
   - implementar RLS una vez establecida la estructura.
4. **Auditoría**
   - AuditEvent siempre incluye tenant_id (y branch_id cuando aplique).

---

## 5) Implicaciones en el modelo de datos

### 5.1 Campos estándar en tablas

- `tenant_id` (obligatorio)
- `branch_id` (muy recomendado en tablas operativas)
- `created_at`, `updated_at`
- `created_by` (cuando aplique)
- `status` (draft/issued/voided/etc.)

### 5.2 Índices mínimos recomendados

- `(tenant_id, created_at)`
- `(tenant_id, branch_id, created_at)`
- `(tenant_id, status)`
- `(tenant_id, third_party_id)` para cartera

---

## 6) Implicaciones en UX y producto

- Selector de sucursal en topbar (cuando hay más de una).
- Reportes:
  - consolidado por negocio
  - filtrable por sucursal
- Roles:
  - usuarios pueden tener acceso a una o varias sucursales.

---

## 7) Decisiones relacionadas (futuras ADR)

- ADR: estrategia de offline sync (event-sourcing parcial)
- ADR: numeración de documentos por sucursal vs negocio
- ADR: RLS en Postgres (implementación y roles)
- ADR: estrategia de sharding (si crece masivamente)
