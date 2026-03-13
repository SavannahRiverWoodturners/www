# Claude Context — Savannah River Woodturners Club Website

Hugo-based website for the Savannah River Woodturners Club, hosted on GitHub Pages at savannahriverwoodturners.org.

## Documentation

- **[.claude/site-overview.md](.claude/site-overview.md)** — Club background, audience, officers, and the relationship to the old Jekyll site
- **[.claude/architecture.md](.claude/architecture.md)** — Hugo structure, tech stack, theme status, nav menu, local dev
- **[.claude/meeting-post-workflow.md](.claude/meeting-post-workflow.md)** — How members submit meeting write-ups via GitHub Issues and how the automation publishes them

## Key Facts

- No theme selected yet — `layouts/` is empty and `hugo.toml` has `theme` commented out
- Content lives in `content/`; static assets in `static/`
- Deployment triggers automatically on push to `main` via `.github/workflows/hugo.yml`
- Meeting post images are stored under `static/images/posts/`
- The old Jekyll site is at `../savannahriverwoodturners.github.io` for reference
