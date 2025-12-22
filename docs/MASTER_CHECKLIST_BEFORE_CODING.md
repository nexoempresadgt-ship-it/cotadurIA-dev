# Master Checklist — Antes de programar (para no construir sobre arena)

Este checklist es tu “freno de mano” para arrancar el desarrollo con coherencia.
Si no completas lo mínimo, el proyecto se vuelve un caos al mes 2.

---

## A) Repo y estructura (mínimo)

- [ ] Existe carpeta `/docs` y `/docs/adr`
- [ ] Los docs base están en el repo (no solo en archivos cargados):
  - [ ] docs/00_START_HERE.md
  - [ ] docs/02_PRINCIPIOS_DE_PRODUCTO.md
  - [ ] docs/03_SITEMAP_MAESTRO.md
  - [ ] docs/05_DOMINIO_Y_MODELO_DE_DATOS.md
  - [ ] docs/06_MOTOR_CONTABLE_SIMPLE_VS_AVANZADO.md
  - [ ] docs/07_INVENTARIO_Y_RECETAS_BOM.md
  - [ ] docs/10_ARQUITECTURA_Y_NO_FUNCIONALES.md
  - [ ] docs/11_SEGURIDAD_RBAC_AUDITORIA.md
  - [ ] docs/12_ROADMAP_BACKLOG_Y_CRITERIOS.md
- [ ] ADRs creados:
  - [ ] ADR-0002 multi-tenant
  - [ ] ADR-0003 numeración documentos
  - [ ] ADR-0004 offline sync
- [ ] Rama main protegida (ideal): PR required

---

## B) Decisiones técnicas cerradas (para no re-trabajar)

- [ ] Tenant = negocio; Branch = sucursal (ADR-0002)
- [ ] Estrategia multi-tenant: row-level + (plan RLS v1) (ADR-0002)
- [ ] Numeración por sucursal + serie, offline renumeración (ADR-0003)
- [ ] Offline MVP parcial con cola de eventos + idempotencia (ADR-0004)
- [ ] Método de costeo MVP: promedio ponderado (docs/07)
- [ ] Modo Simple y Avanzado comparten datos (docs/06)

---

## C) Producto: foco y alcance MVP (evitar scope creep)

- [ ] ICP definido: coctelería/heladería/fast food
- [ ] Plantilla inicial definida (docs/04):
  - módulos ON/OFF
  - defaults (unidades ml/g)
  - reglas sugeridas
- [ ] Sidebar MVP definido (máx 10 items visibles)
- [ ] MVP lista “must-have” cerrada (docs/12):
  - Ventas/POS
  - Inventario + Recetas/BOM
  - Gastos fijos/variables + compras
  - Bancos import CSV + clasificación + conciliación simple
  - Reportes simples + dashboard
  - IA mínima (bancos + reglas)

---

## D) Modelo de datos mínimo (antes de escribir 1 pantalla grande)

- [ ] Entidades núcleo MVP definidas (docs/05) y validadas:
  - Tenant, Branch, User, Role, Permission
  - Product, Unit, Recipe, InventoryMovement
  - SaleDocument, SaleLine, Payment
  - Purchase/Expense
  - BankAccount, BankTransaction, ImportBatch
  - JournalEntry + JournalLine (aunque sea interno)
  - AuditEvent
- [ ] Estados estándar definidos:
  - draft / issued / voided / reversed (según entidad)
- [ ] Campos estándar:
  - tenant_id, branch_id, created_at, status
- [ ] Trazabilidad:
  - JournalEntry.source_type/source_id
  - InventoryMovement.source_type/source_id

---

## E) Seguridad y permisos (RBAC) listos

- [ ] Roles base definidos (docs/11):
  - Owner, Admin, Contador, Vendedor/Cajero, Bodeguero
- [ ] Permisos por módulo definidos (docs/11)
- [ ] Acciones sensibles protegidas:
  - anulación, ajustes inventario, cambios fiscales, cambios mapeo, reapertura periodo
- [ ] Auditoría obligatoria definida:
  - lista de event_types mínimos

---

## F) UX premium “desde el día 1” (para no refactor eterno)

- [ ] Styleguide aplicado (docs/STYLEGUIDE_UI_PREMIUM.md)
- [ ] Patrones definidos:
  - tablas pro (búsqueda, filtros, acciones)
  - formularios con validación + campos avanzados
  - estados loading/empty/error
- [ ] Flujos críticos definidos (screen specs):
  - Venta rápida (POS)
  - Crear receta (BOM)
  - Consumo por venta
  - Registrar merma
  - Importar banco
  - Clasificar + crear reglas
  - Conciliar movimientos

---

## G) “Screen Specs” antes de implementar (regla de oro)

Antes de codificar cada pantalla, debe existir una spec usando el template:

- Ruta:
- Objetivo:
- Roles:
- Modo (Simple/Avanzado):
- Campos:
- Validaciones:
- Acciones:
- Automatización:
- Eventos auditable:
- Edge cases (offline/anulación/duplicados):
- Criterios de aceptación:

---

## H) Plan de trabajo (1 dev) — orden recomendado para MVP

1. Shell premium + navegación + auth
2. Tenant/Branch + RBAC + auditoría base
3. Catálogo + unidades
4. Ventas + POS básico + envío PDF email
5. Inventario movimientos base
6. Recetas/BOM + consumo parcial
7. Gastos fijos/variables + compras
8. Bancos import CSV + clasificación + reglas
9. Conciliación simple
10. Dashboard + reportes simples
11. IA mínima (asistida) + anomalías básicas
12. Piloto con cliente + iteración

---

## I) “Red flags” (si pasa esto, para y arregla)

- ❌ Estás hardcodeando por industria en el código
- ❌ No estás registrando AuditEvent en operaciones críticas
- ❌ Estás borrando datos en vez de anular/reversar
- ❌ Estás creando “dos contabilidades” separadas para simple y avanzado
- ❌ Estás agregando features v2 antes de que el ICP use diario el MVP
- ❌ Tablas sin paginación/filtros en módulos grandes (ventas/bancos/inventario)

---

## J) Checklist final “GO / NO GO”

**GO si:**

- [ ] Docs + ADRs están en repo
- [ ] Modelo de datos MVP está claro
- [ ] RBAC + auditoría definidos
- [ ] MVP scope cerrado
- [ ] Styleguide listo

**NO GO si:**

- [ ] hay decisiones críticas sin ADR
- [ ] no existe estrategia offline/numeración
- [ ] no hay trazabilidad (source_id) para contabilidad e inventario
