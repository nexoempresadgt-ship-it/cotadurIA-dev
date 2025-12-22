# 04 — Plantillas por industria (Onboarding + Configuración + Motor de reglas)

Este documento define cómo contadurIA se adapta a distintos tipos de negocio SIN hardcode.
La adaptación se logra con:

- Plantillas de onboarding (defaults)
- Módulos ON/OFF por industria y plan
- Motor de reglas (comportamientos)
- Mapeo Simple ↔ PUC por industria

---

## 1) Objetivo

1. Reducir fricción: un negocio debe quedar listo en minutos con una configuración coherente.
2. Evitar caos: no mostrar módulos irrelevantes al inicio.
3. Permitir crecimiento: activar módulos más adelante sin perder datos.
4. Mantener consistencia: cambios se hacen con “wizard de migración” y auditoría.

---

## 2) Qué es una “plantilla” (Template)

Una plantilla es un conjunto de:

- **Módulos recomendados** (ON/OFF)
- **Defaults operativos** (ej: POS activo, recetas activas, unidades)
- **Defaults de datos** (categorías simples, unidades, impuestos habituales)
- **Reglas sugeridas** (automatizaciones manual/asistido/auto)
- **KPIs sugeridos** para el dashboard
- **Mapeo contable inicial** (categorías → cuentas PUC, si aplica)

> Plantilla = “punto de partida inteligente”, no una restricción.

---

## 3) Motor de reglas (Rule Engine) — concepto

### 3.1 Tipos de reglas

- **Reglas operativas**
  - Ej: “Al vender producto con receta, consumir inventario”
  - Ej: “Al registrar gasto de arriendo, crear recurrencia mensual”
- **Reglas financieras**
  - Ej: “Movimientos bancarios con ‘NEQUI’ → sugerir categoría Ventas”
- **Reglas contables**
  - Ej: “Categoría simple ‘Ventas’ → cuenta PUC 41xx”
- **Reglas de permisos/flujo**
  - Ej: “Compras mayores a X requieren aprobación (plan mediano)”

### 3.2 Niveles de automatización por regla

- Manual (no ejecuta; guía)
- Asistido (sugiere; usuario confirma)
- Automático (ejecuta; registra auditoría; reversible)

### 3.3 Alcance (scope)

Toda regla debe poder aplicarse a:

- Todo el tenant
- Una sucursal específica
- Un usuario/rol
- Un canal de venta
- Un producto/categoría

---

## 4) Componentes de una plantilla (especificación)

Cada plantilla debe incluir:

### 4.1 Módulos ON/OFF

- Inicio/Tareas/Búsqueda
- Ventas
- Clientes
- POS (si aplica)
- Compras
- Gastos fijos/variables
- CxC / CxP
- Bancos + conciliación
- Inventario
- Recetas/BOM (si aplica)
- Costos y rentabilidad
- Contabilidad avanzada (opcional)
- Impuestos (resumen)
- Reportes
- IA & Reglas

### 4.2 Defaults operativos

- Unidades (ml, g, unidad, porción)
- Precio (listas por canal: salón/delivery/ecommerce)
- Métodos de pago (efectivo, tarjeta, transferencia)
- Manejo de propina (si aplica)
- Manejo de lotes/seriales/vencimientos (si aplica)

### 4.3 Defaults contables (si avanzado o con mapeo)

- Categorías simples base
- Mapeo simple→PUC por defecto
- Centros de costo sugeridos (si aplica)

### 4.4 KPIs sugeridos

- Ventas diarias/semanales
- Margen por producto/receta
- Rotación de inventario
- Merma
- Conciliación pendiente
- Cartera vencida

---

## 5) Lista de industrias (mínimo 30) + complejidad

> Complejidad: Baja (B), Media (M), Alta (A)

1. Restaurante / Bar / Coctelería (A)
2. Heladería / Granizados (A)
3. Comidas rápidas (A)
4. Café / Panadería (A)
5. Catering / Eventos (M)
6. Tienda retail general (M)
7. Minimercado / Supermercado pequeño (A)
8. Ferretería (M)
9. Droguería / Farmacia (A: lotes/vencimientos)
10. Licorería (M)
11. Moda / Ropa (M: variantes)
12. Calzado (M: variantes)
13. Papelería (B-M)
14. Librería (B-M)
15. Electrónica (A: seriales)
16. Repuestos / Autopartes (M-A)
17. Taller mecánico (M-A: servicios + repuestos)
18. Belleza / Barbería / Spa (M: servicios + comisiones)
19. Gimnasio (M: suscripciones)
20. Educación / Academia (M: suscripciones)
21. Servicios profesionales (B)
22. Agencia marketing / creativa (M: proyectos)
23. Construcción / Contratista (A: proyectos + centros costo)
24. Inmobiliaria / Arrendamientos (M: recurrentes)
25. Turismo / Operador (M)
26. Hotel / Hostal (A: ocupación futura)
27. Transporte / Logística (M: rutas/peajes)
28. Distribución / Mayorista (A: volumen + listas precios)
29. E-commerce / Tienda online (M-A: canales + comisiones)
30. Vendedor Instagram / WhatsApp (M: pedidos + entregas)
31. Manufactura ligera (A: producción)
32. Servicios técnicos (M: órdenes de servicio)
33. Salud (consultorio) (M-A: agenda + historial futuro)
34. Veterinaria (M-A: productos + servicios)
35. Organización sin ánimo de lucro (B-M: reportes)

---

## 6) Matriz de módulos por industria (v0)

Leyenda:

- ✅ recomendado ON por defecto
- ➕ opcional (se puede activar)
- ❌ normalmente OFF en onboarding

| Industria                  | POS | Ventas | Compras/Gastos | Bancos/Conciliación | Inventario | Recetas/BOM | Costos | CxC/CxP | Contab Avanz | Impuestos | Reportes | IA/Reglas |
| -------------------------- | --: | -----: | -------------: | ------------------: | ---------: | ----------: | -----: | ------: | -----------: | --------: | -------: | --------: |
| Restaurante/Bar/Coctelería |  ✅ |     ✅ |             ✅ |                  ✅ |         ✅ |          ✅ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Heladería/Granizados       |  ✅ |     ✅ |             ✅ |                  ✅ |         ✅ |          ✅ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Comidas rápidas            |  ✅ |     ✅ |             ✅ |                  ✅ |         ✅ |          ✅ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Servicios profesionales    |  ❌ |     ✅ |             ✅ |                  ✅ |         ❌ |          ❌ |     ➕ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Retail general             |  ✅ |     ✅ |             ✅ |                  ✅ |         ✅ |          ❌ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Droguería                  |  ✅ |     ✅ |             ✅ |                  ✅ |         ✅ |          ❌ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| E-commerce                 |  ➕ |     ✅ |             ✅ |                  ✅ |         ✅ |          ❌ |     ✅ |      ✅ |           ➕ |        ✅ |       ✅ |        ✅ |
| Manufactura ligera         |  ➕ |     ✅ |             ✅ |                  ✅ |         ✅ |          ✅ |     ✅ |      ✅ |           ✅ |        ✅ |       ✅ |        ✅ |

> Esta tabla se expande con el resto de industrias en v1 del documento.
> Por ahora sirve para iniciar plantillas base y probar segmentación.

---

## 7) Especificación de plantillas “Top 6” (las primeras para construir)

Estas son las primeras 6 plantillas que se deben diseñar a detalle porque cubren gran parte del mercado:

### 7.1 Plantilla: Restaurante/Bar/Coctelería

**Objetivo**: control de caja + recetas + merma + margen

- Defaults:
  - POS ON
  - Recetas/BOM ON
  - Unidades: ml, g, unidad, porción
  - Propina: ON (configurable)
- Reglas sugeridas:
  - Venta de producto con receta → consumo automático inventario (asistido por defecto)
  - Stock mínimo por insumo crítico
  - Merma diaria (registro rápido)
- Dashboard:
  - Ventas día
  - Margen por cóctel
  - Merma
  - Conciliación pendiente
  - Top productos

### 7.2 Plantilla: Retail (tienda)

- Defaults:
  - POS ON
  - Variantes ON (talla/color si aplica)
- Reglas:
  - Descuento automático por promociones (futuro)
  - Alertas de stock bajo
- Dashboard:
  - Ventas
  - Inventario
  - Rotación
  - Top categorías

### 7.3 Plantilla: Servicios profesionales

- Defaults:
  - POS OFF
  - Inventario OFF
  - Recurrentes ON
- Reglas:
  - Facturación recurrente mensual
  - Recordatorio de cobro
- Dashboard:
  - Ingresos vs gastos
  - Cartera por cobrar
  - Flujo caja 30 días

### 7.4 Plantilla: E-commerce / Marketplaces

- Defaults:
  - Canales ON
  - Devoluciones ON
- Reglas:
  - Importación pedidos por CSV
  - Comisiones por canal (fee)
- Dashboard:
  - Ventas por canal
  - Devoluciones
  - Margen por producto

### 7.5 Plantilla: Droguería

- Defaults:
  - Inventario ON
  - Vencimientos ON
  - Lotes ON (v1+)
- Reglas:
  - alertas de vencimiento a 30/60/90 días
- Dashboard:
  - Vencimientos
  - Rotación
  - Ventas

### 7.6 Plantilla: Distribución/Mayorista

- Defaults:
  - Listas de precios por cliente (v1+)
  - Volúmenes altos
- Reglas:
  - descuentos por volumen
- Dashboard:
  - Ventas por cliente
  - cartera
  - top productos

---

## 8) Wizard de cambio de plantilla (migración controlada)

Al cambiar plantilla:

1. Mostrar módulos que se activan/desactivan
2. Mostrar cambios de defaults (ej: unidades, POS)
3. Mostrar riesgos (ej: activar recetas implicará consumo de inventario)
4. Confirmación
5. Registrar evento auditable:
   - old_template → new_template
   - cambios aplicados

**Regla**: desactivar un módulo no debe borrar datos; solo oculta navegación y evita nuevas operaciones en ese módulo.

---

## 9) Salida de onboarding (resultado esperado)

Al finalizar onboarding con plantilla:

- menú personalizado (sidebar)
- reglas iniciales activas
- categorías simples listas
- al menos un producto/servicio creado
- bancos listos para importación
- primer dashboard funcional

---
