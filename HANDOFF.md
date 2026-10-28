# Portfolio Handoff

Last prepared: October 28, 2026

This file carries the context that previously lived mostly in a long design conversation. Read it before continuing work on another computer.

## Where The Work Lives

- Repository: `https://github.com/TiagoBarral/tiagobarral.github.io.git`
- Working branch: `redesign/editorial-port`
- Production branch: `main`
- Framework: Hugo Extended
- Deployment: GitHub Pages

The redesign branch is intentionally separate from `main`. Cloning and checking out this branch restores the editorial work without changing the deployed site until the branch is merged.

## Product Direction

The site is a personal developer portfolio and learning journal, not an Oportuno marketing site. Its purpose is to show:

- real projects and their development history
- technical decisions and architecture tradeoffs
- mistakes, debugging, and the messy middle
- broader journal reflections
- practical guides distilled from repeated lessons
- a short `/now` snapshot of current work

The voice should be honest, specific, reflective, and production-minded. Oportuno and ProjectDesk are strong examples, but the portfolio should represent Tiago's broader developer journey.

## Editorial Design Direction

The current redesign uses a publication-like visual system:

- Fraunces for display headings and editorial prose
- Inter for interface/body text
- JetBrains Mono for metadata, labels, dates, and issue-style details
- warm cream/paper backgrounds
- dark ink text
- restrained deep green and warm orange accents
- strong hierarchy, generous whitespace, thin rules, and minimal animation

The main navigation is Home, Projects, Guides, Journal, About, and Now.

Avoid generic SaaS styling, decorative card grids everywhere, heavy gradients, excessive rounded UI, and large decorative icon circles. The site should feel like a thoughtful independent publication about building software.

## Current Editorial Port

The active implementation is in the normal Hugo paths:

- `layouts/`
- `content/`
- `static/css/style.css`
- `static/images/`

The `hugo-port/` folder is the supplied design source/reference used during the redesign. It is preserved for comparison, but Hugo does not render it. Active templates have evolved beyond parts of that reference, so compare before copying.

The profile image used by active templates is:

```text
static/images/profile-circle.png
```

Additional image variants are retained in `static/images/` because they were part of the design exploration.

## Work Included In This Snapshot

This migration snapshot includes the local work that had not yet been pushed:

- editorial About page
- editorial Guides index and guide reader
- expanded Getting Started guide
- editorial Now page and `content/now.md`
- editorial Journal reader
- `pullquote`, `compare`, `callout`, and `field-note` shortcodes
- new journal article: “The day scraping became a queue”
- expanded article: “What Oportuno Is Teaching Me”
- homepage and journal-list support for emphasized journal titles
- the portfolio update model and reusable content templates
- retained design-reference files and image variants

## Important Content Decisions

### Projects

Each project page is driven by front matter and dated `entries`. Entries can contain structured reflection sections such as:

- What I built
- What I was trying to learn
- What went wrong
- What I learned
- What I would do differently

Project cards should be clickable across the full card surface.

### Journal

Journal posts are long-form editorial articles. The reader supports:

- large serif title with optional `<em>` emphasis
- tags, summary/dek, author metadata, read time, and word count
- drop-cap opening paragraph
- large serif section headings
- pull quotes
- inline and fenced code
- full-width before/after comparison bands
- related posts

Relevant templates:

```text
layouts/blog/single.html
layouts/blog/list.html
layouts/shortcodes/pullquote.html
layouts/shortcodes/compare.html
```

### Guides

Guides should feel like designed reading experiences rather than generic Markdown pages. Preserve the chapter metadata, reading information, sticky table of contents, drop cap, callouts, code styling, and previous/next chapter cards.

### Maintaining The Site

`PORTFOLIO_UPDATE_MODEL.md` explains what belongs in Now, project entries, Journal, and Guides. It also contains fill-in templates and a weekly/monthly maintenance rhythm.

## New Computer Setup

```powershell
git clone https://github.com/TiagoBarral/tiagobarral.github.io.git
Set-Location tiagobarral.github.io
git switch redesign/editorial-port
hugo server -D
```

Open the local URL printed by Hugo, normally `http://localhost:1313/`.

Before making new changes:

```powershell
git status
git pull --ff-only
```

Before committing:

```powershell
hugo --gc
```

## Files Deliberately Not Transferred

These are machine-local or generated and are excluded by `.gitignore`:

- `public/`
- `resources/`
- `.hugo_build.lock`
- `.hugo-server*.log`
- the duplicate root `hero-illustration.png` (the retained public copy is under `static/images/`)

No private journals, environment files, tokens, or credentials are included in this repository snapshot.

## Suggested First Prompt On The New Computer

```text
Read AGENTS.md and HANDOFF.md first. Continue the Hugo editorial redesign on the redesign/editorial-port branch. Preserve all existing content and run hugo --gc before committing. Tell me the current git status and the local preview URL before changing anything.
```
