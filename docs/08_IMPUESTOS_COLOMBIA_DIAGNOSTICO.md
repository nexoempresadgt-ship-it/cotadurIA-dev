# 08 — Impuestos Colombia (Diagnóstico funcional, DIAN-ready sin integración aún)

> Alcance actual: **Colombia-first**.  
> Integración DIAN (facturación electrónica / documento soporte / nómina electrónica) **NO se implementa aún**, pero contadurIA debe quedar **DIAN-ready** desde el diseño de datos, numeración, trazabilidad y exportables.

---

## 1) Objetivo del módulo de impuestos

1. Permitir que un negocio (sin contador) pueda:

   - configurar su contexto fiscal básico (responsabilidades, IVA/INC, retenciones),
   - ver estimaciones simples,
   - recibir alertas de vencimientos,
   - exportar información para su contador.

2. Permitir que un contador (modo avanzado) pueda:

   - revisar bases, IVA/INC, retenciones,
   - validar soportes y documentos,
   - preparar declaraciones con datos consistentes.

3. Preparar la plataforma para DIAN a futuro:
   - datos mínimos requeridos,
   - numeración,
   - campos tributarios por línea,
   - trazabilidad y bitácora,
   - estructura compatible con anexos técnicos.

---

## 2) Qué cubre contadurIA en MVP vs v1 vs v2

### MVP (sin DIAN)

- Configuración fiscal base (simple, guiada).
- Impuestos en documentos (por ahora: cálculo interno y desglose).
- Estimaciones del periodo (IVA/INC/retenciones) “para control”.
- Alertas y calendario básico (basado en calendario DIAN vigente).
- Exportables para contador (CSV/Excel/PDF simples):
  - ventas por periodo (con impuestos desglosados),
  - compras/gastos (con terceros y soportes),
  - retenciones practicadas (si aplica),
  - resumen bancos.

### v1 (sin DIAN aún, pero más robusto)

- Motor de retenciones completo (reglas por concepto, bases, tarifas, UVT si se decide).
- Reportes fiscales más detallados.
- Validaciones más estrictas por tipo de contribuyente (según onboarding).
- “Checklist” fiscal mensual y auditoría de cambios de configuración.

### v2 (fase DIAN)

- Facturación electrónica DIAN + notas + eventos DIAN.
- Documento soporte a no obligados (electrónico).
- Nómina electrónica (si aplica).
- Documento equivalente electrónico (si aplica).
- RADIAN (si aplica para título valor, futuro).

---

## 3) Modelo mental de usuario (UX): de “no sé impuestos” a “tengo control”

### 3.1 En Modo Simple

El usuario no ve “IVA débito/crédito” complejo.
Ve:

- “Impuesto en ventas”
- “Impuesto en compras”
- “Retenciones aplicadas”
- “Saldo estimado del periodo”
  Y sugerencias:
- “Posible obligación: IVA bimestral/cuatrimestral (por validar)”
- “Revisa si aplicas INC por tipo de negocio (por validar)”

> Importante: contadurIA no debe “prometer” que declara por el usuario en MVP. Debe hablar como: “estimación”, “para control”, “para tu contador”.

### 3.2 En Modo Avanzado

El contador ve:

- desglose por impuesto, por tercero, por documento
- trazabilidad al asiento contable
- exportables orientados a declaración

---

## 4) Entidades y datos mínimos DIAN-ready (aunque no se conecte)

### 4.1 Datos de empresa (Tenant)

- Razón social / Nombre comercial
- Tipo de identificación + número (NIT/CC) + DV (si NIT)
- Dirección (ciudad/departamento), país
- Email(s) emisores para envío de documentos
- Responsabilidades/atributos fiscales (captura guiada, no “técnica”)
- Preferencias:
  - mostrar precios con/sin impuesto
  - manejo de propina (si aplica)
  - redondeos (si aplica)

### 4.2 Datos de tercero (Cliente / Proveedor)

- Nombre / razón social
- Tipo y número de identificación
- DV (si NIT, opcional según caso)
- Dirección (opcional MVP), email (para envío)
- Indicadores: cliente/proveedor/ambos

### 4.3 Documentos (ventas / compras / gastos)

Aunque sean “internos”, deben guardar:

- numeración (serie + consecutivo)
- fecha emisión
- fecha contable
- moneda
- totales:
  - subtotal base
  - impuestos (por tipo)
  - descuentos
  - total
- por línea:
  - base gravable
  - impuesto aplicado (tipo y %)
  - valor impuesto
  - retenciones (si aplica)

> Esto permite “enchufar” DIAN después sin reconstruir la base.

---

## 5) Flujos funcionales del módulo Impuestos

### 5.1 Configuración fiscal (onboarding y ajustes)

Ruta sugerida:

- `/app/impuestos/configuracion`

**Pantallas / pasos**

1. “Tu tipo de negocio” (ya viene de plantilla)
2. “Tu forma de operar”
   - ¿vendes productos? ¿servicios? ¿ambos?
   - ¿manejas propina? ¿delivery? ¿canales?
3. “Impuestos que podrían aplicar” (selector con explicación simple)
   - IVA (posible)
   - INC (posible)
   - Retenciones (posible)
4. “Confirmación y advertencias”
   - “Esto es para control interno. Tu contador valida.”

**Validaciones**

- Evitar combos inconsistentes:
  - marcar impuesto pero no definir tasa
  - productos sin categoría tributaria
- Si cambia configuración:
  - registrar auditoría
  - indicar si aplica a futuro o recalcular desde fecha (v1+)

---

### 5.2 Impuestos en ventas

Ruta sugerida:

- `/app/impuestos/resumen` y `/app/reportes/impuestos`

Debe permitir ver por periodo:

- total ventas
- total base gravable
- total impuesto (IVA o INC según configuración)
- total retenciones practicadas (si aplica)

**Controles**

- Comparación:
  - “lo emitido” vs “lo pagado/conciliado” (para flujo de caja)
- “Semáforo”:
  - verde: consistente
  - amarillo: faltan soportes o clasificaciones
  - rojo: descuadres grandes (posible error)

---

### 5.3 Impuestos en compras/gastos + soportes

Ruta sugerida:

- `/app/impuestos/documentos-soporte`

Objetivo MVP:

- adjuntar soporte a compras/gastos (PDF/foto)
- marcar estado: “con soporte / sin soporte”
- extraer datos básicos (v1 OCR/IA)

En v2:

- documento soporte electrónico (para no obligados) con transmisión DIAN (cuando toque).

---

### 5.4 Retenciones (diseño)

Ruta:

- `/app/impuestos/retenciones`

En MVP (simple):

- permitir:
  - activar “retenciones aplican”
  - registrar manualmente retención en compra/venta (si usuario sabe)
  - reportar total retenciones del periodo

En v1 (motor):

- reglas por concepto:
  - base mínima
  - % retención
  - quién retiene (según rol fiscal)
- cálculo automático/asistido

> Nota: las retenciones dependen de variables legales; el motor debe diseñarse como “reglas configurables”, no hardcode.

---

### 5.5 Calendario y alertas de vencimientos

Ruta:

- `/app/impuestos/calendario`

MVP:

- cargar calendario oficial por año (manual inicial, luego automatizar)
- permitir:
  - seleccionar obligaciones que el negocio cree que aplica
  - notificaciones in-app
  - export “vencimientos del mes”

v1:

- generar calendario sugerido según onboarding y tipo de contribuyente (por validar).

---

## 6) Qué automatizar vs qué asistir con IA (MVP)

### 6.1 Automatizar (seguro y estable)

- Numeración interna de documentos.
- Cálculo de impuestos por línea si ya está configurado (tasa fija).
- Reglas simples por producto/categoría (si el usuario lo definió).
- Exportables consistentes por periodo.

### 6.2 Asistir con IA (siempre confirmable)

- Sugerir categoría tributaria por tipo de producto/servicio (por plantilla).
- Sugerir si aplica IVA o INC según industria (marcar “por validar”).
- Detectar inconsistencias:
  - “hay ventas con impuesto activado pero líneas sin impuesto”
  - “hay compras con proveedor sin identificación”
- Explicar en lenguaje humano:
  - “por qué subió tu impuesto estimado”

---

## 7) Riesgos legales + mitigaciones (diseño responsable)

### 7.1 Riesgos

- Usuario interpreta “estimación” como “declaración oficial”.
- Configuración fiscal incorrecta → cálculos errados.
- Falta de soporte/documentación → riesgos tributarios.
- Cambios retroactivos sin auditoría.

### 7.2 Mitigación obligatoria

- Etiquetas claras: “Estimación / Control interno”.
- Wizard de configuración con confirmación + recomendaciones por industria.
- Auditoría de cambios fiscales:
  - quién cambió, cuándo, qué cambió, motivo.
- Roles y permisos:
  - solo admin/contador puede tocar configuración fiscal o mapeos.
- Periodos:
  - (v1) cierres y bloqueos para evitar cambiar pasado sin control.

---

## 8) Fuentes oficiales y referencias (para diseño y fase DIAN)

> Estas fuentes se usan para validar el diseño DIAN-ready y, más adelante, para implementar la integración.

- Micrositio DIAN Sistema de Facturación Electrónica:
  https://micrositios.dian.gov.co/sistema-de-facturacion-electronica/
- Resolución DIAN 000042 de 2020 (sistemas de facturación, anexo técnico, etc.):
  https://normograma.dian.gov.co/dian/compilacion/docs/resolucion_dian_0042_2020.htm
- Documentación técnica DIAN (anexos, herramientas, WS):
  https://micrositios.dian.gov.co/sistema-de-facturacion-electronica/documentacion-tecnica/
- Documento soporte en adquisiciones a no obligados (marco normativo):
  https://www.dian.gov.co/impuestos/Paginas/Sistema-de-Factura-Electronica/Documento-Soporte-adquisiciones-no-obligados.aspx
- Nómina electrónica (documento soporte) marco normativo:
  https://www.dian.gov.co/impuestos/Paginas/Sistema-de-Factura-Electronica/Documento-Soporte-de-Pago-de-Nomina-Electronica.aspx
- Calendario tributario DIAN 2025 (PDF):
  https://www.dian.gov.co/Calendarios/Calendario_Tributario_2025.pdf
- Documento equivalente electrónico (DIAN):
  https://micrositios.dian.gov.co/sistema-de-facturacion-electronica/documento-equivalente-electronico/
