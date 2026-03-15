# Meeting Post Submission Workflow

Members submit meeting write-ups via GitHub Issues without needing Git or Hugo knowledge.

## Flow

1. Member opens a new issue using the **Blog Post** template (`.github/ISSUE_TEMPLATE/blog-post.yml`)
2. They fill in: meeting date, author name, title, content (markdown), and optionally drag-and-drop photos
3. Issue is auto-labeled `blog-post` + `pending-review`
4. A club admin reviews and applies label `approved` or `denied`
5. The `process-blog-post.yml` workflow fires on the label event:
   - **approved:** Parses the issue form, downloads attached images to `static/images/posts/YYYY-MM-DD/`, creates a Hugo post in `content/posts/`, commits and pushes → triggers the `hugo.yml` deploy. Comments on the issue and closes it.
   - **denied:** Comments with rejection message and closes the issue.

## Hugo Post Front Matter (auto-generated)
```yaml
---
title: "September 2025 Meeting Minutes"
date: 2025-09-20
author: "John Doe"
categories: ["meeting-minutes"]
attendees: 12
demonstrator: "Jane Smith"
demonstration: "Hollow Forms on the Lathe"
next_meeting: "2025-10-18"
next_meeting_location: "Wheatly shop"
---
```

The `layouts/posts/single.html` template renders `attendees`, `demonstrator`, `demonstration`, `next_meeting`, and `next_meeting_location` as styled UI elements above and below the post body.

## Image Handling
- Images attached to the GitHub Issue are downloaded to `static/images/posts/YYYY-MM-DD/image-N.jpg`
- In-content references use the image title syntax for captions: `![alt text](/images/posts/YYYY-MM-DD/image-N.jpg "Caption text here")`
- The Goldmark render hook at `layouts/_default/_markup/render-image.html` wraps titled images in `<figure>`/`<figcaption>` automatically
- Do **not** use italicized text on the line after an image for captions — use the title syntax above

## Submit Link
The "Submit Minutes" page links directly to the issue form:
`https://github.com/SavannahRiverWoodturners/www/issues/new?template=blog-post.yml`

> **Note:** Update this URL in `content/submit-minutes.md` once the repo is confirmed at `SavannahRiverWoodturners/www`.
