# Meeting Post Workflow

## Overview

Meeting posts live in `content/posts/`. They use a custom layout (`layouts/posts/single.html`) that renders structured metadata from front matter above and below the main content.

## Creating a Post from PDF Minutes

1. Drop the PDF into `inbox/`
2. Extract images with PyMuPDF:
   ```python
   import fitz, os
   doc = fitz.open("inbox/S2025-11_MeetingMinutes.pdf")
   os.makedirs("static/images/posts/YYYY-MM-DD", exist_ok=True)
   img_num = 1
   for page in doc:
       for img in page.get_images(full=True):
           base = doc.extract_image(img[0])
           open(f"static/images/posts/YYYY-MM-DD/image-{img_num}.{base['ext']}", "wb").write(base["image"])
           img_num += 1
   ```
3. Create the post file: `hugo new posts/YYYY-MM-slug.md` (uses `archetypes/posts.md`)
4. Fill in front matter and write the post body
5. Update `data/meetings.yaml` — add `location`, `demonstrator`, and `project` to the entry for this meeting date if not already present

## Front Matter

All fields used by `layouts/posts/single.html`:

```yaml
---
title: "November 2025 Meeting Minutes"
date: 2025-11-15              # Must match the date in data/meetings.yaml for the post link to appear
author: "Savannah River Woodturners"
categories: ["meeting-minutes"]  # Required — used by meeting dates page to link to this post
attendees: 14                    # Total headcount (members + guests)
demonstrator: "Wayne Jensen"     # Displayed in the demonstrator callout block
demonstration: "Glass Bulb Snowmen with String Light Entrails"  # Detailed topic description
---
```

**Fields no longer needed:** `next_meeting` and `next_meeting_location` are ignored by templates. The "Next Meeting" footer is now auto-sourced from `data/meetings.yaml`.

**Important:** `date` must exactly match the corresponding entry in `data/meetings.yaml` (format `YYYY-MM-DD`). The meeting dates page builds a lookup map keyed by this date to generate the "Read more →" link.

## Updating `data/meetings.yaml` After a Meeting

When creating a post, also update the YAML entry for that meeting date with any metadata that wasn't filled in beforehand:

```yaml
- date: "2026-02-21"
  time: "9:00 am"
  location: "Dixon's s'hanger"
  demonstrator: "Gene Dixon"
  project: "Creative problem solving with a spindle key holder"
```

This ensures the meeting-dates page and the next-meeting footer in subsequent posts all display correct information. The `project` field in YAML is a short schedule label; the `demonstration` field in post front matter is the full topic description — they can differ slightly.

## Post Body

Write in standard markdown. Suggested sections:

```markdown
Brief club business notes (elections, announcements, upcoming schedule).

## Demonstration: [Topic]

Intro to the demonstrator and topic.

### [Sub-section, e.g. Fixturing]

Content...

![alt text](/images/posts/YYYY-MM-DD/image-N.jpg "Caption text here")

---

## Show 'n Tell

Notes on member show and tell items.
```

## Image Caption Syntax

**Use the markdown title syntax — NOT italicized text below the image.**

```markdown
✅ Correct:
![Wayne at the lathe](/images/posts/2025-11-15/image-5.jpeg "On with the turning")

❌ Wrong (render hook cannot see this caption):
![Wayne at the lathe](/images/posts/2025-11-15/image-5.jpeg)
*On with the turning*
```

The Goldmark render hook at `layouts/_default/_markup/render-image.html` automatically wraps titled images in `<figure>`/`<figcaption>`. Images without a title render as plain `<img>` tags.

## How the Layout Renders the Post

`layouts/posts/single.html` renders these sections in order:

1. **Metadata bar** — date and attendance count from front matter
2. **Demonstrator callout** — dark wood-toned block with name (`demonstrator`) and topic (`demonstration`) from front matter
3. **Post body** — `.Content` (the markdown)
4. **Affiliate rail** — driven by `data/affiliates.yaml` (AAW, Beyond the Bevel)
5. **Next meeting banner** — auto-sourced from `data/meetings.yaml`: finds the first entry with a date strictly after the post's own date, displays that entry's date, time, and location

## GitHub Issue Submission (for club members)

Members without Git access can submit posts via GitHub Issues:

1. Member opens: `https://github.com/SavannahRiverWoodturners/www/issues/new?template=blog-post.yml`
2. Fills in meeting date, author, title, content, drag-and-drops photos
3. Issue is auto-labeled `blog-post` + `pending-review`
4. Admin applies `approved` or `denied` label
5. `process-blog-post.yml` workflow fires:
   - **approved:** downloads images to `static/images/posts/YYYY-MM-DD/`, creates post, commits & pushes
   - **denied:** comments with reason and closes

> **Note:** The `process-blog-post.yml` automation is scaffolded but not yet fully active. Manual post creation (described above) is the current workflow.

## Meeting Dates Integration

After creating a post, push to `main`. The deploy rebuilds the site and the meeting dates page (`data/meetings.yaml` → `layouts/meetings/single.html`) will automatically:
- Move that meeting from "Upcoming" to "Past"
- Show a "Read more →" link on that meeting row
- Display `location`, `demonstrator`, and `project` from the YAML entry inline

No edits to `data/meetings.yaml` are needed solely for the post link — just the post `date` front matter must match. But do update the YAML metadata fields if they weren't filled in when the meeting was scheduled.
