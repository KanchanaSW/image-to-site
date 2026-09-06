---
name: image-to-site
description: >-
  Use when the user attaches a website screenshot or webpage image and wants a
  generated site, image-to-site, a React Vite site from a screenshot, or Pexels
  photos matching a reference webpage.
---

# Image to site

Turn a website screenshot into an original Vite + React site in `site/`. Match the screenshot’s **industry**. Fill every photo slot with a similar Pexels image — never a crop of the screenshot.

**Ask the mode question first. Stop until the user answers.** Then follow that mode for layout, color, and content.

Read [reference.md](reference.md) before generating (inventory template, hue-shift math, Pexels recipe, copy examples).

## 0. Ask before anything else

Before inventory, Pexels, scaffolding, or any files in `site/`, ask **this question** with two options:

> Do you want:
> 1. **Exact** — the same layout and colors as the image
> 2. **Similar** — nearby colors and original content in the same industry

Then **STOP**. Wait for the answer. Do not inventory, scaffold, search Pexels, or write code.

**Skip the question only if** this same user message already chose a mode (e.g. “match the screenshot colors”, “exact layout”, “similar palette”, “inspired by / nearby colors”). If unclear, ask.

| Excuse | Reality |
|--------|---------|
| “They said generate / just build it / no extra questions” | Still ask. Mode changes colors and how close the layout is. |
| “The skill used to shift hues, so default to similar” | There is no default. Ask. |
| “A screenshot means they want an exact copy” | Screenshot ≠ mode. Ask. |
| “I’ll inventory first, then ask” | Asking is step 0. Inventory is after the answer. |
| “I’ll ask in the same turn while scaffolding” | Stop. No files until they pick. |

## Hard rules (all modes)

- Generate only into `site/`. Never write app files at the repo root. Never delete `.cursor/` or `.claude/`.
- Do not copy visible strings, logos, or trademarked marks from the screenshot.
- Do not crop or embed the screenshot as a site image.
- Icons, logos, and abstract decorations stay SVG/CSS — do not force a Pexels photo into those slots.
- Never download photos to disk. Never write `site/public/images/`. Use the Pexels image URL as `src`.
- Credit Pexels and each photographer. Never commit `PEXELS_API_KEY` or `.env`.

### Exact mode

- Recreate the screenshot’s **layout**: section order, splits/grids, spacing density, radius, shadows.
- Use the screenshot’s **hex colors as-is** for CSS variables. Record `token → hex` in a CSS comment. Do not hue-shift.
- Still invent a new brand and all-new copy. Photos still come from Pexels.

### Similar mode

- Same industry and the same *kind* of layout (hero still a hero, 3-col stays 3-col), not a pixel-faithful clone.
- Nearby palette, not identical — hue-shift; **forbid** exact screenshot hexes.
- Invent all-new brand and copy. Nearby Pexels photos, not the original pictures.

## Checklist

Copy and tick:

```
- [ ] Mode chosen by user (exact | similar) — STOP until answered
- [ ] Inventory written (layout, type, colors, photo slots)
- [ ] New brand and all-new copy (same industry, no screenshot strings)
- [ ] Palette: exact = screenshot hexes; similar = hue +12° to +20° remap table
- [ ] Pexels photo URL per photo slot (remote `src`, no local image files)
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

### 3. Colors

**Exact:** put screenshot hexes on `:root` variables. Comment the token table. Skip hue-shift.

**Similar:** follow the HSL recipe in [reference.md](reference.md):

- Rotate hue **+12° to +20°** in one direction for the whole palette
- Nudge saturation ±4–8% and lightness ±3–6%
- Keep contrast roles (dark-on-light stays dark-on-light)
- Record `original → remapped` in a CSS comment
- Forbid exact screenshot hexes

### 4. Nearby Pexels images

For every photo slot, search Pexels for a shot that is close in subject, orientation, and mood.

- Query = subject + setting + industry
- Match orientation (`landscape` / `portrait` / `square`)
- Optionally filter by palette color (exact hexes, or remapped hexes in similar mode)
- Skip logos, watermarks, famous brand products
- **Preferred:** `GET https://api.pexels.com/v1/search` with `Authorization: $PEXELS_API_KEY` (no `Bearer`). Read the key from `.env` / the environment.
- **Fallback:** search Pexels on the web, open a matching photo page, copy the image URL
- Put `src.large` or `src.large2x` (or the page’s image URL) on `<img>` / CSS backgrounds
- Do not curl, save, or commit image files. Do not use `/images/...` local paths
- Attribution: `Photo by {name} on Pexels` linking to the photo page, plus footer `Photos provided by Pexels` linking to https://www.pexels.com

Full recipe: [reference.md](reference.md).

### 5. Scaffold

```bash
npm create vite@latest site -- --template react
cd site && npm install
```

If `site/` already exists, delete its contents and recreate. Do not touch skill folders.

### 6. Implement

- Vanilla CSS with `:root` variables for the chosen palette
- One React component per section, composed in `App.jsx`
- Google Font: exact = closest match to the screenshot’s type vibe; similar = nearby in the same category (never the screenshot’s identifiable brand font)
- Single page unless the image clearly shows multiple pages
- **Exact:** responsive layout that reconstructs the screenshot’s structure
- **Similar:** responsive layout in the same pattern, not a pixel clone
- `<img>` tags use Pexels image URLs; alt text describes the **new** brand

### 7. Verify

Start the Vite dev server, open the app, walk the page on desktop and a mobile width. Confirm:

- No original brand strings in the code
- **Exact:** CSS uses the screenshot hexes; layout reads as the same page
- **Similar:** no screenshot hexes in CSS; layout still reads as the same *kind* of page
- Every photo slot is a remote Pexels URL (or SVG for icons), not a local file
- Photographer + Pexels credits are visible

## Red flags — STOP and ask

- Starting inventory or Vite before the user picked exact vs similar
- Defaulting to similar because that was the old skill
- Hue-shifting in exact mode, or copying screenshot hexes in similar mode
- “They said just generate” as a reason to skip the question

## Additional resources

- [reference.md](reference.md) — inventory template, hue-shift, Pexels search, copy examples
