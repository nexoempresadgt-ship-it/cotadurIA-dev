# Launch Pilot Plan — Piloto con cliente inicial (coctelería/fast food)

Este documento define cómo lanzar contadurIA en piloto sin morir en soporte,
obteniendo feedback útil para iterar hacia el MVP sólido.

---

## 1) Objetivo del piloto (no es “lanzamiento público”)

1. Validar que el negocio puede operar su día a día:
   - ventas + caja + inventario/recetas + gastos + bancos
2. Validar que el diferencial funciona:
   - consumo parcial (ml/g), merma, margen
3. Validar que la UI es usable en hora pico:
   - ventas rápidas
4. Crear lista real de prioridades para v1
5. Evitar “deuda de soporte”:
   - reglas claras, onboarding guiado, checklist diario

---

## 2) Preparación previa (Semana 0)

### 2.1 Reunión de kick-off (60–90 min)

Recolectar:

- tipos de productos (reventa, preparados)
- recetas (ingredientes por cóctel/granizado)
- unidades reales (ml, g, porción)
- métodos de pago usados
- cómo manejan caja
- frecuencia de compras
- cómo registran gastos hoy

Entregables:

- plantilla coctelería aplicada
- seed de productos e insumos iniciales
- roles creados (owner + vendedor)

### 2.2 Setup de catálogo mínimo

- 10–20 productos de venta rápida
- 8–12 recetas (top sellers)
- 10–15 insumos
- 5 gastos fijos
- 1 cuenta banco + 1 caja

---

## 3) Alcance del piloto (qué sí y qué no)

### Incluye (MVP)

- POS venta rápida
- Historial + anulación
- Recetas + consumo inventario
- Merma
- Gastos fijos/variables
- Importación banco CSV + clasificación + reglas
- Conciliación simple
- Dashboard básico

### Excluye (para protegerte)

- Integración DIAN real
- Integración bancaria automática
- Nómina completa
- Reportes contables avanzados
- Offline total (solo parcial si se implementó)

---

## 4) Métricas del piloto (las que importan)

### 4.1 Uso

- # ventas registradas / día
- % ventas registradas vs ventas reales (si se puede estimar)
- # gastos registrados / semana

### 4.2 Control

- % consumo inventario “coherente” vs conteo físico semanal
- # eventos de merma registrados
- margen estimado por producto (si lo usan)

### 4.3 Bancos

- % movimientos clasificados
- # reglas creadas
- # conciliaciones realizadas

### 4.4 UX

- tiempo promedio para emitir venta
- errores frecuentes (por pantalla)
- NPS cualitativo (pregunta simple): “¿lo usarías mañana?”

---

## 5) Cadencia de feedback (para aprender rápido)

### Diario (5–10 min, WhatsApp)

- “¿Qué te estorbó hoy?”
- “¿Qué fue lo más útil hoy?”

### Semanal (30–45 min)

- revisar métricas
- revisar top 10 bugs / fricciones
- definir 3 mejoras para la siguiente semana

### Registro de feedback

- todo feedback se registra en:
  - `docs/12_ROADMAP...` (como historias)
  - o issues en GitHub (ideal)

---

## 6) Protocolo de soporte (para no quemarte)

### 6.1 Prioridades

- S1: bloquea ventas o rompe inventario/dinero → se atiende primero
- S2: afecta flujo pero hay workaround
- S3: mejoras/estética

### 6.2 Canal único

- Un solo canal para piloto (WhatsApp o email), no varios.

### 6.3 Respuesta estándar

- “Recibido. Necesito: qué pantalla, qué hiciste, qué esperabas, screenshot.”

---

## 7) Plan por semanas (4 semanas recomendado)

### Semana 1 — Operación básica

- POS + catálogo + recetas top
- validar emisión de ventas
- ajustar UI de venta rápida

### Semana 2 — Inventario y merma

- validar consumo parcial real
- registrar mermas
- ajustar conversiones/unidades

### Semana 3 — Bancos y conciliación

- importar extracto
- clasificar con reglas
- conciliar 20–50% movimientos

### Semana 4 — Reportes y cierre informal

- dashboard y reportes simples
- preparar “cierre mensual simple” (sin cierre contable formal)
- definir backlog v1 basado en uso real

---

## 8) Criterios de éxito del piloto

**Éxito si:**

- registran ventas diarias durante 2+ semanas
- consumo inventario se acerca al conteo físico (con merma registrada)
- clasifican +60% de movimientos banco con reglas
- dueño dice “esto me da control y lo usaría”

**No éxito si:**

- abandonan por fricción en POS
- inventario se vuelve inservible por inconsistencia
- bancos se siente “más trabajo que antes”

---

## 9) Post-piloto: decisiones obligatorias

Al final:

- elegir qué entra en MVP final
- lista de bugs S1/S2 cerrada
- definir pricing inicial (planes por sucursal/usuarios)
- decidir si avanzar a 2° cliente o fortalecer v1
