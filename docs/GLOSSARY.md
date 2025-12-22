# Glossary — ContadurIA (términos y definiciones)

Este glosario mantiene un vocabulario consistente entre:

- docs,
- código,
- UI,
- y comunicación con clientes.

---

## A

### Auditoría (Audit / Event Log)

Registro inmutable de acciones relevantes (quién, qué, cuándo, antes/después, motivo).
Base para trazabilidad, reversos y cumplimiento.

---

## B

### BOM (Bill of Materials) / Receta

Lista de insumos y cantidades para producir/vender un producto preparado.
Ej: “Cuba libre” consume 60ml ron + 200ml coca.

### Branch (Sucursal)

Unidad operativa dentro de un Tenant (negocio). Puede tener caja, bodega, consecutivos, usuarios.
Se usa para segmentar numeración, inventario y reportes.

---

## C

### Caja (Cashbox)

Lugar donde se registran movimientos de efectivo en una sucursal.
Puede abrir/cerrar turnos.

### CxC / Cartera (Cuentas por cobrar)

Dinero que clientes deben al negocio.

### CxP (Cuentas por pagar)

Dinero que el negocio debe a proveedores/bancos.

### Conciliación bancaria

Proceso de “emparejar” movimientos bancarios con documentos (ventas, pagos, gastos) para asegurar consistencia.

### Costeo promedio ponderado

Método de costeo donde el costo unitario se calcula como promedio de compras en inventario.
Recomendado para MVP por simplicidad operativa.

---

## D

### DIAN-ready

Diseño de datos y flujos preparados para DIAN (numeración, tax breakdown, trazabilidad),
aunque aún no exista integración/transmisión electrónica.

### Doble partida

Principio contable: cada transacción afecta al menos dos cuentas, débitos y créditos equivalentes.

### Documento (contable/operativo)

Entidad que representa una operación: venta, compra, gasto, ajuste inventario, etc.
Debe ser auditable y reversible.

---

## E

### Event (Evento)

Registro de una acción en el sistema. Puede ser:

- evento de negocio (audit event)
- evento de sincronización offline
- evento IA (sugerencia aceptada/rechazada)

---

## F

### Flujo de caja

Control del efectivo disponible: entradas/salidas en caja y bancos, proyectado con recurrencias y vencimientos.

---

## I

### IA asistida

IA sugiere y el usuario confirma. Se registra auditoría y se puede deshacer.

### Idempotencia

Propiedad de una operación que, si se ejecuta 2 veces con la misma llave, produce el mismo resultado sin duplicar.

### Inventario (Kardex)

Registro de entradas/salidas/ajustes de productos e insumos, con trazabilidad al origen.

---

## M

### Merma

Pérdida natural o desperdicio (ej: evaporación, derrames, cocina).
Puede parametrizarse en recetas o registrarse como ajuste.

### Modo Simple

Vista y experiencia para no contables:

- categorías amigables
- automatización guiada
- reportes explicados
  Internamente usa doble partida.

### Modo Avanzado

Vista para contables:

- PUC
- terceros
- centros de costo
- documentos contables
- cierres/periodos (v1)

---

## O

### Offline (modo offline parcial)

Capacidad de operar sin internet para acciones críticas (ej: ventas POS).
Se basa en cola de eventos y sincronización idempotente.

---

## P

### POS (Point of Sale)

Pantalla de venta rápida para caja.
Optimizada para velocidad (hora pico).

### PUC (Plan Único de Cuentas)

Catálogo estándar de cuentas contables en Colombia (modo avanzado).

---

## R

### RBAC (Role-Based Access Control)

Control de acceso basado en roles y permisos.
Ej: vendedor puede vender, no ver contabilidad.

### Regla (motor de reglas)

Configuración reusable tipo:
“Si el concepto bancario contiene ‘NEQUI’ → categoría ‘Ventas’”
Debe ser auditable.

---

## S

### Serie (numeración)

Prefijo o código que identifica una secuencia de documentos.
Ej: POS-00001234.

### Source (origen de un movimiento)

Referencia de trazabilidad:

- source_type (sale, expense, adjustment)
- source_id (id del documento)
  Permite reconstruir reportes y auditoría.

---

## T

### Tenant (Negocio)

Entidad principal del SaaS. Representa una empresa/negocio cliente.
Un tenant puede tener varias sucursales.

---

## U

### Unidad base (ml/g/unidad)

Unidad estándar para inventario y recetas.
Ej: ron en ml, harina en g, vasos en unidad.
