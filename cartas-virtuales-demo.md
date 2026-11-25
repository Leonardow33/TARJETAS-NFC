# Cartas virtuales NFC — Páginas de muestra (demo)

> Documento para **Claude Code**: construir **dos cartas virtuales de ejemplo** para mostrar a clientes de mi emprendimiento de tarjetas/stands NFC. Son demos (datos ficticios de una cevichería tipo "Riko Riko") que subiré a **GitHub Pages** y cuyos links enviaré por WhatsApp como prueba. Español de Perú, precios en soles (S/).

---

## 1. Contexto y objetivo

Vendo tarjetas y stands NFC + QR. Una de mis líneas es la **carta virtual**: el NFC/QR lleva a un link fijo que muestra la carta del restaurante, y yo edito la carta sin cambiar el link ni reimprimir.

Ofrezco **dos niveles**, y necesito una página de muestra de cada uno para que el cliente vea la diferencia antes de comprar:

- **Carta estándar (S/ 150):** simple y limpia, tipo imagen o "cuaderno digital". Solo platos y precios, sin lujos.
- **Carta personalizada (S/ 250):** dinámica, a la marca del restaurante, con categorías, fotos de platos, diseño y navegación cómoda.

El objetivo de estas demos es **vender la diferencia**: que al abrir ambos links, el cliente entienda por qué la personalizada cuesta más.

---

## 2. Qué construir

Dos páginas web **independientes y autocontenidas** (cada una su propia carpeta), pensadas para verse en el **celular** (mobile-first):

```
/cartas-demo/
  /estandar/index.html      → Carta estándar de muestra
  /personalizada/index.html → Carta personalizada de muestra
```

Requisitos técnicos:
- **HTML + CSS + JS en un solo archivo** por carta (sin frameworks, sin dependencias externas pesadas), para que funcione directo en GitHub Pages.
- **Mobile-first**, responsive, que se vea bien al abrir desde un celular.
- Carga rápida y sin login.
- Datos **ficticios** de una cevichería de muestra (ver sección 5). Dejar comentarios `<!-- EDITAR -->` donde el cliente real cambiaría nombre, platos y precios.
- Las dos deben usar los **mismos platos y precios** para que la comparación sea justa: lo que cambia es la **presentación**, no el contenido.

---

## 3. Carta ESTÁNDAR (S/ 150) — cómo debe verse

Simple, tipo lista o "cuaderno digital". La idea es que se vea **ordenada y legible**, pero sin diseño elaborado.

- Encabezado simple: nombre del restaurante (texto) + una línea de "Carta".
- Lista de platos agrupada por categoría (solo títulos de categoría en negrita).
- Cada plato: **nombre + precio** en una fila. Descripción opcional corta en gris.
- Sin fotos de platos (o a lo mucho una foto de portada).
- Paleta sobria (blanco/negro, una tipografía).
- Un pie con un botón de WhatsApp para pedidos (opcional).

Debe sentirse como "la carta de siempre, pero digital". Funcional, nada más.

---

## 4. Carta PERSONALIZADA (S/ 250) — cómo debe verse

Dinámica, atractiva, a la marca. Aquí se justifica el precio mayor.

- **Portada** con nombre del restaurante, logo (placeholder), una foto de fondo apetitosa (placeholder) y un lema corto.
- **Navegación por categorías**: pestañas o botones que filtran/saltan a cada sección (Entradas, Ceviches, Platos de fondo, Bebidas, Postres).
- Cada plato como **tarjeta** con: foto (placeholder), nombre, descripción, precio, y opción de destacar "⭐ El más pedido" o "🌶️ Picante".
- Paleta a la marca (colores cálidos de cevichería: azul marino + naranja/limón, por ejemplo), tipografía con más carácter.
- **Botón flotante de WhatsApp** para pedidos/reservas.
- Detalles de experiencia: animación suave al hacer scroll, botón "volver arriba", sección de horarios y ubicación al final.
- Opcional: buscador de platos, o filtro "solo picantes / sin gluten".

Debe sentirse como una **mini-web del restaurante**, no solo una lista.

---

## 5. Datos de muestra (usar en AMBAS cartas)

Restaurante ficticio: **Cevichería demo** (nombre de muestra, editable).

**Entradas**
- Causa limeña — S/ 18
- Jalea mixta personal — S/ 25
- Choritos a la chalaca — S/ 20

**Ceviches**
- Ceviche clásico de pescado — S/ 28 — ⭐ el más pedido
- Ceviche mixto — S/ 34
- Ceviche de conchas negras — S/ 38
- Leche de tigre — S/ 15

**Platos de fondo**
- Arroz con mariscos — S/ 32
- Chicharrón de pescado — S/ 30
- Sudado de pescado — S/ 29
- Tacu tacu con mariscos — S/ 35 — 🌶️ opción picante

**Bebidas**
- Chicha morada (jarra) — S/ 12
- Limonada frozen — S/ 10
- Gaseosa personal — S/ 5
- Cerveza — S/ 10

**Postres**
- Suspiro a la limeña — S/ 12
- Mazamorra morada — S/ 10

> Todos los datos son ficticios y editables. Dejar claro en comentarios dónde se cambian.

---

## 6. Cómo publicar (GitHub Pages)

- Las páginas van en mi repositorio de GitHub Pages (uso usuario `leonardow33`, repo tipo `TARJETAS-NFC`).
- Rutas finales de prueba esperadas:
  - `https://leonardow33.github.io/TARJETAS-NFC/cartas-demo/estandar/`
  - `https://leonardow33.github.io/TARJETAS-NFC/cartas-demo/personalizada/`
- Que las rutas sean **relativas** para que funcionen dentro del subdirectorio del repo (no usar rutas absolutas tipo `/assets/...`).
- Imágenes: usar **placeholders** (colores o SVG inline) para no depender de archivos externos; dejar comentado cómo reemplazarlas por fotos reales del cliente.

---

## 7. Importante — un link por cliente

Cuando venda a un cliente real, cada uno necesita **su propia carpeta/carta** (ej. `/cartas/rikoriko/`), con su propio enlace para grabar en el NFC. Estas dos (`/cartas-demo/...`) son **solo muestras** para enseñar; no se graban en tarjetas vendidas.

Sugerencia para Claude Code: dejar una **plantilla base reutilizable** (la personalizada bien parametrizada) para que crear la carta de un cliente nuevo sea solo duplicar la carpeta y cambiar los datos.

---

## 8. Criterios de aceptación

- Abro cada link desde el celular y la carta se ve bien, rápida y legible.
- La **estándar** se ve claramente más simple; la **personalizada** se ve claramente más trabajada y a la marca — la diferencia de valor es obvia a simple vista.
- Ambas tienen los mismos platos y precios (solo cambia la presentación).
- Todo funciona en GitHub Pages con rutas relativas, sin dependencias externas que fallen.
- Quedan comentarios `<!-- EDITAR -->` donde se cambian nombre, platos, precios, colores y WhatsApp.
