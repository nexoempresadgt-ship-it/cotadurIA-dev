# 01 — Visión y estrategia (Producto y posicionamiento)

Este documento define **por qué existe contadurIA**, qué problema resuelve, a quién sirve primero y cómo va a ganar contra competidores.

---

## 1) Visión

Construir la plataforma contable-operativa más **configurable** y **usable** para emprendedores → medianas empresas en Colombia, que permita operar el negocio (ventas, caja, inventario, bancos) y llevar contabilidad real (doble partida) sin requerir un contador desde el día 1, pero habilitando profundidad contable cuando el negocio crece.

---

## 2) Norte del producto (north star)

**“Que un negocio pueda operar y entender su realidad financiera en minutos al día, con datos confiables.”**

Indicadores asociados:

- % de transacciones clasificadas automáticamente
- tiempo promedio para registrar venta/gasto
- porcentaje de conciliación resuelta sin fricción
- precisión de costos (teórico vs real en recetas)
- adopción de reportes (dueño mira su panel semanal)

---

## 3) Propuesta de valor

### 3.1 En una frase

**Operación + contabilidad + inventario avanzado por recetas + caja/bancos + IA + configuración por industria**, en una sola app.

### 3.2 Qué promete contadurIA

- Reducir tiempo manual (conciliación, clasificación, reportes)
- Reducir errores (validaciones, auditoría, reversos)
- Aumentar control (inventario por recetas, costos reales, cartera, deudas)
- Dar claridad (modo simple comprensible + modo avanzado profesional)

---

## 4) Segmentos y enfoque inicial (Colombia-first)

### 4.1 Segmentos (por etapa)

1. **Emprendedor** (1–2 personas, sin contador fijo)
   - Dolor: no sabe dónde se va la plata, mezcla cuentas, no controla gastos.
2. **Pequeño negocio** (3–15 personas)
   - Dolor: caja e inventario se descuadran, proveedores y cartera se vuelven caos.
3. **Mediano negocio** (15–80 personas / múltiples sedes)
   - Dolor: requiere controles, auditoría, cierres, centros de costo, aprobaciones.

### 4.2 Cliente inicial (ICP)

Negocio tipo coctelería/heladería/granizados + comidas rápidas.
Razón:

- Alto valor inmediato con recetas/BOM (ml, g, porciones)
- Alto dolor en inventario, mermas y costos
- POS/caja y control de ventas es crítico

---

## 5) Problemas que atacamos primero (prioridad estratégica)

1. **Caja y bancos no cuadran** (falta conciliación + clasificación)
2. **Inventario se pierde** (mermas, consumo sin control, recetas no registradas)
3. **Costos y margen no se entienden** (precio sin base, costos variables ocultos)
4. **Gastos fijos/variables no se controlan** (sin previsión ni recurrencias)
5. **Cartera y deudas** (pagos y cobros desordenados)
6. **Contabilidad “como obligación”, no como herramienta** (modo simple + mapeo real)

---

## 6) Diferenciadores reales vs competencia (Alegra / Siigo)

### 6.1 Diferenciadores “duros”

- **Recetas/BOM con consumo parcial** y costo real vs teórico
- **Motor de reglas** para automatizar comportamientos sin hardcode
- **Modo Simple** que igual guarda contabilidad válida (doble partida)
- **IA operativa** (clasificación bancaria, conciliación, anomalías, cierre asistido)

### 6.2 Diferenciadores “de experiencia”

- Onboarding por industria con configuración por defecto
- UI premium e intuitiva (tablas pro, formularios rápidos, atajos)
- Explicación en lenguaje humano de reportes y errores

---

## 7) Modelo de negocio (pricing base)

Suscripción mensual por **negocio o sucursal** (según ADR), con planes:

### 7.1 Plan Emprendedor

- 1 sede
- usuarios limitados
- módulos: ventas, gastos, bancos (import), reportes básicos
- modo simple por defecto
- IA: clasificación sugerida + reglas simples

### 7.2 Plan Pequeño

- 1–2 sedes
- más usuarios
- inventario + recetas/BOM
- conciliación mejorada
- reportes completos
- IA: reglas avanzadas + anomalías básicas

### 7.3 Plan Mediano

- múltiples sedes
- aprobaciones (compras/gastos)
- contabilidad avanzada (PUC, cierres, centros costo)
- auditoría ampliada + export
- IA: cierre asistido + detección avanzada

> El pricing se define con investigación de mercado más adelante, pero la estructura por límites debe existir desde el diseño.

---

## 8) Roadmap estratégico (macro)

### MVP (ganar el cliente inicial)

- Operación diaria impecable:
  - ventas / caja / gastos / bancos import / inventario + recetas
- Reportes mínimos para decisión:
  - ventas, caja, margen por receta, inventario y mermas
- IA mínima útil:
  - clasificación bancaria asistida + reglas

### v1 (escalabilidad y control)

- Contabilidad avanzada activable (PUC + asientos + cierres)
- Conciliación sólida
- Roles y auditoría completa
- Aprobaciones (plan mediano)

### v2 (ecosistema)

- Integraciones: e-commerce, bancos directos
- Preparación DIAN avanzada + conexión real
- App móvil (consumo y operaciones rápidas)
- Multi-país: capa de configuración fiscal por país

---

## 9) Métricas y señales de éxito

### Éxito del MVP

- Negocio registra ventas/gastos diariamente
- Inventario por recetas reduce pérdidas visibles
- Dueño entiende su margen y caja semanalmente
- Usuario no experto puede operar sin abandonar

### Indicadores prácticos

- Tiempo promedio para registrar venta: < 20s (objetivo)
- % movimientos bancarios clasificados: > 70% con reglas + IA
- % productos con receta completa: > 60% en coctelería
- % cierres de caja sin diferencias críticas: tendencia a cero

---

## 10) Riesgos principales y mitigación

1. **Complejidad por hiperconfiguración**
   - Mitigación: plantillas por industria + “modo recomendado” + guardrails.
2. **Errores contables por automatización**
   - Mitigación: auditoría, reversos, validación, IA siempre confirmable.
3. **Exceso de scope**
   - Mitigación: MVP con foco (coctelería) + backlog priorizado.
4. **Percepción de “no confiable”**
   - Mitigación: UI clara, reportes consistentes, trazabilidad visible.

---

## 11) Principios de decisión (para evitar debates eternos)

Cuando haya dudas entre dos opciones, se prioriza:

1. Confiabilidad y trazabilidad > features
2. UX simple > control avanzado (siempre que no rompa contabilidad real)
3. Modularidad y configuración > hardcode
4. Entregar valor al cliente inicial > “sistema perfecto”
