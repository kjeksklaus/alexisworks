# AlexisWorks — Decisions & Technical Debt

This document records **why** key choices were made and what remains unfinished. Update it when direction changes.

---

## Strategic decisions

### Single-page landing (`index.html` only)

**Decision:** One scrollable homepage with anchor navigation. No per-app detail pages.

**Why:**
- Goal is a simple app showcase, not a full marketing site.
- Faster to maintain — all content in one file.
- Ideal for GitHub Pages (no routing configuration).
- HTML5 UP's `landing.html` template is kept in repo as reference but excluded from nav.

**Trade-off:** Long-form app descriptions or App Store embeds would require either expanding tile content or adding new pages later.

**Update:** Per-app **Privacy** and **Terms** pages are an exception — they live at `apps/{appname}/privacy.html` and `apps/{appname}/terms.html`, linked from the app tile via `.tile-legal`. The homepage remains the primary entry point.

---

### Per-app legal pages (`apps/{appname}/`)

**Decision:** Each app with OAuth or store review requirements gets its own Privacy Policy and Terms of Service as standalone static HTML pages.

**Why:**
- Google OAuth and Apple App Review require clear, publicly accessible policy URLs.
- Tile links (`Privacy` / `Terms`) on the homepage satisfy discoverability without cluttering other tiles.
- Static HTML per app keeps policies independent and easy to update.

**Rules:**
- Add `.tile-legal` links only when both policy pages exist for that app.
- Maconzo is the first implementation (`apps/maconzo/`).
- Use relative paths (`../../assets/`) so GitHub Pages serves pages correctly from subfolders.

---

### Forty HTML5 UP template as foundation

**Decision:** Customize the existing **Forty** static template rather than building from scratch or adopting a framework.

**Why:**
- Professional responsive layout out of the box (hero, tiles, menu, contact, footer).
- CCA 3.0 license allows commercial use with attribution (kept in footer).
- No build tooling required — matches GitHub Pages static hosting.
- Tile grid maps naturally to an app portfolio.

**Trade-off:** Inherited jQuery dependency and demo HTML files we do not use. Template branding and Latin placeholder copy required cleanup.

---

### No build pipeline / no framework

**Decision:** Ship precompiled CSS and vanilla HTML. No React, Vue, Astro, or npm scripts.

**Why:**
- GitHub Pages serves files as-is — zero deploy complexity.
- Site is small enough that a framework adds more overhead than value.
- Editable by anyone with a text editor; no `npm install` step.

**Trade-off:** SCSS source (`assets/sass/`) can drift from compiled `assets/css/main.css` if both are not updated together. See technical debt below.

---

### Equal-size app tiles (3×2 grid)

**Decision:** All six tiles use `width: calc(100% / 3)` on desktop. Removed the template's asymmetric 40%/60% layout.

**Why:**
- No app should appear more prominent than another.
- Uniform cards provide room for icon + title + bullet features.

**Trade-off:** Less visual drama than the original Forty masonry-style grid.

---

### Separate icon and screenshot per app

**Decision:** Each tile uses two images — `.app-icon` (small, visible) and a hidden screenshot (hover background only).

**Why:**
- Using the app icon as the tile background (Maconzo v1) caused illegible text — the chip graphic competed with white copy.
- Screenshots on hover give context without cluttering the default view.
- `.tile-content` dark panel ensures readability on any background.

**Rule enforced in `.cursorrules`:** Never set the icon as the tile background image.

---

### Display-only tiles (no links)

**Decision:** Tile titles are plain `<h3>` without `class="link"`.

**Why:**
- Single-page site — no internal detail pages to link to.
- Avoids the template's full-screen blur/redirect click animation.
- App Store links can be added later by restoring `.link` with `target="_blank"`.

---

### Maconzo brand accent (`.tile-maconzo`)

**Decision:** First app tile uses custom red accent `#c62828` instead of the default blue (accent1).

**Why:**
- Matches Maconzo app icon branding.
- Keeps tile appearance consistent with solid-color overlays on other tiles.

**Pattern for future apps:** Add `class="tile-{appname}"` + a `:before` background-color override after the nth-child accent rules.

---

### Simplified contact section

**Decision:** Removed the contact form. Kept email and location only.

**Why:**
- Form posted to `#` with no backend — misleading to visitors.
- Static GitHub Pages cannot process form submissions without a third-party service.
- Email link is sufficient for a basic landing page.

---

## GitHub Pages

**Decision:** Target deployment platform is GitHub Pages from repository root.

**Status:** Not yet configured — no git repository or Pages settings detected in project.

**When deploying:**
- Push `index.html`, `assets/`, and `images/` to `main`.
- Enable Pages on root branch.
- Add real GitHub URL to footer icon (currently `href="#"`).
- Consider `.nojekyll` if needed; custom domain optional.

---

## Pending tasks

| Item | Priority | Notes |
|------|----------|-------|
| Initialize git repo and enable GitHub Pages | High | Required for deployment |
| Uncomment and customize placeholder tiles 2–6 | Medium | HTML commented out in `index.html`; uncomment when apps ship |
| Update footer GitHub link | Medium | Currently `#` placeholder |
| Add legal pages for apps 2–6 | Medium | Maconzo done; others when apps ship |
| Replace `banner.jpg` | Low | Still default Unsplash hero from template |
| Remove or archive unused HTML demos | Low | `landing.html`, `generic.html`, `elements.html` — safe to delete when confirmed unneeded |
| Add App Store links to tiles | Low | Requires restoring `.link` class + `target="_blank"` in `main.js` path |

---

## Technical debt

### SCSS / CSS dual maintenance

**Issue:** Style changes were applied to both `assets/sass/components/_tiles.scss` and `assets/css/main.css` manually. No compiler is in the repo.

**Risk:** Future edits to SCSS only will not appear in the browser until `main.css` is recompiled or updated by hand.

**Mitigation options (pick one later):**
1. Continue editing both files (current approach).
2. Add a one-line npm script with `sass` CLI.
3. Stop editing SCSS; treat `main.css` as sole source (simplest for a site this size).

---

### jQuery dependency

**Issue:** Template relies on jQuery + plugins for menu, scroll, and tile backgrounds.

**Risk:** Legacy dependency; unnecessary for a mostly static page.

**Mitigation:** Accept for now — replacing would require rewriting `main.js` behavior. Not worth the churn unless removing jQuery becomes a explicit goal.

---

### Google Fonts external load

**Issue:** Source Sans Pro loaded from `fonts.googleapis.com`.

**Risk:** Privacy/performance on strict static setups; offline dev requires network.

**Mitigation:** Self-host fonts in `assets/webfonts/` if needed later.

---

### Placeholder images for apps 2–6

**Issue:** `pic02.jpg`–`pic06.jpg` are Unsplash demo photos used as both icon and screenshot placeholders.

**Risk:** Misleading preview until real app assets are added.

---

### Unused template assets

**Issue:** `pic07.jpg`–`pic11.jpg` and three demo HTML pages remain from the original Forty download.

**Risk:** Repo clutter; no functional impact.

---

## Decision log (changelog)

| Date | Decision |
|------|----------|
| 2026-06-06 | Converted Forty template to AlexisWorks single-page app landing |
| 2026-06-06 | Equal 3-column tile grid with `.tile-content` readability panels |
| 2026-06-06 | Maconzo added as first real app (icon + screenshot + custom red accent) |
| 2026-06-06 | Institutional memory docs initialized (`.cursorrules`, ARCHITECTURE.md, DECISIONS.md) |
| 2026-06-07 | Maconzo Privacy & Terms pages at `apps/maconzo/`; `.tile-legal` links on Maconzo tile |
| 2026-06-07 | Placeholder app tiles (2–6) commented out in `index.html` until more apps ship; single-tile centered layout via `article:only-child` |

---

## How to use this document

- Before a significant change, check if it contradicts a recorded decision.
- After changing direction, add a row to the decision log and update pending tasks.
- When debt is resolved, move the item out of **Technical debt** or mark it done in **Pending tasks**.
