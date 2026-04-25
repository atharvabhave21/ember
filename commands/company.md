---
description: Add a target company to your Notion Companies database. Supports conversational flow or a fast-track one-liner. Use when asked to track a company, add a company to the pipeline, or say things like "I want to target [company]".
argument-hint: "[Company, Industry, Stage, HQ City]"
---

# /ember:company

Add a new target company to the Notion Companies database with Is Target = true.

Supports two modes:
- `/ember:company` — conversational flow, asks one field at a time
- `/ember:company [info]` — fast-track, parses the one-liner and asks only for missing fields

Example fast-track: `/ember:company Zepto, quick commerce, Series D, Bangalore, found on LinkedIn`

Behaviour is defined in `skills/06-company.md`.
