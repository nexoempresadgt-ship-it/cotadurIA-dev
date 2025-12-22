# 00 — START HERE (Fuente de verdad del proyecto)

Este documento es la puerta de entrada para cualquier persona (incluyéndote a ti mismo en 2 semanas) que necesite entender **cómo se trabaja contadurIA** sin perder contexto.

---

## 1) Qué es contadurIA (en 60 segundos)

**contadurIA** es una app web contable hiperconfigurable (Colombia-first) para emprendedores, pequeños y medianos negocios.  
Tiene dos modos:

- **Modo Simple:** para no contables (operación + reportes claros, sin fricción)
- **Modo Avanzado:** para contables (PUC, asientos, cierres, centros de costo, terceros, auditoría)

**Regla clave:** aunque el usuario esté en Modo Simple, por debajo el sistema registra **doble partida** y conserva trazabilidad.

---

## 2) Objetivo de esta carpeta `/docs`

Esta carpeta contiene:

1. **Definición de producto** (qué se construye, para quién y por qué)
2. **Decisiones de arquitectura** (cómo se construye y qué invariantes no se rompen)
3. **Sitemap + módulos** (qué pantallas existen)
4. **Modelo de datos** (qué entidades existen)
5. **Backlog y roadmap** (qué hacemos primero y cuándo)
6. **Guías** (UI premium, calidad, seguridad, auditoría)

> Estos documentos son la **fuente de verdad**.  
> Si algo cambia, se actualiza aquí primero y se registra la decisión (ADR si aplica).

---

## 3) Cómo se trabaja el proyecto (reglas operativas)

### 3.1 Regla de “1 fuente de verdad”

- Lo que esté en `/docs` manda.
- Las conversaciones sirven para producir cambios en `/docs` o especificaciones listas para implementar.
- Las “ideas” no implementables deben convertirse en:
  - una historia de usuario
  - un criterio de aceptación
  - una decisión (ADR) o
  - un ítem de backlog

### 3.2 Regla de “no hardcode por industria”

Las diferencias entre industrias se resuelven con:

- **Plantillas de onboarding**
- **Motor de reglas**
- **Configuración por módulos**

No se permite “si industria == restaurante entonces…” enterrado en el código sin pasar por reglas/config.

### 3.3 Regla de “no borrar”

- No se borra información operativa o contable.
- Se usa:
  - `status` (anulado/revertido/activo)
  - **asiento reverso** cuando aplique
  - **evento auditable** siempre

### 3.4 Regla de “todo cambio relevante es auditable”

Cualquier acción que afecte números o configuración debe generar un evento:

- crear/editar/anular ventas, compras, gastos
- movimientos inventario (consumos BOM, ajustes)
- conciliación bancaria
- cambios de mapeo (simple ↔ PUC)
- cierres y reaperturas
- cambios de permisos/roles
- cambios de configuración fiscal

---

## 4) Glosario mínimo (terminología estandarizada)

### 4.1 Negocio, Sucursal, Tenant

- **Negocio**: entidad comercial (marca/razón social).
- **Sucursal**: sede u operación separada.
- **Tenant**: el “contenedor” aislado de datos.
  > En ADR se define si tenant = negocio o tenant = sucursal.

### 4.2 Documento operativo

Entidad que nace de la operación:

- venta / compra / gasto / pago / ingreso caja / transferencia / producción

### 4.3 Evento (Event Log)

Registro inmutable y auditable de una acción. Debe incluir:

- actor (usuario/sistema/IA)
- tenant/sucursal
- tipo de evento
- entidad afectada (ej: venta #123)
- payload mínimo (diff o snapshot)
- fecha real (created_at)
- fecha contable (si aplica)

### 4.4 Asiento (doble partida)

Movimiento contable con líneas:

- cuenta
- débito/crédito
- tercero
- centro de costo
- fecha contable
- origen (documento operativo / manual)

### 4.5 Modo Simple vs Modo Avanzado

- Simple: UX amigable, categorías y reportes simplificados
- Avanzado: PUC, cierres, controles, contabilidad profunda
  > Ambos registran datos compatibles (capa de mapeo).

---

## 5) Cómo se divide el trabajo (para múltiples chats sin choque)

### 5.1 Tipos de entregables válidos (lo que produce cada chat)

Un chat solo puede “terminar” entregando al menos uno:

- Documento `/docs/...` actualizado
- ADR en `/docs/adr/...`
- Backlog item en `/docs/12...`
- Checklist de QA / release
- Decisión de diseño UI (con reglas)

### 5.2 Regla de coordinación

- Arquitectura y modelo de datos se decide con ADR.
- Los módulos (ventas, inventario, etc.) deben referenciar:
  - entidades del modelo de datos
  - eventos auditable
  - permisos RBAC
- Si algo no cabe, se abre un ADR.

---

## 6) Calidad mínima (Definition of Done global)

Una funcionalidad se considera “hecha” cuando:

1. Tiene UX completa (incluye empty states y errores)
2. Tiene validación de datos
3. Respeta RBAC
4. Genera evento auditable
5. No rompe Modo Simple/Avanzado
6. Tiene criterios de aceptación cumplidos
7. Actualiza docs si cambió reglas o modelo

---

## 7) Checklist de inicio para arrancar desarrollo (sin código)

- [ ] `/docs` creado con los documentos base
- [ ] ADR template listo
- [ ] Definido: tenant = negocio o tenant = sucursal (ADR)
- [ ] Definido: roles base y permisos mínimos (doc 11)
- [ ] Definido: entidades núcleo (doc 05)
- [ ] Definido: sitemap maestro (doc 03)
- [ ] Definido: roadmap MVP → v1 → v2 (doc 12)

---

## 8) Enlaces internos recomendados

- Ver `01_VISION_Y_ESTRATEGIA.md` para visión.
- Ver `03_SITEMAP_MAESTRO.md` para navegación.
- Ver `05_DOMINIO_Y_MODELO_DE_DATOS.md` para entidades.
- Ver `10_ARQUITECTURA_Y_NO_FUNCIONALES.md` para invariantes técnicos.
- Ver `docs/adr/` para decisiones formales.
