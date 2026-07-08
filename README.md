# Prototipo de Interfaz — Portal de Beneficios · Colegio de Arquitectos (MVP)

Prototipo navegable del flujo completo de interfaces. Modulariza en archivos
independientes las vistas que estaban embebidas en un único `index.html`
(Login, Menú Beneficios y QR) y las cablea con las 4 pantallas ya diseñadas
(Menú Afiliado, Panel de Control, Formulario y QR Validación).

## Cómo correr

Se recomienda un **servidor estático local** (para que `localStorage` se comparta
entre páginas y así un beneficio guardado aparezca en todos los paneles):

```powershell
# Opción A: script incluido (usa Python o npx serve automáticamente)
powershell -File serve.ps1

# Opción B: manual
python -m http.server 5500
```

Luego abrí **http://localhost:5500/**.

> También podés abrir `index.html` directamente (doble click). Cada pantalla
> funciona, pero en `file://` algunos navegadores aíslan `localStorage`, por lo
> que el "beneficio recién registrado" podría no verse reflejado entre páginas.

## Flujo de navegación

- **`index.html` (Login)** → según el rol:
  - **Arquitecto** → `menu-beneficios.html`
  - **Personal del Colegio** → `panel-control.html`
  - **Afiliado** → `menu-afiliado.html`
- **Arquitecto** (`menu-beneficios.html`):
  - "Solicitar Beneficio" → `qr-beneficio.html`
  - Tocar un evento del carrusel → `qr-entrada-evento.html`
- **Afiliado** (`menu-afiliado.html`):
  - "Detalle de beneficio" → detalle embebido (interno)
  - "Modificar beneficio" → `formulario-beneficio.html` (precargado)
  - "Escanear QR" → `qr-validacion.html`
  - "Agregar beneficio" → `formulario-beneficio.html`
- **Personal** (`panel-control.html`):
  - "Agregar / Modificar beneficio" → `formulario-beneficio.html`
- **Formulario** (`formulario-beneficio.html`):
  - "Guardar Cambios" → registra el beneficio y vuelve al panel de origen
  - "Cancelar" → vuelve sin cambios
  - El beneficio registrado aparece en las grillas de los paneles y del Menú Beneficios.

## Estructura

| Archivo | Rol |
|---|---|
| `index.html` | Login (entrada; rutea por rol) |
| `menu-beneficios.html` | Menú Beneficios (Arquitecto) |
| `qr-beneficio.html` | QR de beneficio comercial/académico |
| `qr-entrada-evento.html` | QR de entrada a evento (nueva) |
| `menu-afiliado.html` | Menú Afiliado (bundle + `flow.js`) |
| `panel-control.html` | Panel de Control (bundle + `flow.js`) |
| `formulario-beneficio.html` | Formulario registro/modificación (bundle + `flow.js`) |
| `qr-validacion.html` | QR Validación (bundle + `flow.js`) |
| `store.js` | Datos mock + estado compartido (localStorage) |
| `flow.js` | Capa de flujo/redirecciones para las pantallas "bundle" |
| `assets/` | Logo e imágenes de eventos |
| `assets/vendor/` | React, ReactDOM y Babel locales (para correr sin internet) |

## Notas de implementación

- Las pantallas Login, Menú Beneficios y ambos QR se reescribieron como páginas
  React independientes, extraídas del `index.html` original. React, ReactDOM y
  Babel se sirven **localmente** desde `assets/vendor/` (no por CDN), para que el
  prototipo funcione **sin conexión a internet** ("ejecutable con doble clic").
- Las 4 pantallas provistas son "bundles" (se conservan intactas); la navegación,
  la precarga del formulario y la inyección de beneficios se agregan con `flow.js`.
- Todo dato que no proviene de un input declarado está **mockeado** con ejemplos
  (regional, logo/iniciales, color, usos, fechas, códigos QR).
- La integración del beneficio nuevo dentro de la grilla de los bundles es
  *best-effort* (clona una tarjeta existente); si no encuentra plantilla, muestra
  una sección "Beneficios registrados (demo)".
- **Responsive:** todas las pantallas se adaptan a mobile. En los bundles se logra
  con un bloque `<style id="cac-responsive">` (media query `max-width:720px`) que
  colapsa las grillas multi-columna a una sola columna y la barra lateral del Menú
  Afiliado a un bloque apilado; el layout de escritorio no cambia.
- **Navegación:** el Panel de Control tiene botón **"Salir"** (→ login) y el
  Formulario tiene el botón de volver cableado, que regresa a la pantalla de origen
  según `?from=` (`panel` → Panel de Control, `afiliado` → Menú Afiliado).

## Reset del estado

Para limpiar los beneficios registrados de la demo, en la consola del navegador:

```js
localStorage.removeItem('cac_extra_benefits');
localStorage.removeItem('cac_edit_benefit');
```
