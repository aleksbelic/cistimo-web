# Cistimo - Web App

- Small Web presentation project; mobile-first.
- Uses HTML, CSS & vanilla JavaScript; no framework included.
- Hosted by GitHub and deployed via GitHub Actions.
- Browser target: Chrome/Edge, Firefox and Safari from 2021 or newer. Use only HTML, CSS and JS features available in those versions — avoid newer CSS such as `text-wrap: balance`, `:has()`, CSS nesting, container queries and `subgrid`. `aspect-ratio` has an `@supports` fallback, and the JavaScript is plain ES5 with no build step.
- CSS uses physical properties (`width`, `margin-left`, `border-top`), not logical ones (`inline-size`, `margin-inline`, `border-block-start`), and no `inset` shorthand. Flex containers use margins instead of `gap`; grid `gap` is fine. All of these are missing from Safari 14.0 (iOS 14.0-14.4) and only arrived in 14.1.

## Layout and screen sizes

- All sizes are in `rem`, so the whole page scales with the root font size. On large screens the root size grows: 18 px from 1920 px wide, 24 px from 2560 px and 32 px from 3840 px (`@media (min-width: 120rem / 160rem / 240rem)` at the end of `style.css`), so the content does not look tiny on QHD and 4K monitors. From 1920 px the yellow call-to-action card is capped at `70rem`, the same width as the gallery.
- The hero uses `min-height: calc(100svh + 1px)` after a `100vh` fallback. Browsers without `svh` (before Safari 15.4, Chrome 108, Firefox 101) ignore the second line and keep `100vh`; newer mobile browsers do not resize the hero when the address bar hides.

## Visual effects

All effects are plain CSS plus a few lines of ES5 in `index.html`; there are no extra files (the SVGs are inlined as `data:` URIs in `style.css`). Every animation is switched off under `prefers-reduced-motion`.

- **Hero wave:** `.hero::before` draws a three-layer wave at the bottom of the hero. Its front layer is the page color `#fffdf7` (`--paper`); if that color changes, update the SVG too.
- **Hero bubbles:** `.hero__bubbles` holds three empty `<span>` layers (far, middle, near), each a soap-bubble SVG tile of 1280×1000 px that rises with `transform: translateY(-1000px)` at 130 s, 80 s and 52 s (parallax). The tile height and the keyframe distance must stay equal, otherwise the loop jumps. The whole layer has `opacity: 0.5`. Only `transform` is animated, so it stays cheap for the browser.
- **Footer wave:** `.site-footer::before`, the mirror of the hero wave. Its front layer (`#071d49`) must match the start color of the footer gradient (`--navy-800`). On 768 px and wider the footer is a two-column grid (logo and location left, contact icons right); below that it is a single stacked column.
- **Header:** two states, switched by the `is-scrolled` class. It becomes small as soon as the page scrolls down and large again once the page is within 40 px of the top (the direction is tracked so it does not flicker). Its height is `4.25rem` (not `px`) so it scales on large screens. The logo is nudged down with `translateY(0.22em)` because the swash of the **V** in Alex Brush rises above its line box and would be clipped otherwise.
- **Service cards:** each service has its own Lucide icon.
- **Scroll reveal:** service cards, the "Zašto baš mi?" cards and the gallery images fade in one after another when scrolled into view (`IntersectionObserver`, small stagger per row). A small script in `<head>` adds the `js-reveal` class only if the browser supports it, so without support or without JavaScript everything is simply visible. With `prefers-reduced-motion` the elements are visible immediately. To reveal another element, add its selector to the `.js-reveal ...` rules in `style.css` and to the `revealItems` query in the inline script.
- **Gallery:** images are intentionally not clickable (no lightbox, no hover zoom), so they do not look like links.
- **Keyboard focus:** `a:focus-visible` shows a gold outline, dark blue inside `.cta` (the yellow card), where gold would not be visible.

## Icons

- Using Inline SVG icons (including the six service icons), hand-written, simplified versions adapted from the [Lucide icon set](https://lucide.dev/icons), which is licensed under the ISC license (free for commercial use, no attribution required).
- styling through the `.icon` class using `currentColor`, so icons inherit their color from the surrounding text.

## Gallery images

- Full-size originals live in `img/original/`. Names follow a 5-character, mixed-case alphanumeric pattern with a lowercase `.jpg` extension (e.g. `q7Kp2.jpg`, `jUa41.jpg`).
- The page never loads the originals directly. It loads small thumbnails from `img/thumbs/<name>.jpg` (480×600, quality 80, ~49–73 KB each), which cuts the gallery payload from ~3.3 MB to ~0.7 MB. Originals are kept only as sources for regenerating thumbnails and are not published (see [Deploy](#deploy)).
- Thumbnail files are center-cropped to a uniform 480×600 frame. `.gallery__image` renders them at `aspect-ratio: 4/5` with `object-fit: cover`, so the cards keep the 4:5 shape regardless of the column width. `height: auto` is required here: the `height` attribute on the `<img>` tags is only an intrinsic-size hint and would otherwise override `aspect-ratio`, stretching the cards to 600 px tall. Every card is the same size with no letterbox bars. Sizes are kept small because there is no lightbox and the full-size images are never opened.
- Thumbnails are saved without EXIF, XMP, ICC, IPTC or comments.

## Fonts, icons and favicons

- The wordmark font is **Alex Brush**, self-hosted in `fonts/Alex_Brush/`: `AlexBrush-Regular.woff2` (~52 KB, primary) and `AlexBrush-Regular.ttf` (~115 KB, fallback). The `@font-face` rule lives in `style.css` and uses `font-display: swap`, so there are no external Google Fonts requests. The font is licensed under the SIL Open Font License — see `fonts/Alex_Brush/OFL.txt`.
- Favicons are `favicon.svg` (modern browsers) and `favicon.ico` at 48×48 (fallback for older browsers such as Safari 2021). Both use the Lucide `truck` icon in the site's navy/gold palette.
- All inline icons are simplified Lucide paths (ISC licensed).

## Contact and structured data

- The yellow call-to-action card has two buttons: the phone link (`tel:+381616648269`) and a Viber button (`viber://chat?number=%2B381616648269`). The Viber link has no web fallback, so it does nothing on a device without Viber. WhatsApp is intentionally not offered. Both buttons share the same hover effect (slightly larger text and a bigger shadow); the Viber button has a fixed `height` so the card does not grow on hover.
- The footer repeats the contact as two icon-only round buttons (phone and Viber); they have `aria-label`s because they have no visible text.
- `index.html` contains a `LocalBusiness` JSON-LD block (name, description, url, image, telephone, area served) for search engines. The gallery images have no hover effect, so they do not look clickable.
- The Alex Brush `.woff2` is preloaded in `<head>` to avoid a late font swap in the wordmark.

## Deploy

`.github/workflows/static.yml` publishes to GitHub Pages on every push to `main`. It does not upload the whole repository, only what the page loads: `index.html`, `style.css`, `favicon.ico`, `favicon.svg`, `robots.txt`, `sitemap.xml`, `fonts/`, `img/thumbs/`, `img/og-image.jpg` and `CNAME` (if present). `img/original/` (about 4 MB of full-size photos) stays out of the published site.

The workflow uses the Node 24 versions of the GitHub actions (`checkout@v6`, `configure-pages@v6`, `upload-pages-artifact@v5`, `deploy-pages@v5`), which removes the "Node.js 20 is deprecated" warning.

The workflow then checks that every file referenced from `index.html` and `style.css` is part of the published set and fails the deploy if one is missing. When the page starts using a new file or folder (a script, new images, another font), add it to the **Collect site files** step in the workflow.

## SEO files

- `sitemap.xml`: update `<lastmod>` to the publish date whenever the page content changes (format `YYYY-MM-DD`).
- `robots.txt`: search engines read it only from the root of a domain, so while the site is served from `https://abelic.com/cistimo-web/` this file has no effect there. Effective rules would have to live at `https://abelic.com/robots.txt`. It starts working normally once the site moves to `cistimo.rs` (see below).

## TODO: Custom domain

The site is currently served from `https://abelic.com/cistimo-web/`. When the new domain is bought, switch it to `https://cistimo.rs/` (root of the domain).

### DNS (at the registrar)

- A records for the apex domain `cistimo.rs` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`.
- CNAME record for `www.cistimo.rs` → `aleksbelic.github.io`.
- If apex A records are not supported, ask the registrar for ALIAS/ANAME/flattened CNAME pointing to the same IPs.

### GitHub

- Repository → Settings → Pages → Custom domain → `cistimo.rs` → Save.
- Add a `CNAME` file in the repository root containing a single line: `cistimo.rs`.
- After DNS propagates, enable **Enforce HTTPS** (GitHub issues a free certificate).
- Keep *Build and deployment → Source* set to **GitHub Actions**; the workflow needs no changes.

### Code changes (7 places)

- `index.html`: canonical link → `https://cistimo.rs/`.
- `index.html`: `og:url` and the `og:image` URL → `https://cistimo.rs/...`.
- `index.html`: the `url` and `image` values in the JSON-LD block → `https://cistimo.rs/...`.
- `sitemap.xml`: `<loc>` → `https://cistimo.rs/`.
- `robots.txt`: `Sitemap:` → `https://cistimo.rs/sitemap.xml`.
- `robots.txt`: remove the comment about the site being served under `/cistimo-web/` — on the new domain the site lives at the root, so `robots.txt` and `sitemap.xml` work normally.

`og:title`, `og:description`, `og:image:width`, `og:image:height`, `og:image:alt` and `twitter:card` need no change.

Asset paths are relative (`img/thumbs/...`, `style.css`), so they work both on a subpath and at the domain root.

### SEO

- Set up a 301 redirect from the old URL `https://abelic.com/cistimo-web/` to `https://cistimo.rs/` at the old host, otherwise search engines will see two copies of the same site.
