# Architecture

## Tech Stack

| Component | Detail |
|---|---|
| Static site generator | Hugo v0.157.0+extended |
| Theme | [dot-org-hugo-theme](https://github.com/cncf/dot-org-hugo-theme) by CNCF (git submodule) |
| Hosting | GitHub Pages |
| Deployment | GitHub Actions — `.github/workflows/hugo.yml`, triggers on push to `main` |
| CSS pipeline | PostCSS + autoprefixer via npm (`postcss.config.js`) |
| SCSS compilation | Dart Sass (required by theme — separate binary, NOT libsass) |
| Domain | `savannahriverwoodturners.org` — staging deploys to `/www/` subpath |

## Hugo Site Structure

```
www/
├── config/
│   ├── _default/
│   │   ├── hugo.yaml       # Core settings: theme, language, permalinks, goldmark
│   │   ├── menus.yaml      # Nav menu (main)
│   │   └── params.yaml     # Theme params: logos, custom_css, social links
│   ├── development/
│   │   └── hugo.yaml       # baseURL: http://localhost:1313/
│   └── production/
│       └── hugo.yaml       # baseURL: https://savannahriverwoodturners.org/
│                           # (overridden at build time by --baseURL from configure-pages)
├── content/
│   ├── _index.md           # Homepage (showHeader: false)
│   ├── contact.md
│   ├── club-officers.md
│   ├── meeting-dates.md    # type: meetings — uses layouts/meetings/single.html
│   ├── club-history.md
│   ├── submit-minutes.md
│   └── posts/
│       ├── _index.md
│       └── YYYY-MM-*.md    # Meeting posts (use layouts/posts/single.html)
├── data/
│   ├── meetings.yaml       # All meeting dates — auto-split past/upcoming at build time
│   └── affiliates.yaml     # AAW, Beyond the Bevel — rendered on homepage + post rail
├── layouts/
│   ├── index.html          # Homepage: 'hero' block + 'main' block (2-col grid: sidebar w/ Next Meeting card | prose) + affiliates
│   ├── meetings/
│   │   └── single.html     # Meeting dates page: next callout, upcoming list, past list (all metadata from YAML)
│   ├── posts/
│   │   └── single.html     # Meeting post: metadata bar, demo callout, content, affiliate rail, next meeting (auto from YAML)
│   └── _default/
│       ├── baseof.html     # Custom base layout: renders 'hero' block BEFORE .container.wrap, 'main' inside it
│       ├── contact.html    # Contact page (layout: contact) — address + phone cards with inline SVG icons
│       ├── club-officers.html   # Officers page (layout: club-officers) — photo + grid from data/officers.yaml
│       ├── club-history.html    # History page (layout: club-history) — prose + framed vase image
│       └── _markup/
│           └── render-image.html  # Goldmark hook: wraps ![alt](src "caption") in <figure>/<figcaption>
├── archetypes/
│   └── posts.md            # Scaffold for new meeting posts (hugo new posts/YYYY-MM-slug.md)
├── static/
│   ├── css/
│   │   ├── theme.css       # :root overrides — woody color palette (replaces theme green)
│   │   └── posts.css       # Meeting posts, figures, affiliates, meeting dates page styles
│   ├── images/
│   │   ├── affiliates/     # Affiliate logos (aaw-logo.jpg)
│   │   └── posts/          # Meeting post images, organized by date (YYYY-MM-DD/)
│   └── img/                # Site-wide assets: logo, hero image, officer photo
├── themes/
│   └── dot-org-hugo-theme/ # Git submodule — do not edit directly
├── inbox/                  # Drop PDF meeting minutes here for conversion to posts
├── package.json            # npm deps: autoprefixer, postcss, postcss-cli, sass-embedded
├── postcss.config.js
└── .github/
    ├── workflows/
    │   ├── hugo.yml                # Build & deploy (Node 20, Dart Sass, npm ci, hugo --minify)
    │   └── process-blog-post.yml  # Issue → post automation (not yet active)
    └── ISSUE_TEMPLATE/
        └── blog-post.yml           # Member submission form
```

## Config Notes

- **No `hugo.toml`** — config is split across `config/` directory
- **`goldmark.renderer.unsafe: true`** is required — the theme uses HTML in shortcodes
- **`contentDir: "content"`** — overrides the theme's default `content/en/` multilingual path
- **`baseURL` at build time:** The workflow uses `--baseURL "${{ steps.pages.outputs.base_url }}/"` which GitHub sets to `https://savannahriverwoodturners.org/www/`. The production config sets it to the root domain as a fallback.

## Theme

The dot-org theme uses **Dart Sass** (not libsass). Hugo Extended alone is not enough.

- **Locally:** Dart Sass binary at `~/bin/dart-sass/` (added to PATH in `~/.zprofile`). Run `hugo server` from a shell that has sourced `~/.zprofile`.
- **CI:** Installed via `sudo snap install dart-sass` in the workflow.
- **npm:** `sass-embedded` is also in `package.json` as a fallback but the standalone binary is what Hugo uses.

## Hugo Template Lookup Gotchas

- **Home page template is `layouts/index.html` only.** Hugo does NOT use `layouts/home/index.html` or `layouts/home/baseof.html` for the home page. Only `layouts/index.html` is used. The baseof for it resolves to `layouts/_default/baseof.html`.
- **Full-width sections:** To render content outside the theme's `.container.wrap`, define a separate block (e.g. `hero`) in `layouts/_default/baseof.html` placed before the container. Use `layouts/index.html` to populate that block. CSS hacks like `width: 100vw` with negative margins are fragile — prefer the block approach.
- **`overflow-x: hidden` on `html` breaks vertical scrolling** in some browsers. Avoid it.
- **Static images must be committed to git** — untracked files in `static/` exist locally but are absent in production after deploy.

## Subdirectory Deployment Gotchas

The site deploys at `/www/` not `/`. This affects URL generation:

- **CSS/JS paths in params** — must be relative (no leading `/`) so `absURL` appends to the full baseURL. E.g. `logo_on_white: "img/srwoodturners-logo.jpg"` not `"/img/..."`.
- **Markdown image paths** — `![alt](/images/...)` produces root-relative paths. The render hook at `layouts/_default/_markup/render-image.html` rewrites them by stripping the leading `/`, calling `absURL`, and extracting the path component.
- **Custom CSS paths** — listed in `params.yaml` without leading `/`, so the theme's `absURL` call resolves correctly.

## Nav Menu

Defined in `config/_default/menus.yaml`. Order controlled by `weight`.

| Name | URL | Weight |
|---|---|---|
| Contact | /contact/ | 10 |
| Club Officers | /club-officers/ | 15 |
| Meeting Dates | /meeting-dates/ | 20 |
| Club History | /club-history/ | 30 |
| Meeting Posts | /posts/ | 40 |

## Local Dev

```bash
# First time only — install deps
npm install

# Start dev server (must have dart-sass in PATH)
source ~/.zprofile
hugo server --buildDrafts --environment development
# → http://localhost:1313/
```

> nvm may shadow system tools in non-interactive shells. If commands like `tail` or `grep` go missing after `source ~/.nvm/nvm.sh`, use Python or dedicated tools instead.
