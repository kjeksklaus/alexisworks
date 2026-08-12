# AlexisWorks — Architecture

## Purpose

A single-page static landing site for **AlexisWorks** indie apps. Visitors see a hero banner, a grid of app tiles, an about section, and contact details. The site is designed to deploy to **GitHub Pages** with zero build steps.

## Tech stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Markup | HTML5 | `index.html` is the homepage; per-app legal pages live under `apps/` |
| Styles | CSS (+ SCSS source) | Precompiled `assets/css/main.css` is what the browser loads |
| Scripts | jQuery 3 + HTML5 UP plugins | Menu, scroll, tile background setup |
| Icons | Font Awesome 5 | Bundled in `assets/css/fontawesome-all.min.css` |
| Fonts | Source Sans Pro | Loaded from Google Fonts via `main.scss` |
| Hosting | GitHub Pages (planned) | Static files at repo root |

No Node.js, npm, bundler, or CMS.

## Directory structure

```
AlexisWorksSite/
├── index.html              # Live site — edit this for all content changes
├── landing.html            # Unused HTML5 UP demo (reference only)
├── generic.html            # Unused HTML5 UP demo (reference only)
├── elements.html           # Unused HTML5 UP demo (reference only)
├── ARCHITECTURE.md         # This file
├── DECISIONS.md            # Rationale and pending work
├── .cursorrules            # AI/editor guidance
├── apps/
│   ├── dockless/
│   │   ├── privacy.html
│   │   └── terms.html
│   ├── biotic-waves/
│   │   ├── privacy.html
│   │   └── terms.html
│   └── maconzo/            # Kept for later; not on homepage while delayed
│       ├── privacy.html
│       └── terms.html
├── images/
│   ├── banner.jpg          # Hero background (CSS-referenced)
│   ├── dockless-icon.png
│   ├── dockless-screenshot.png
│   ├── biotic-waves-icon.png
│   ├── maconzo-icon.png
│   ├── maconzo-screenshot.png
│   └── pic01.jpg … pic06.jpg  # Placeholder assets for remaining apps
└── assets/
    ├── css/
    │   ├── main.css        # Primary stylesheet (browser loads this)
    │   ├── noscript.css
    │   └── fontawesome-all.min.css
    ├── js/
    │   ├── main.js         # Site init: menu, banner, tiles, scroll
    │   ├── util.js
    │   ├── jquery.min.js
    │   ├── jquery.scrolly.min.js
    │   ├── jquery.scrollex.min.js
    │   ├── browser.min.js
    │   └── breakpoints.min.js
    ├── sass/               # SCSS source (optional recompile)
    │   ├── main.scss
    │   ├── components/_tiles.scss   # App tile grid (customized)
    │   ├── layout/ …
    │   └── libs/_vars.scss          # Accent colors, palette
    └── webfonts/           # Font Awesome font files
```

## Page structure (`index.html`)

```
┌─────────────────────────────────────┐
│  #header — logo + hamburger menu    │
├─────────────────────────────────────┤
│  #banner — hero (AlexisWorks)       │
├─────────────────────────────────────┤
│  #main                              │
│    #one  — .tiles (6 app cards)     │
│    #two  — About AlexisWorks        │
├─────────────────────────────────────┤
│  #contact — email + location        │
├─────────────────────────────────────┤
│  #footer — GitHub icon + copyright  │
└─────────────────────────────────────┘
```

Navigation uses in-page anchors: `#one`, `#two`, `#contact`. Smooth scrolling is handled by jQuery Scrolly.

## How components relate

### App tiles (`#one .tiles`)

Each `<article>` is one app card:

1. **Hidden `.image img`** — `main.js` reads this `src` and sets it as the tile's CSS `background-image` for the hover-reveal effect.
2. **`.app-icon`** — Small visible icon (separate file from background).
3. **`.tile-content`** — Dark semi-opaque panel ensuring text readability over any background.
4. **`.app-features`** — Bullet list of key features.
5. **Optional `.tile-legal`** — Privacy/Terms links below the content panel (only when policy pages exist).
6. **Optional `.tile-{name}`** — Overrides accent color (e.g. `.tile-maconzo` uses red `#c62828`).

Tile CSS lives in `assets/sass/components/_tiles.scss` and is mirrored in `assets/css/main.css`.

### Tile background layers (bottom to top)

```
z-index 1  :after   — dark tint
z-index 2  :before  — accent color overlay (fades on hover)
z-index 3  header / .tile-legal — content panel and legal links
```

On hover, `:before` opacity drops to 0, revealing the screenshot background.

### JavaScript (`assets/js/main.js`)

| Feature | Behavior |
|---------|----------|
| Tiles | Sets `background-image` from hidden img; attaches click handler only if `.link` exists |
| Menu | Slide-out panel toggle |
| Banner | Parallax / scroll effects on hero |
| Scrolly | Smooth anchor scrolling |

AlexisWorks tiles intentionally omit `.link` — tiles are informational, not navigational.

### Styles

- **Source of truth (ideal):** `assets/sass/` compiled via `sass assets/sass/main.scss assets/css/main.css`
- **Practical workflow:** Edit both SCSS and `main.css` when changing styles, since no compiler is bundled in the repo.

Accent colors for tiles 1–6 cycle through six palette values defined in `assets/sass/libs/_vars.scss`. Per-app overrides use article classes (see `.tile-maconzo`).

## GitHub Pages deployment

**Target:** Serve the repository root as a static site.

### Setup (when ready)

1. Initialize git repo and push to GitHub.
2. In repository Settings → Pages, set source to **Deploy from branch** → `main` → `/ (root)`.
3. Site will be available at `https://<user>.github.io/<repo>/` (or custom domain if configured).

### Deployment workflow

```
Edit index.html / images / CSS
        ↓
git add → git commit → git push
        ↓
GitHub Pages serves updated static files (no build)
```

No CI/CD required. Optionally add a `.nojekyll` file at root if GitHub Pages Jekyll processing causes issues with paths starting with `_` (not currently needed).

## Update workflows

### Add or update an app tile

1. Add `{app}-icon.png` and `{app}-screenshot.png` to `images/`.
2. Uncomment a placeholder `<article>` in `index.html` (inside the `<!-- Placeholder tiles -->` block) or duplicate the Maconzo tile structure.
3. Edit the `<article>`:
   - Screenshot → hidden `.image img`
   - Icon → `.app-icon`
   - Name, features → `h3` and `.app-features`
4. Optionally add `.tile-{appname}` class and a matching accent color rule in `_tiles.scss` + `main.css`.
5. Add `.tile-legal` links when privacy/terms pages exist.

Only Dockless and Biotic Waves are live on the homepage; remaining placeholder tiles stay commented out until those apps ship. Maconzo assets and legal pages remain in the repo for a later restore. With two tiles active, the equal 3-column grid applies (a single tile would center via `article:only-child`).

### Add legal pages for an app

Required for Google OAuth and App Store review. Pattern: one folder per app.

1. Create `apps/{appname}/privacy.html` and `apps/{appname}/terms.html`.
2. Copy structure from `apps/maconzo/` — use `../../assets/` and `../../index.html` for relative paths.
3. Add `.tile-legal` nav to the app's `<article>` in `index.html`:

```html
<nav class="tile-legal" aria-label="{App} legal">
  <a href="apps/{appname}/privacy.html">Privacy</a>
  <a href="apps/{appname}/terms.html">Terms</a>
</nav>
```

Only add links when both policy pages exist. Placeholder apps 2–6 have no legal links yet.

### Change site copy or contact info

Edit sections in `index.html` directly (`#banner`, `#two`, `#contact`, `#footer`).

### Change tile layout or readability

Edit `assets/sass/components/_tiles.scss` and apply the same changes to `assets/css/main.css`.

### Change global colors or typography

Edit `assets/sass/libs/_vars.scss` and recompile or mirror to `main.css`.

## What is intentionally out of scope

- Multi-page app marketing sites (`landing.html` unused)
- Per-app legal pages (`apps/{appname}/`) are in scope
- Contact form backend
- Analytics, CMS, or dynamic content
- npm/build toolchain

See `DECISIONS.md` for rationale and known gaps.
