# Savannah River Woodturners Club Website

Official website for the **Savannah River Woodturners Club**, serving members, prospective members, and anyone interested in woodturning in the CSRA (Central Savannah River Area) region.

Live site: [savannahriverwoodturners.org](https://savannahriverwoodturners.org)

## About the Site

Built with [Hugo](https://gohugo.io/) and hosted on GitHub Pages. The site provides:

- **Club information** — history, officers, and contact details
- **Meeting schedule** — upcoming meeting dates and times
- **Meeting posts** — write-ups and photos from previous meetings

## Site Structure

| Path | Description |
|------|-------------|
| `content/_index.md` | Homepage |
| `content/club-history.md` | Club history (founded 2008, affiliated with AAW) |
| `content/club-officers.md` | Current officers and advisory board |
| `content/meeting-dates.md` | Upcoming meeting schedule |
| `content/contact.md` | Contact information |
| `content/submit-minutes.md` | Instructions for submitting meeting posts |
| `content/posts/` | Meeting write-ups and other blog posts |
| `static/img/` | Site images and logos |
| `hugo.toml` | Site configuration |

## Meetings

Meetings are held on **Saturdays at 9:00 am** at 144 North Belair Road, Evans, Georgia. The December meeting is typically a Christmas party at noon.

## Publishing Meeting Posts

Members can submit meeting write-ups (with photos) without needing to know Git or Hugo. The process uses a GitHub Issue form:

1. Go to the **Issues** tab and open a new issue using the **Blog Post** template
2. Fill in the meeting date, title, content, and attach any photos
3. Submit the issue — it gets labeled `pending-review`
4. A club admin reviews and labels it `approved` (or `denied`)
5. On approval, a GitHub Actions workflow automatically creates the post, downloads the images, commits everything to the repo, and publishes it to the site

## Local Development

```bash
# Serve locally with live reload
hugo server --buildDrafts
```

The site will be available at `http://localhost:1313`.

## Deployment

Pushing to the `main` branch automatically triggers the GitHub Actions workflow (`.github/workflows/hugo.yml`), which builds and deploys the site to GitHub Pages.

## Theme

The site is ready for a theme. To add one:

```bash
# Example: add a theme as a git submodule
git submodule add https://github.com/author/theme-name themes/theme-name
```

Then set `theme = "theme-name"` in `hugo.toml`.
