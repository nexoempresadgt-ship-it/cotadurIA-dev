# PROJECT_BRIEF_MASTER — ContadurIA (Colombia-first)

**Versión:** 1.0  
**Fecha:** 2025-12-22  
**Owner:** Daniel  
**Producto:** ContadurIA — app web contable hiperconfigurable con IA útil y auditoría total  
**Enfoque inicial:** Colombia (DIAN-ready, sin integración DIAN en MVP)  
**ICP (cliente inicial):** coctelería/heladería/granizados + comidas rápidas (ventas rápidas + recetas + inventario parcial)

---

## 1) Visión y meta

### Visión

Construir la app contable y operativa más **útil**, **configurable** y **fácil** para emprendedores, pequeños y medianos negocios en Colombia, con una experiencia premium y moderna, capaz de crecer a un ecosistema (CRM, organizador de pautas, etc.).

### Meta principal

Que el negocio **opere todos los días** con contadurIA (ventas, gastos, inventario, bancos) y sienta:

- control real,
- reducción de errores,
- ahorro de tiempo,
- y claridad para tomar decisiones.

### Diferenciadores (contra Alegra/Siigo y similares)

1. **Recetas + consumo parcial (ml/g) + merma** con costeo realista y trazable.
2. **Bancos + clasificación + reglas + IA asistida**, enfocada en ahorrar tiempo sin cometer locuras.
3. **Modo Simple y Modo Avanzado** sobre la misma verdad contable (no dos contabilidades).
4. **Hiperconfigurable sin caos**: plantillas por industria + motor de reglas, evitando hardcode.
5. **UI premium** (estilo Stripe/Linear): rápida, limpia, con microcopy humano y estados impecables.
6. **Auditoría total**: no borrar, solo anular/reversar con trazabilidad.

---

## 2) Principios no negociables (reglas de oro)

### 2.1 Experiencia premium y moderna (obligatorio)

- Diseño minimalista, limpio, con buen “aire”.
- Tablas pro (filtros, búsqueda, acciones por fila, paginación).
- Formularios con secciones claras, validación inmediata y campos avanzados colapsables.
- Estados perfectos: loading skeleton, empty state con CTA, error con guía.
- Experiencia rápida: siempre feedback (sin “pantallas congeladas”).
- Microcopy humano: explica impacto (“Esto descontará 60ml de ron por venta”).

### 2.2 “Funcional primero” (antes que features bonitas)

Si una función no mejora al menos uno:

- operación diaria,
- control de caja/bancos,
- inventario/costos,
- o reportes clave,
  se va a v1/v2.

### 2.3 Nada se borra

- Siempre “anular”, “reversar” o “ajustar”.
- Todo cambio relevante genera AuditEvent.
- Reportes deben rastrear su origen (source_id/source_type).

### 2.4 Simple y Avanzado conviven

- Modo Simple = categorías amigables y automatización guiada.
- Modo Avanzado = PUC + terceros + centros de costo + cierres (v1).
- Ambos operan sobre la misma verdad contable interna (doble partida).

### 2.5 Configuración por industria sin hardcode

- Plantillas por industria definen defaults.
- Motor de reglas define comportamiento.
- El código no “si restaurante entonces …” (evitar).

### 2.6 IA sin humo (asistida por defecto)

- IA sugiere, humano confirma.
- Debe explicar “por qué”.
- Se audita aceptación/rechazo.
- Riesgo legal controlado con guardrails.

---

## 3) Alcance MVP (lo que sí y lo que no)

### 3.1 MVP incluye (must-have)

**Operación diaria:**

- POS venta rápida + historial + anulación con motivo
- Productos/insumos con unidades (ml/g/unidad)
- Recetas (BOM) versionadas
- Consumo inventario por venta (asistido por defecto)
- Merma + ajustes inventario básicos
- Gastos fijos/variables + compras básicas
- Bancos: importación CSV/Excel + clasificación + reglas + conciliación simple
- Dashboard con alertas y KPIs simples

**Plataforma:**

- Multi-tenant (tenant = negocio)
- Sucursales (branch) y selector (si aplica)
- RBAC básico (roles)
- Auditoría mínima obligatoria (event log)
- DIAN-ready en datos (sin transmitir)

### 3.2 MVP NO incluye (para proteger el foco)

- Integración DIAN real
- Integración bancaria automática (open banking)
- Nómina completa
- Reportes contables avanzados (libro mayor completo, etc.) → v1
- Offline total (solo offline parcial) → v1/v2

---

## 4) Segmentos y planes (producto y pricing)

### Planes (suscripción mensual por negocio o sucursal)

- **Emprendedor:** 1 sucursal, pocos usuarios, operación básica.
- **Pequeño negocio:** más usuarios, más volumen, módulos extra.
- **Mediano negocio:** multi-sucursal, roles avanzados, auditoría ampliada, integraciones.

Límites típicos:

- # usuarios/vendedores
- # sucursales
- # transacciones mensuales
- # importaciones bancarias
- # recetas/productos
- integraciones habilitadas

---

## 5) Experiencia del cliente (Customer Experience) — “Premium + Intuitiva + Completa”

### 5.1 Onboarding perfecto (10 minutos)

- Selecciona industria (coctelería/heladería/fast food).
- Selecciona Modo Simple o Avanzado (recomendación: Simple).
- Se crea:
  - catálogo inicial y unidades
  - caja y métodos de pago
  - categorías de gastos
  - defaults de recetas (si el usuario quiere)
- Resultado: termina con una pantalla “Tu negocio está listo para vender”.

### 5.2 Operación diaria sin fricción

- POS diseñado para velocidad.
- Inventario se “siente automático”, pero confiable.
- Bancos se siente como “limpieza asistida”, no carga extra.

### 5.3 Claridad para decisiones (dashboard)

- “Qué hacer hoy”:
  - stock bajo
  - conciliación pendiente
  - gastos fijos próximos
  - mermas altas
- KPIs básicos:
  - ventas hoy/semana
  - margen estimado (teórico)
  - caja/bancos
  - top productos

### 5.4 Confianza total

- Historial y auditoría en documentos críticos.
- Anulación siempre pide motivo.
- Reportes explican su origen (cuando aplique).

---

## 6) Offline parcial (MVP) — experiencia segura

- Banner claro cuando está offline.
- POS puede emitir ventas offline con numeración temporal.
- Al volver online:
  - sincroniza
  - renumera
  - registra auditoría
- Conflictos (stock) se convierten en tareas de resolución, no “errores silenciosos”.

---

## 7) DIAN-ready (sin DIAN en MVP)

- Se guarda tax breakdown por línea y totales.
- Numeración preparada para series y futuro DIAN.
- PDFs y emails internos con estructura compatible para migrar.

---

## 8) Arquitectura (resumen de decisiones clave)

- Next.js + TypeScript + Tailwind (web premium).
- Postgres + Prisma.
- Multi-tenant row-level: tenant_id en todo.
- Auditoría como columna vertebral (AuditEvent).
- Jobs para tareas pesadas (importaciones, PDFs, IA batch en v1).

---

## 9) Pantallas MVP críticas (las que no pueden fallar)

1. Onboarding
2. POS venta rápida
3. Historial ventas + anulación
4. Productos/insumos + unidades
5. Crear receta (BOM)
6. Kardex inventario
7. Merma / ajuste
8. Gasto rápido (fijo/variable)
9. Importar banco (CSV/Excel)
10. Movimientos bancarios + clasificación + reglas
11. Conciliación
12. Dashboard

---

## 10) “Cómo debe trabajar ChatGPT en este proyecto” (instrucciones operativas)

### 10.1 Prioridades de respuesta

1. UX premium y funcionalidad real (cliente final)
2. Consistencia con docs y ADR
3. Seguridad, auditoría, no borrar
4. Configurabilidad por industria sin hardcode
5. IA asistida con trazabilidad

### 10.2 Formato de entregas que debe producir ChatGPT

Cuando se pida diseñar un módulo o pantalla:

- Ruta/pantalla
- Objetivo
- Roles
- Modo (simple/avanzado)
- Campos y validaciones
- Automatización (manual/asistida/auto)
- Eventos auditable
- Edge cases (offline, anulación, duplicados)
- Criterios de aceptación
- Checklist QA rápido

### 10.3 “Stop conditions” (si falta info)

Si una decisión cambia:

- modelo de datos
- multi-tenant
- numeración
- offline sync
- contabilidad
  entonces debe proponerse ADR o actualización de docs antes de avanzar.

---

## 11) Próximos pasos (prioridad inmediata)

1. Subir repo a GitHub (cuando tengas Git instalado)
2. Conectar GitHub a ChatGPT
3. Crear branches por módulo (ventas, inventario, bancos, contabilidad)
4. Confirmar MVP scope final con cliente inicial (productos/recetas reales)
5. Empezar desarrollo por orden MVP (shell → tenant/RBAC/audit → POS → inventario/recetas → bancos)
