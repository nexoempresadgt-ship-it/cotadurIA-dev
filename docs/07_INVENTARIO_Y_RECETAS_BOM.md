# 07 — Inventario avanzado + Recetas/BOM (consumo parcial, mermas, costos)

Este documento define el núcleo diferencial de contadurIA para el cliente inicial:
inventario por insumos, recetas (BOM), consumo parcial (ml/g) y costos.

---

## 1) Objetivo

- Controlar inventario real de insumos y productos terminados.
- Permitir productos “preparados” que consumen ingredientes en cantidades parciales.
- Gestionar merma, sustitutos, combos y transformaciones.
- Calcular costo y margen:
  - costo teórico (por receta)
  - costo real (por compras + costeo)
- Integrar con contabilidad (asientos automáticos/asistidos).

---

## 2) Conceptos clave

### 2.1 Tipos de producto

- **Insumo (Inventory item):** ron, hielo, jarabe, vasos.
- **Producto preparado (Prepared item):** cóctel, granizado, combo.
- **Producto reventa:** botella cerrada, snack empaquetado.
- **Servicio:** entrega, propina, etc. (sin inventario)

### 2.2 Unidades y conversiones

Debe existir un sistema de unidades:

- unidad (u)
- mililitro (ml)
- litro (L)
- gramo (g)
- kilogramo (kg)
- porción (por)
- paquete (paq)

Reglas:

- Un producto tiene una **unidad base**.
- Se permiten conversiones:
  - 1 L = 1000 ml
  - 1 kg = 1000 g
- Para ciertos productos se permite “densidad” (v2) si se requiere.

---

## 3) Modelo de Receta (BOM)

### 3.1 Estructura mínima de receta

- recipe:
  - product_prepared_id
  - version
  - yield_qty (rendimiento: cuántas porciones produce)
  - yield_unit (porciones/unidades)
  - active (true/false)
- recipe_lines:
  - ingredient_product_id
  - qty
  - unit
  - waste_percent (merma)
  - is_optional (opcional, v1)
  - substitutes (lista, v1)

### 3.2 Versionado

- Cambios en receta crean nueva versión.
- No se edita una receta “histórica” que ya se usó en ventas sin versionar.
- La venta guarda `recipe_version_used`.

---

## 4) Flujo principal: venta de producto preparado (consumo automático)

### 4.1 Evento

Cuando se emite una venta que contiene producto preparado:

1. Identificar receta activa (y versión)
2. Calcular consumo:
   - por línea: cantidad vendida × qty receta
   - aplicar merma si está habilitada
3. Crear InventoryMovements:
   - tipo: `sale_out` o `production_consume`
   - por cada ingrediente
4. Registrar evento auditable:
   - sale_issued → inventory_consumed
5. Generar asiento contable (si está configurado):
   - Débito: costo de ventas
   - Crédito: inventario

### 4.2 Nivel de automatización (configurable)

- Manual: sugiere consumo, usuario confirma
- Asistido: ejecuta y permite revisión
- Automático: ejecuta sin pasos extra

---

## 5) Merma (waste) y su control

### 5.1 Tipos de merma

- Merma por receta (porcentaje constante)
- Merma por evento (derrame, daño, vencimiento)
- Merma por inventario (conteo físico)

### 5.2 Registro de merma

Debe existir una pantalla “Registro rápido”:

- seleccionar insumo
- cantidad perdida
- motivo (lista)
- evidencia (foto opcional)

### 5.3 Contabilidad de merma

Políticas (configurable por empresa):

- tratar merma como gasto separado
- o incluirla en costo de ventas

---

## 6) Sustitutos y variaciones de receta

### 6.1 Sustitutos permitidos (v1)

Ej: “si no hay Ron A, usar Ron B”.
Reglas:

- sustituto debe ser del mismo tipo/unidad compatible
- se registra en el evento de consumo cuál sustituto se usó

### 6.2 Variación por sucursal

En cadenas:

- recetas base, con overrides por sucursal (v2)
- o diferentes listas de precios por sucursal/canal

---

## 7) Combos y “kits”

Ejemplo: combo hamburguesa + bebida.
Opciones:

- Combo como producto preparado con “componentes”
- O combo como venta con descuento agrupado

Regla:

- Si el combo incluye items con receta, debe consumir inventario correctamente.

---

## 8) Transformación / Producción (cuando aplica)

### 8.1 Casos

- Preparar jarabe (insumo A → insumo B)
- Producción en lote (panadería)

### 8.2 Modelo (v1)

- ProductionOrder:
  - producto_salida
  - cantidad_salida
  - ingredientes
  - estado
- ProductionEvent:
  - consume insumos
  - produce salida
  - genera movimientos inventario

---

## 9) Costeo (MVP recomendado)

### 9.1 Método recomendado MVP: Promedio ponderado

Razones:

- es más simple de implementar
- funciona bien para insumos frecuentes
- reduce complejidad

### 9.2 FIFO/PEPS (v1+)

- necesario para industrias reguladas o con lotes/vencimientos
- se agrega después

### 9.3 Costo teórico vs costo real

- Teórico: costo receta (según costos actuales de insumos)
- Real: costo por método de costeo y compras
  Debe existir reporte:
- margen teórico por producto
- margen real estimado
- desviación y alertas

---

## 10) Inventario por bodegas/sucursales

### 10.1 Reglas

- Todo movimiento debe indicar:
  - warehouse_id (bodega)
  - branch_id (sucursal)
- Transferencias:
  - salida de bodega A
  - entrada a bodega B
  - evento auditable único que crea 2 movimientos

---

## 11) Alertas y controles (premium)

Alertas:

- stock mínimo (por insumo)
- quiebres de stock proyectados (v2)
- vencimientos (si aplica)
- variación de costos (subió precio insumo)
- consumo anómalo (IA: “se consumió más ron del esperado”)

Controles:

- conteo cíclico (v1)
- auditoría de ajustes

---

## 12) Casos de negocio (ejemplos)

### 12.1 Cóctel con 60ml de ron

- Venta 1 unidad “Cuba Libre”
- Receta:
  - Ron: 60ml
  - Hielo: 120g
  - Coca: 200ml
- Se consumen esos insumos del inventario.
- Se calcula costo de esa unidad.

### 12.2 Granizado con jarabe

- Jarabe puede ser insumo o producto transformado.
- Si se produce jarabe: producción consume azúcar + agua → produce jarabe.

---

## 13) Checklist para implementación (Inventario + Recetas)

- [ ] Sistema de unidades y conversiones
- [ ] Productos con tipos (insumo/reventa/preparado/servicio)
- [ ] Receta versionada (recipe + recipe_lines)
- [ ] Venta de preparado → consumo inventario
- [ ] Ajustes y merma (pantalla rápida)
- [ ] Costeo promedio (cálculo por insumo)
- [ ] Reporte margen por receta (teórico vs real)
- [ ] Eventos auditable en todos los movimientos
