# Image-to-site reference

Read this while generating. Do not skip the inventory, palette table, or Pexels credits.

## Mode

SKILL.md step 0 picks **exact** or **similar** before you use this file. There is no default.

- **Exact:** paste screenshot hexes into `:root`. Skip the hue-shift algorithm. Reconstruct layout (section order, splits/grids, spacing, radius, shadows).
- **Similar:** run the hue-shift algorithm. Reject any hex equal to a screenshot token. Keep the same *kind* of layout, not a pixel clone.

Both modes still invent new brand/copy and use Pexels URLs for photo slots.

## Inventory template

Fill this from the screenshot **before** scaffolding:

```
Page type:
Industry:
Section order:
  1. {name} — layout: {split / centered / grid-N / ...}
  2. ...

Type vibe: {geometric sans / humanist serif / ...}
Spacing density: {tight / medium / airy}
Radius: {none / sm / md / lg / pill}
Shadows: {none / hairline / soft / deep}

Colors (screenshot hex):
  background:
  surface:
  text:
  muted:
  primary:
  accent:
  border:

Photo slots:
  - id: hero
    role: hero | product | portrait | background | icon-photo
    subject:
    orientation: landscape | portrait | square
    mood/setting:
    dominant color:
    crop: {full-bleed left / circle avatar / ...}
```

Icon, logo, and decoration slots are **not** photo slots. Rebuild those in SVG/CSS.

## Exact palette

In exact mode, write screenshot hexes onto CSS variables. Do not rotate hue.

```css
:root {
  /* exact screenshot tokens
     background #0B1220
     primary    #2563EB
     accent     #F59E0B
  */
  --bg: #0b1220;
  --surface: #...;
  --text: #...;
  --muted: #...;
  --primary: #2563eb;
  --accent: #f59e0b;
  --border: #...;
}
```

## Hue-shift algorithm (similar mode only)

Apply the **same** hue rotation to every token so the palette stays related. Skip this entire section in exact mode.

1. Parse each screenshot hex as sRGB → HSL (`H` in 0–360, `S` and `L` in 0–100).
2. Pick `deltaH` once in **+12 to +20**. Use that `deltaH` for every token.
3. Remap:

   - `H' = (H + deltaH) % 360`
   - `S' = clamp(S + deltaS, 0, 100)` where `deltaS` is between −8 and +8, not 0
   - `L' = clamp(L + deltaL, 0, 100)` where `deltaL` is between −6 and +6, not 0
   - Keep contrast roles: if original text is dark on light, remapped text stays darker than its background. If a nudge would flatten contrast, flip the sign of `deltaL` for that token only.
4. Convert `H'S'L'` back to hex. **Reject any hex that equals a screenshot token.**
5. Write the table into `site/src/index.css` (or equivalent) as a comment:

```css
:root {
  /* palette remap, deltaH=+16
     background #0B1220 → #0E1426
     primary    #2563EB → #3B5CDE
     accent     #F59E0B → #F07A1A
  */
  --bg: #0e1426;
  --surface: #...;
  --text: #...;
  --muted: #...;
  --primary: #...;
  --accent: #...;
  --border: #...;
}
```

Worked example (`deltaH = +16`, `deltaS = +5`, `deltaL = −3`):

| Token | Screenshot | HSL in | HSL out | Remapped |
| --- | --- | --- | --- | --- |
| primary | `#2563EB` | 221°, 83%, 53% | 237°, 88%, 50% | `#3b4de0` (approx) |
| accent | `#F59E0B` | 38°, 92%, 50% | 54°, 97%, 47% | `#ebc304` (approx) |

Recalculate; do not copy these example hexes into a real site unless they happen to be the true output.

### Fonts

Do not use the screenshot’s identifiable brand font. Load a Google Font in `index.html` and set `font-family` on `body`.

- **Exact:** closest match to the screenshot’s type vibe (same category and similar weight/width).
- **Similar:** a **nearby** Google Font in the same category (another geometric sans, another old-style serif).

## Pexels search recipe

Goal: a **different** photograph that could sit in the same layout hole.

### Query

`{subject} {setting} {industry}` — concrete nouns, not the original brand.

| Screenshot slot | Good query | Bad query |
| --- | --- | --- |
| Woman with laptop in a bright cafe | `woman working laptop cafe` | `Starbucks` / brand name |
| Overhead avocado toast | `overhead avocado toast restaurant` | crop from screenshot |
| Team standing in an office | `small team standing modern office` | `Google campus` |

### API (preferred)

Read `PEXELS_API_KEY` from the environment or project `.env`. Do not print the key. Do not commit it.

```bash
curl -sS -H "Authorization: $PEXELS_API_KEY" \
  "https://api.pexels.com/v1/search?query=woman+working+laptop+cafe&orientation=landscape&per_page=15"
```

Header is the raw key — **no** `Bearer` prefix.

Useful query params:

- `query` (required)
- `orientation`: `landscape` | `portrait` | `square` — match the slot
- `color`: nearest named color or palette hex (exact screenshot hex, or remapped hex in similar mode) so the photo sits near the theme
- `per_page`: 10–15

From each photo object use:

- `src.large` or `src.large2x` as the `<img>` / background URL (do not download)
- `photographer`
- `photographer_url`
- `url` (Pexels photo page — required for attribution)

Pick the first result that matches subject, orientation, and mood, and does **not** show logos, watermarks, or famous brand products. If the first page is a poor match, try a tighter query before accepting a mismatch.

In React, use the remote URL:

```jsx
<img src={photo.src.large} alt="..." />
```

Never `curl` the image. Never write `site/public/images/`. Never use local `/images/...` paths.

### Fallback (no API key)

1. Open or fetch `https://www.pexels.com/search/{urlencoded-query}/`
2. Open a result that matches orientation and mood
3. Copy the large image URL (`images.pexels.com/...`) — do not download the file
4. Record photographer name and photo page URL for credits
5. Use that URL as `src` in React

### Attribution (required)

On or near each photo:

```html
<a href="{photoPageUrl}">Photo by {photographer} on Pexels</a>
```

In the footer, once:

```html
<a href="https://www.pexels.com">Photos provided by Pexels</a>
```

Keep a list in code or comments:

```
hero       Photo by Ada Lovelace  https://www.pexels.com/photo/...
feature-1  Photo by Alan Turing   https://www.pexels.com/photo/...
```

### Not photographs

If the slot is an icon, wordmark, illustration, or geometric decoration, implement it as original SVG or CSS. Do not search Pexels for it.

## Content invention examples

Keep the **job** of each block. Change every **string**.

**Nav**

- Screenshot: `Acme Cloud · Product · Pricing · Login`
- Invented: `Nimbus Forge · Platform · Plans · Sign in`

**Hero**

- Screenshot: `Ship faster with Acme. The developer platform for modern teams.`
- Invented: `Launch quieter with Nimbus Forge. Infrastructure notes for small product crews.`

**CTA**

- Screenshot: `Start free trial`
- Invented: `Open a sandbox`

**Social proof**

- Screenshot: `Trusted by 12,000 companies` + named logos
- Invented: `Used by 8,400 workshops` + invented names or letter-mark SVGs (never the screenshot logos)

**Testimonial**

- Screenshot: `"Acme cut our deploy time in half." — Jordan, VP Eng at Globex`
- Invented: `"Nimbus Forge made Friday releases boring." — Priya, lead builder at Northwell Labs`

Rules of thumb:

- Same industry (SaaS stays SaaS, restaurant stays restaurant)
- New brand name that does not rhyme with or abbreviate the original
- Stats in the same order of magnitude, not the same numbers
- People names, companies, and quotes all invented
- Alt text describes the new scene, not the original site

## Implement sketch

```
site/
  index.html
  src/
    main.jsx
    App.jsx              # composes sections
    index.css            # :root palette + token comment (exact hexes or remap table)
    components/
      Nav.jsx
      Hero.jsx
      ...
      Footer.jsx
```

One component per inventoried section. CSS variables for color. No Tailwind unless the user asks.

## Verify

- Desktop and a ~390px-wide viewport
- No screenshot brand strings in `site/src`
- **Exact:** CSS hexes match the screenshot token table; layout reconstructs the page
- **Similar:** no screenshot hexes in CSS (compare to the remap table)
- Every photo slot uses a remote Pexels URL (`images.pexels.com` or `src.large`), not a local file
- Credits visible without hunting
