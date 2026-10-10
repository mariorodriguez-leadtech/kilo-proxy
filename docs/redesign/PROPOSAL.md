# Kilo Proxy — Propuesta de rediseño "Brutalist claro"

Capturas del estado actual: `docs/redesign/before/*.png` (renderizadas headless a 2x con
`KILO_SCREENSHOT_DIR=... go test -tags desktop -run TestNativeScreenshots`).

## 1. Diagnóstico (estado actual)

| # | Problema | Dónde | Impacto UX |
|---|----------|-------|------------|
| D1 | **Todo es una tarjeta con borde 2px + sombra dura 4px.** Banners, secciones, tarjetas de agente, tarjetas de modelo y bloques de ajustes pesan igual → no hay jerarquía; el ojo no sabe dónde empezar. | Todas | Alto |
| D2 | **El estado del proxy (lo más importante de la app) es un chip gris de 11px** arriba a la derecha. "Detenido" y "Activo" apenas se distinguen. El endpoint local está enterrado en Ajustes. | Cabecera | Alto |
| D3 | **Los controles secundarios parecen texto.** "Options ›", "What's included ›", "Advanced ›", "Edit ›" son botones ghost sin borde ni subrayado: no se perciben como clicables. | Agentes, Modelos, Actividad, Ajustes | Alto |
| D4 | **Seleccionado = gris claro (#e6e6e6)**, el mismo tono que estados vacíos/deshabilitados. "Recommended", "Cloudflare", "English", el modelo por defecto… leen como desactivados. | Opciones, segmented, tarjetas de modelo | Alto |
| D5 | **Ajustes es una página infinita** que mezcla cuenta, conexión, control del proxy (Save & start / Stop / Check gateway), endpoint, imágenes grandes, apariencia, actualizaciones y comandos de terminal. 3 botones de acción primaria compitiendo. | Ajustes | Alto |
| D6 | **Tarjetas de agentes heterogéneas**: alturas distintas, avatares de 2 letras ambiguos (dos "CL"), párrafos largos de advertencia dentro de la tarjeta (Claude Desktop), selector de carpeta compitiendo con "Open". | Agentes | Medio |
| D7 | **Modelos: el selector de ventana de contexto domina** la pantalla por encima de la lista de modelos, que es el contenido principal. La tarjeta del modelo por defecto se pinta gris (parece deshabilitada). Cuadrícula de 2 columnas desaprovecha ancho. | Modelos | Medio |
| D8 | **Actividad: 3 bloques casi idénticos de métricas** (cuenta Kilo, observado en dispositivo, sesión). Estados vacíos como "—" sin explicación, "Checking Kilo…" duplicado. | Actividad | Medio |
| D9 | **Detalle de agente**: botón "Open" aislado bajo un input, chips de estado apilados ("Installed", "Profile not prepared") sin relación con la acción, la acción principal no está arriba. | Agente › Opciones | Medio |
| D10 | Tipografía display Go Mono (serif mono) en H1, H2 y eyebrows a la vez: demasiados niveles con la misma fuente → ruido. | Global | Bajo |

## 2. Principios del rediseño

1. **Mantener el ADN brutalist**: negro sobre blanco, esquinas rectas, reglas gruesas, Go Mono, sombras duras.
2. **La sombra dura es un privilegio**: solo la llevan los elementos *accionables principales* (tarjetas de agente, botones primarios, el bloque de estado del proxy). Las secciones informativas pasan a ser **bloques con regla superior gruesa** (sin caja). Resultado: 3 niveles visuales claros.
3. **Un único color de acento: citrus de la marca `#E8F36A`** (ya definido en `internal/brand`). Se usa para *selección/activo* (nav activo, opción elegida, modelo por defecto) y para el bloque de estado "en marcha". El gris deja de significar "seleccionado".
4. **Todo lo clicable lo parece**: los disclosure pasan a mono MAYÚSCULAS con `+`/`−` y subrayado; los enlaces llevan subrayado; los botones ghost reciben borde al hover.
5. **Estado del sistema siempre visible**: bloque de estado del proxy fijo en la barra lateral (grande, color, endpoint copiable, botón Start/Stop).
6. **Navegación secundaria en Ajustes**: pestañas (Cuenta · Imágenes · Apariencia · Terminal · Actualizaciones) en lugar de un scroll infinito.

## 3. Cambios por sección

### Global / shell
- Sidebar: nav activo con fondo citrus + texto negro (antes gris oscuro). Debajo de la nav, **tarjeta de estado del proxy**: `● EN MARCHA` (citrus) / `● DETENIDO` (blanco con borde), `127.0.0.1:8877/v1` y botón Start/Stop. La cabecera de página queda limpia (título + descripción).
- Cabecera: H1 en Go Mono 28; H2 de sección pasan a **eyebrow numerado** `01 — MODELOS` en Go Mono 12 mayúsculas + regla 3px. Cuerpo en Inter.
- `section()` = regla superior 3px + título + descripción, sin caja. `card()` conserva borde + sombra (solo para elementos accionables).
- Seleccionado (option cards, segmented, tabs, modelo por defecto) = fondo citrus + borde 3px negro.
- Disclosure = `+ OPCIONES` / `− OPCIONES` en mono, subrayado.

### Agentes
- Cuadrícula uniforme de tarjetas (2 columnas ≥ 940dp), misma estructura: cabecera (icono + nombre + chip de estado), descripción de 1 línea, fila de acción **[Abrir ▸] [Opciones]**.
- Avisos largos (Claude Desktop) se reducen a una línea + "Ver detalles" en Opciones.
- Resumen "Tus modelos" pasa a una franja fina (sin sombra) con enlace "Editar modelos".

### Modelos
- Lista de modelos primero; "Ventana de contexto" pasa a un control segmentado compacto en una franja bajo el título.
- Modelo por defecto: chip citrus `★ POR DEFECTO`, tarjeta blanca (no gris).

### Actividad
- Fila hero de 3 KPI grandes (Coste hoy · Peticiones · Saldo) arriba; los bloques de detalle como secciones con regla.
- Estado vacío explícito: "Sin datos todavía — aparecerán tras tu primera petición".

### Ajustes
- Pestañas: **Cuenta y conexión · Imágenes · Apariencia · Terminal · Actualizaciones**.
- Cuenta: separar "Cuenta Kilo" (sesión, equipo) de "Endpoint local" (URL + copiar). Start/Stop sale de aquí (vive en la sidebar).

### Onboarding
- Stepper con paso activo citrus; tarjeta única con sombra; CTA principal fijo abajo a la derecha (se mantiene).

## 4. Fuera de alcance
- Interfaz web opcional (`ui/`), bandeja del sistema, textos legales/ayuda.
- No se cambia lógica de negocio, sólo presentación y jerarquía.
