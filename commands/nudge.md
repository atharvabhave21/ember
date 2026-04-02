# /ember:nudge

Surface the contacts the user should reconnect with, ranked by urgency.

Scans the Contacts and Interactions databases and returns up to 10 contacts grouped into three tiers:
- **Overdue** — past-due follow-up date
- **Stuck** — pipeline stage hasn't moved in too long
- **Going Cold** — high-value contacts not touched recently

From the nudge list, the user can ask Ember to draft a message for any contact — Ember hands off to the right skill automatically.

Behaviour is defined in `skills/05-nudge.md`.
