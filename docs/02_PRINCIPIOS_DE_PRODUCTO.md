# 02 — Principios de Producto (UX, Configuración, Simple vs Avanzado)

Este documento define **las reglas de diseño del producto** que NO se deben romper.
Si alguien propone una funcionalidad que viola estos principios, debe justificarse con un ADR.

---

## 1) Principios de UX (lo que hace que contadurIA se sienta premium)

### 1.1 Claridad > Complejidad

- El usuario debe entender “qué está pasando” sin saber contabilidad.
- Cada pantalla debe responder:
  - ¿Qué estoy viendo?
  - ¿Qué puedo hacer aquí?
  - ¿Qué pasa si hago X?
  - ¿Cómo deshago/revierto?

### 1.2 Acción rápida (operación diaria en segundos)

- Ventas/gastos deben poder registrarse en:
  - **modo rápido** (mínimos campos) y
  - **modo completo** (detalles).
- Cualquier formulario grande debe ser:
  - por pasos (wizard) o
  - colapsable con “Campos avanzados”.

### 1.3 Prevención de errores (guardrails)

- Validación en tiempo real, mensajes humanos.
- Confirmaciones para acciones irreversibles (anular, cierre, reapertura).
- Si una acción puede afectar números:
  - mostrar preview del impacto (ej: “se ajustará inventario -120ml ron”).

### 1.4 Estados vacíos (empty states) que enseñan

- Si una tabla está vacía, no debe verse “muerta”.
- Debe tener:
  - explicación breve,
  - CTA principal (“Crear primera venta”),
  - guía mínima (“Qué necesitas para empezar”).

### 1.5 Tablas premium (nivel pro)

Toda tabla importante debe tener:

- búsqueda
- filtros por rango de fechas
- columnas configurables (v1+)
- exportar (CSV/Excel)
- paginación/infinite scroll (según volumen)
- acciones por fila (ver/editar/anular/duplicar)

### 1.6 Feedback y microinteracciones

- Toasts coherentes (éxito/error)
- Loading states (skeleton)
- Confirmación visual de guardado
- “Undo” cuando sea posible

---

## 2) Principios de Configuración (hiperconfigurable sin caos)

### 2.1 Plantillas por industria (onboarding)

Un negocio inicia con una **plantilla**:

- habilita módulos recomendados
- define defaults
- activa reglas sugeridas
- configura categorías simples y mapeo contable base

**Regla:** una plantilla nunca debe ser una “jaula”; el negocio puede activar más módulos luego.

### 2.2 Motor de reglas (sin hardcode)

Toda diferencia por industria o preferencia debe resolverse con:

- reglas
- opciones configurables
- flags por módulo

Ejemplos de reglas:

- “Al vender producto con receta → consumir inventario automáticamente”
- “Si pago es en efectivo → sumar a caja”
- “Si movimiento bancario contiene ‘NEQUI’ → sugerir categoría ‘Ventas’”

### 2.3 Niveles de automatización (configurable)

Cada módulo debe ofrecer hasta 3 niveles:

1. **Manual**
   - el usuario decide todo (máximo control)
2. **Asistido**
   - el sistema sugiere y el usuario confirma (IA/recomendaciones)
3. **Automático**
   - el sistema ejecuta reglas preaprobadas (con auditoría y opción de reverso)

**Regla:** la app debe permitir escoger nivel por módulo y cambiar después.

### 2.4 Migración de configuración

Cuando el usuario cambia:

- plantilla de industria
- nivel de automatización
- modo simple ↔ avanzado
- mapeo contable

Debe existir un “wizard de cambio” que:

- explique impacto
- solicite confirmación
- registre auditoría
- garantice que no se pierdan datos históricos

---

## 3) Modo Simple vs Modo Avanzado (reglas no negociables)

### 3.1 Regla de oro: datos únicos, dos vistas

- La base de datos es la misma.
- Simple es una **vista/experiencia**.
- Avanzado es otra **vista/experiencia**.
- No se crean “dos contabilidades”.

### 3.2 Modo Simple (experiencia y límites)

**Objetivo:** operar sin fricción para quien no sabe contabilidad.

- Categorías amigables:
  - Ventas
  - Compras de inventario
  - Gastos fijos (arriendo, servicios, nómina)
  - Gastos variables (insumos, transporte, comisiones)
  - Deudas y pagos
- Reportes:
  - Caja/Bancos
  - Ventas y margen simple
  - Gastos fijos vs variables
  - Inventario básico (si aplica)

**Lo que NO se muestra (pero existe por debajo)**

- PUC completo
- asientos manuales
- cierres contables
- libro mayor/diario

### 3.3 Modo Avanzado (experiencia y capacidades)

**Objetivo:** control contable profesional.
Incluye:

- PUC Colombia
- documentos contables
- asientos manuales y plantillas
- terceros contables con NIT/DV
- centros de costo/proyectos
- cierres por periodo
- auditoría extendida

### 3.4 Capa de mapeo (Simple → PUC)

Debe existir un sistema de mapeo:

- Categoría simple → cuenta PUC
- Reglas por industria (por defecto)
- Edición por contador/admin

**Reglas de consistencia**

- Si el periodo está cerrado, el mapeo no puede cambiar “hacia atrás” sin reapertura formal.
- Cambios de mapeo generan evento auditable.

### 3.5 Migración Simple → Avanzado

Cuando un usuario activa Avanzado:

- Se habilitan pantallas contables.
- Se muestra contabilidad ya generada históricamente.
- Se pide completar:
  - terceros faltantes
  - centros de costo (opcional)
  - ajustes de mapeo

**Regla:** el usuario nunca debe “perder” lo operado por estar en Simple.

---

## 4) Principios de datos y documentos (operación y contabilidad)

### 4.1 No borrar: anular / reversar

- Ventas: anulación genera evento y (si aplica) reverso contable e inventario.
- Compras: anulación revierte inventario y cuentas.
- Movimientos: se reversan con contramovimiento.

### 4.2 Todo documento debe tener “origen”

- Manual (usuario)
- Importación (CSV)
- Sistema (recurrencia)
- IA (sugerido/aceptado)

### 4.3 Identificadores y numeración

Aunque no haya DIAN aún:

- toda venta debe tener:
  - consecutivo interno
  - serie/numeración configurable
  - PDF exportable
  - envío por correo
- se debe diseñar para luego insertar DIAN sin reestructurar.

---

## 5) Principios de “Premium product” (operación real)

### 5.1 El usuario debe sentir control

- Siempre ver:
  - estado del documento (borrador, emitido, anulado)
  - quién lo creó / editó
  - historial de cambios (resumen)
- Mostrar “impacto” antes de confirmar acciones grandes.

### 5.2 Errores con solución

No mostrar “Error 500”.
Mostrar:

- qué pasó
- qué puede hacer el usuario
- cómo recuperarse
- link a soporte/ayuda

### 5.3 Tiempo real percibido

- El sistema debe responder rápido o explicar “procesando”.
- Importaciones grandes deben ir a jobs con progreso.

---

## 6) Checklist de revisión de diseño (antes de construir una pantalla)

Antes de implementar cualquier módulo, validar:

- [ ] ¿Existe regla/config para esto?
- [ ] ¿Cómo se ve en Simple y en Avanzado?
- [ ] ¿Qué permisos aplican?
- [ ] ¿Qué eventos auditable genera?
- [ ] ¿Qué pasa si el usuario anula/revierte?
- [ ] ¿Qué pasa si está offline?
- [ ] ¿Cuál es el “modo rápido” vs “modo completo”?
- [ ] ¿Qué empty state y errores tendrá?
