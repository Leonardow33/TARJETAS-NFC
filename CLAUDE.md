# Diseñador de tarjetas NFC

Responde siempre en español. Lee este archivo antes de tocar código y actualízalo al terminar cada cambio importante.

## Qué es
App web de un solo archivo (`index.html`) para crear stickers de tarjetas NFC CR80 (85.6 × 54 mm) y etiquetas térmicas.
- En línea: https://leonardow33.github.io/TARJETAS-NFC/ (tras cada push: Ctrl+F5)
- Repo: https://github.com/Leonardow33/TARJETAS-NFC, rama `main`
- Registro de cambios (Claude Docs): https://claude.ai/artifact/LXordyaZsW6rFcCFVkYFLQ

## Git
- Usa solo el repo de esta carpeta. NUNCA el repo externo de `C:/Users/elirg` (es todo el home).
- Push por https a `origin main`. No hay `gh` CLI.
- Commits en español, con `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`.

## Código (`index.html`, ~2300 líneas)
- JS ES5 dentro de una IIFE (`var`, `function`, sin flechas). CSS con tokens y modo oscuro.
- Librerías por cdnjs: jsPDF 2.5.1 (`window.jspdf.jsPDF`) y qrcode-generator 1.4.4.
- Pestañas: `#view-design` (Foto), `#view-tpl` (Plantillas), `#view-print` (Hoja A4). `showView(v)` las cambia.
- **Foto:**
  - `state`: size `card`|`l70x40`, orientation, img, zoom, cx, cy, bg, text, bright, contrast, auto, dither.
  - `draw(c,S,bleed,d,mono)` y `renderCard(ppmm,bleed,d,mono)`.
  - En `l70x40`, `photoToMono` aplica la mejora automática (niveles 1–99 %, gamma 1.45, enfoque) y el tramado: atkinson, fs, bayer o none.
  - Exportación con `photoLabelCanvas()`. Una foto vertical se gira para que los 70 mm vayan a lo ancho.
- **Plantillas:**
  - `tpl` usa `TPL_SIZES` (card / l70x40 / l50x25) y tiene frente y reverso.
  - `drawTemplate` incluye los helpers `stack`, `qr`, `logo` y `nfc`.
  - En etiquetas (`FILL`) el contenido crece hasta 1.6×, y el nombre manda: las demás líneas quedan en ≤ 72 % de su tamaño.
  - En modo mono (`BOLDEN`) el texto lleva un trazo extra de 0.12 mm.
  - Hay 8 diseños en `TEMPLATES`. "Tarjeta completa en térmica" va centrada y girada en una etiqueta de 60 × 90.
  - "Agregar a hoja A4" (`#t-card`, `t-add`) se ve en todos los tamaños y siempre agrega la tarjeta en 85.6 × 54 (frente y reverso).
  - Datos por defecto: Doriant Cardenas / Analista de Inteligencia Comercial / Ingeniero A / +51 910236424 / doriantleodor@gmail.com / linkedin.com/in/doriantcardenas. `loadTpl` cambia los antiguos de ejemplo (Ana Torres…) guardados en localStorage.
- **Hoja A4:**
  - La cola `queue` guarda solo diseños de tamaño tarjeta: `item.render(ppmm, bleed)`.
  - Máximo 11 por A4 con distribución mixta.
- **PDF de etiquetas:** `labelPDF(pages, w, h, copies, name)`, una página por etiqueta.

## Pruebas
- Sintaxis: `node -e` con `new Function(script)`.
- Render: Edge headless lanzado desde **PowerShell** con `Start-Process ... --dump-dom`. Desde Bash falla a ratos.
- Usa una página de prueba en el scratchpad que exponga funciones (`window.__rt`, `window.__setImg`) y escriba en `<pre id="res">` los dataURL o los logs. Parchea `jspdf.jsPDF.API.save` para contar páginas.

## Impresora del usuario: BIENEX HQ300B
- **Hardware:** 203 DPI, etiquetas de 20–80 mm de ancho, rollo de máximo 50 mm de diámetro. USB solo en Windows, Bluetooth solo en Android.
- **Driver:** "Label Mode Windows Driver", modelo de 3 pulgadas / 203 DPI. No usar HiLabel_2inch ni 300DPI.
  - Si Windows la muestra como "HQ300_3C83" (Bluetooth), hay que quitarla.
- **Page Settings:**
  - papel 70x40 (ancho 70, alto 40), Rotation 0, Offset 0;
  - Media Gap de 3 mm, Direct, Tear;
  - Speed 50–102 mm/s;
  - **Density 8 para fotos y 10–12 para texto.**
- Halftone: None. Watermark: apagado.
- **Chrome:** papel 70x40, Ver más → Márgenes: Ninguno y Escala: Personalizado 100. En la app, "Girar 90°" va desmarcado.
- **Rollos:** prueba con 70 × 40. Para la tarjeta completa, 60 × 90 térmica directa (ideal con top coat), diámetro ≤ 50 mm, núcleo de 25 mm.
- **Nota:** el papel térmico se desvanece con sol, calor y roce. Para tarjetas duraderas: laminar, o usar la hoja A4 con tinta o láser.

## Pendiente
- Que el usuario pruebe la foto en vertical con la mejora automática, Density 8 y márgenes en Ninguno.
- Posible: ajuste de desplazamiento si la impresión sale corrida, y tamaño 50 × 25 para fotos.
- **Sistema de marcaciones NFC:** solo está planificado, falta que el usuario decida:
  1. lector (tablet Android, PC con lector USB o celular);
  2. almacenamiento (Google Sheets con Apps Script recomendado, Supabase o M365);
  3. marcas de entrada/salida o también refrigerio;
  4. número de trabajadores y sedes.
