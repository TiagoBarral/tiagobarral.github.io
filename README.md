# Tiago Barral Portfolio

Personal portfolio and learning journal built with Hugo and deployed to GitHub Pages.

Live site: [https://tiagobarral.github.io/](https://tiagobarral.github.io/)

The site focuses on real projects, technical decisions, mistakes, and lessons learned while building.

## Local Development

```powershell
hugo server -D
```

## Build

```powershell
hugo --gc
```

## Structure

- `content/projects/` — project pages and dated development journals
- `content/blog/` — journal posts
- `content/guides/` — learning guides
- `layouts/` — Hugo templates
- `static/css/style.css` — site styling

## Deployment

Pushes to `main` deploy through GitHub Actions to GitHub Pages.
