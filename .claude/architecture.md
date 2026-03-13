# Architecture

## Tech Stack
- **Static site generator:** Hugo (v0.157.0+extended)
- **Hosting:** GitHub Pages
- **Deployment:** GitHub Actions (`hugo.yml` — triggers on push to `main`)
- **Domain:** savannahriverwoodturners.org (CNAME — set this up in repo settings once Pages is enabled)

## Hugo Site Structure

```
www/
├── hugo.toml               # Site config: baseURL, title, menu, permalinks
├── content/
│   ├── _index.md           # Homepage
│   ├── contact.md
│   ├── club-officers.md
│   ├── meeting-dates.md
│   ├── club-history.md
│   ├── submit-minutes.md
│   └── posts/
│       ├── _index.md       # Posts list page
│       └── YYYY-MM-DD-*.md # Individual meeting posts
├── static/
│   ├── img/                # Logo, hero image, officer photo
│   └── images/posts/       # Images downloaded from GitHub Issues (auto-populated)
├── layouts/                # Custom templates (empty — theme will provide these)
├── themes/                 # Theme goes here (not yet selected)
└── .github/
    ├── workflows/
    │   ├── hugo.yml                # Build & deploy to GitHub Pages
    │   └── process-blog-post.yml  # Issue → post automation
    └── ISSUE_TEMPLATE/
        └── blog-post.yml           # Form for member submissions
```

## Theme
No theme is selected yet. To add one:
```bash
git submodule add https://github.com/author/theme-name themes/theme-name
```
Then set `theme = "theme-name"` in `hugo.toml`. Themes being considered:
Mainroad, PaperMod, Clarity, Ananke.

## Nav Menu
Defined in `hugo.toml` under `[menu]`. Order is controlled by `weight`.
Current order: Contact → Club Officers → Meeting Dates → Club History → Meeting Posts → Submit Minutes.

## Post Permalinks
Posts use `/posts/:year/:month/:slug/` (set in `hugo.toml`).

## Local Dev
```bash
hugo server --buildDrafts   # live-reloads at http://localhost:1313
```
