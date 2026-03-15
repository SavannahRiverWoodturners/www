# Data Files, Layouts, and CSS

## Data Files

### `data/meetings.yaml`

Single source of truth for all meeting dates. The layout template splits them into upcoming/past at build time using Hugo's `now`.

```yaml
- date: "2025-11-15"    # ISO 8601
  time: "9:00 am"
  note: ""              # optional — shown for special events (Christmas Party, etc.)
```

- **Adding a meeting:** add a new entry in chronological order
- **Past/upcoming split:** automatic at build time — no manual editing when meetings pass
- **Next meeting callout:** first entry with date ≥ today
- **Post link:** if a post exists with `date: YYYY-MM-DD` and `categories: ["meeting-minutes"]` matching `.date`, a "Read more →" link appears automatically

### `data/affiliates.yaml`

Affiliates displayed on the homepage card grid and the post-page rail.

```yaml
- name: "American Association of Woodturners"
  short: "AAW"              # Shown in the initials fallback circle when no logo
  description: "..."
  url: "https://www.woodturner.org"
  logo: "images/affiliates/aaw-logo.jpg"  # relative path, no leading /
                                           # leave blank ("") for initials fallback
```

- **Adding an affiliate:** add an entry to this file — no template changes needed
- **Logo images:** drop files in `static/images/affiliates/` and set the `logo:` field
- **Logo path:** no leading slash — `absURL` in the template prepends the full baseURL

---

## Layouts

### `layouts/index.html` — Homepage

Renders `{{ .Content }}` from `content/_index.md` followed by the affiliates grid from `data/affiliates.yaml`.

### `layouts/meetings/single.html` — Meeting Dates Page

Triggered by `type: meetings` in `content/meeting-dates.md` front matter.

Logic:
1. Builds a scratch map of meeting-minutes posts keyed by `date` string
2. Splits `data/meetings.yaml` into upcoming (≥ now) and past (< now)
3. Renders: next meeting callout → upcoming list → past list with post links

### `layouts/posts/single.html` — Meeting Post Pages

Renders all meeting posts. See [meeting-post-workflow.md](meeting-post-workflow.md) for full details.

### `layouts/_default/_markup/render-image.html` — Image Render Hook

Applied to every markdown image sitewide. Handles two things:

1. **Subdirectory path fix:** root-relative paths (`/images/...`) are rewritten to include the `/www/` deploy prefix by stripping the leading `/`, calling `absURL` (which appends to the full baseURL), then extracting the path component.
2. **Figure wrapping:** if the image has a title (`![alt](src "title")`), wraps in `<figure class="post-figure"><img>...<figcaption>title</figcaption></figure>`. Without a title, renders a plain `<img>`.

---

## CSS System

Two custom stylesheets are loaded after the theme CSS, in order:

### `static/css/theme.css` — Color Palette Override

Overrides the dot-org theme's green primary palette with warm walnut/amber tones via `:root` CSS custom property overrides:

| Variable | Value | Role |
|---|---|---|
| `--primary-400` | `#c07d3a` | Warm amber oak |
| `--primary-500` | `#a0621e` | Medium walnut (links) |
| `--primary-600` | `#7a4a18` | Dark walnut (link hover) |
| `--primary-700` | `#5c3410` | Deep brown (buttons) |
| `--primary-800` | `#3d2b1f` | Espresso (button hover, UI accents) |

All theme components (buttons, links, footer hover, site title) inherit these automatically.

### `static/css/posts.css` — Component Styles

Contains styles for:
- **Meeting post layout** — `.meeting-meta`, `.meeting-demo`, `.meeting-next`
- **Post images** — `.post-figure`, `figcaption`
- **Affiliates — homepage grid** — `.affiliates-section`, `.affiliates-grid`, `.affiliate-card`
- **Affiliates — post rail** — `.affiliates-rail`
- **Meeting dates page** — `.meeting-next-callout`, `.meeting-list`, `.meeting-section-heading`

**Adding styles:** append to `posts.css`. To add a new stylesheet, add its path (no leading `/`) to `custom_css` in `config/_default/params.yaml`.

---

## Archetypes

### `archetypes/posts.md`

Scaffold for new meeting posts. Use:
```bash
hugo new posts/2026-01-january-meeting.md
```
Pre-fills title, date, author, categories, and all front matter fields used by the post layout.
