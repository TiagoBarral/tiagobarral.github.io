# Portfolio Agent Rules

This repository is Tiago Barral's Hugo portfolio and learning journal. Treat the written content and the editorial design as durable product work, not disposable scaffolding.

## Project Priorities

- Preserve existing content unless Tiago explicitly asks to rewrite or remove it.
- Keep the site static, lightweight, and compatible with GitHub Pages.
- Do not introduce React, Tailwind, JavaScript frameworks, or external UI systems.
- Prefer existing Hugo templates, front matter, shortcodes, and CSS conventions.
- Keep the editorial character: strong Fraunces display type, Inter body text, JetBrains Mono metadata, warm paper backgrounds, dark ink, and restrained green/orange accents.
- Match supplied visual references closely before inventing a new direction.

## Active Files

- `content/` contains all public writing and project data.
- `layouts/` contains the active Hugo templates.
- `static/css/style.css` is the active stylesheet.
- `static/images/` contains public image assets.
- `hugo-port/` is a design-reference snapshot only. It is not rendered by Hugo. Never copy it wholesale over active files without comparing the current implementation.

## Content Model

- Projects: `content/projects/*.md`
- Journal posts: `content/blog/*.md`
- Guides: `content/guides/*.md`
- Current activity: `content/now.md`
- About copy: `content/about/_index.md` and `layouts/about/list.html`
- Portfolio maintenance guidance: `PORTFOLIO_UPDATE_MODEL.md`

Project history is stored in each project's `entries` front matter. Keep entries dated, specific, and honest about decisions, failures, lessons, and next steps.

Journal posts use the editorial reader in `layouts/blog/single.html`. The supported article shortcodes include:

- `pullquote`
- `compare`
- `callout`
- `field-note`

Guide pages use their own editorial reader in `layouts/guides/single.html`. Preserve the typography, table of contents, notes, quotes, code blocks, and previous/next navigation.

## Implementation Rules

- Read the surrounding template and CSS before editing.
- Keep changes scoped; do not rewrite the site structure unless requested.
- Use semantic HTML and accessible labels/alt text.
- Ensure cards and obvious interactive surfaces are fully clickable.
- Maintain responsive behavior at desktop, tablet, and mobile widths.
- Do not commit generated Hugo output, build locks, local server logs, or secrets.
- Do not expose values from `.env` files or private project journals.

## Verification

Before committing site changes, run:

```bash
hugo --gc
```

For local review, run:

```bash
hugo server -D
```

Check the changed page in a browser when visual layout is involved.

## Git Workflow

- Current redesign work lives on `redesign/editorial-port` until intentionally merged.
- Use focused commits with descriptive messages.
- Do not force-push or rewrite history without explicit approval.
- Keep `main` deployable; GitHub Pages uses the repository at `https://github.com/TiagoBarral/tiagobarral.github.io`.

Read `HANDOFF.md` at the start of a new computer or agent session.
