# aureq-web — transformar la plantilla de Stitch en un sitio Astro

Objetivo: convertir el diseño de `plantilla_stitch` (`code.html` + `screen.png`) en una landing de una sola página, limpia y mantenible, con Astro y CSS BEM, en un proyecto separado del sitio anterior.

Stack: Astro 7 + CSS puro. Runner: `pnpm build` / `pnpm dev`.

## Specs

- **S1 — Fidelidad visual**: replicar el diseño de Stitch sobre fondo `#08090a`, superficie `#111216`, borde `#22252c`, acento `#0052ff`, texto `#e8eaee` y muted `#9aa1ad`. Tipografía Noto Sans (400–800) para el cuerpo y BIZ UDPGothic (400/700) para los títulos (S9). Sin `Playfair Display`: el diseño real usa el logo de Aureq.
- **S2 — Secciones**: header sticky con menú móvil, hero, beneficios (4 tarjetas), proceso (4 pasos), franja tecnológica, funcionalidades (8 ítems + mock de navegador), precios (3 planes), portfolio (2 proyectos reales), FAQ (6 preguntas, la primera abierta), contacto y footer.
- **S3 — Contenido**: hero `"DISEÑO WEB PARA NEGOCIOS Y PROFESIONALES"`; planes `$249.999`, `$399.999` y presupuesto a medida; CTA de contacto exclusivamente por WhatsApp `+54 9 11 2401-2037`.
- **S4 — Sin enlaces muertos**: toda navegación apunta a anclas existentes (`#inicio`, `#como-trabajamos`, `#precios`, `#preguntas`, `#contacto`). Se eliminaron los íconos sociales y los enlaces de políticas que no tenían destino.
- **S5 — Portfolio real**: capturas de `totalmecanica.com.ar` y `ayelenargerich.com` en `public/projects/`, con `loading="lazy"` y enlaces externos con `rel="noopener noreferrer"`.
- **S6 — Responsive**: sin scroll horizontal en 390 px y 1440 px; menú móvil accesible por teclado (Escape cierra y devuelve el foco); respeto de `prefers-reduced-motion`.
- **S7 — Intro con el logo**: al cargar, capa negra a pantalla completa con el logo corto centrado, respetando la proporción áurea entre el logo y los laterales (logo / margen = 1.618). El logo parpadea dos veces como un tubo de gas y queda encendido. Al hacer scroll la capa se desvanece y muestra el sitio. Con `prefers-reduced-motion` no hay parpadeo; sin JavaScript la capa no aparece.
- **S8 — Agenda sin backend**: la sección de contacto ofrece reservar 30 minutos contra el Cal.com del cliente (`emiliano-dambrosio/30min`) sin agregar servidor. El embed se inyecta por IntersectionObserver a 400px de la sección y no bloquea la carga inicial. Desde 899px hacia abajo el widget no se embebe (necesita ~900px para las dos columnas) y se ofrece un botón que abre la agenda en Cal.com.
- **S9 — Jerarquía tipográfica**: títulos en BIZ UDPGothic 700 y cuerpo en Noto Sans; H1 en mayúsculas (el hero ya las tenía) y H2–H6 en sentence case tal como están escritos en el contenido. No se aplica `text-transform: lowercase` global porque destruiría nombres propios y siglas (Aureq, Total Mecánica, Ayelén Argerich, SEO, WhatsApp).

## Tasks

- [x] T1 — Scaffold Astro, tokens y `global.css` (S1)
- [x] T2 — Header + menú móvil (S2, S6)
- [x] T3 — Hero, beneficios, proceso, franja tech (S2, S3)
- [x] T4 — Funcionalidades, precios, portfolio, FAQ (S2, S3, S5)
- [x] T5 — Contacto y footer (S2, S3, S4)
- [x] T6 — Verificación responsive y de enlaces (S4, S6)
- [x] T7 — Intro con GSAP: logo, parpadeo y salida al scroll (S7)
- [x] T8 — Agenda de Cal.com embebida con carga diferida y fallback mobile (S8)
- [x] T9 — Cambio de tipografía: BIZ UDPGothic en títulos, Noto Sans en cuerpo (S9)

## Log

### L1 — Pedido original

> "En la carpeta /plantilla_stitch hay un código y una imagen. Corresponden a un diseño que hice en stitch. Necesito que tomes como referencia ese código y lo transformes a un sitio web base desarrollado..."

*(Recuperado truncado del registro de memoria del 2026-10-20.)*

### L2 — Decisiones de implementación

- Proyecto nuevo en `aureq-web/`; el sitio anterior (`Sitio Web/`, oscuro con dorado) queda intacto.
- Contenido en el frontmatter de cada componente en lugar de un archivo de datos central: menos indirección para una sola página.
- El logo se toma del sitio anterior (`public/aureq-logo.svg`) y se descarta Playfair Display.
- Se eliminó todo destino inexistente (redes, políticas) en vez de dejar `href="#"`.
- Las capturas del portfolio son reales y locales, no placeholders remotos.

### L3 — Correcciones

- Desborde horizontal en móvil por `min-width: auto` en ítems de grid → `.hero__inner > * { min-width: 0 }`.
- Imagen del portfolio con alto incorrecto (289 px en vez de 224) → `position: absolute; inset: 0` en la imagen y sin `place-items: center` en el contenedor.
- Menú móvil sin affordance de cierre → icono X y etiqueta "Cerrar menú" al abrirse, fondo opaco y sombra.
- Espacio muerto en el hero → menos padding inferior y `min-height` del escenario ajustado por breakpoint.

### L4 — Evidencia

- `pnpm build` completo, 1 página, sin errores.
- 390 px y 1440 px: `document.documentElement.scrollWidth` igual al ancho del viewport.
- Todas las anclas internas resuelven a un elemento existente.
- Primera pregunta del FAQ abierta por defecto (142 px contra 59 px cerrada).

### L5 — Intro animada (2026-10-20)

- Se instaló `gsap@3.15.0` y se creó `src/components/Intro.astro`, renderizado antes del header en `index.astro`.
- Logo: se usa la versión corta `Aq logo blanco` (copiada como `public/aureq-logo-mark.svg`, viewBox 1249.34 × 979.36) en lugar del logo completo, porque es la versión pensada para marca pequeña.
- Composición: logo = 44.72% del ancho y márgenes de 27.64%, así `logo / margen = 1.618`. Se usa `%` y no `vw` para que el scrollbar no descuadre la razón.
- Secuencia: dos ráfagas de parpadeo (opacidad 0 → .55 → .08 → .88 → .24 → 1, y una segunda corta), con `scale` y `drop-shadow` asentándose. La capa se puede retirar a los 0.78 s, sin esperar el brillo final.
- Salida: `wheel`, `touchmove`, teclas de navegación o click. La capa se desvanece en 0.6 s y se elimina del DOM.
- Sin bloqueo de scroll a propósito: la página se mueve detrás de la capa y no hay salto de layout al retirarla.
- Evidencia: razón medida 0.619 (1440) y 0.621 (390); opacidades muestreadas muestran el parpadeo; con `prefers-reduced-motion` la opacidad es 1 desde el inicio; el scroll retira la capa y mueve la página.

### L6 — Agenda de Cal.com (2026-10-20)

- El pedido era agendar reuniones sin contratar un servidor Node. Se resolvió con el embed oficial de Cal.com: la reserva y la disponibilidad viven en el SaaS, el sitio sigue siendo estático.
- Se integró en `#contacto` como bloque `.contact__calendar` (BEM, dentro de `Contact.astro`), no como sección nueva, para no duplicar llamados a la acción.
- Se respetó el snippet del cliente (namespace `30min`, `calLink` `emiliano-dambrosio/30min`, `theme: dark`) y se le agregaron tres cosas: carga diferida, altura estable y fallback.
- Carga diferida con `IntersectionObserver` (`rootMargin: 400px`): antes de acercarse a la sección no se descarga nada de `app.cal.com`.
- El iframe de Cal se autodimensiona: fija `min-height` en el contenedor y deja `overflow: visible`. Con `overflow: hidden` se recortaban 50px del widget; con `height` fijo, Cal quedaba más alto que la caja.
- **Mobile**: el widget necesita ~900px para mostrar calendario y horarios en dos columnas; abajo de eso apila los horarios fuera de la caja visible y obliga a un scroll anidado. Se decidió no embeber por debajo de 899px y mostrar un botón que abre la agenda en Cal.com.
- Evidencia: desktop 1440 → frame 896×610, `cal-inline` 894×608, calendario y horarios visibles, 0 errores de consola; mobile 390 y tablet 820 → frame oculto, CTA visible, `calLoaded: false` (cero requests a `app.cal.com`); `overflowX` 0 en los tres anchos.

### L7 — Tipografía BIZ UDPGothic + Noto Sans (2026-10-21)

- Prueba de diseño pedida por el cliente: títulos geométricos (BIZ UDPGothic, inspirada en la señalética japonesa) y cuerpo neutro y legible (Noto Sans), en reemplazo de Urbanist.
- Se agregó `--font-heading` en `:root` y una regla base `h1…h6 { font-family: var(--font-heading); font-weight: 700 }`; `--font-sans` pasa a Noto Sans. El cuerpo hereda `--font-sans` desde `body`, así que no hubo que tocar los estilos de párrafos.
- **Pesos**: BIZ UDPGothic solo distribuye 400 y 700, así que los títulos que estaban en 800 (`.hero__title`, `.section-heading__title`, `.features__title`, `.contact__title`) bajaron a 700 para evitar negrita sintética. `.pricing__price` sigue en 800 porque usa Noto Sans.
- **Mayúsculas**: el H1 ya era `text-transform: uppercase` en `.hero__title`; se subió a la regla `h1` para que sea la norma. Los H2/H3 ya están escritos en sentence case en el contenido, así que no se forzó `lowercase`: rompería "Aureq", "Total Mecánica", "SEO", "WhatsApp".
- Google Fonts carga solo el subset latino con `display=swap` (`BIZ+UDPGothic:wght@400;700` + `Noto+Sans:wght@400..800`).
- Evidencia: `getComputedStyle` en dev → h1/h2 `"BIZ UDPGothic"` 700 uppercase, body `"Noto Sans"`, `.pricing__price` Noto 800; `document.fonts.load('700 56px "BIZ UDPGothic"')` devuelve 3 caras y `measureText` da 370 px contra 312 (Noto) y 311 (Arial), o sea la fuente se descarga y se pinta. `overflowX` 0 en 1440, 900, 390 y 320; H1 de 106 px (3 líneas) en mobile sin recortes.

### L8 — Estrategia de carga de imágenes (2026-10-21)

- **Logo del header**: NO se difiere. Es el primer pintado y candidato a LCP; `loading="lazy"` lo retrasaría y dejaría el hueco del logo vacío. Va con `fetchpriority="high"` + `decoding="async"`, que es el default eager pero explícito y priorizado.
- **Logo del footer**: `loading="lazy"` + `decoding="async"`. Es el mismo URL que el del header, así que ya está en caché: no agrega requests, solo evita que compita por decodificación.
- **Portfolio**: ya tenía `loading="lazy"` (S5); se sumó `decoding="async"`. Verificado: las dos imágenes (65 KB cada una) no están cargadas al tope de la página y sí después de scrollear.
- **Calendario**: no hay `<iframe>` propio que diferir. El embed lo crea el script de Cal.com dentro de `#my-cal-inline-30min`, y el `<script>` que lo pide solo corre cuando el `IntersectionObserver` (400 px) detecta la sección. Medido: 0 iframes al cargar, 1 al acercarse. `loading="lazy"` no aplica porque ese atributo solo existe en `<img>`/`<iframe>` escritos por nosotros.
- **Ancla del logo**: el `<img>` está envuelto en `<a href="#inicio">` y `#inicio` es el `<section class="hero">`. No se le puso `id="inicio"` al `<img>`: duplicaría el id y haría ambiguo el ancla. Medido: click desde el fondo → `scrollY` 6700 → 28, `location.hash` `#inicio`, un solo elemento con ese id.
