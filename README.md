# Cistimo - Web App

- Small Web presentation project; mobile-first.
- Uses HTML, CSS & vanilla JavaScript; no framework included.
- Hosted by GitHub and deployed via GitHub Actions.
- Browser target: Chrome/Edge, Firefox and Safari from 2021 or newer. Use only HTML, CSS and JS features available in those versions — avoid newer CSS such as `text-wrap: balance`, `:has()`, CSS nesting, container queries and `subgrid`. `aspect-ratio` has an `@supports` fallback, and the JavaScript is plain ES5 with no build step.

## Icons

- Using Inline SVG icons, hand-written, simplified versions adapted from the [Lucide icon set](https://lucide.dev/icons), which is licensed under the ISC license (free for commercial use, no attribution required).
- styling through the `.icon` class using `currentColor`, so icons inherit their color from the surrounding text.

## Gallery images

- Full-size originals live in `img/original/`. Names follow a 5-character, mixed-case alphanumeric pattern with a lowercase `.jpg` extension (e.g. `q7Kp2.jpg`, `jUa41.jpg`).
- The page never loads the originals directly. It loads small thumbnails from `img/thumbs/<name>.jpg` (480×600, quality 80, ~49–73 KB each), which cuts the gallery payload from ~3.3 MB to ~0.7 MB. Originals are kept only as sources for regenerating thumbnails.
- Thumbnail files are center-cropped to a uniform 480×600 frame. `.gallery__image` renders them at `aspect-ratio: 4/5` with `object-fit: cover`, so the cards keep the 4:5 shape regardless of the column width. `height: auto` is required here: the `height` attribute on the `<img>` tags is only an intrinsic-size hint and would otherwise override `aspect-ratio`, stretching the cards to 600 px tall. Every card is the same size with no letterbox bars. Sizes are kept small because there is no lightbox and the full-size images are never opened.
- Thumbnails are saved without EXIF, XMP, ICC, IPTC or comments.

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

### Code changes (4 places)

- `index.html`: canonical link → `https://cistimo.rs/`.
- `sitemap.xml`: `<loc>` → `https://cistimo.rs/`.
- `robots.txt`: `Sitemap:` → `https://cistimo.rs/sitemap.xml`.
- `robots.txt`: remove the comment about the site being served under `/cistimo-web/` — on the new domain the site lives at the root, so `robots.txt` and `sitemap.xml` work normally.

Asset paths are relative (`img/thumbs/...`, `style.css`), so they work both on a subpath and at the domain root.

### SEO

- Set up a 301 redirect from the old URL `https://abelic.com/cistimo-web/` to `https://cistimo.rs/` at the old host, otherwise search engines will see two copies of the same site.
