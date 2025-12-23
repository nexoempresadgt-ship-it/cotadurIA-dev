# ContadurIA (cotadurIA-dev)

App web contable/operativa hiperconfigurable para negocios en Colombia (Colombia-first), con UX premium, auditoría total, contabilidad Simple/Avanzada y soporte de inventario por recetas (BOM) con consumo parcial (ml/g).

> Estado: en construcción (MVP en progreso).
> Fuente de verdad de producto/arquitectura: carpeta `/docs` y `/docs/adr`.

---

## Objetivo del MVP

Validar operación diaria para el ICP inicial (coctelería/heladería/fast food):

- POS (venta rápida) + historial + anulación con motivo
- Inventario + recetas/BOM + consumo parcial + merma
- Gastos fijos/variables
- Bancos: import CSV/Excel + clasificación + reglas + conciliación simple
- Dashboard con KPIs y alertas
- IA asistida (sin humo) con auditoría

---

## Stack (MVP)

- Next.js (App Router) + TypeScript
- Tailwind CSS
- PostgreSQL + Prisma (cuando se integre persistencia)
- RBAC + Auditoría (event log) como base

Más detalles: `docs/adr/0001_tech_stack_and_project_structure.md`

---

## Getting Started (Local)

### Requisitos

- Node.js LTS (recomendado)
- npm

### Instalar dependencias

```bash
npm install
```
