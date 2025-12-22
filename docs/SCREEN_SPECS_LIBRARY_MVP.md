# Screen Specs Library (MVP) — ContadurIA

Este documento contiene las primeras pantallas críticas del MVP ya especificadas,
para que cuando empieces a programar no improvises flujos.

> Regla: no se construye una pantalla “grande” sin spec.
> Cada spec incluye permisos, validaciones, auditoría y edge cases.

---

## 0) Template (usar si agregas nuevas screens)

- Ruta:
- Nombre:
- Objetivo:
- Roles que acceden:
- Modo (Simple/Avanzado):
- Componentes UI:
- Datos:
- Acciones:
- Validaciones:
- Automatización (manual/asistida/auto):
- Eventos auditable:
- Edge cases (offline/anulación/duplicados):
- Criterios de aceptación:

---

## 1) Onboarding — Selección de plantilla y modo

- Ruta: `/onboarding`
- Nombre: Onboarding inicial
- Objetivo: configurar tenant (empresa) con defaults por industria y modo simple/avanzado
- Roles: Owner/Admin
- Modo: ambos (elige aquí)
- Componentes UI:
  - wizard por pasos
  - cards de industria (coctelería/heladería/fast food)
  - selector de modo (Simple/Avanzado)
  - preview de módulos que se activarán
- Datos:
  - tenant profile (nombre, nit/cc opcional MVP, email)
  - branch inicial (sucursal 1)
  - moneda (COP)
- Acciones:
  - aplicar plantilla
  - crear primer usuario owner
- Validaciones:
  - nombre obligatorio
  - email válido
- Automatización:
  - asistida (sugiere defaults por industria)
- Eventos auditable:
  - tenant.created
  - template.applied
- Edge cases:
  - usuario abandona a mitad → guardar borrador
- Criterios:
  - onboarding termina en < 10 min con setup mínimo.

---

## 2) Configuración inicial — Sucursales, cajas, métodos de pago

- Ruta: `/app/configuracion/empresa`
- Nombre: Configuración base
- Objetivo: dejar listo POS y control de caja
- Roles: Owner/Admin
- Modo: Simple
- Componentes:
  - tabs: Empresa / Sucursales / Cajas / Métodos de pago
- Datos:
  - branches
  - cashboxes
  - payment_methods
- Acciones:
  - crear/editar sucursal
  - abrir caja (si aplica MVP)
- Validaciones:
  - no permitir borrar sucursal con datos (solo desactivar)
- Eventos:
  - branch.created/updated/deactivated
  - cashbox.created/opened/closed
- Edge:
  - multi-sucursal futura: selector en topbar
- Criterios:
  - caja lista y métodos de pago visibles en POS.

---

## 3) POS — Venta rápida (online/offline)

- Ruta: `/app/pos`
- Nombre: POS (venta rápida)
- Objetivo: registrar venta en < 20s
- Roles: Vendedor/Cajero, Admin
- Modo: Simple
- Componentes:
  - catálogo de productos (grid + búsqueda)
  - carrito
  - selector método de pago
  - confirmación “Emitir”
  - modo offline banner
- Datos:
  - productos activos por sucursal
  - precios por canal (POS)
- Acciones:
  - agregar/remover items
  - aplicar descuento simple (opcional MVP)
  - emitir venta
  - enviar comprobante por email (opcional en POS)
- Validaciones:
  - stock (si receta/insumo) → según configuración:
    - asistido: advertir si bajo
    - automático: permitir emitir y marcar conflicto (offline/stock)
- Automatización:
  - consumo inventario por receta (asistido por defecto)
- Eventos:
  - sale.created
  - sale.issued
  - inventory.consumed.from_sale (si aplica)
  - offline.event.created (si offline)
- Edge:
  - offline: asigna offline_number y sincroniza luego (ADR-0004)
- Criterios:
  - venta emite y queda en historial; si offline queda en cola.

---

## 4) Ventas — Historial y anulación

- Ruta: `/app/ventas/historial`
- Nombre: Historial de ventas
- Objetivo: buscar, filtrar, ver y anular ventas
- Roles: Admin/Owner/Contador (lectura), Vendedor (solo propias)
- Modo: Simple/Avanzado
- Componentes:
  - tabla pro (filtros fecha, estado, sucursal, vendedor)
  - drawer “ver detalle”
  - acción “anular”
- Acciones:
  - ver detalle
  - imprimir/descargar PDF
  - reenviar email
  - anular (con motivo)
- Validaciones:
  - si anula:
    - reverso contable (si posteado)
    - reverso inventario (si consumió)
- Eventos:
  - sale.voided
  - journal.entry.reversed (si aplica)
  - inventory.movement.reversed (si aplica)
- Edge:
  - venta offline renumerada → mostrar ambos números
- Criterios:
  - anular nunca borra; deja historial y motivo.

---

## 5) Productos — Catálogo + unidades

- Ruta: `/app/inventario/productos`
- Nombre: Productos e insumos
- Objetivo: crear productos de reventa, insumos y productos preparados
- Roles: Admin/Owner, Bodeguero (limitado)
- Modo: Simple
- Componentes:
  - tabla pro + botón “Crear”
  - form con secciones:
    - básico
    - precios
    - inventario (si aplica)
    - impuestos (config)
- Datos:
  - product.type (insumo/reventa/preparado/servicio)
  - unit_base
- Validaciones:
  - no permitir cambiar type si ya hay movimientos (o forzar wizard)
- Eventos:
  - product.created/updated
- Edge:
  - multi-precio por canal (v1) → dejar campos listos
- Criterios:
  - productos listos para POS y recetas.

---

## 6) Recetas — Crear receta (BOM) versionada

- Ruta: `/app/recetas/recetas/nueva`
- Nombre: Crear receta
- Objetivo: definir ingredientes (ml/g) para un producto preparado
- Roles: Admin/Owner, Bodeguero (si permitido)
- Modo: Simple (con conceptos claros)
- Componentes:
  - selector producto preparado
  - lista de ingredientes (repeatable rows)
  - conversiones unidad
  - preview costo teórico
- Validaciones:
  - ingredientes deben tener unidad compatible
  - qty > 0
- Automatización:
  - calcula costo teórico con costo promedio actual
- Eventos:
  - recipe.created
  - recipe.version.created
- Edge:
  - editar receta usada → crear nueva versión (no editar histórico)
- Criterios:
  - receta activa se usa en consumo por venta.

---

## 7) Inventario — Kardex y movimientos

- Ruta: `/app/inventario/kardex`
- Nombre: Kardex
- Objetivo: ver entradas/salidas por producto y sucursal/bodega
- Roles: Admin/Owner/Bodeguero, Contador (lectura)
- Modo: Simple/Avanzado
- Componentes:
  - filtros (producto, fecha, tipo mov)
  - tabla pro
- Datos:
  - inventory_movements
- Acciones:
  - ver movimiento (detalle + origen)
- Validaciones:
  - no editar movimientos; solo reverso o ajuste
- Eventos:
  - (solo lectura)
- Edge:
  - performance con filtros e índices
- Criterios:
  - cada movimiento tiene source_type/source_id.

---

## 8) Inventario — Ajuste rápido + Merma

- Ruta: `/app/inventario/ajustes/nuevo`
- Nombre: Ajuste / Merma
- Objetivo: registrar pérdidas o correcciones con motivo
- Roles: Admin/Owner/Bodeguero
- Modo: Simple
- Componentes:
  - selector insumo/producto
  - qty + unidad
  - motivo (lista)
  - adjunto opcional
- Acciones:
  - crear ajuste
- Validaciones:
  - qty > 0
  - motivo obligatorio
- Automatización:
  - asientos contables opcionales (según config)
- Eventos:
  - inventory.adjustment.created
  - audit.event.created
- Edge:
  - offline (v1) o solo online en MVP
- Criterios:
  - ajuste queda trazable y auditable.

---

## 9) Gastos — Registrar gasto rápido (fijo/variable)

- Ruta: `/app/gastos/registrar`
- Nombre: Gasto rápido
- Objetivo: registrar gastos sin fricción
- Roles: Admin/Owner
- Modo: Simple
- Componentes:
  - form corto
  - toggle: fijo/variable
  - recurrencia si fijo (mensual)
  - adjuntar soporte
- Validaciones:
  - monto > 0
  - categoría simple obligatoria
- Automatización:
  - fijo → crear recurrencia (si user lo confirma)
- Eventos:
  - expense.created
  - recurring.expense.created (si aplica)
- Edge:
  - offline permitido (ADR-0004)
- Criterios:
  - gasto impacta reportes y caja/bancos.

---

## 10) Bancos — Importar extracto CSV/Excel

- Ruta: `/app/bancos/importar`
- Nombre: Importación bancaria
- Objetivo: subir extracto y mapear columnas
- Roles: Admin/Owner
- Modo: Simple
- Componentes:
  - uploader
  - preview tabla
  - mapeo columnas (fecha, concepto, monto, saldo opcional)
  - guardar plantilla de mapeo (v1 si no MVP)
- Acciones:
  - iniciar importación (job)
- Validaciones:
  - detectar duplicados (hash por fecha+concepto+monto)
- Automatización:
  - job en cola + progreso
- Eventos:
  - bank.import.started/completed/failed
- Edge:
  - archivos grandes → job obligatorio
- Criterios:
  - movimientos quedan listos para clasificar.

---

## 11) Bancos — Clasificación asistida + reglas

- Ruta: `/app/bancos/movimientos`
- Nombre: Movimientos bancarios
- Objetivo: clasificar movimientos con reglas e IA asistida
- Roles: Admin/Owner, Contador (lectura/edición según permiso)
- Modo: Simple/Avanzado
- Componentes:
  - tabla pro
  - panel lateral: sugerencia IA + “por qué”
  - botón “Crear regla”
- Acciones:
  - asignar categoría
  - vincular a documento (venta/gasto)
  - crear regla “si contiene X → categoría Y”
- Validaciones:
  - no permitir clasificar como “transferencia” sin cuenta destino (si aplica)
- Eventos:
  - bank.tx.classified
  - rule.created
  - ai.suggestion.accepted/rejected (si aplica)
- Edge:
  - idempotencia en reclasificación (historial)
- Criterios:
  - correcciones alimentan reglas.

---

## 12) Conciliación — Match simple

- Ruta: `/app/conciliacion/pendiente`
- Nombre: Conciliación
- Objetivo: emparejar movimientos banco con ventas/pagos/gastos
- Roles: Admin/Owner
- Modo: Simple
- Componentes:
  - lista de movimientos pendientes
  - sugerencias de match
  - confirmación de match
- Acciones:
  - confirmar match
  - marcar como “no conciliable” (motivo)
- Validaciones:
  - no permitir match duplicado
- Eventos:
  - reconciliation.match.created
  - reconciliation.match.removed (si se deshace)
- Edge:
  - diferencias de centavos → tolerancia configurable
- Criterios:
  - conciliación reduce pendientes y mejora flujo de caja.

---

## 13) Dashboard — KPIs y alertas (MVP)

- Ruta: `/app/inicio`
- Nombre: Dashboard
- Objetivo: dar control al dueño
- Roles: Owner/Admin, Contador (lectura)
- Modo: Simple
- Componentes:
  - cards KPI
  - lista de tareas/alertas
  - atajos (crear venta/gasto/import banco)
- Datos:
  - ventas día/semana
  - margen estimado
  - stock bajo
  - conciliación pendiente
  - cartera (si aplica)
- Validaciones:
  - no aplica
- Eventos:
  - (lectura)
- Edge:
  - widgets según industria
- Criterios:
  - dueño entiende “qué hacer hoy”.

---
