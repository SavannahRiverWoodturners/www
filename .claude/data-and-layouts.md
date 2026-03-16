# Data Files, Layouts, and CSS

## Data Files

### `data/meetings.yaml`

Single source of truth for all meeting schedule metadata. The layout templates split entries into upcoming/past at build time using Hugo's `now`. **All schedule display across the site — homepage card, meeting-dates page, and post "next meeting" footer — reads exclusively from this file.**

```yaml
- date: "2026-03-21"    # ISO 8601 — must match the post's date: field for the Read more link to appear
  time: "9:00 am"
  note: ""              # optional — shown for special events (e.g. "Christmas Party")
  location: ""          # optional — where the meeting is held
  demonstrator: ""      # optional — who is demonstrating
  project: ""           # optional — brief description of the demonstration topic
```

- **Adding a meeting:** add a new entry in chronological order
- **Past/upcoming split:** automatic at build time — no manual editing when meetings pass
- **Next meeting callout:** first entry with date ≥ today
- **Post link:** if a post exists with `date: YYYY-MM-DD` and `categories: ["meeting-minutes"]` matching `.date`, a "Read more →" link appears automatically
- **Omitting optional fields:** leave them out entirely — templates render nothing for absent fields
- **Naming note:** posts use `demonstration` for the full topic description; YAML uses `project` as a shorter schedule label. Both refer to the same concept — no need to keep them identical.

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

Defines two blocks:

- **`hero` block** — full-width image (`img/hero-lathe.jpg`) with dark overlay and title/subtitle text.
- **`main` block** — two-column grid layout:
  - **Sidebar (1/3, source-order first):** contains the **Next Meeting card**, which reads `date`, `time`, `note`, `location`, `demonstrator`, and `project` from the first upcoming entry in `data/meetings.yaml`. On mobile the sidebar stacks above the prose.
  - **Prose (2/3):** renders `{{ .Content }}` from `content/_index.md`.
  - **Affiliates section** (full-width, below the grid): rendered from `data/affiliates.yaml`.

The two-column grid uses `grid-template-areas` so the sidebar is always visually right of the prose on desktop regardless of source order.

### `layouts/meetings/single.html` — Meeting Dates Page

Triggered by `type: meetings` in `content/meeting-dates.md` front matter.

Logic:
1. Builds a scratch map of meeting-minutes posts keyed by `date` string
2. Splits `data/meetings.yaml` into upcoming (≥ now) and past (< now)
3. Renders:
   - **Next meeting callout** (dark card) — first upcoming entry; shows date, time, note, location, demonstrator, project
   - **Upcoming meetings list** — remaining upcoming entries; same fields as callout
   - **Past meetings list** — reversed chronological; shows date, time, note, location, demonstrator, project from YAML plus a "Read more →" link when a post exists

All metadata (location, demonstrator, project) comes from `data/meetings.yaml` for both upcoming and past entries. Post front matter is not used here.

### `layouts/posts/single.html` — Meeting Post Pages

Renders all meeting posts. See [meeting-post-workflow.md](meeting-post-workflow.md) for full details.

The "Next Meeting" footer banner at the bottom of each post is **auto-sourced from `data/meetings.yaml`** — it finds the first YAML entry with a date strictly after the post's own date, then displays that entry's date, time, and location. No front matter is needed for this section.

### `layouts/_default/contact.html` — Contact Page

Triggered by `layout: contact` in `content/contact.md`. Renders two styled rows (address and phone) with inline SVG icons. No data file — content is hard-coded in the layout.

### `layouts/_default/club-officers.html` — Club Officers Page

Triggered by `layout: club-officers` in `content/club-officers.md`. Renders an officer photo and a grid of officer cards from `data/officers.yaml`.

### `layouts/_default/club-history.html` — Club History Page

Triggered by `layout: club-history` in `content/club-history.md`. Renders a two-column layout with prose and a framed vase image.

### `layouts/_default/_markup/render-image.html` — Image Render Hook

Applied to every markdown image sitewide. Handles two things:

1. **Subdirectory path fix:** root-relative paths (`/images/...`) are rewritten to include the `/www/` deploy prefix by stripping the leading `/`, calling `absURL` (which appends to the full baseURL), then extracting the path component.
2. **Figure wrapping:** if the image has a title (`![alt](src "title")`), wraps in `<figure class="post-figure"><img>...<figcaption>title</figcaption></figure>`. Without a title, renders a plain `<img>`.

---

## CSS System

Two custom stylesheets are loaded after the theme CSS, in order:

### `static/css/theme.css` — Color Palette and Page-level Components

Overrides the dot-org theme's green primary palette with warm walnut/amber tones via `:root` CSS custom property overrides:

| Variable | Value | Role |
|---|---|---|
| `--primary-400` | `#c07d3a` | Warm amber oak |
| `--primary-500` | `#a0621e` | Medium walnut (links) |
| `--primary-600` | `#7a4a18` | Dark walnut (link hover) |
| `--primary-700` | `#5c3410` | Deep brown (buttons) |
| `--primary-800` | `#3d2b1f` | Espresso (button hover, UI accents) |

All theme components (buttons, links, footer hover, site title) inherit these automatically.

Also contains page-specific component styles:

| Component | Classes |
|---|---|
| Homepage two-column grid | `.homepage-grid`, `.homepage-prose`, `.homepage-sidebar` |
| Homepage Next Meeting card | `.next-meeting-card`, `__eyebrow`, `__date`, `__details`, `__location`, `__demo`, `__link` |
| Contact page | `.contact-card`, `.contact-item`, `.contact-icon`, `.contact-label`, `.contact-value`, `.contact-phone`, `.contact-person` |
| Club Officers page | `.officers-photo-wrap`, `.officers-photo`, `.officers-grid`, `.officer-card`, `__role`, `__name` |
| Club History page | `.history-layout`, `.history-text`, `.history-vase` |
| Homepage hero | `.hero`, `.hero__image`, `.hero__overlay`, `.hero__content`, `.hero__title`, `.hero__subtitle` |

### `static/css/posts.css` — Post and Meeting Page Components

Contains styles for:
- **Meeting post layout** — `.meeting-meta`, `.meeting-demo`, `.meeting-next` (incl. `__time`, `__location`)
- **Post images** — `.post-figure`, `figcaption`
- **Affiliates — homepage grid** — `.affiliates-section`, `.affiliates-grid`, `.affiliate-card`
- **Affiliates — post rail** — `.affiliates-rail`
- **Meeting dates page** — `.meeting-next-callout` (incl. `__location`, `__demo`), `.meeting-list` (incl. `__extra`, `__location`, `__demo`), `.meeting-section-heading`

**Adding styles:** append to the relevant file. To add a new stylesheet, add its path (no leading `/`) to `custom_css` in `config/_default/params.yaml`.

---

## Archetypes

### `archetypes/posts.md`

Scaffold for new meeting posts. Use:
```bash
hugo new posts/2026-01-january-meeting.md
```
Pre-fills title, date, author, categories, and all front matter fields used by the post layout.
