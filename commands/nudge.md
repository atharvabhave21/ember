---
description: Surface the contacts you should reconnect with, ranked by urgency. Scans your Contacts and Interactions databases and returns up to 10 people grouped by Overdue, Stuck, and Going Cold. Use when asked who to follow up with, who you've been neglecting, or who to reconnect with.
---

# /ember:nudge

Surface the contacts the user should reconnect with, ranked by urgency.

Scans the Contacts and Interactions databases and returns up to 10 contacts grouped into three tiers:
- **Overdue** — past-due follow-up date
- **Stuck** — pipeline stage hasn't moved in too long
- **Going Cold** — high-value contacts not touched recently

From the nudge list, the user can ask Ember to draft a message for any contact — Ember hands off to the right skill automatically.

Behaviour is defined in `skills/05-nudge.md`.
