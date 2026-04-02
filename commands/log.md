---
description: Log an interaction with a contact into your Notion Interactions database. Supports conversational flow or a fast-track one-liner. Use when asked to log a call, coffee chat, email, DM, or any interaction with a contact.
argument-hint: "[interaction details, e.g. coffee chat with Alex yesterday, discussed X, follow up next week]"
---

# /ember:log

Log an interaction with a contact into the Notion Interactions database.

Supports two modes:
- `/ember:log` — conversational, asks one field at a time
- `/ember:log [info]` — fast-track one-liner

Example fast-track: `/ember:log — coffee chat with Jamie yesterday, she offered to make an intro, follow up next week`

Always captures the Next Step field — this powers follow-up drafting in `/ember:follow-up`.

Behaviour is defined in `skills/04-log.md`.
