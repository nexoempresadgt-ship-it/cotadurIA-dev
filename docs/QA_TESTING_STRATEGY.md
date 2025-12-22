# QA + Testing Strategy (MVP) — ContadurIA

Este documento define cómo asegurar calidad sin frenar el avance (1 dev),
priorizando módulos donde un bug cuesta dinero o credibilidad.

---

## 1) Principios de QA (para un SaaS contable)

1. Lo contable/financiero debe ser **determinístico**:
   - mismo input → mismo resultado
2. Nada se borra:
   - siempre anulación/reverso con auditoría
3. Multi-tenant nunca debe fallar:
   - 0 fugas de datos
4. Edge cases del mundo real:
   - duplicados en bancos
   - internet inestable
   - ventas rápidas en hora pico
   - inventario parcial (ml/g)
5. UX premium es parte de QA:
   - loading/empty/error states obligatorios

---

## 2) Niveles de pruebas (realistas para 1 dev)

### 2.1 Smoke tests (siempre)

- Verifica lo crítico en 5–10 min.
- Se corre antes de demo/piloto.

### 2.2 Tests de integración mínimos (recomendado)

- Para:
  - ventas → inventario → contabilidad
  - import banco → clasificación → conciliación
  - anulación → reversos

### 2.3 Tests unitarios selectivos

- Donde el cálculo es complejo:
  - consumo por receta
  - conversiones de unidades
  - costeo promedio
  - balance de asientos

### 2.4 QA manual guiado (checklists)

- Ideal para UI premium y flujos.

---

## 3) “Rutas críticas” (donde NO puede fallar)

### A) Venta rápida (POS)

- Emisión correcta
- Método de pago correcto
- Registro en historial
- (si receta) consumo inventario correcto
- envío PDF email (si aplica)

### B) Recetas y consumo parcial

- 60ml descuenta 60ml, no “1 botella”
- conversiones correctas (L↔ml, kg↔g)
- merma aplicada si está activa

### C) Bancos y duplicados

- Importación no duplica (idempotencia)
- Clasificación se guarda con auditoría
- Regla “si contiene X” funciona

### D) Conciliación

- No permite match doble
- Permite deshacer con auditoría

### E) Anulación / reversos

- Anular venta revierte inventario y asiento (si aplica)
- Siempre pide motivo
- Nunca borra

### F) Multi-tenant / permisos

- Un usuario NO ve datos de otro tenant
- Roles limitan acciones (vendedor no ve contabilidad)

---

## 4) Smoke Test Checklist (MVP)

> Ejecutar en dos cuentas distintas (Tenant A y Tenant B) para verificar aislamiento.

### 4.1 Acceso y navegación

- [ ] login funciona
- [ ] sidebar muestra módulos correctos por rol
- [ ] selector sucursal funciona (si aplica)

### 4.2 Ventas

- [ ] crear venta rápida
- [ ] emitir y aparece en historial
- [ ] anular con motivo
- [ ] reverso visible

### 4.3 Inventario + recetas

- [ ] crear insumo con unidad ml/g
- [ ] crear receta con insumos
- [ ] vender producto preparado
- [ ] verificar descuento de inventario exacto
- [ ] registrar merma

### 4.4 Gastos

- [ ] registrar gasto variable
- [ ] registrar gasto fijo recurrente (mensual)
- [ ] aparece en reportes simples

### 4.5 Bancos

- [ ] importar CSV pequeño (10 líneas)
- [ ] detectar duplicado al reimportar
- [ ] clasificar 3 movimientos
- [ ] crear regla desde corrección

### 4.6 Conciliación

- [ ] conciliar 1 movimiento con 1 venta/gasto
- [ ] deshacer conciliación

### 4.7 Multi-tenant

- [ ] en Tenant B no aparecen datos del Tenant A
- [ ] endpoints y tablas filtran por tenant

---

## 5) Casos de prueba de “cálculo” (unitarios recomendados)

### 5.1 Receta + conversiones

- Receta: 60ml ron + 200ml coca
- Venta: 3 unidades
- Esperado:
  - ron: 180ml
  - coca: 600ml

### 5.2 Costeo promedio (promedio ponderado)

- Compra 1: 1L ron @ 50,000
- Compra 2: 1L ron @ 70,000
- Costo promedio esperado: 60,000/L
- Venta consume 60ml:
  - costo esperado: 3,600

### 5.3 Merma

- Merma 5% en receta:
  - 60ml → 63ml consumo
- Validar redondeo (política de redondeo definida)

### 5.4 Balance contable

- Venta efectivo:
  - débito caja = crédito ingresos
- Al anular:
  - asiento reverso exacto

---

## 6) Estrategia de datos de prueba (seed)

Crear dataset seed por industria (plantillas):

- coctelería:
  - 10 insumos (ron, vodka, coca, hielo…)
  - 8 recetas (cócteles)
  - 15 productos reventa
  - 1 caja y 1 cuenta banco
  - 3 métodos de pago
  - 5 gastos fijos comunes
- retail:
  - 20 productos, sin recetas
- servicios:
  - 10 servicios, recurrentes

Objetivo:

- demo rápida y QA repetible.

---

## 7) Bug policy (clasificación y tiempos)

### Severidad S1 (bloqueante)

- fuga multi-tenant
- cálculos errados de dinero/inventario
- ventas duplicadas por import/sync
- anulación borra o deja inconsistente

Acción:

- detener release/piloto hasta corregir.

### Severidad S2 (alta)

- conciliación incorrecta
- reportes inconsistentes
- permisos fallan (vendedor ve finanzas)

### Severidad S3 (media)

- UI glitch, performance, estados vacíos

---

## 8) Checklist “Release Candidate” (antes de piloto)

- [ ] smoke tests pasan
- [ ] rutas críticas ok
- [ ] logs/auditoría visibles
- [ ] backup básico probado
- [ ] import banco idempotente probado
- [ ] anulación/reversos probados
- [ ] performance aceptable con 1,000 ventas y 5,000 movimientos banco (mínimo)
