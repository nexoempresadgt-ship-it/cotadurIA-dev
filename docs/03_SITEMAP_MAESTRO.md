# 03 — Sitemap Maestro (Navegación completa + segmentación por módulos, roles e industria)

Este documento define **toda la navegación** posible de contadurIA.
La app real no mostrará todo a todos. Se segmentará por:

- industria (plantilla)
- rol (RBAC)
- plan (Emprendedor/Pequeño/Mediano)
- modo (Simple/Avanzado)

---

## 1) Reglas de navegación (para no crear un menú infinito)

### 1.1 Límite de items visibles

- Sidebar: máximo 8–10 items visibles
- El resto entra por:
  - submenús
  - “Más”
  - pantallas internas de cada módulo

### 1.2 Navegación por módulos

La navegación se organiza en módulos:

1. Inicio
2. Ventas
3. Compras & Gastos
4. Bancos
5. Inventario & Recetas
6. Reportes
7. IA & Reglas
8. Contabilidad (solo si Avanzado o rol contador)
9. Impuestos (según plan/rol)
10. Configuración

### 1.3 Segmentación por rol (ejemplo)

- Vendedor/Cajero: Ventas + POS + clientes (limitado)
- Dueño/Admin: casi todo (pero en Simple se oculta contabilidad profunda)
- Contador: Contabilidad + Impuestos + Reportes (acceso a operación solo lectura)

---

## 2) Rutas base (estructura recomendada)

- `/auth/*` autenticación
- `/onboarding/*` plantillas y configuración inicial
- `/app/*` aplicación principal

Dentro de `/app`, se recomienda agrupar así:

- `/app/inicio`
- `/app/ventas/*`
- `/app/compras/*`
- `/app/bancos/*`
- `/app/inventario/*`
- `/app/reportes/*`
- `/app/ia/*`
- `/app/contabilidad/*`
- `/app/impuestos/*`
- `/app/configuracion/*`
- `/app/ayuda/*`

---

## 3) Módulo: Inicio y control

### 3.1 Inicio (Dashboard)

**Pantallas**

- `/app/inicio`
  **Secciones recomendadas**
- KPIs principales (ventas, caja, margen, inventario, cartera)
- Atajos: crear venta / gasto / compra / conciliación
- Panel por rol
- Widgets configurables por industria

### 3.2 Tareas y alertas

- `/app/tareas`
  **Incluye**
- conciliación pendiente
- stock bajo / vencimiento
- facturas por cobrar / por pagar
- pagos de deudas próximos
- checklist de cierre (avanzado)

### 3.3 Búsqueda global

- `/app/buscar`
  **Incluye**
- búsqueda por entidad (cliente, factura, producto, movimiento banco)
- command palette (Ctrl+K) con acciones rápidas

---

## 4) Módulo: Ventas

### 4.1 Ventas

- `/app/ventas`
  - `/nueva`
  - `/historial`
  - `/cobros`
  - `/devoluciones`
  - `/recurrentes`
  - `/canales`

**Funciones**

- Venta rápida (modo simple)
- Venta completa (impuestos/retenciones según config)
- Envío PDF por email (DIAN-ready)
- Venta a crédito + abonos
- Notas/devoluciones con reverso de inventario

### 4.2 Clientes

- `/app/clientes`
  - `/nuevo`
  - `/:id`

**Funciones**

- Perfil + historial + saldo
- Segmentación (frecuentes, morosos, top)
- (v1+) CRM ligero: notas, tareas, recordatorios

### 4.3 Cotizaciones / Pedidos (según industria)

- `/app/cotizaciones`
- `/app/pedidos`

**Funciones**

- Cotización → pedido → venta
- Reserva stock (si aplica)
- Flujo de estados (pendiente / aprobado / entregado)

### 4.4 POS / Caja (por industria)

- `/app/pos`
  **Variaciones**
- Restaurante/bar: mesas, propinas (opcional por fase)
- Retail: lector código barras, variantes
- Servicios: POS oculto por defecto

---

## 5) Módulo: Compras & Gastos

### 5.1 Compras

- `/app/compras`
  - `/nueva`
  - `/historial`
  - (v1+) `/ordenes-compra`
  - (v1+) `/recepciones`

**Funciones**

- Compra de inventario
- Compra de gasto (no inventario)
- Adjuntar soporte (OCR futuro)
- Retenciones (modo avanzado/config)

### 5.2 Proveedores

- `/app/proveedores`
  - `/nuevo`
  - `/:id`

### 5.3 Gastos

- `/app/gastos`
  - `/registrar`
  - `/fijos`
  - `/variables`
  - (mediano) `/aprobaciones`

**Clave diferencial**

- Separación de gastos fijos vs variables
- Recurrencias y previsión en flujo de caja

### 5.4 CxP / Pagos

- `/app/cxp`
- `/app/pagos`
  **Funciones**
- vencimientos
- pagos parciales
- comprobantes
- reportes por proveedor y edad de saldos

---

## 6) Módulo: Bancos y tesorería

### 6.1 Bancos

- `/app/bancos`
  - `/cuentas`
  - `/movimientos`
  - `/transferencias`
  - `/importar`

**Funciones**

- Importación CSV/Excel
- Clasificación con reglas + IA asistida
- Etiquetas y asignación a documentos

### 6.2 Conciliación

- `/app/conciliacion`
  - `/pendiente`
  - `/resuelto`
  - `/diferencias`

### 6.3 Caja chica

- `/app/caja-chica`

### 6.4 Flujo de caja

- `/app/flujo-caja`
  - `/hoy`
  - `/proyeccion`
  - (v1+) `/escenarios`

### 6.5 Deudas

- `/app/deudas`
  - `/creditos`
  - `/tarjetas`
  - `/calendario`

---

## 7) Módulo: Inventario & Recetas (BOM)

### 7.1 Inventario

- `/app/inventario`
  - `/productos`
  - `/kardex`
  - `/bodegas`
  - `/transferencias`
  - `/ajustes`
  - `/alertas`

**Funciones**

- stock por sede/bodega
- lotes/seriales (según industria, v1+)
- vencimientos (farmacia/alimentos)
- mermas, conteos físicos

### 7.2 Recetas / BOM

- `/app/recetas`
  - `/recetas`
  - `/:id`
  - (por industria) `/produccion`

**Funciones**

- unidades parciales (ml/g/porción)
- merma y sustitutos
- consumo automático por venta (si regla)
- costo teórico vs real

### 7.3 Costos y rentabilidad

- `/app/costos`
  - `/costeo`
  - `/rentabilidad`
  - `/desviaciones`

---

## 8) Módulo: Reportes

- `/app/reportes`

  - `/financieros`
  - `/ventas`
  - `/inventario`
  - `/cartera`
  - `/compras-gastos`
  - `/impuestos` (resúmenes)

- `/app/presupuestos` (v1+)
- `/app/bi` (export/API v2)

---

## 9) Módulo: IA & Reglas

- `/app/ia`
  - `/asistente`
  - `/clasificacion`
  - `/reglas`
  - `/anomalias`
  - `/cierre-asistido` (avanzado)

**Regla**

- IA siempre sugiere. Usuario confirma. Todo auditable y reversible.

---

## 10) Módulo: Contabilidad (Avanzado)

- `/app/contabilidad`
  - `/resumen` (simple-friendly)
  - `/documentos` (avanzado)
  - `/asientos`
  - `/puc`
  - `/mapeo-simple`
  - `/terceros`
  - `/centros-costo`
  - `/cierres`
  - `/auditoria`

---

## 11) Módulo: Impuestos (Colombia-first, DIAN-ready)

- `/app/impuestos`
  - `/configuracion`
  - `/resumen`
  - `/retenciones`
  - `/calendario`
  - `/documentos-soporte`
  - `/reportes-fiscales`

**Nota**

- Integración DIAN queda para fase posterior, pero la data y flujos deben ser compatibles.

---

## 12) Módulo: Configuración y plataforma

- `/app/configuracion`
  - `/empresa`
  - `/usuarios` (RBAC)
  - `/planes` (suscripción contadurIA)
  - `/onboarding-plantillas`
  - `/documentos` (numeración, plantillas PDF/email)
  - `/integraciones`
  - `/offline-sync`

---

## 13) Módulo: Ayuda y soporte

- `/app/ayuda`
  - guías por industria
  - guías por rol
  - soporte / bugs / feedback
- `/app/estado` (v1+)

---

## 14) Sidebar recomendado (base)

Items visibles (máximo 10):

1. Inicio
2. Ventas
3. Gastos
4. Bancos
5. Inventario
6. Reportes
7. IA
8. Contabilidad (condicional)
9. Impuestos (condicional)
10. Configuración

> La plantilla por industria reordena estos items y puede ocultar módulos no aplicables.

---

## 15) Variaciones rápidas por industria (qué se prioriza)

### Restaurante / Bar / Coctelería

- Principal: POS, Ventas, Inventario, Recetas, Costos, Bancos
- Secundario: Cotizaciones, Suscripciones

### E-commerce / Marketplaces

- Principal: Ventas, Canales, Pagos, Bancos, Inventario, Devoluciones
- Secundario: Recetas

### Servicios profesionales

- Principal: Ventas (servicios), Gastos, Bancos, CxC, Reportes
- Secundario: Inventario

### Retail / Ferretería

- Principal: POS, Inventario, Compras, Bancos, Reportes
- Secundario: Recetas
