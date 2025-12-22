# UI Premium Style Guide (contadurIA)

Este documento define cómo se ve y se siente contadurIA.
La UI es parte del producto: si se ve “barato”, se percibe “no confiable”.

---

## 1) Principios visuales (para verse premium)

### 1.1 Consistencia absoluta

- mismos paddings, radios, sombras, tamaños de fuente
- mismas jerarquías y patrones de interacción

### 1.2 Espacio y respiración

- el “premium” se siente por el espacio
- evitar pantallas densas; preferir secciones y cards

### 1.3 Tipografía y jerarquía

- Titular claro
- Subtítulos cortos
- Texto auxiliar “microcopy” útil (no adorno)
- Números alineados y legibles

### 1.4 Estados impecables

- Loading skeletons
- Empty states con CTA
- Error states con “qué hacer”

---

## 2) Layout base (estándar contadurIA)

### 2.1 Estructura

- Sidebar izquierda (módulos)
- Topbar con:
  - búsqueda global
  - selector de sucursal
  - accesos rápidos (crear venta/gasto)
  - perfil usuario

### 2.2 Reglas de navegación

- máximo 8–10 items visibles
- submenús con categorías claras
- breadcrumbs en pantallas profundas

---

## 3) Componentes obligatorios (design system)

### 3.1 Tablas “pro”

Toda tabla en contadurIA debe tener:

- búsqueda
- filtros (fecha, estado, sucursal)
- ordenamiento
- selección múltiple (cuando aplique)
- acciones por fila (dropdown)
- export (CSV/Excel) (v1 si no MVP)

Patrones:

- “Quick filters” arriba (chips)
- “More filters” en panel lateral

### 3.2 Formularios premium

- validación en tiempo real
- campos agrupados por secciones
- campos avanzados colapsables
- CTA principal fijo (sticky) en formularios largos
- autoguardado cuando sea seguro (borradores)

### 3.3 Modales / drawers

- Modales para acciones pequeñas (confirmar/anular)
- Drawers para edición rápida sin salir de tabla

### 3.4 Empty states que venden

Estructura:

- título
- explicación de 1–2 líneas
- CTA principal
- CTA secundaria (importar, ver guía)

Ejemplo:
“Todavía no tienes ventas. Crea tu primera venta o importa desde Excel.”

---

## 4) Microcopy (texto) — “habla humano”

### 4.1 Reglas

- frases cortas
- evitar jerga contable en modo simple
- explicar impacto:
  - “Esto consumirá 60ml de ron por venta”

### 4.2 Ejemplos

- Malo: “Error: invalid payload”
- Bueno: “No pudimos guardar. Revisa los campos marcados e intenta de nuevo.”

---

## 5) UX por rol (diseño de experiencias)

### 5.1 Vendedor/Cajero

- pantallas rápidas (POS)
- mínimo número de clicks
- confirmaciones claras
- no exponer complejidad contable

### 5.2 Dueño/Admin

- dashboard con control y alertas
- reportes simples explicados
- accesos a configuración guiada

### 5.3 Contador

- tablas densas pero organizadas
- filtros potentes
- trazabilidad (de reporte → asiento → documento)

---

## 6) Patrones de interacción críticos

### 6.1 Anulación / reverso

Siempre pedir:

- motivo
- mostrar impacto:
  - “Se revertirá inventario”
  - “Se creará asiento reverso”
- confirmar

### 6.2 Cierre mensual (v1)

Wizard con:

- checklist
- inconsistencias detectadas
- confirmación
- bloqueo

### 6.3 Importación bancaria

Flujo premium:

1. Subir archivo
2. Previsualizar columnas
3. Mapear columnas (guardar plantilla)
4. Procesar (job) + progreso
5. Resultados:
   - clasificados
   - pendientes
   - errores con explicación
6. CTA: “Crear regla” desde correcciones

---

## 7) Accesibilidad y usabilidad (mínimos)

- contraste suficiente
- focus states visibles
- teclado en tablas y formularios
- tamaños clickables adecuados
- mensajes de error accesibles

---

## 8) QA visual (checklist antes de “done”)

- [ ] Espaciados consistentes
- [ ] Tipografía jerárquica
- [ ] Estados: loading/empty/error
- [ ] Tablas: filtros y acciones claras
- [ ] Formularios: validación y microcopy
- [ ] Confirmaciones en acciones sensibles
- [ ] Responsive aceptable (aunque sea web-first)

---

## 9) Referencias de estilo (inspiración)

- Linear (simplicidad + velocidad)
- Stripe Dashboard (claridad + jerarquía)
- Shopify Admin (tablas y flujos robustos)
- Notion (empty states útiles)
