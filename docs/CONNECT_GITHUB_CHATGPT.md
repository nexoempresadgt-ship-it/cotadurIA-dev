# Conectar GitHub a ChatGPT (para trabajar con el repo y los docs como “fuente de verdad”)

Este documento explica cómo conectar tu repositorio de contadurIA a ChatGPT y cómo usarlo de forma segura,
sin volver el repo un caos.

> Importante: “Connectors” ahora se llaman “Apps / Connected apps” en ChatGPT (misma idea, nuevo nombre).

---

## 1) ¿Qué te permite hacer la conexión?

Con GitHub conectado, ChatGPT puede:

- buscar en tu repo,
- leer archivos y ayudarte a analizarlos,
- ayudarte a mantener `/docs` como fuente de verdad,
- revisar estructura, naming, decisiones, etc.

**Lo que NO hace por sí solo:**

- no “monitorea” el repo en tiempo real sin que tú le pidas algo,
- no hace merges automáticamente,
- no reemplaza tu control de PRs y ramas.

---

## 2) Requisitos previos (recomendado)

Antes de conectar:

- repo privado en GitHub
- estructura base:
  - `/docs`
  - `/docs/adr`
  - `/src` (cuando empieces)
- ramas:
  - `main` (estable)
  - `develop` (opcional)
  - `feature/*` (trabajo por módulo)
- protección mínima:
  - PR requerido para main (ideal)

---

## 3) Paso a paso: conectar GitHub en ChatGPT

1. Abre **ChatGPT → Settings**
2. Ve a **Apps & Connectors** (o “Connected apps”)
3. Busca **GitHub** y selecciona **Connect**
4. Te redirigirá a GitHub para:
   - instalar/autorizar la app,
   - elegir qué repos puede acceder ChatGPT
5. Elige SOLO el repo de contadurIA (principio de mínimo acceso).
6. Guarda.

Luego, si quieres cambiar repos permitidos:

- vuelve a Settings y entra al engranaje/configuración de GitHub.

---

## 4) Mejores prácticas (para que ChatGPT sea útil de verdad)

### 4.1 Mantén `/docs` como “API humana”

El repo debe tener:

- decisiones (ADRs)
- reglas de negocio
- roadmap/backlog
- especificaciones de pantallas

ChatGPT trabaja mejor cuando:

- existe una fuente única de verdad (docs),
- todo está versionado,
- y cada módulo tiene specs.

### 4.2 Estructura recomendada para documentos (mínimo)

- docs/00_START_HERE.md
- docs/03_SITEMAP_MAESTRO.md
- docs/05_DOMINIO_Y_MODELO_DE_DATOS.md
- docs/10_ARQUITECTURA_Y_NO_FUNCIONALES.md
- docs/11_SEGURIDAD_RBAC_AUDITORIA.md
- docs/12_ROADMAP_BACKLOG_Y_CRITERIOS.md
- docs/adr/000x\_\*.md

### 4.3 Rutina de trabajo recomendada (muy efectiva)

- Cada vez que decidas algo importante:
  1. actualiza un doc o crea un ADR
  2. haz commit
  3. PR pequeño
- Cada módulo avanza en su rama `feature/...`
- Nada “vive” solo en el chat.

---

## 5) Cómo usar ChatGPT con el repo (prompts útiles)

### 5.1 Navegación y lectura

- “Busca en el repo dónde está definido el sitemap y dime si hay inconsistencias.”
- “Revisa docs/05 y dime qué entidades faltan para el módulo bancos.”
- “Haz una lista de ADRs existentes y qué decisiones cubren.”

### 5.2 Revisión de coherencia

- “Compara docs/03 (sitemap) vs docs/05 (entidades) y marca gaps.”
- “Valida que los flujos de inventario en docs/07 tengan eventos de auditoría.”

### 5.3 Preparar historias

- “Con base en docs/12, crea 10 historias MVP con criterios de aceptación + eventos + permisos.”

---

## 6) Seguridad y permisos (no te dispares en el pie)

- Conecta SOLO el repo necesario.
- Evita dar acceso a repos con credenciales o datos sensibles.
- Nunca guardes secretos en el repo:
  - usa variables de entorno + secrets manager (cuando aplique).

---

## 7) (Opcional) Crear un GPT “ContadurIA Builder”

### 7.1 Cuándo vale la pena

Sí vale la pena si:

- quieres consistencia de tono y reglas,
- quieres que siempre arranque leyendo `/docs`,
- quieres plantillas fijas para historias, ADRs, screen specs.

### 7.2 Qué meterle al GPT (mínimo)

- Instrucciones del rol:
  - PM + Arquitecto + Contable (Colombia-first)
  - “docs manda”
- Archivos de conocimiento (prioridad):
  - docs/00, 02, 03, 05, 06, 07, 10, 11, 12
  - ADRs
- Plantillas:
  - Screen Spec Template
  - ADR Template
  - User Story Template

> Nota: el editor de GPTs permite adjuntar hasta **20 archivos** como “Knowledge”, y cada archivo puede ser grande (límites oficiales aplican).  
> Si te quedas corto, lo ideal es **consolidar** docs (ej: 1 doc “CONTADURIA_MASTER.md”) y mantenerlo actualizado.

---

## 8) Troubleshooting (común)

- “No veo GitHub en Settings”:
  - puede depender del plan/rol en tu workspace o de políticas del admin.
- “No encuentra archivos”:
  - confirma que autorizaste el repo correcto
  - confirma que el archivo existe en la rama que estás consultando
- “Me devuelve cosas viejas”:
  - pide explícitamente revisar `main` o la rama actual
  - o referencia rutas exactas.

---
