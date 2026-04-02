# /ember:add

Add a new contact to the Notion Contacts database.

Supports two modes:
- `/ember:add` — conversational flow, asks one field at a time
- `/ember:add [info]` — fast-track, parses the one-liner and asks only for missing fields

Example fast-track: `/ember:add Alex Rivera, PM at Acme Corp, met at a Kellogg networking event`

Behaviour is defined in `skills/01-contact-intake.md`.
