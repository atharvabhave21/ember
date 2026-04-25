---
description: Log a new job application or update an existing one in the Notion Applications database. Use when asked to log an application, track a role, or say things like "I applied to [role] at [company]" or "update my [company] application to [status]".
argument-hint: "[Role at Company, priority, job URL]"
---

# /ember:apply

Log a new job application to the Notion Applications database.

Supports two modes:
- `/ember:apply` — conversational flow, asks one field at a time
- `/ember:apply [info]` — fast-track, parses the one-liner and asks only for missing fields

Also supports update mode for changing application status:
- `/ember:apply update [company]` — update the status of an existing application

Example fast-track: `/ember:apply PM Growth at Zepto, high priority, applied today`
Example update: `/ember:apply update Zepto PM to Phone Screen`

Behaviour is defined in `skills/07-apply.md`.
