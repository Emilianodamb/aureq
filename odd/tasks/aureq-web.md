# aureq-web — transformar la plantilla de Stitch en un sitio Astro

Objetivo: convertir el diseño de `plantilla_stitch` (`code.html` + `screen.png`) en una landing de una sola página, limpia y mantenible, con Astro y CSS BEM, en un proyecto separado del sitio anterior.

Stack: Astro 7 + CSS puro. Runner: `pnpm build` / `pnpm dev`.

## Specs

- **S1 — Fidelidad visual**: replicar el diseño de Stitch sobre fondo `#08090a`, superficie `#111216`, borde `#22252c`, acento `#0052ff`, texto `#e8eaee` y muted `#9aa1ad`. Tipografía Plus Jakarta Sans (400–800). Sin `Playfair Display`: el diseño real usa el logo de Aureq.
- **S2 — Secciones**: header sticky con menú móvil, hero, beneficios (4 tarjetas), proceso (4 pasos), franja tecnológica, funcionalidades (8 ítems + mock de navegador), precios (3 planes), portfolio (2 proyectos reales), FAQ (6 preguntas, la primera abierta), contacto y footer.
- **S3 — Contenido**: hero `"DISEÑO WEB PARA NEGOCIOS Y PROFESIONALES"`; planes `$249.999`, `$399.999` y presupuesto a medida; CTA de contacto exclusivamente por WhatsApp `+54 9 11 2401-2037`.
- **S4 — Sin enlaces muertos**: toda navegación apunta a anclas existentes (`#inicio`, `#como-trabajamos`, `#precios`, `#preguntas`, `#contacto`). Se eliminaron los íconos sociales y los enlaces de políticas que no tenían destino.
- **S5 — Portfolio real**: capturas de `totalmecanica.com.ar` y `ayelenargerich.com` en `public/projects/`, con `loading="lazy"` y enlaces externos con `rel="noopener noreferrer"`.
- **S6 — Responsive**: sin scroll horizontal en 390 px y 1440 px; menú móvil accesible por teclado (Escape cierra y devuelve el foco); respeto de `prefers-reduced-motion`.

## Tasks

- [x] T1 — Scaffold Astro, tokens y `global.css` (S1)
- [x] T2 — Header + menú móvil (S2, S6)
- [x] T3 — Hero, beneficios, proceso, franja tech (S2, S3)
- [x] T4 — Funcionalidades, precios, portfolio, FAQ (S2, S3, S5)
- [x] T5 — Contacto y footer (S2, S3, S4)
- [x] T6 — Verificación responsive y de enlaces (S4, S6)

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
