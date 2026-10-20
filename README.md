# Aureq — sitio web

Landing page de una sola página para Aureq, construida con Astro y CSS puro (metodología BEM). Sin frameworks de UI, sin dependencias de runtime y sin CSS-in-JS: HTML estático y una única hoja de estilos.

## Requisitos

- Node.js 22.22 o superior
- pnpm 9

## Comandos

```bash
pnpm install      # instalar dependencias
pnpm dev          # servidor de desarrollo en http://localhost:4321
pnpm build        # generar el sitio estático en dist/
pnpm preview      # servir dist/ para revisar el build
```

En Windows, si PowerShell bloquea el shim de pnpm por la política de ejecución, usá `cmd /c "pnpm dev"` (o ejecutá `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` una sola vez).

## Estructura

```
public/
  aureq-logo.svg            logo completo de Aureq (blanco, SVG)
  aureq-logo-mark.svg       versión corta "Aq", usada en la intro
  favicon.svg               favicon
  robots.txt
  projects/                 capturas de los proyectos del portfolio
src/
  layouts/BaseLayout.astro  <head>, fuentes, metadatos y skip link
  pages/index.astro         composición de la página y SEO
  components/               una sección por componente
  styles/global.css         todos los estilos (tokens + bloques BEM)
```

## Cómo editar el contenido

Cada sección guarda sus datos en el frontmatter del propio componente, como un array. No hay archivo central de datos: si querés cambiar un texto, lo cambiás en el componente que lo muestra.

| Qué querés cambiar | Archivo |
| --- | --- |
| Textos del hero y botones | `src/components/Hero.astro` |
| Tarjetas de beneficios | `src/components/Value.astro` |
| Pasos del proceso | `src/components/Process.astro` |
| Lista de funcionalidades incluidas | `src/components/Features.astro` |
| Planes y precios | `src/components/Pricing.astro` |
| Proyectos del portfolio | `src/components/Portfolio.astro` |
| Preguntas frecuentes | `src/components/Faq.astro` |
| Navegación y menú móvil | `src/components/Header.astro` |
| Columnas y tarjeta final del footer | `src/components/Footer.astro` |
| Número de WhatsApp | `src/components/Contact.astro` |
| Agenda de Cal.com (link y embed) | `src/components/Contact.astro` |
| Título y descripción para SEO | `src/pages/index.astro` |
| Pantalla de entrada (intro) | `src/components/Intro.astro` |

### Número de WhatsApp

Está en `src/components/Contact.astro`, en la constante `whatsappUrl`:

```js
const whatsappUrl =
  'https://wa.me/5491124012037?text=Hola%20Aureq%2C%20quiero%20consultar%20por%20una%20p%C3%A1gina%20web.';
```

Cambiá `5491124012037` por el número destino (formato internacional, sin `+` ni espacios) y el parámetro `text` por el mensaje precargado.

### Portfolio

Para reemplazar un proyecto, guardá la captura en `public/projects/` (JPEG, 900×605 idealmente) y actualizá el objeto correspondiente en `Portfolio.astro`: `image`, `alt`, `domain`, `title`, `text`, `tags` y `href`.

## Agenda de reuniones

La sección `#contacto` incluye un calendario de Cal.com embebido, además del botón de WhatsApp.

- **Sin backend**: la reserva, la disponibilidad y los recordatorios los resuelve Cal.com. El sitio sigue siendo 100% estático y se puede subir a cualquier hosting.
- **Link de la agenda**: `emiliano-dambrosio/30min`. Aparece en tres lugares de `Contact.astro`: `calPageUrl`, el `calLink` del embed y el fallback. Si cambiás el evento en Cal.com, actualizá los tres.
- **Carga diferida**: el script de Cal.com se inyecta recién cuando la sección está a 400px de entrar en pantalla (IntersectionObserver), así no pesa en la carga inicial.
- **Desde 899px hacia abajo no se embebe**: el widget necesita ~900px para mostrar calendario y horarios en dos columnas. En mobile y tablet se muestra el botón "Ver horarios disponibles", que abre la agenda en Cal.com (su página está optimizada para celular).
- **Tema**: `dark` con `cssVarsPerTheme`; el color de marca se controla en `cal-brand` (hoy `#f8f8f8` en dark) dentro del bloque `ui`.
- **El badge "Cal.com"** del pie del widget lo agrega el plan gratuito y no se puede ocultar desde el código.

## Pantalla de entrada

`src/components/Intro.astro` monta una capa negra a pantalla completa con el logo corto (`aureq-logo-mark.svg`). El logo parpadea dos veces —como un tubo de gas que arranca— y queda encendido. Al primer scroll (o click, o tecla) la capa se desvanece y deja ver el sitio.

- **Animación**: GSAP. La línea de tiempo está en el `<script>` del componente; los tiempos del parpadeo se editan ahí.
- **Composición áurea**: el logo ocupa 44.72% del ancho y el aire de cada lateral 27.64%, así que `logo / margen = 1.618`. En el eje vertical queda centrado con aire mayor: con un logo de proporción fija no se pueden sostener las dos razones al mismo tiempo.
- **Cambiar la imagen**: reemplazá `public/aureq-logo-mark.svg`. Si cambia la proporción, actualizá `--intro-logo-ratio` en `global.css` (ancho / alto del viewport del SVG).
- **Quitar la intro**: eliminá `<Intro />` de `src/pages/index.astro`.
- **Accesibilidad**: con `prefers-reduced-motion: reduce` no hay parpadeo, el logo aparece directo. Sin JavaScript la capa no se muestra (`<noscript>`).

## Estilos

Todo vive en `src/styles/global.css`, en este orden:

1. Tokens en `:root` (colores, radios, transiciones, ancho de contenedor).
2. Reset y estilos base.
3. Utilidades: `.container`, `.sr-only`, `.skip-link`, `.button`.
4. Un bloque BEM por sección (`hero`, `value`, `process`, `tech`, `features`, `pricing`, `portfolio`, `faq`, `contact`, `footer`).

Los nombres siguen BEM: `bloque__elemento--modificador`. Los media queries están al final de cada bloque, no en un archivo aparte.

### Paleta

| Token | Valor | Uso |
| --- | --- | --- |
| `--color-bg` | `#08090a` | Fondo general |
| `--color-bg-soft` | `#111216` | Secciones alternas y tarjetas |
| `--color-border` | `#22252c` | Bordes |
| `--color-accent` | `#0052ff` | Acciones y detalles |
| `--color-text` | `#e8eaee` | Texto principal |
| `--color-muted` | `#9aa1ad` | Texto secundario |

## Deploy

`pnpm build` genera un sitio estático completo en `dist/`. Se puede publicar en cualquier hosting de archivos estáticos (Netlify, Vercel, Cloudflare Pages, un VPS con nginx). No requiere servidor Node.
