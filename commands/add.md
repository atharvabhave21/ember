---
description: Add a new contact to your Notion Contacts database. Supports conversational flow or a fast-track one-liner. Use when asked to add a contact, save someone's info, or track a new person in the network.
argument-hint: "[Name, Role at Company, how you met]"
---

# /ember:add

Add a new contact to the Notion Contacts database.

Supports two modes:
- `/ember:add` — conversational flow, asks one field at a time
- `/ember:add [info]` — fast-track, parses the one-liner and asks only for missing fields

Example fast-track: `/ember:add Alex Rivera, PM at Acme Corp, met at a Kellogg networking event`

Behaviour is defined in `skills/01-contact-intake.md`.
