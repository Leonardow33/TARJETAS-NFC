# Diseñador de tarjetas NFC

Responde siempre en español. Lee este archivo antes de tocar código y actualízalo al terminar cada cambio importante.

## Qué es
App web de un solo archivo (`index.html`) para crear:
- stickers de tarjetas NFC CR80 (85.6 × 54 mm);
- etiquetas térmicas;
- stickers para soportes NFC de acrílico en L (7.6 × 12.75 cm);
- un brochure PDF para enviar a clientes (usos de los soportes NFC y link de reseñas de Google);
- un panel del negocio (pestaña Negocio): ventas, compras, costos, stock y recuperación del equipo.

**El producto NO es impresión directa en PVC:** es un sticker de vinil de buena calidad con laminado ("cubridor") que se pega encima de la tarjeta, el chip o el acrílico. Las maquetas y vistas previas lo muestran así (ver "Acabado del sticker").

Planes de la propuesta comercial (soles): 1 stand 49 · 2 stands 88 · 3 stands 120 · 4 stands 148 + 2 tarjetas con soporte pequeño de regalo · combo stand + 2 tarjetas 78 · tarjeta adicional 18.

Enlaces:
- En línea: https://leonardow33.github.io/TARJETAS-NFC/ (tras cada push: Ctrl+F5)
- Repo: https://github.com/Leonardow33/TARJETAS-NFC, rama `main`
- Registro de cambios (Claude Docs): https://claude.ai/artifact/LXordyaZsW6rFcCFVkYFLQ
- **Tarjeta digital del usuario** (para grabar en el chip NFC o en un QR): https://leonardow33.github.io/TARJETAS-NFC/perfil/
  - `perfil/index.html` (página aparte, oscura con dorado, sin librerías) y `perfil/doriant-cardenas.vcf` (botón "Guardar contacto").
  - WhatsApp y llamada al +51 910236424, LinkedIn `linkedin.com/in/doriant-cardenas-fernandez-72b071240`, Instagram `@cf.leonardo`. El usuario no pidió poner su correo.
- **Cartas virtuales de muestra** (para mandar por WhatsApp o grabar en un NFC de muestra; spec local `cartas-virtuales-demo.md`, sin subir):
  - `cartas-demo/` = portada oscura con dorado (como `perfil/`) que presenta las dos: estándar S/ 150 y personalizada S/ 250, con "Quiero mi carta" a su WhatsApp. https://leonardow33.github.io/TARJETAS-NFC/cartas-demo/
  - `cartas-demo/estandar/`: lista blanca y negra (nombre … precio), HTML fijo con `<!-- EDITAR -->`.
  - `cartas-demo/personalizada/`: plantilla reutilizable; todo sale del objeto `CARTA` (nombre, lema, WhatsApp, horario, dirección, mapa, categorías y platos con `tags` "top"/"hot" y `foto` emoji o archivo .jpg/.png/.webp). Portada azul marino con olas, abierto/cerrado según el horario, chips de categoría que siguen el scroll, buscador, filtros (más pedidos, picantes), tarjetas con aparición suave, horario y ubicación, WhatsApp flotante y volver arriba.
  - Mismos 17 platos y precios ficticios (Cevichería Demo) en las dos. Sin librerías ni archivos externos; rutas relativas. El pie dice "carta de muestra" y lleva a su WhatsApp (quitarlo en la carta de un cliente real).
  - Para un cliente real: copiar `personalizada/` a otra carpeta (p. ej. `cartas/<cliente>/`) y editar `CARTA`.

## Git
- Usa solo el repo de esta carpeta. NUNCA el repo externo de `C:/Users/elirg` (es todo el home).
- Push por https a `origin main`. No hay `gh` CLI.
- Commits en español, con `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`.
- El usuario espera push tras cada cambio terminado y probado.
- No subir `guia-costos-nfc.md` (costos y márgenes del negocio): el repo es público.

## Código (`index.html`, ~6000 líneas)
- JS ES5 dentro de una IIFE (`var`, `function`, sin flechas). CSS con tokens y modo oscuro.
- Librerías por cdnjs: jsPDF 2.5.1 (`window.jspdf.jsPDF`) y qrcode-generator 1.4.4.
- Pestañas: `#view-design` (Foto), `#view-tpl` (Plantillas), `#view-sup` (Soportes), `#view-print` (Hoja A4), `#view-biz` (Negocio) y `#view-links` (Links). `showView(v)` las cambia.
- **Calidad de impresión** (`.q-dpi`: `#q-dpi1` en Hoja A4 y `#q-dpi2` en Soportes → Imprimir, localStorage `nfc-dpi`): `PRINT_DPI` 300 o 600 y `EXPORT_PPMM = PRINT_DPI / 25.4`. Afecta A4, PDF y PNG a tamaño real; la térmica usa sus 203 DPI y no cambia.
- **Acabado del sticker** (solo en pantalla; no cambia A4, PDF, PNG ni térmica), junto a `texTri`:
  - `stickerOutline(w,h,r,circle)` da el contorno en mm; `supOutline()` el del soporte; `stickerPath`, `stickerEdge` (canto y sombra, antes del diseño) y `stickerGloss` (brillo del laminado y bisel, después).
  - `tplMock(img, t)`: vista previa y miniaturas de Plantillas con el sticker sobre la tarjeta o el chip de PVC blanco (con su grosor); las etiquetas térmicas van sin laminado. Casilla `#t-mock`.
    - En tarjeta y chip el sticker se muestra al menos 0.9 mm más chico por lado que el PVC (`vi`, solo en pantalla) y lleva una línea fina en el filo, para que se note el corte del sticker blanco sobre el PVC blanco.
  - Soportes: vista previa y miniaturas de modelos con canto y brillo. Casilla `#s-fx` ("Relieve y laminado").
- Utilidades de color, junto al código de Soportes: `hex2rgb`, `rgb2hex`, `lum`, `sat`, `mix`, `contrastOn`, `vivid` e `inkFor`. Para medir imagen o canvas: `iw` e `ih`.
- **Texturas de fondo** (compartidas por Plantillas y Soportes), antes de `drawTemplate`:
  - `TEX` = { honey (madera miel, la de referencia del usuario, veta a lo largo del lado corto), wood (madera natural, la original), oak, walnut, marble, marbleBlack, linen, kraft, concrete, slate } con `bg` (color promedio para contrastes y temas), `ink` (tinta que resalta), `dark` y los parámetros de su `kind` (wood, marble, paper, linen, concrete, slate). Orden de los botones: `TEX_ORDER`.
  - `texPaint(g, T, w, h, S)` dibuja en mm con semilla fija (`texRand`), así sale igual a cualquier resolución; helpers `texCloud` y `texDots`. `texCanvas` la guarda en caché (8 entradas, por id + medida + S) y `drawTexture(c, id, x, y, w, h, S)` la pega. `drawWood` = `drawTexture("wood")`; `WOOD_BG`/`WOOD_INK` siguen como alias.
  - `texPicker(el, cur)` arma los botones `.tex` con su muestra (`texThumb`) y marca el elegido.
- Maquetas 3D (soporte y tarjeta) comparten `drawTable3D`, `drawCaption3D`, `shadeCol` y `sticker3D(c, outl, sw, sh, F3, scr, tS, tex, k, bp, cut)` (sombra, canto blanco, malla `texTri`, brillo y bisel). Los videos usan `startVideo(p, nombre, makeFrame)` + `recordVideo` con los controles `p-3dv*` (p = "s" o "t").

### Foto
- `state`: size `card`|`l70x40`, orientation, img, zoom, cx, cy, bg, text, bright, contrast, auto, dither.
- `draw(c,S,bleed,d,mono)` y `renderCard(ppmm,bleed,d,mono)`.
- En `l70x40`, `photoToMono` aplica la mejora automática (niveles 1–99 %, gamma 1.45, enfoque) y el tramado: atkinson, fs, bayer o none.
- Exportación con `photoLabelCanvas(d)`. Una foto vertical se gira para que los 70 mm vayan a lo ancho.
- Cola de etiquetas 70 × 40 (`labelQueue`, `updateLabelQueue`, botones `p-qadd`, `p-qpdf`, `p-qclear`):
  - guarda una copia del estado y su cantidad (toma "Copias") y genera un solo PDF con todas;
  - vive en memoria y se pierde al recargar.

### Plantillas (tarjeta y etiquetas)
- `tpl` usa `TPL_SIZES` (card / l70x40 / l50x25 / chip25 / chip30) y tiene frente y reverso. El tamaño se elige en la lista desplegable `#tplsize` (select con grupos Tarjeta, Etiqueta térmica y Chip NFC).
- `drawTemplate` incluye los helpers `stack`, `qr`, `logo` y `nfc`.
- En etiquetas (`FILL`) el contenido crece hasta 1.6×, y el nombre manda: las demás líneas quedan en ≤ 72 % de su tamaño.
- En modo mono (`BOLDEN`) el texto lleva un trazo extra de 0.12 mm.
- **Chips NFC** (`chip25` redonda Ø 25 con `circle`, `chip30` de 30 × 20.8; ambos con `chip: true`):
  - Redonda: recorta en círculo y dibuja el diseño en el cuadrado inscrito (72 % del diámetro), con un margen más chico (`chipP`).
  - Al elegir un chip: la cara pasa a "grande" y el sello (reverso) a "qrsolo" (redonda) o "qrpal" (rectangular), si no tenían ya un diseño apto.
  - **Cara y sello:** `tplChipItem` trae `cv`/`cvBack` y `at(ppmm, lado)`. `tkItems` los pone seguidos (`pairStart`) y `tkTickets` no los separa entre etiquetas.
  - Con `at`, cada sticker se dibuja a la resolución exacta de la impresora y se copia en píxeles enteros, para que el QR salga nítido.
  - Sellos: "qrsolo" (solo QR) y "qrpal" (QR + `short2`, p. ej. "IA · DATOS"; en rectangular las palabras van a la derecha).
    - El QR del sello ocupa el alto menos 0.1 mm: su borde blanco hace de margen.
    - Con 1 mm de franja, el rectangular sale con cuadros de 4 puntos a 203 DPI. Ojo: con más margen el QR cae a 3 puntos.
  - **Franja visible** (`tpl.chipInset`, `#t-chipinset`, por defecto 1 mm por lado): `tplDims` achica el sticker, no el chip.
    - Ø25 → sticker Ø23; 30 × 20.8 → 28 × 18.8. Así se ve un borde del chip y el corte no tiene que ser perfecto.
  - El QR usa `chipLink(t)` = `t.chiplink` (por defecto la tarjeta digital `perfil/`) o, si está vacío, `t.link`. `qr(x,y,size,link)` acepta un link propio.
  - Botones de etiqueta 70 × 40 / 70 × 50 (`.tkpre`, se recuerdan en localStorage `nfc-ticket`). Ø25: 1 chip (cara + sello) por etiqueta; 30 × 20.8 en 70 × 50: 2 chips.
  - Se imprime con el ticket compartido de Soportes (`tplChipItem`, `updateTplTicket`, `tplTicketPDF`, `#t-tk`). `tkQueue` y la medida de la etiqueta se comparten (t-tkw ↔ s-tkw).
  - Si la lista tiene diseños, el PDF imprime la lista; si está vacía, el diseño actual × copias.
- **"Texto grande (chips)"** (id `grande`): usa el campo `short` (`#t-short`, por defecto "IA").
  - `shortLines` corta donde haya "/"; si no, palabra por palabra.
  - Pensado para que la térmica de 203 DPI no empaste letras chicas.
- Hay 11 diseños en `TEMPLATES`. "Tarjeta completa en térmica" va centrada y girada en una etiqueta de 60 × 90.
- "Agregar a hoja A4" (`#t-card`, `t-add`, `tplA4Snap`) se ve en todos los tamaños. Los chips se agregan con la medida de su sticker (Ø 23 / 28 × 18.8, cara y sello, corte redondo en la Ø25); las etiquetas 70 × 40 y 50 × 25 pasan a tarjeta 85.6 × 54. "PDF tamaño real" usa la misma medida.
- **Maqueta 3D de tarjeta y chips** (`#t-3dbtn`, `#t-3d`, `renderCard3D(o)`; oculta en etiquetas térmicas, `card3DOK`):
  - PVC blanco con grosor (canto sombreado por segmento) y el sticker laminado en cada cara con diseño (`sticker3D(..., cut = true)`); una cara sin diseño queda en PVC liso.
  - Para que se noten las líneas del sticker, como en Soportes: el sticker va al menos 0.9 mm más chico por lado que el PVC (`vi`), con el mismo grosor exagerado que en Soportes (`tS` = 1.1 % del lado menor). Además, `cut` agrega una línea de contacto oscura en la base y un filo fino arriba.
  - Posición `#t-3dpose`: "stand" (parada e inclinada 8° en un bloque de acrílico transparente con ranura; el bloque se achica para chips) o "flat" (acostada; el reverso se voltea). Frente/Reverso usa `tpl.side` (en stand suma 180° de giro).
  - Giro `#t-3dyaw` de −180 a 180 (arrastrar da la vuelta completa). Encuadre fijo calculado con todos los giros, para que no cambie de tamaño. Mesa `#t-3dtable`, PNG `#t-3ddl`.
  - Texturas en caché `t3dTex` (se borra en `renderTpl`, que además refresca la maqueta). `o` acepta { cv, yaw, pose, side, table, tex {front, back}, zoom, caption }.
  - Video (`t-3dv*`): en el soporte, vuelta completa con easing (frente, reverso, frente) si hay reverso; si no, o sobre la mesa, vaivén suave.
- **Logo** (`tpl.logo` { img, cache }, no se guarda al recargar; `tpl.logoMode` sí, en `TPL_SAVE`): seg `#tpllogomode` con "color" (colores originales, dibuja la imagen tal cual con su transparencia), "logo" (un color con `logoMask`, más "Invertir logo") y "photo" (tramado).
  - En la térmica (`t.thermal`), "color" cae a `logoMask`. `#t-logonote` lo explica; "Invertir" se oculta en modo color.
  - Solo las plantillas Bloque, Banda y "Logo / imagen" usan el logo.
- **Material** (`tpl.material`, `#tplmat`): white, black o el id de una textura de `TEX` ("Con textura" abre `#tpltex`; por defecto honey). Reemplaza a "Invertir".
  - `tpl.invert` queda en sync para compatibilidad.
  - `tplTexOK(t)`: texturas en tarjeta y chips, no en las etiquetas 70 × 40 / 50 × 25 ni cuando se dibuja para la térmica (ahí cae a blanco).
  - `tplMonoPV(t)`: la vista previa, las miniaturas y la maqueta 3D de los chips van en B/N como la térmica, salvo un chip con textura, que va a color (se imprime en A4).
- Datos por defecto: Doriant Cardenas / Analista de Inteligencia Comercial / Ingeniero A / +51 910236424 / doriantleodor@gmail.com / linkedin.com/in/doriantcardenas.
  - `loadTpl` cambia los antiguos de ejemplo (Ana Torres…) guardados en localStorage.

### Soportes (`#view-sup`, ids `s-*`)
Editor libre para el sticker que se pega en el soporte de acrílico.
- **Diseño de pantalla:**
  - Pantalla ancha (≥ 760 px, todas las pestañas): la columna `.controls` (340 px) es sticky con scroll propio, así la vista previa no se pierde.
  - Ordenador (≥ 1200 px): `.wrap` llega a 1500 px y `.controls` (salvo `.s-controls`) va en 2 columnas CSS (`column-count`, 700 px, sin scroll propio). Soportes usa 420 px y una vista previa más alta (hasta 780 px).
  - Soportes: menú de pestañas `#s-tabs` (Modelos, Elemento, Medida, Fondo, Imprimir) y solo se ve el `.group[data-tab]` con clase `on`. `showSupTab(t)`.
  - Al tocar un elemento se abre la pestaña Elemento (en `syncSupEl`), y un punto verde indica que hay uno seleccionado.
  - Celular: la vista previa queda fija arriba (`.stage.stick`, `syncSupStick`, `--stage-h`), salvo con la maqueta 3D abierta.
- `sup`: w, h (mm), preset, bg, rounded, els, material, texture, baseBg, logoPal, tplId, shape y link.
  - preset: `127.5x76` = 12.75 × 7.6 cm, `85.6x54`, `25x25c`, `30x20.8` o `custom`, en la lista desplegable `#s-preset` (select). Los campos van en cm.
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
- **Editor de imagen** (modal `#ie`, `openImgEditor(img, ed0, cb, title)`): se abre al subir el logo ("Cambiar logo"), al subir **una** imagen con "Imagen" o "Reemplazar", y con "Editar logo" (`s-logoedit`) o "Editar imagen" (`s-imgedit`, pestaña Elemento).
  - `ed` = { cut, sens (10–100, 50 normal), crop {x,y,w,h} en fracción, mode "orig" | "one" | "map", one, map [{from,to}] }.
  - Vista previa a 900 px (`ieBase` → `IE.base`, `ieView` → `IE.view`); al aplicar, `edRender` a `IMG_MAX` = 2400 px, `recolor` y `edCrop`.
  - Recorte arrastrando esquinas, bordes o el centro (`ieHit`, `IE_CURSOR`); un toque suelto no recorta. "Ajustar al contenido" = `autoCrop` (`alphaBox` + 2 %).
  - Fondo (si `logoAnalyze` detecta uno liso): logo de un tono (sin `pal`) → `cutMono` (transparencia por distancia al fondo: se van los huecos de las letras y los bordes quedan suaves); con colores → `cutFlood` (relleno desde el borde; si el fondo no es blanco, también los huecos cerrados; con fondo blanco se dejan porque suelen ser textos blancos dentro de una figura) y separa el contorno para que no quede halo del fondo viejo. Así no hay dos negros distintos.
  - Colores: "Un color" pinta todo lo visible y conserva la transparencia; "Cambiar" (`imgColors` saca hasta 6 colores) mueve cada píxel con un promedio ponderado por distancia (los bordes suaves se conservan).
  - Fondo de la vista previa (`ieBgv`): el del soporte, cuadros, blanco o negro. Si el logo de un tono se va a teñir (`logoFor`), la vista previa ya lo muestra teñido (`IE.tinted`).
  - En memoria se guardan `orig` y `ed` (logo: `supLogo.orig`/`.ed`; imagen: `e.orig`/`e.ed`, fuera de `SUP_EL_KEYS`) para volver a editar desde la original. Tras recargar se edita la versión guardada.
  - `storeURL` guarda en localStorage una copia de hasta 1600 px; la de memoria (hasta 2400 px) es la que se imprime.
- **Logo:** `supLogoProcess(img, done, ed0)` abre el editor y, al aplicar, arma `supLogo` (img, src0, pal, ink, tintable = colores originales y sin fondo, orig, ed), aplica el tema y llama `done`. `supPlace(img, src, idx, extra)` lo pone en los elementos `idx` (o lo agrega).
  - `logoAnalyze` (acepta img o canvas) detecta el color del borde si es un fondo liso y saca hasta 4 colores saturados.
  - `logoFor(d)` tiñe con `logoTint` un logo sin color que no contrasta con el fondo de ese diseño.
- **Tema del logo** (`supApplyTheme(pal)`; con `null` restaura):
  - Fondo sin material: si el modelo era oscuro, pasa a un tono muy oscuro del color principal (80 %); si era blanco, sigue blanco; si era de color, pasa al principal oscurecido al 45 %.
  - Con material, el fondo no cambia.
  - Multicolor (aro, barra): toma la paleta del logo avivada (`vivid`).
  - Estrellas de color: doradas (`GOLD`); blancas o negras siguen la tinta. El QR y los elementos `lock` no cambian.
  - Textos e íconos neutros siguen la tinta, según su rol ink o soft.
  - Logo sin colores: estilo monocromo con acento dorado.
- **Material del soporte** (`#s-mat`): free, white, black o "Textura" (abre `#s-tex`; `sup.material` = id de `TEX`). En la pestaña Fondo, `#s-bgtex` aplica la textura igual. Los dos llaman `supSetMaterial(m)`; `supTexUI` marca los botones.
  - `applyMaterial(d, mat)` fija el fondo; `MATS` incluye cada textura con `texture: id`, e `inkFor` usa su `ink`.
  - En la maqueta 3D, una textura `dark` usa acrílico negro (`darkMat`).
  - Recalcula desde `e.orig` los textos e íconos neutros a `inkFor` y marca `base.role`.
  - Elegir un color de fondo a mano vuelve a "free".
- **Maqueta 3D** (`render3D`, `#s-3d`):
  - Soporte en L de acrílico con inclinación de 10° y pie detrás de 5 cm. Bordes y pie del color del material.
  - "Soporte de acrílico" (`#s-3dacr`, localStorage `nfc-3dacr`): según el material, blanco o negro; vale para la maqueta, el PNG y el video (`o.mat` del brochure manda). El negro lleva un reflejo fino en las aristas para no perderse en la mesa oscura; el sticker conserva su canto blanco, sombra y brillo.
  - El frente es un sticker laminado pegado sobre el acrílico: 1.5 mm más chico por lado (se ve el marco del acrílico), con grosor de ~0.8 mm (exagerado para que se note), canto blanco, sombra, bisel y una franja de brillo que se mueve con el giro (en el video recorre el sticker).
  - El diseño va en perspectiva real, con una malla de triángulos afines (`texTri`) recortada al contorno del sticker.
  - Se gira con el deslizador o arrastrando (yaw de ±60°). Mesa oscura, clara o de madera, y descarga en PNG.
  - "Mostrar medidas" (`#s-3dmeas`, activo por defecto): cotas de alto, ancho y base (pie) en cm; salen también en el PNG.
  - `render3D(o)` acepta `{ cv, yaw, tex, zoom, side, caption }` para dibujar en otro canvas; textos y líneas escalan con `U`.
  - **Video para redes** (`s-3dvid`, `s-3dvfmt`, `s-3dvdur`, `s-3dvtxt`): `videoFrame` gira de −52° a −4° con un acercamiento leve, en un canvas de 1080×1920, 1080×1080 o 1920×1080. Se graba con `captureStream` + `MediaRecorder` (MP4 en Chrome y Edge; si no, WEBM), sin audio: la música se agrega en CapCut, InShot, etc.
  - Para probar la grabación real, el tiempo virtual no sirve: lanza Edge con `--remote-debugging-port` y lee el resultado con `cdp.js` (Node con WebSocket).
- **Link del cliente** (`#s-link`, `sup.link`, se guarda): se pone en todos los QR del diseño y de los modelos que se apliquen. Debajo, dos `details.howto`: cómo sacar el link de reseñas de Google ("Pedir reseñas", `g.page/r/…/review`) y cómo el cliente crea su Perfil de Empresa (google.com/business, verificación). Alternativa: `search.google.com/local/writereview?placeid=…`.
- **Brochure para clientes** (`#s-brochure`, `brochurePDF()`): PDF A4 de 3 páginas hecho con jsPDF (texto vectorial, helvetica, solo Latin-1).
  - P1: portada oscura con maqueta 3D de "califica-g" (`render3D` acepta `w`, `h`, `mat`, `meas`, `table`), qué es NFC y 4 beneficios. P2: 6 modelos predeterminados (sin el logo actual) con su uso, y tarjetas/stickers NFC. P3: link de Google paso a paso, crear el perfil, flujo del pedido y contacto con QR vectorial a `chipLink`.
  - Nombre, cargo y teléfono salen de los datos de Plantillas (`tpl`).
- **Chips NFC pequeños:**
  - Presets `25x25c` (redonda Ø 25 mm; `sup.shape = "circle"`, check `#s-circle`) y `30x20.8` (rectangular).
  - `supSetSize(w,h,scale)` escala también los elementos al cambiar de medida, no al girar.
  - Modelos chip-toca, chip-logo, chip-texto, chip-texto-rect y chip-rect con `fit` (medida, forma y preset que toman). Al elegir un modelo normal desde un chip, se vuelve a 76 × 127.5.
  - Zona segura: min(3 mm, 7 % del lado). El A4 dibuja el corte redondo con `doc.circle`.
- **Ticket térmico** (`#s-tk`, en la pestaña Imprimir): varios stickers por etiqueta (70 × 40 por defecto).
  - `tkQueue` vive en memoria; si está vacía, se usa el diseño actual × `s-tkqty`.
  - `tkFit` prueba normal y girado con margen de 1.5 mm. `tkTickets(ppmm)` pone un solo tamaño por etiqueta, aplica `toMono` (B/N y tramado opcional) y dibuja la línea de corte. El PDF se arma con `labelPDF`.
  - Ø25 → 2 por 70 × 40; 30 × 20.8 → 3 girados.
- **"Agregar a hoja A4"** (`s-addA4`, pestaña Imprimir): manda una copia del soporte (× Copias) a la hoja A4 compartida (`kind: "sup"`, sin botón de editar en la lista).
- **Guardado:** localStorage `nfc-soporte`, con imágenes y logo en dataURL; si no caben, sin ellas.
- **Exportar:**
  - `A4Fit()` calcula cuántos entran por A4, normal o girado (margen 6 mm, separación elegible). 12.75 × 7.6 da 4 por hoja.
  - PDF A4 con líneas de corte, PDF a tamaño real y PNG a 300 o 600 DPI (calidad de impresión). Sangrado opcional de 2 mm.
  - El diseño cubre toda la cara. Con sangrado, la línea de corte sigue en el tamaño exacto.

### Hoja A4 y PDF
- La cola `queue` acepta cualquier medida: `item.render(ppmm, bleed)`, `item.mm` {w,h} (si falta, `cardMM(item.d)`), `item.shape` ("circle") y `item.rounded`. Helpers `itemMM`, `itemKey`, `mmLabel`.
- `sheetGroups(o)` agrupa por medida y `sheetLayout(o, mm)` da la mejor distribución de cada una (tarjeta: 11 por A4 con distribución mixta).
- `paginate(o)` → `[{slots, items}]`: las hojas llenas usan esa distribución; lo que sobra de cada medida se acomoda en franjas y comparte hoja con las demás.
- `needsRotate` nunca gira cuadrados ni redondos. "Completar hoja" completa la última hoja de cada medida.
- `labelPDF(pages, w, h, copies, name)`: una página por etiqueta, y los canvas repetidos se guardan una sola vez.

### Negocio (`#view-biz`, ids `b-*`, localStorage `nfc-negocio`)
Basado en `guia-costos-nfc.md` (guía local del usuario, sin subir al repo porque es público y tiene costos).
- `biz` = { cfg, mats, plans, clientes, ventas, invs, seeds, plansV, matsV } (las tres últimas son marcas de migraciones que se aplican una sola vez). `bizDefaults()` trae los valores de la guía y el equipo inicial: impresora + lectora/grabadora NFC (S/ 350) y selladora USB (S/ 7.78).
  - cfg: hora, minStand, minTarjeta, stkStand (4), stkTarjeta (11), impresion, impuesto (%), tiempo (bool), umbral.
  - Catálogo con los precios del restock del 23 nov 2026 (`matsV: 2`; `loadBiz` los aplica una vez y luego respeta lo que edite el usuario): stand 5 × S/ 47.47 → 9.49, chip NFC 6 × S/ 7.49 → 1.25 y soporte pequeño 60 × S/ 14.79 → 0.25.
    - Con esto, un stand cuesta ≈ S/ 12.43 en materiales y una tarjeta ≈ S/ 1.71. El plan con menos margen es "4 stands + 2 tarjetas" (≈ 57 %, contando el tiempo).
  - Materiales del restock que no entran en las recetas: "Tarjeta NFC RFID tipo llave" (`tarjllave`, S/ 0.67) y "Stand acrílico negro mate" (`standneg`, S/ 4.76). Solo suman al stock; la receta del stand usa el precio del blanco (`stand`).
- **Cotizador por volumen** (`#b-quote`, arriba de Registrar venta; `renderQuote` se llama en `renderBiz`):
  - Producto `#b-qprod` (`QPROD`: stand blanco, stand negro con `swap: "standneg"`, tarjeta con soporte), cantidad `#b-qqty`, precio normal `#b-qbase` (`cfg.qbase[producto]`; si no hay, el plan de 1 stand o de 1 tarjeta) y margen mínimo `#b-qmin` (`cfg.qmin`, 35 %).
  - `qCost` = receta (`qRecipe`) × catálogo + tiempo si `cfg.tiempo`. `qFloor` = costo ÷ (1 − margen mínimo) es "lo más bajo que puedes dejarlo".
  - Escala `cfg.qtiers` [{min, desc}] (por defecto `BIZ_QTIERS`: 1 → 0 %, 2 → 10, 3 → 18, 4 → 24, 10 → 30, 20 → 35, 50 → 40, 100 → 45), editable en la tabla `#b-qtbl` (+ Escalón, ×, "Escala por defecto"). `qPrice` redondea a soles enteros, así 2, 3 y 4 stands dan 88, 120 y 148 como los planes. Los escalones bajo el mínimo salen en rojo con ⚠.
  - Lista `#b-qneed`: lo que falta comprar para el pedido según `bizStock()` (comprado − usado, compartido con la tabla de stock), en packs y soles.
  - "Copiar cotización para WhatsApp" (`#b-qcopy`): texto con cantidad, precio c/u, descuento, total e "Incluye".
- `bizRecipe("stand"|"tarjeta")`: materiales por producto (la hoja A4 en fracción). `bizProdCost`, `bizTimeCost`, `bizCalc(v)` → mat, time, cost, util, utilMat, margin. Usa siempre el catálogo actual: si cambia un precio, cambian todos los márgenes.
- Venta: fecha, cliente, clienteId, plan (nombre), cant, total, stands, tarjetas (totales), notas, `adj` (metadatos de archivos) y `links` (lista de URL). El plan llena stands, tarjetas y precio (editables). "Otro (manual)".
- **Historial de negocios** (`#b-hist`, `renderClients`): `biz.clientes` = { id, nombre, tel, contacto, dir, link, notas, creado }.
  - Se arma solo con las ventas: `bizCliEnsure` busca por nombre normalizado (`cliKey`: sin tildes, mayúsculas ni espacios de más) y `bizLinkClients` (en `loadBiz`) une las ventas antiguas o restauradas.
  - El campo Cliente tiene `datalist` (`#b-clist`) y `#b-vcliinfo` avisa si el negocio ya existe. "Nuevo negocio" usa `prompt()`.
  - Cada negocio (`details.bcli`, `cliOpen` recuerda los abiertos) muestra datos editables, compras con su 📎, "Registrar venta a este negocio", "Usar su link en Soportes" (pone `#s-link`) y WhatsApp (`waLink`, +51 si son 9 dígitos). Solo se borra si no tiene ventas. Al renombrarlo cambia `v.cliente` de sus ventas.
  - `keepFocus(fn)` vuelve a dibujar sin perder el campo activo.
- **Links por venta** (`v.links`): en el formulario, filas `#b-vlinks` (2 al inicio, "+ Otro link" hasta 8, "Traer el link de Soportes" usa `sup.link`); en la ventana `#bv`, lista con Copiar y Quitar y campo `#bv-linkin` (Enter agrega).
  - `linkNorm` (agrega https://), `linkList` (sin repetidos), `linkKind` (Reseñas Google, link corto, Instagram, WhatsApp, Facebook, TikTok, LinkedIn, Tarjeta digital o el dominio).
  - `cliTakeLink`: si el negocio no tiene link de reseñas, toma el de la venta. El buscador del historial también busca en los links. El botón de la tabla muestra "📎 n · 🔗 m". CSV ventas con columna links.
- **Archivos por venta** (IndexedDB `nfc-adjuntos`, store `files` { id, venta, name, type, size, fecha, blob, design? }): `adjPut/adjGet/adjAll/adjDel/adjClear`, `adjAdd(v, recs)`.
  - Al registrar la venta: dos espacios de archivo (`#b-vfile1`, `#b-vfile2`) y `#b-vdesign` (diseño actual de Soportes, Plantillas o los dos). Ventana `#bv` (`bvOpen`, botón 📎 en ventas y en el historial): notas, miniaturas, Ver, Descargar, Borrar y subir más.
  - `adjDesign(area, v)`: PNG a `EXPORT_PPMM` + JSON editable (`supPack(true)` o las claves `TPL_SAVE`). "Editar en Soportes/Plantillas" (`adjOpenDesign`) escribe `nfc-soporte`/`nfc-plantilla`, deja `sessionStorage nfc-goto` y recarga.
  - Borrar una venta borra sus archivos. El respaldo JSON lleva `adjuntos` en base64 (`data`); al restaurar se reemplaza el IndexedDB.
- Panel (`renderBiz`): filtro por mes (`b-month`), KPI (vendido, invertido, ganancia, caja neta), barra de recuperación del equipo (utilidad solo materiales ÷ inversión en equipo, todo el historial), ventas, por plan, por mes, compras y stock (comprado − usado; aviso si queda menos que el umbral).
- Editores en `details.bizcfg` (`buildBizEditors`, `bizEditorNums`): parámetros, catálogo y planes.
- Compras (`b-iadd`): tipo `stock` (material del catálogo), `equipo` o `gasto` (envío, publicidad, pasajes: suma en Invertido y caja neta, no en stock ni en la recuperación del equipo; sin piezas).
  - "+ Otro material (nuevo)…" (`__new`, `#b-inew`) lo agrega al catálogo con pack = piezas y precio = costo; si el nombre ya existe (`cliKey`), usa ese.
  - En la tabla de compras, piezas y costo se editan en la misma fila (`data-ie`).
  - **Compras precargadas** (`BIZ_SEED`, `BIZ_SEED_MATS`, `bizSeed(o)` en `loadBiz`): se agregan una sola vez con id fijo y quedan en `biz.seeds`, así que si se borran no vuelven. Si un material no está en el catálogo, se agrega. Primer lote: restock de Temu del 23 nov 2026 (tarjetas llave 24, stand blanco 10, stand negro 5, chips 18, soportes pequeños 60; S/ 172.02, con el precio de Temu tomado como precio por pack × cantidad).
  - Catálogo: "Agregar material" (`#b-madd`, `prompt()`) y × solo en los materiales propios (los de `BIZ_BASE_MATS` usan las recetas y no se quitan). Los materiales nuevos no entran en el costo de los productos.
- CSV (`bizCSV`, con BOM, separador coma) de ventas (con nombres de archivos), inversiones, catálogo, planes y negocios; respaldo y restauración en JSON.

### Links (`#view-links`, ids `l-*`)
- Links guardados para copiar y enviar a los negocios: `biz.links` = [{ id, titulo, url, cat, para, msg }] (va en `nfc-negocio`, así entra en el respaldo JSON).
- `LINK_SEED` (`LINK_HOME` + `cartas-demo/`, `estandar/`, `personalizada/` y `perfil/`) se agrega una vez desde `bizSeed` con su id en `biz.seeds`; si se borra, no vuelve.
- `renderLinks`: selector `#l-to` (negocio del historial), buscador `#l-q`, chips de categoría `#l-cats` (`lCat`) y tarjetas `.lcard` con Copiar mensaje, Copiar link, WhatsApp (chat del negocio con `waLink(c.tel)` o `wa.me/` para elegir contacto), "Al QR" (pone el link en `#s-link`), ✎ y ×.
- `lMsg(l)`: cambia `{negocio}` y `{link}` (si falta `{link}`, va al final) y limpia "Hola ," → "Hola,". `copyTxt(txt, btn, label)` copia con respaldo `prompt()`.
- Formulario `#l-form` para agregar o editar (`lEdit`, `lFormReset`). Abajo, `#l-auto` arma solo los links de cada negocio: reseñas (`c.link`), tarjeta digital (`dcURL`) y los de sus ventas.

### Tarjetas digitales de clientes (Negocio → `#b-site`, ventana `#dc`)
- Para vender tarjetas digitales como `perfil/`, alojadas en **Cloudflare Pages** (plan gratis). La guía paso a paso, los costos y el dominio propio están en `details.howto` dentro de `#b-site`.
- Datos: `c.card` en cada negocio (`biz.clientes`): on, slug, titulo, sub, desc, wa, tel, wamsg, email, web, resenas, menu, ig, fb, tt, li, dir, maps, horario, accent, theme (dark/light), logobg y logo (bool). `dcDefaults(c)` lo llena con los datos del negocio.
  - Logo: PNG de hasta 320 px en IndexedDB con id `cardlogo-<id>` (mismo store que los adjuntos, así entra en el respaldo); en memoria `dcLogoURL`. Se sube pasando por `openImgEditor` (`#ie` tiene z-index 70 y `#fn` 80, encima de `#dc`).
- `biz.cfg.siteBase` (`#b-sitebase`) = dirección del sitio. `dcURL(card)` = base + "/" + slug + "/". `slugify`, `dcSlugUnique` (no repite ni usa "404").
- `dcHTML(d, o)`: página de un solo archivo (sin librerías, logo incrustado, mismo estilo que `perfil/`, color de acento y fondo oscuro o claro). Botones solo de los campos llenos: reseña (ancho, acento), WhatsApp, llamar, Instagram, Facebook, TikTok, LinkedIn, menú, web, correo, cómo llegar (Maps o búsqueda de la dirección) y guardar contacto. Si quedan impares, el último medio botón pasa a ancho. Pie con link a la portada (`dcFoot`).
  - `dcPhone`: +51 si son 9 dígitos con 9, fijo con 0 o 7 dígitos de Lima. `dcHandle`: @usuario o link.
- `dcVCF(d, url, jpg)`: vCard 3.0 (CRLF, líneas de 75, foto JPEG de 240 px con `dcJpg`).
- `dcBuildSite` + `zipBlob` (ZIP propio sin compresión con `crc32`, sin librerías): `index.html` (portada con los datos de `tpl`), `404.html` y `<slug>/index.html` + `<slug>/contacto.vcf` de cada tarjeta publicada. Cloudflare reemplaza todo el sitio en cada subida, por eso siempre se exporta completo.
- Ventana: vista previa en un iframe tipo celular (`srcdoc`, se actualiza 250 ms después de escribir), Copiar link, Usar en Soportes (QR), Guardar en su última venta (`v.links`), Abrir vista previa (blob) y Quitar tarjeta. En el historial, botón "Crear tarjeta digital" / "Tarjeta digital ✓" (`data-ca="card"`).
- Límites de Cloudflare (verificados en 2026-11): gratis con 20 000 archivos por sitio y 25 MiB por archivo; arrastrando desde el panel, 1 000 archivos por subida (≈ 490 tarjetas: 2 archivos cada una); con Wrangler, 20 000. Un .com en Cloudflare Registrar cuesta ≈ US$ 10.5 al año.

### Descargas
- Todo pasa por `downloadBlob(blob, name, named)`: abre el cuadro `#fn` (`askFileName`) para escribir el nombre; la extensión se conserva y `fnClean` quita caracteres inválidos. Los PDF usan `savePDF(doc, name)` (no `doc.save`). El PNG de Foto (`#resultdl`) también pregunta.
- El video pide el nombre **antes** de grabar (`recordVideo(mime, ext, fname)`) y al terminar descarga directo.

## Pruebas
- Sintaxis: `node -e` con `new Function(script)`.
- Render: Edge headless lanzado desde **PowerShell** con `Start-Process ... --dump-dom --virtual-time-budget=...`. Desde Bash falla a ratos.
- Usa una página de prueba en el scratchpad:
  - exponla con reemplazos dentro de la IIFE (p. ej. `window.__lg`);
  - escribe en `<pre id="res">` los logs y las imágenes como `IMG<nombre> data:...`;
  - extrae las imágenes con un script de Python y revísalas con Read;
  - los PDF ya no usan `doc.save`: para contarlos o sacarlos, parchea `jspdf.jsPDF.API.output` o `HTMLAnchorElement.prototype.click` (captura el `href` del blob tras pulsar `#fn-ok`).
- Para ver un PDF no hay poppler (Read no lo abre): se renderiza con pdf.js 3.11.174 de cdnjs en Edge headless.
  - El PDF va embebido en base64 (fetch de file:// falla) y `pdf.worker.min.js` se carga como `<script>` (worker falso); `--virtual-time-budget=30000`.
  - Scripts en el scratchpad: `mkbro.py` (página de prueba), `expdf.py`, `mkpv.py` y `eximg.py`.
- Para varias líneas de Python desde PowerShell, usa un `.py`: `python -c` con comillas y `<` se rompe.
- Capturas de la página completa: `--screenshot=archivo.png --window-size=1300,1700 --hide-scrollbars` en vez de `--dump-dom`.
- Usa un `--user-data-dir` nuevo por prueba: si no, el localStorage de la corrida anterior se mezcla (p. ej. ventas duplicadas en Negocio).
- Scripts del editor y de Negocio en el scratchpad: `mkt.py` (página de prueba con modos gray, wob, map, biz, dpi, shotbiz, shotie), `run.ps1 <modo>` (dump-dom + `showres.py`) y `shot.ps1 <modo>` (captura).
- El scratchpad cambia en cada sesión: si no están los scripts, hay que rehacerlos. Los de la sesión del acabado laminado:
  - `mktest.py <index.html>`: copia la página con una prueba por pasos (`?n=N` corre los N primeros) que escribe en `#res` los logs y las imágenes `IMG<nombre>`.
  - `cdp.js <url> <salida> [s]`: Edge por CDP que acepta los `confirm()` y espera DONE.
  - `ex.py <salida> <carpeta>`: extrae las imágenes en PNG.
- **Ojo:** aplicar un modelo de Soportes abre un `confirm()` que deja colgado el Edge headless (con `--dump-dom` nunca termina). Para pruebas que hagan clic en modelos, usa `cdp.js` (scratchpad): lanza Edge con `--remote-debugging-port`, acepta los diálogos (`Page.handleJavaScriptDialog`) y espera a que `#res` diga DONE. `--timeout` no sirve: vuelca el DOM al cargar.
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
- **Papel recomendado (acabado pro):** vinil adhesivo blanco imprimible A4 + laminado en frío transparente.
  - Impresora de tinta: vinil para inyección (brillante o satinado), calidad alta, tipo "fotográfico brillante"; secar 20–30 min antes de laminar.
  - Impresora láser: vinil o papel adhesivo para láser (el de inyección se derrite).
  - Laminado mate: elegante, sin reflejos y el QR se lee mejor (ideal con fondo negro). Brillante: combina con el acrílico.
  - Evitar papel metalizado, holográfico, dorado o plateado (bloquea el NFC), vinil transparente sobre acrílico negro (no hay tinta blanca) y papel bond o adhesivo común.
  - Proceso: laminar y luego cortar con cúter y regla metálica; esquinas con sacabocados R3–R5; limpiar el acrílico con alcohol y pegar desde un borde con una tarjeta para que no queden burbujas.
  - En Perú: "vinil adhesivo imprimible A4" y "laminado en frío A4" (librerías, insumos de impresión, Mercado Libre).

## Herramientas externas evaluadas
El usuario prefiere no instalar nada: antes de recomendar un repo o herramienta, revísalo y explica riesgos y costos.
- **open-jarvis/openjarvis:** asistente de IA local (Ollama), NO da voz a Claude Code; ejecuta comandos, pide Google OAuth y se instala con `curl | bash`. No recomendado.
  - Para dictarle a Claude Code: dictado de Windows con **Win + H** en la caja de texto.
- **tashfeenahmed/freellmapi:** router de las capas gratuitas de otros proveedores con las claves propias del usuario. No incluye Claude ni modelos top, no sirve para Claude Code, es inestable y los planes gratis pueden usar los datos para entrenar.
- **lemomo-ai/lemo-opuscar:** plugin real de Claude Code que hace videos animados con código (canvas/WebGL + TTS + música). Gasta muchos tokens (30–60 min por video) e instala ffmpeg, Node y Python. Se descartó; en su lugar se hizo el video del soporte dentro de la app (ver Maqueta 3D).
- **google/elephant-goldfish-model (EGM):** plugin de Claude Code (Apache 2.0, ~20k estrellas; "no es producto oficial de Google") para escribir y revisar documentos de diseño de software con subagentes "sin memoria". Solo trae skills y agentes en markdown, sin hooks ni MCP. Útil para proyectos grandes con varias personas; para esta app de un archivo aporta poco y gasta muchos tokens por revisión. Opcional, no instalado.

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
- **Links acortados de reseñas (p. ej. `reviewthis.biz/...`):** respondido, sin implementar. Son un 302 a `search.google.com/local/writereview?placeid=…` (verificado con curl). Desde la app (GitHub Pages) no se puede leer la redirección por CORS. Opciones ofrecidas: campo que acepte el link final pegado y saque el `placeid`, o un proxy propio (Google Apps Script o Cloudflare Worker gratis) para resolverlo solo. Recomendado grabar en el chip el link directo de Google, no el acortador.
- **Ofrecido, sin respuesta del usuario:**
  - "Reducir sticker" (0.5–1 mm por lado) en Soportes, como en tarjetas, para cuando el fondo coincide con el plástico;
  - estrellas doradas brillantes con borde oscuro sobre madera (hoy salen en dorado oscuro).
- **Por probar con el usuario:**
  - escanear con el celular el QR impreso de los chips NFC (no se pudo verificar sin OpenCV);
  - el brochure con un negocio real: los nombres de los botones de Google ("Pedir reseñas") pueden cambiar;
  - impresión real de un soporte (medidas, corte y sangrado);
  - foto en vertical con la mejora automática, Density 8 y márgenes en Ninguno;
  - el editor de imagen en el celular (recortar con el dedo) y volver a subir su logo "Industry Secrets" (el guardado se procesó con el método anterior);
  - imprimir un A4 a 600 DPI y comparar con 300;
  - el relieve y el brillo del sticker en pantalla (maqueta, video y vistas previas): ajustar si lo quiere más fino o con menos brillo (`tS`, `ins` y la franja `bp` en `render3D`; `stickerGloss`).
- **Calidad:** el usuario quiere la mejor calidad (HD) en A4, PDF y PNG; en la térmica, solo si se puede mejorar sin romper nada.
- **Negocio:** las compras de material anteriores al restock del 23 nov 2026 siguen sin registrar, así que el stock de antes sale incompleto. Falta confirmar con el total del pedido de Temu si el precio era por pack (se cargó así) o por línea. También hay que ver si las "tarjetas llave RFID" son NTAG21x: si son MIFARE Classic, el iPhone no las lee. El precio del laminado sigue sin confirmar (S/ 0.50 por stand).
  - Ofrecido, sin respuesta: que una venta pueda indicar stand negro o blanco (o tarjeta tipo llave), para descontar el stock y el costo correctos.
  - Planes actualizados a la propuesta comercial (49/88/120/148/78/18) y se quitó "Promo 4 stands". `loadBiz` migra una sola vez (`plansV: 2`) solo los planes que seguían con el precio anterior; si el usuario los había editado, se respetan.
- **Posible:** ajuste de desplazamiento si la impresión sale corrida, y tamaño 50 × 25 para fotos.
- **Tarjetas digitales en Cloudflare:** el usuario aún no crea la cuenta ni el proyecto. Falta probar con él la primera subida del ZIP, el botón "Guardar contacto" (.vcf con foto) en Android y en iPhone, el fondo claro y decidir si compra un dominio propio antes de grabar chips. Su tarjeta personal sigue en GitHub Pages (`perfil/`).
- **Sistema de marcaciones NFC:** solo está planificado, falta que el usuario decida:
  1. lector (tablet Android, PC con lector USB o celular);
  2. almacenamiento (Google Sheets con Apps Script recomendado, Supabase o M365);
  3. marcas de entrada/salida o también refrigerio;
  4. número de trabajadores y sedes.
