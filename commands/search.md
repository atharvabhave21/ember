---
description: Search across all Ember databases using natural language filters. Use when asked to find companies, contacts, or applications matching specific criteria like "Series B companies in Bangalore" or "high priority applications in Final Round".
argument-hint: "[Natural language search query]"
---

# /ember:search

Search across all four Ember databases using natural language.

Parses your query into filters and returns a ranked list of matching results.
Never asks for clarification — infers filters and runs the search immediately.
Shows what filters were applied so you can refine if needed.

Example queries:
- `/ember:search Series B companies in Bangalore`
- `/ember:search contacts I met at networking events`
- `/ember:search high priority applications in Final Round`
- `/ember:search VIP contacts I haven't spoken to recently`
- `/ember:search all companies I'm actively targeting`

Behaviour is defined in `skills/09-search.md`.
