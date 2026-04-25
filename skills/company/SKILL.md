---
name: company
description: "Adds a new target company to the Notion Companies database. Supports conversational flow or a fast-track one-liner. Triggers when the user runs /ember:company, wants to track a company, or says things like 'I want to add [company]' or 'track [company] as a target'."
argument-hint: "[Company name, industry, stage, city, how you found them]"
---

# Ember Skill 06 — Company Intake

## Purpose
Add a new target company to the user's Notion Companies database with Is Target = true.
Supports both a fast-track one-liner and a conversational flow.
Keeps intake fast — collect the minimum needed, nothing more.
All select field options (Industry, Stage, Size, Source, Overall Status) must be fetched
dynamically from the Companies database schema — never hardcode them.

---

## Trigger
- User types `/ember:company`
- User types `/ember:company` followed by company info (fast-track)
- User says "I want to track [company]" or "add [company] to my pipeline"

---

## Behavior

### Step 1 — Detect mode

**If the user included info** (e.g. `/ember:company Zepto, Series D, quick commerce, Bangalore`):
→ Parse what's there immediately. Go to **Fast-track flow**.

**If the user typed `/ember:company` with nothing else**:
→ Go to **Conversational flow**.

---

### Conversational flow

Ask one field at a time. Do not present this as a form.

**1a. Name**
> "Which company do you want to track?"

**1b. Check if already exists**
Silently search Companies DB for this name. If a record already exists:
> "[Company] is already in your tracker with status [Overall Status]. Want to update it instead?"
- If yes → update the existing record and exit
- If no → end cleanly

**1c. Why Attractive**
> "What makes [Company] interesting to you?"

This is the most important field — capture it fully.

**1d. Industry**
Silently fetch Industry select options from the Companies DB schema.
> "What industry are they in?"
Present the available options. Accept free text and map to closest option.

**1e. Stage**
Silently fetch Stage select options from the Companies DB schema.
> "What's their funding stage?"
Present the available options.

**1f. HQ City**
> "Where are they based?"

**1g. Source**
Silently fetch Source select options from Companies DB schema.
> "How did you find them?"
Present the available options.

**1h. Overall Status**
Default to **Researching** silently — do NOT ask unless the user has mentioned they're
already actively targeting this company, in which case default to **Targeting**.

**Optional fields** — populate silently if the user mentions them, never ask:
- **Size** → if user mentions headcount or size signal
- **Website** → if user provides a URL
- **LinkedIn** → if user provides a LinkedIn company URL
- **Notes** → any additional context the user shares

---

### Fast-track flow

Parse the one-liner. Extract whatever fields are present:
Name, Why Attractive, Industry, Stage, Size, HQ City, Website, Source

Check if company already exists in Companies DB before creating.

For each **missing essential field** (Name, Why Attractive, Industry, HQ City), ask one at a time.
For each **present field**, skip and move on.
Set Overall Status to Researching silently unless context suggests otherwise.

---

### Step 2 — Write to Notion

Search the user's Notion workspace for the **Companies** database by name.
Do NOT use any hardcoded URL — always find it dynamically.
Fetch the database schema to get current select options before writing any select fields.

Write the following fields:
- **Name** → from intake
- **Is Target** → checked (true) — always, this is a target company
- **Why Attractive** → from intake
- **Industry** → from intake (matched to schema option)
- **Stage** → from intake (matched to schema option)
- **HQ City** → from intake
- **Source** → from intake (matched to schema option)
- **Overall Status** → Researching (default) or inferred from context
- **Size** → if provided
- **Website** → if provided
- **LinkedIn** → if provided
- **Notes** → if provided

---

### Step 3 — Confirm + offer next step

> "Done — [Company] is in your pipeline as [Overall Status]."
> "[Why Attractive summary in one line]"
>
> "Do you have any contacts there you want to add, or want to log an application?"

- If contacts → hand off to Skill 01 (`/ember:contact`) with company pre-filled
- If application → hand off to Skill 07 (`/ember:apply`) with company pre-filled
- If neither → end cleanly

---

## Tone guidelines
- Energetic and focused — the user is in research/targeting mode
- Why Attractive is the most important field — give it space
- Keep confirmations tight — name, status, one-line summary

## What NOT to do
- Do not hardcode select options — always fetch from the Companies DB schema dynamically
- Do not hardcode any Notion URLs — always search dynamically
- Do not set Is Target to false — this skill is only for target companies
- Do not ask for Overall Status — infer it silently
- Do not ask for Size, Website, LinkedIn, Notes — populate only if volunteered
- Do not create a duplicate if the company already exists — check first
- Do not ask more than one question at a time
