# 10 — Arquitectura y No Funcionales (Escalabilidad, Multi-tenant, Offline, Performance)

Este documento define las decisiones y reglas técnicas para construir contadurIA sin que el sistema colapse al crecer.
Si se cambia algo crítico (multi-tenant, auditoría, offline, colas), debe registrarse en un ADR.

---

## 1) Objetivos no funcionales (NFRs)

### 1.1 Seguridad

- Aislamiento multi-tenant real.
- RBAC (roles y permisos) y trazabilidad (auditoría).
- Protección de datos sensibles (NIT, valores, extractos).
- Acceso mínimo necesario por rol.

### 1.2 Confiabilidad

- No pérdida de datos.
- No “borrar”; siempre reversar/anular con evento.
- Backups y recuperación.
- Idempotencia en importaciones y sincronización (offline).

### 1.3 Rendimiento

- Tablas grandes (ventas, movimientos banco, inventario) deben ser consultables con filtros y paginación.
- Reportes deben ser rápidos o ejecutarse en background.
- Importaciones masivas con progreso.

### 1.4 Escalabilidad

- Multi-sucursal.
- Multi-país a futuro (capas de configuración, impuestos por país).
- Capacidad de integrar DIAN y bancos sin reescribir el core.

### 1.5 UX “premium” y operable

- Respuesta rápida o feedback claro (jobs/colas).
- Modo offline parcial y sincronización confiable.
- Estados vacíos, validación y microinteracciones.

---

## 2) Arquitectura recomendada (alto nivel)

### 2.1 Enfoque general

- App web fullstack en un solo repo (monolito modular) para velocidad de desarrollo.
- Backend dentro de Next.js (API Routes / Server Actions) + Prisma/Postgres.
- Jobs/colas para tareas pesadas.

> En v2 se puede separar servicios, pero no es necesario para arrancar.

### 2.2 Componentes principales

1. **UI App** (Next.js)
2. **Core Domain** (módulos: ventas, bancos, inventario, contabilidad…)
3. **DB Postgres** (fuente de verdad)
4. **Event Log** (auditoría + soporte offline/sync)
5. **Job Queue** (importaciones, reportes, OCR, IA batch)
6. **Integraciones** (bancos, DIAN, e-commerce) desacopladas por “adapters”

---

## 3) Multi-tenant (opciones y recomendación)

### 3.1 Opciones

1. **Row-level tenant_id** en todas las tablas + RLS en Postgres (recomendado)
2. Schema por tenant
3. Database por tenant

### 3.2 Recomendación (para SaaS + 1 dev)

**Row-level con RLS**:

- más simple de operar
- escalable
- permite mover a sharding después si crece

### 3.3 Reglas multi-tenant obligatorias

- Cada tabla de negocio debe incluir `tenant_id` (y casi siempre `branch_id`).
- Cada query debe filtrar por `tenant_id`.
- Logs/auditoría deben incluir `tenant_id`.

> La estrategia se formaliza con ADR: `0002_multi_tenant_strategy.md`.

---

## 4) Diseño modular (para escalar sin caos)

### 4.1 Módulos como “bounded contexts” livianos

- ventas
- compras/gastos
- bancos/conciliación
- inventario/recetas
- contabilidad
- impuestos
- IA/reglas
- configuración/usuarios

Cada módulo debe tener:

- rutas/pantallas
- servicios (lógica)
- validaciones (schemas)
- repositorio de datos (queries)
- eventos de auditoría

### 4.2 Regla de dependencias

- Módulos operativos pueden depender del core contable para posteo.
- El core contable NO debe depender de UI.
- IA/reglas es transversal: sugiere, pero no “posee” la verdad.

---

## 5) Event Log (auditoría) como columna vertebral

### 5.1 Qué debe resolver

- Auditoría legal (quién cambió qué).
- Reversos (no borrar).
- Soporte de sincronización offline (eventos en cola).
- Debugging de errores (trazabilidad).

### 5.2 Regla

- Todo cambio numérico o de configuración genera un evento inmutable.
- Reportes deben poder rastrear su origen.

---

## 6) Jobs / Colas (background processing)

### 6.1 Qué va a jobs

- Importación de extractos bancarios (CSV/Excel)
- Reconstrucción de reportes (caches)
- OCR de soportes (v1)
- IA batch (clasificación masiva)
- Emails masivos (facturas)
- Generación de PDFs pesados

### 6.2 Requisitos de jobs

- Idempotencia: ejecutar dos veces no debe duplicar.
- Progreso: porcentaje y estado.
- Reintentos con backoff.
- Registro de fallos + notificación.

---

## 7) Offline parcial (diseño realista)

### 7.1 Alcance offline (MVP)

Offline debe permitir:

- crear ventas (modo POS simple)
- registrar gastos
- registrar consumo/ajuste inventario (si aplica)
- guardar soportes localmente (pendiente sync)

### 7.2 Estrategia recomendada

- PWA + cache de assets.
- Cola local en IndexedDB:
  - lista de eventos pendientes
  - con identificadores idempotentes
- Sync:
  - al volver online, enviar eventos al servidor
  - resolver conflictos con políticas claras

### 7.3 Políticas de conflicto

- “Last write wins” NO es suficiente para contabilidad.
- Conflictos deben resolverse con:
  - revisión manual si afectan dinero
  - o estrategias específicas:
    - ventas: permitir emitir offline con numeración “offline” y reasignar luego
    - inventario: reconciliar con conteo físico / ajuste

> La estrategia exacta se define en ADR cuando se implemente offline avanzado.

---

## 8) Performance y escalabilidad (tablas grandes)

### 8.1 Reglas de UI

- Paginación obligatoria en:
  - ventas
  - movimientos banco
  - kardex
  - asientos contables
- Filtros por fechas y búsqueda siempre disponibles.

### 8.2 Reglas de DB

- Índices mínimos:
  - tenant_id + created_at
  - tenant_id + branch_id + created_at
  - tenant_id + status
  - tenant_id + tercero_id (para cartera)
- Evitar joins gigantes sin filtros.

### 8.3 Caches y snapshots (v1)

- Reportes pesados pueden tener:
  - snapshots por periodo
  - recalculados por job
- Mantener trazabilidad:
  - snapshot debe guardar “generado en” + “fuente” + “hash datos”

---

## 9) Integraciones (arquitectura desacoplada)

### 9.1 Principio

Integraciones deben ser “adapters”, no lógica dentro del core.

### 9.2 Tipos de integraciones

- Bancos (import/conciliación; conexión directa futura)
- DIAN (futuro)
- Pasarelas de pago (futuro)
- E-commerce (futuro)
- WhatsApp/email (notificaciones)

### 9.3 Reglas

- Integración nunca escribe directamente números sin evento auditable.
- Integración debe producir:
  - eventos + documentos que el usuario confirma (asistido) o reglas aprobadas (auto).

---

## 10) Observabilidad (operación del sistema)

### 10.1 Logs

- Logs técnicos (errores)
- Logs de auditoría (event log)
- No mezclar: auditoría es para negocio; logs técnicos para debug.

### 10.2 Métricas

- tiempos de respuesta (p95)
- jobs en cola / fallidos
- tasa de errores
- uso por módulo

### 10.3 Alertas (v1)

- errores críticos
- fallos de importación
- fallos en envío de email
- problemas de sync offline

---

## 11) Seguridad de datos (mínimos)

- cifrado en tránsito (HTTPS)
- cifrado de datos sensibles en reposo si aplica
- backups regulares
- rotación de secretos
- mínimo acceso por rol
- protección contra CSRF/XSS/inyección (framework + validación)

---

## 12) Checklist de arquitectura lista

- [ ] ADR multi-tenant definido
- [ ] Event log obligatorio implementado (al menos en diseño)
- [ ] Jobs definidos (lista de tareas background)
- [ ] Estrategia offline MVP definida
- [ ] Principios de integraciones por adapters definidos
- [ ] Performance: reglas de paginación + filtros en tablas críticas
