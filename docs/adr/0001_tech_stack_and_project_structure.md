# ADR-0001 — Tech Stack y estructura del proyecto (MVP ContadurIA)

**Estado:** Aceptado  
**Fecha:** 2025-12-22  
**Decisores:** Owner (Daniel) + Arquitectura contadurIA

---

## 1) Contexto

ContadurIA inicia con:

- 1 dev (velocidad y foco),
- app web primero (desktop web), móvil después,
- necesidad de UI premium,
- multi-tenant desde el inicio,
- módulos con lógica contable/inventario que requieren consistencia, auditoría y escalabilidad.

Se necesita un stack que:

- sea rápido de desarrollar,
- tenga buenas prácticas,
- sea mantenible,
- y escale a v1/v2 sin rehacer todo.

---

## 2) Decisión

### 2.1 Stack seleccionado (MVP)

**Frontend + Backend (fullstack):**

- **Next.js (App Router) + TypeScript**
- **Tailwind CSS**
- (opcional recomendado) **shadcn/ui** para componentes premium consistentes

**Base de datos:**

- **PostgreSQL**

**ORM / Data layer:**

- **Prisma**

**Auth:**

- (MVP) NextAuth/Auth.js o Clerk
- preferencia: empezar con solución estable y rápida (Auth.js o Clerk), evitar auth casera

**Validación:**

- Zod (schemas compartidos)

**Jobs/colas:**

- MVP: arquitectura preparada; implementación inicial con:
  - un job runner simple (cron + tabla jobs) o
  - una cola como BullMQ/Cloud queue en v1
    > MVP no debe depender de colas complejas para arrancar, pero sí dejar el diseño listo.

**Email:**

- MVP: proveedor simple (SendGrid / Resend / SMTP)
- foco: enviar comprobante PDF (DIAN-ready interno)

**Storage soportes:**

- MVP: S3 compatible (o proveedor equivalente)
- guardar adjuntos con metadatos y auditoría

---

## 3) Estructura del repo (convención)

### 3.1 Carpetas base

- `/app` (Next App Router)
- `/public`
- `/docs`
- `/docs/adr`

### 3.2 Convenciones de código (cuando empiece el desarrollo)

_(documento futuro: CODESTYLE.md si se necesita)_

- Separar por dominios/módulos:
  - `src/modules/sales`
  - `src/modules/inventory`
  - `src/modules/banking`
  - `src/modules/accounting`
  - `src/modules/taxes`
  - `src/modules/ai`
  - `src/modules/platform` (auth, tenant, rbac, audit, jobs)
- Dentro de cada módulo:
  - `ui/` (componentes)
  - `services/` (lógica)
  - `repo/` (queries)
  - `schemas/` (zod)
  - `types/`
  - `events/` (tipos de audit events)

> Regla: UI no debe tener lógica contable; la lógica vive en services.

---

## 4) Alternativas consideradas

### A) Backend separado (NestJS/FastAPI) + frontend separado

**Pros**

- separación clara
- escalable para equipos grandes

**Contras**

- más overhead para 1 dev
- más tiempo de integración

### B) Firebase / Supabase full

**Pros**

- velocidad
- auth/storage integrados

**Contras**

- contabilidad/inventario con reglas complejas puede requerir control fino
- multi-tenant + auditoría a gran escala requiere diseño cuidadoso

### C) Django/Laravel

**Pros**

- frameworks maduros

**Contras**

- tú ya vas por Next/TS, y el objetivo es UI premium moderna

---

## 5) Consecuencias

### Ventajas

- desarrollo rápido
- UI premium con Tailwind + componentes
- fullstack en un solo repo para MVP
- TypeScript reduce errores

### Riesgos

- se puede “ensuciar” el repo si no se modulariza
- sin disciplina de dominio, la lógica se mezcla con UI

### Mitigaciones

- modularización por dominio desde el inicio
- ADRs para decisiones críticas
- docs como fuente de verdad
- auditoría/event log como columna vertebral

---

## 6) Plan por fases

### MVP

- Next + TS + Tailwind
- Postgres + Prisma
- Auth rápido
- PDF email interno
- Storage soportes básico
- Preparación de jobs y event log

### v1

- Cola robusta para jobs (import bancos, OCR, IA batch)
- RLS en Postgres (si se adopta)

### v2

- separación de servicios si el producto/escala lo exige
- DIAN + integraciones complejas

---

## 7) Referencias internas

- docs/10_ARQUITECTURA_Y_NO_FUNCIONALES.md
- docs/11_SEGURIDAD_RBAC_AUDITORIA.md
- ADR-0002 multi-tenant
- ADR-0004 offline sync
