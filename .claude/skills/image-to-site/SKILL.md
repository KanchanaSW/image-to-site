---
name: image-to-site
description: >-
  Use when the user attaches a website screenshot or webpage image and wants a
  generated site, image-to-site, a React Vite site from a screenshot, or Pexels
  photos matching a reference webpage.
---

# Image to site

Turn a website screenshot into an original Vite + React site in `site/`. Match the screenshot’s **industry and layout**. Invent all-new brand and copy. Shift colors nearby. Fill every photo slot with a similar Pexels image — never a crop of the screenshot.

Read [reference.md](reference.md) before generating (inventory template, hue-shift math, Pexels recipe, copy examples).

## Hard rules

- Generate only into `site/`. Never write app files at the repo root. Never delete `.cursor/` or `.claude/`.
- Do not copy visible strings, logos, or trademarked marks from the screenshot.
- Do not reuse exact hex colors from the screenshot.
- Do not crop or embed the screenshot as a site image.
- Same industry, new brand. Nearby palette, not identical. Nearby Pexels photos, not the original pictures.
- Icons, logos, and abstract decorations stay SVG/CSS — do not force a Pexels photo into those slots.
- Credit Pexels and each photographer. Never commit `PEXELS_API_KEY` or `.env`.

## Checklist

Copy and tick:

```
- [ ] Inventory written (layout, type, colors, photo slots)
- [ ] New brand and all-new copy (same industry, no screenshot strings)
- [ ] Palette remapped (hue +12° to +20°, original → remapped table)
- [ ] Pexels photo per photo slot, saved under site/public/images/
- [ ] Vite + React scaffolded or overwritten in site/ only
- [ ] Section components implemented with CSS variables
- [ ] Browser-verified (desktop + mobile); credits visible
```

## Workflow

### 1. Inventory (before any code)

Study the screenshot. Write down:

- Page type and industry
- Section order and layout per section (split hero, 3-col features, etc.)
- Type vibe, spacing, radius, shadow density
- Color tokens: background, surface, text, muted, primary, accent, border
- Each **photo slot**: role, subject, orientation, mood/setting, dominant color, crop

Use the inventory template in [reference.md](reference.md).

### 2. Invent content

New name, tagline, nav, headlines, body, CTAs, stats, testimonials, product names. Keep section *jobs* (hero still sells, pricing still prices). Never reuse a visible string from the image.

### 3. Nearby color remap

Follow the HSL recipe in [reference.md](reference.md):

- Rotate hue **+12° to +20°** in one direction for the whole palette
- Nudge saturation ±4–8% and lightness ±3–6%
- Keep contrast roles (dark-on-light stays dark-on-light)
- Record `original → remapped` in a CSS comment
- Forbid exact screenshot hexes

### 4. Nearby Pexels images

For every photo slot, search Pexels for a shot that is close in subject, orientation, and mood.

- Query = subject + setting + industry
- Match orientation (`landscape` / `portrait` / `square`)
- Optionally filter by remapped color
- Skip logos, watermarks, famous brand products
- **Preferred:** `GET https://api.pexels.com/v1/search` with `Authorization: $PEXELS_API_KEY` (no `Bearer`). Read the key from `.env` / the environment.
- **Fallback:** search Pexels on the web, open a photo page, download the free image
- Save as `site/public/images/{slot}.jpg` (e.g. `hero.jpg`, `feature-1.jpg`)
- Use local `/images/...` paths in React
- Attribution: `Photo by {name} on Pexels` linking to the photo page, plus footer `Photos provided by Pexels` linking to https://www.pexels.com

Full recipe: [reference.md](reference.md).

### 5. Scaffold

```bash
npm create vite@latest site -- --template react
cd site && npm install
```

If `site/` already exists, delete its contents and recreate. Do not touch skill folders.

### 6. Implement

- Vanilla CSS with `:root` variables for the remapped palette
- One React component per section, composed in `App.jsx`
- Nearby Google Font (same category, not the screenshot’s exact brand font)
- Single page unless the image clearly shows multiple pages
- Responsive layout matching the screenshot’s structure
- `<img>` tags use downloaded Pexels files; alt text describes the **new** brand

### 7. Verify

Start the Vite dev server, open the app, walk the page on desktop and a mobile width. Confirm:

- No original brand strings or screenshot hexes in the code
- Every photo slot is a Pexels image (or SVG for icons)
- Photographer + Pexels credits are visible
- Layout still reads as the same kind of page

## Additional resources

- [reference.md](reference.md) — inventory template, hue-shift, Pexels search, copy examples
