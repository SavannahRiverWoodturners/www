# Claude Context — Savannah River Woodturners Club Website

Hugo-based staging site for the Savannah River Woodturners Club redesign.
- **Staging URL:** `https://savannahriverwoodturners.org/www/` (GitHub Pages project page)
- **Live site (separate repo):** `https://savannahriverwoodturners.org/` — do not touch
- **Repo:** `SavannahRiverWoodturners/www`
- **Deploy:** Automatic on push to `main` via GitHub Actions

## Documentation

- **[.claude/site-overview.md](.claude/site-overview.md)** — Club background, audience, officers
- **[.claude/architecture.md](.claude/architecture.md)** — Hugo structure, theme, local dev, known gotchas
- **[.claude/meeting-post-workflow.md](.claude/meeting-post-workflow.md)** — How to create meeting posts (front matter, images, captions)
- **[.claude/data-and-layouts.md](.claude/data-and-layouts.md)** — Data files, custom layouts, and the CSS system

## Key Facts

- **Theme:** dot-org Hugo theme by CNCF (`themes/dot-org-hugo-theme` git submodule)
- **Config:** split `config/` directory — `_default/`, `development/`, `production/`; no `hugo.toml`
- **Nav menu:** defined in `config/_default/menus.yaml`
- **Color palette:** warm walnut/amber — overrides the theme's green via `static/css/theme.css`
- **Meeting posts:** use `type: posts` (default); layout in `layouts/posts/single.html`
- **Meeting dates page:** data-driven from `data/meetings.yaml`; auto-classifies past vs upcoming at build time
- **Affiliates:** driven by `data/affiliates.yaml`; appear on homepage and in post rail
- **Images in markdown:** use `![alt](src "caption")` title syntax — a render hook wraps them in `<figure>`
- **Inbox:** PDF meeting minutes dropped in `inbox/` for conversion to posts

## Issue Workflow

Before closing any issue: validate each acceptance criterion, then `git push`. See open issues at `https://github.com/SavannahRiverWoodturners/www/issues`.
