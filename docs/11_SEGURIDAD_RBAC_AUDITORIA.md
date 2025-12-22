# 11 — Seguridad, RBAC y Auditoría (roles, permisos, periodos, cambios sensibles)

Este documento define:

- roles y permisos (RBAC)
- políticas de auditoría
- acciones sensibles y guardrails
- cierres/reaperturas y control de cambios

---

## 1) Principios de seguridad

1. **Least privilege**: cada usuario ve y hace solo lo necesario.
2. **Multi-tenant isolation**: nada cruza entre tenants.
3. **No borrar**: anular/reversar con trazabilidad.
4. **Acciones sensibles requieren confirmación** y/o doble control.
5. **Auditoría obligatoria** en cualquier acción con impacto financiero o de configuración.

---

## 2) Roles base (MVP)

### 2.1 Dueño (Owner)

- acceso total
- puede ver reportes, finanzas, configuración y usuarios
- puede activar modo avanzado (si plan lo permite)

### 2.2 Admin

- gestiona operación y usuarios
- puede configurar módulos y reglas (según política)
- no necesariamente puede reabrir periodos contables (v1)

### 2.3 Contador

- acceso a:
  - contabilidad, impuestos, cierres, reportes financieros
- acceso a operación en modo lectura (por defecto)
- puede editar mapeo simple↔PUC (si se le concede)

### 2.4 Vendedor / Cajero

- ventas / POS
- ver clientes limitados
- no ver contabilidad ni configuración
- no exportar datos completos

### 2.5 Bodeguero

- inventario (entradas/salidas/ajustes)
- no ve bancos/contabilidad (por defecto)

> Se pueden agregar roles por industria (ej: “Supervisor de caja” para restaurantes).

---

## 3) Modelo de permisos (RBAC)

### 3.1 Permisos por módulo y acción

Formato sugerido:

- `module.action`

Ejemplos:

- `sales.view`, `sales.create`, `sales.void`, `sales.export`
- `inventory.view`, `inventory.adjust`, `inventory.transfer`
- `bank.view`, `bank.import`, `bank.reconcile`
- `accounting.view`, `accounting.post`, `accounting.edit`, `accounting.close_period`
- `settings.manage`, `users.manage`
- `rules.manage`, `template.change`

### 3.2 Permisos “especiales” (acciones críticas)

- `accounting.reopen_period`
- `settings.tax_config_edit`
- `settings.mapping_edit`
- `audit.view_full`

Estas acciones deben:

- requerir rol alto (owner/contador)
- pedir confirmación + motivo
- registrar auditoría ampliada

---

## 4) Segmentación por rol (UX)

### 4.1 Qué ve cada rol (resumen)

**Vendedor/Cajero**

- Inicio (limitado)
- Ventas / POS
- Clientes (limitado)
- (opcional) Caja del turno
- NO: contabilidad, impuestos, auditoría, configuración

**Bodeguero**

- Inventario + recetas (si aplica) + ajustes
- NO: bancos, contabilidad, impuestos, configuración

**Contador**

- Contabilidad (completa)
- Impuestos (completa)
- Reportes financieros
- Auditoría (lectura)
- Operación (lectura o edición controlada)

**Owner/Admin**

- todo según plan, con control de acciones sensibles

---

## 5) Auditoría (qué, cómo y por qué)

### 5.1 Acciones obligatorias a auditar

- ventas: crear/emitir/anular/editar
- compras/gastos: crear/postear/anular/editar
- bancos: importación, clasificación, conciliación
- inventario: movimientos, ajustes, consumos por receta, transferencias
- contabilidad: asientos generados, reversos, manuales, cambios de mapeo
- impuestos: cambios de configuración fiscal, tasas, categorías
- configuración: plantillas, reglas, niveles de automatización
- usuarios/permisos: cambios de roles y accesos
- cierres y reaperturas

### 5.2 Qué debe guardar un evento de auditoría

- actor (user/system/ai) + id
- tenant_id + branch_id (si aplica)
- tipo de acción
- entidad afectada
- timestamps
- antes/después (diff) o snapshot relevante
- motivo (si acción sensible)
- metadata (origen: manual/import/ai, ip, user-agent)

### 5.3 Auditoría visible (UX premium)

En documentos críticos (venta/compra):

- “Historial” con:
  - creado por
  - editado por
  - anulado por + motivo
- Link a eventos asociados (modo avanzado)

---

## 6) Cierres y periodos (v1, pero diseñar desde ya)

### 6.1 Objetivo del cierre

- impedir cambios retroactivos que rompan reportes
- permitir consolidación del periodo

### 6.2 Reglas de cierre

- un periodo cerrado:
  - no permite editar documentos con fecha contable dentro del periodo
  - no permite cambiar mapeo simple↔PUC retroactivo
  - no permite recontabilizar sin reapertura formal

### 6.3 Reapertura

- solo `owner` o `contador` con permiso especial
- requiere motivo
- genera evento auditable “alto riesgo”

---

## 7) Acciones sensibles (guardrails)

Lista de acciones sensibles y su política mínima:

1. Cambiar configuración fiscal
   - rol alto + confirmación + auditoría
2. Cambiar mapeo simple↔PUC
   - rol alto + afecta futuro por defecto + auditoría
3. Reabrir periodo
   - permiso especial + motivo + auditoría ampliada
4. Anular ventas/compra emitidas
   - requiere motivo + genera reverso contable e inventario si aplica
5. Ajustes de inventario
   - requiere motivo + evidencia opcional + auditoría
6. Edición de asientos manuales
   - preferir reverso/ajuste; edición directa solo con permiso especial

---

## 8) Checklist RBAC/Auditoría listo para construir

- [ ] Roles base definidos
- [ ] Lista de permisos por módulo definida
- [ ] Acciones sensibles listadas y protegidas
- [ ] Auditoría obligatoria definida (event types)
- [ ] UX de historial de cambios en documentos definida
- [ ] Política de cierres/reaperturas definida (aunque se implemente en v1)
