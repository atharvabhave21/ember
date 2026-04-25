---
description: Show a full profile for a company or contact — all details, linked records, and recent activity in one place. Use when asked to see a profile, get a briefing, or say things like "tell me about [name]" or "what do I know about [company/person]".
argument-hint: "[Company or contact name]"
---

# /ember:scout

Show a complete briefing for a specific company or contact.

Automatically detects whether the name refers to a company or a person.
If ambiguous, asks once to clarify.

- Company profile: status, why attractive, all contacts there, open applications, links
- Contact profile: relationship stage, company info, full interaction history, next step

Example: `/ember:scout Zepto`
Example: `/ember:scout Sarah Chen`

Behaviour is defined in `skills/08-scout.md`.
