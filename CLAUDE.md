# Diseñador de tarjetas NFC

Responde siempre en español. Lee este archivo antes de tocar código y actualízalo al terminar cada cambio importante.

## Qué es
App web de un solo archivo (`index.html`) para crear:
- stickers de tarjetas NFC CR80 (85.6 × 54 mm);
- etiquetas térmicas;
- stickers para soportes NFC de acrílico en L (7.6 × 12.75 cm).

Enlaces:
- En línea: https://leonardow33.github.io/TARJETAS-NFC/ (tras cada push: Ctrl+F5)
- Repo: https://github.com/Leonardow33/TARJETAS-NFC, rama `main`
- Registro de cambios (Claude Docs): https://claude.ai/artifact/LXordyaZsW6rFcCFVkYFLQ

## Git
- Usa solo el repo de esta carpeta. NUNCA el repo externo de `C:/Users/elirg` (es todo el home).
- Push por https a `origin main`. No hay `gh` CLI.
- Commits en español, con `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`.
- El usuario espera push tras cada cambio terminado y probado.

## Código (`index.html`, ~4000 líneas)
- JS ES5 dentro de una IIFE (`var`, `function`, sin flechas). CSS con tokens y modo oscuro.
- Librerías por cdnjs: jsPDF 2.5.1 (`window.jspdf.jsPDF`) y qrcode-generator 1.4.4.
- Pestañas: `#view-design` (Foto), `#view-tpl` (Plantillas), `#view-sup` (Soportes) y `#view-print` (Hoja A4). `showView(v)` las cambia.
- Utilidades de color, junto al código de Soportes: `hex2rgb`, `rgb2hex`, `lum`, `sat`, `mix`, `contrastOn`, `vivid` e `inkFor`. Para medir imagen o canvas: `iw` e `ih`.
- Madera compartida: `WOOD_BG`, `WOOD_INK` y `drawWood(c, x, y, w, h, S)`, con veta en mm y semilla fija para que salga igual a cualquier resolución.

### Foto
- `state`: size `card`|`l70x40`, orientation, img, zoom, cx, cy, bg, text, bright, contrast, auto, dither.
- `draw(c,S,bleed,d,mono)` y `renderCard(ppmm,bleed,d,mono)`.
- En `l70x40`, `photoToMono` aplica la mejora automática (niveles 1–99 %, gamma 1.45, enfoque) y el tramado: atkinson, fs, bayer o none.
- Exportación con `photoLabelCanvas(d)`. Una foto vertical se gira para que los 70 mm vayan a lo ancho.
- Cola de etiquetas 70 × 40 (`labelQueue`, `updateLabelQueue`, botones `p-qadd`, `p-qpdf`, `p-qclear`):
  - guarda una copia del estado y su cantidad (toma "Copias") y genera un solo PDF con todas;
  - vive en memoria y se pierde al recargar.

### Plantillas (tarjeta y etiquetas)
- `tpl` usa `TPL_SIZES` (card / l70x40 / l50x25) y tiene frente y reverso.
- `drawTemplate` incluye los helpers `stack`, `qr`, `logo` y `nfc`.
- En etiquetas (`FILL`) el contenido crece hasta 1.6×, y el nombre manda: las demás líneas quedan en ≤ 72 % de su tamaño.
- En modo mono (`BOLDEN`) el texto lleva un trazo extra de 0.12 mm.
- Hay 8 diseños en `TEMPLATES`. "Tarjeta completa en térmica" va centrada y girada en una etiqueta de 60 × 90.
- "Agregar a hoja A4" (`#t-card`, `t-add`) se ve en todos los tamaños y siempre agrega la tarjeta en 85.6 × 54 (frente y reverso).
- **Material** (`tpl.material`, `#tplmat`): white, black o wood. Reemplaza a "Invertir".
  - `tpl.invert` queda en sync para compatibilidad.
  - Madera solo en tamaño tarjeta y no en térmica.
- Datos por defecto: Doriant Cardenas / Analista de Inteligencia Comercial / Ingeniero A / +51 910236424 / doriantleodor@gmail.com / linkedin.com/in/doriantcardenas.
  - `loadTpl` cambia los antiguos de ejemplo (Ana Torres…) guardados en localStorage.

### Soportes (`#view-sup`, ids `s-*`)
Editor libre para el sticker que se pega en el soporte de acrílico.
- `sup`: w, h (mm), preset, bg, rounded, els, material, texture, baseBg, logoPal y tplId.
  - preset: `127.5x76` = 12.75 × 7.6 cm, `85.6x54` o `custom`. Los campos van en cm.
- Elementos en `sup.els`: x, y (centro en mm), rot (×90°), w o size (mm) y color.
  - Tipos: img, text, qr, ring (aro), bar, stars y nfc (ícono).
  - ring, bar y text pueden llevar `colors` (multicolor, por segmento o letra). `palette` guarda los colores para "Volver a multicolor". `thick` es el grosor.
  - `base` es el color sin tema, `orig` el original del modelo y `slot: "logo"` marca el espacio del logo.
- `drawSupport(c,S,bleed,d?)`: `d` permite dibujar otro diseño, para las miniaturas. También `renderSupport(ppmm,bleed)`.
- Vista previa `renderSup()`: se arrastra con snap al centro, la rueda cambia el tamaño, las flechas mueven y Supr borra.
  - La línea punteada azul es la zona segura de 3 mm.
- **Modelos** (`SUP_TEMPLATES`): resenas, resenas2, instagram, wifi, menu, pago, califica y califica-g.
  - califica / califica-g: fondo negro como "Paga aquí", con estrellas blancas en vez de "A nombre de". califica-g lleva "Google" con `lock: true` (colores de Google que el tema no cambia).
  - Usan `fy` (y opcional `fx`) en fracción y medidas para `ref` (por defecto 76 × 127.5 vertical). `supTemplateEls` los escala.
  - Al aplicar un modelo, el soporte pasa a vertical (u horizontal si el `ref` es horizontal) y se conserva el logo actual.
  - Usa siempre `supModels()` / `supModel(id)`, no `SUP_TEMPLATES` directo.
- **Editar modelos** (`s-msave`, `s-mnew`, `s-mreset`, `syncModelBar`):
  - `supCustom` en localStorage `nfc-sup-modelos`; `supToModel(id,name)` convierte el diseño actual (el logo pasa a "TU LOGO").
  - Uno con el id de un predeterminado lo reemplaza (`edited`, ✎ en la miniatura) y se puede restaurar; los nuevos se pueden borrar.
  - Imágenes de modelos guardados: `cachedImg(src)`.
  - `supTemplateDesign(t,W,H)` arma el modelo con material, logo y tema. Lo usan las miniaturas (`buildSupTplGrid`) y la aplicación del modelo.
- **Logo** ("Cambiar logo", `s-logo` → `supPutImage` → `supLogoProcess`):
  - `logoAnalyze` detecta el color del borde si es un fondo liso y saca hasta 4 colores saturados.
  - `logoCutBg` quita el fondo por inundación desde el borde, si "Quitar el fondo liso" está marcado.
  - `supLogo` guarda el logo sin teñir (img/canvas, src0, pal, ink, tintable).
  - `logoFor(d)` tiñe con `logoTint` un logo sin color que no contrasta con el fondo de ese diseño.
- **Tema del logo** (`supApplyTheme(pal)`; con `null` restaura):
  - Fondo sin material: si el modelo era oscuro, pasa a un tono muy oscuro del color principal (80 %); si era blanco, sigue blanco; si era de color, pasa al principal oscurecido al 45 %.
  - Con material, el fondo no cambia.
  - Multicolor (aro, barra): toma la paleta del logo avivada (`vivid`).
  - Estrellas de color: doradas (`GOLD`); blancas o negras siguen la tinta. El QR y los elementos `lock` no cambian.
  - Textos e íconos neutros siguen la tinta, según su rol ink o soft.
  - Logo sin colores: estilo monocromo con acento dorado.
- **Material del soporte** (`#s-mat`): free, white, black o wood.
  - `applyMaterial(d, mat)` fija el fondo; madera usa `texture "wood"`.
  - Recalcula desde `e.orig` los textos e íconos neutros a `inkFor` y marca `base.role`.
  - Elegir un color de fondo a mano vuelve a "free".
- **Maqueta 3D** (`render3D`, `#s-3d`):
  - Soporte en L de acrílico con inclinación de 10° y pie detrás de 5 cm. Bordes y pie del color del material.
  - El frente lleva el diseño en perspectiva real, con una malla de triángulos afines (`texTri`).
  - Se gira con el deslizador o arrastrando (yaw de ±60°). Mesa oscura, clara o de madera, y descarga en PNG.
  - "Mostrar medidas" (`#s-3dmeas`, activo por defecto): cotas de alto, ancho y base (pie) en cm; salen también en el PNG.
- **Guardado:** localStorage `nfc-soporte`, con imágenes y logo en dataURL; si no caben, sin ellas.
- **Exportar:**
  - `A4Fit()` calcula cuántos entran por A4, normal o girado (margen 6 mm, separación elegible). 12.75 × 7.6 da 4 por hoja.
  - PDF A4 con líneas de corte, PDF a tamaño real y PNG de 300 DPI. Sangrado opcional de 2 mm.
  - El diseño cubre toda la cara. Con sangrado, la línea de corte sigue en el tamaño exacto.

### Hoja A4 y PDF
- La cola `queue` guarda solo diseños de tamaño tarjeta: `item.render(ppmm, bleed)`. Máximo 11 por A4 con distribución mixta.
- `labelPDF(pages, w, h, copies, name)`: una página por etiqueta, y los canvas repetidos se guardan una sola vez.

## Pruebas
- Sintaxis: `node -e` con `new Function(script)`.
- Render: Edge headless lanzado desde **PowerShell** con `Start-Process ... --dump-dom --virtual-time-budget=...`. Desde Bash falla a ratos.
- Usa una página de prueba en el scratchpad:
  - exponla con reemplazos dentro de la IIFE (p. ej. `window.__lg`);
  - escribe en `<pre id="res">` los logs y las imágenes como `IMG<nombre> data:...`;
  - extrae las imágenes con un script de Python y revísalas con Read;
  - parchea `jspdf.jsPDF.API.save` para contar páginas.
- Las cargas de archivos (FileReader, toBlob) a veces no terminan dentro del tiempo virtual: dales 1.5–2.5 s y repite antes de suponer un error.
- Los parches grandes van en un `.py` del scratchpad (Write + `python archivo.py`). Los heredoc largos en Bash fallan con las comillas.

## Soporte físico (Temu)
- Acrílico en L, blanco o negro: cara de 7.6 × 12.75 cm y pie de 5 cm.
- El sticker se imprime en vinil o papel adhesivo A4 (tinta o láser), se recorta y se pega en la cara.
- **Sangrado:** depende del fondo del diseño frente al papel, que es blanco, y no del plástico.
  - Fondo de color, negro o madera: usar sangrado.
  - Fondo blanco: no hace falta.
- **Plástico:** si el fondo del diseño coincide con el plástico, un sticker un poco chico o corrido no se nota. Si no coinciden, hay que cortar exacto y centrar bien.
- Medir el acrílico antes de una tanda, porque puede variar 1–2 mm. Si cambia, usar la medida "Otra".

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
- **Ofrecido, sin respuesta del usuario:**
  - "Reducir sticker" (0.5–1 mm por lado) en Soportes, como en tarjetas, para cuando el fondo coincide con el plástico;
  - estrellas doradas brillantes con borde oscuro sobre madera (hoy salen en dorado oscuro).
- **Por probar con el usuario:**
  - impresión real de un soporte (medidas, corte y sangrado);
  - foto en vertical con la mejora automática, Density 8 y márgenes en Ninguno.
- **Posible:** ajuste de desplazamiento si la impresión sale corrida, y tamaño 50 × 25 para fotos.
- **Sistema de marcaciones NFC:** solo está planificado, falta que el usuario decida:
  1. lector (tablet Android, PC con lector USB o celular);
  2. almacenamiento (Google Sheets con Apps Script recomendado, Supabase o M365);
  3. marcas de entrada/salida o también refrigerio;
  4. número de trabajadores y sedes.
