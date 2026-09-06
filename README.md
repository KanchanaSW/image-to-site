# Image to site

Project skill for Cursor and Claude: attach a website screenshot and generate an original Vite + React site in `site/`.

The generated page keeps the screenshot’s industry and layout, invents a new brand and copy, shifts colors to nearby hues, and fills photo slots with similar [Pexels](https://www.pexels.com) free images.

## Use

### Cursor chat

1. Attach a website screenshot in chat.
2. Ask to generate the site (or say “image-to-site”).
3. The agent scaffolds or overwrites `site/` only — skill folders stay untouched.

### Cursor agent (CLI)

From the repo root, pass a screenshot path. `--force` lets the agent write files without prompting:

```bash
agent -p --force "Use the image-to-site skill. Generate an original Vite + React site from this screenshot: ./screenshot.png"
```

Replace `./screenshot.png` with your image. For an interactive session instead:

```bash
agent "Use the image-to-site skill. Generate an original Vite + React site from this screenshot: ./screenshot.png"
```

Then preview:

```bash
cd site
npm install
npm run dev
```

## Optional Pexels API key

Without a key, the agent searches Pexels on the web and downloads free photos.

With a key, it uses the official search API.

1. Request a key at https://www.pexels.com/api/
2. Put it in a project `.env` (never commit this file):

```
PEXELS_API_KEY=your_key_here
```

The Authorization header is the raw key — no `Bearer` prefix.

## What the skill will not copy

- Visible text, logos, or trademarks from the screenshot
- Exact hex colors
- Crops of the screenshot itself

Photographer and Pexels credits belong on the generated page.

## Skill files

- Cursor: `.cursor/skills/image-to-site/`
- Claude: `.claude/skills/image-to-site/`

Keep both copies in sync if you edit the skill.
