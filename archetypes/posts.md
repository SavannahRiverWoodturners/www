---
title: "{{ replace .File.ContentBaseName "-" " " | title }} Meeting Minutes"
date: {{ .Date }}
author: "Savannah River Woodturners"
categories: ["meeting-minutes"]
attendees:
demonstrator: ""
demonstration: ""
next_meeting:
next_meeting_location: "Wheatly shop"
---

Brief notes on club business discussed at the meeting.

## Demonstration: {{ .Params.demonstration }}

Introduce the demonstrator and topic here.

---

## Show 'n Tell

Notes on Show 'n Tell items.
