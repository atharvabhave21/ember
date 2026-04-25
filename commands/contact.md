---
description: Add a new contact to your Notion Contacts database. Supports conversational flow or a fast-track one-liner. Use when asked to add a contact, save someone's info, or track a new person in the network.
argument-hint: "[Name, Role at Company, how you met]"
---

# /ember:contact

Add a new contact to the Notion Contacts database.

Supports two modes:
- `/ember:contact` — conversational flow, asks one field at a time
- `/ember:contact [info]` — fast-track, parses the one-liner and asks only for missing fields

Example fast-track: `/ember:contact Alex Rivera, PM at Zepto, met at a Kellogg networking event`

Behaviour is defined in `skills/01-contact-intake.md`.
