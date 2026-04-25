---
description: Shows a bird's eye view of all target companies, grouped by status, with contacts and applications at each. Triggers when the user runs /ember:pipeline, asks about their company pipeline, or says things like "where do I stand with companies" or "show me my job search pipeline".
---

# Ember Skill 10 — Pipeline

## Purpose
Give the user a complete, actionable overview of their job search pipeline at the
company level. Shows all target companies grouped by Overall Status, with key signals
about contacts and applications at each. Flags stale companies that need attention.

This is the company-level equivalent of `/ember:nudge` — but instead of urgency ranking,
it shows the full pipeline so the user can see where everything stands.

---

## Trigger
- User types `/ember:pipeline`
- User says "show me my pipeline" or "where do I stand with companies"
- User says "what's my job search looking like"

---

## Behavior

### Step 1 — Query all target companies

Search the Companies database dynamically. Never use hardcoded URLs.

Load **only companies where Is Target = true**.

For each company, fetch:
- Name, Industry, Stage, HQ City, Overall Status
- All linked Contacts → count + most recent interaction date per contact
- All linked Applications → Role Title, Status, Priority for each

Calculate today's date. For each company determine:
- Days since last interaction with any linked contact
- Whether any application has a passed Follow-Up Date
- Whether the company is stale (see thresholds below)

**Stale thresholds by Overall Status:**
| Status | Stale if no contact interaction in |
|---|---|
| Researching | 21 days |
| Targeting | 14 days |
| Active | 10 days |
| Passed | Never — already resolved |

---

### Step 2 — Group and format

Group companies by Overall Status in this order:
1. **Active** (highest priority)
2. **Targeting**
3. **Researching**
4. **Passed** (show collapsed at the bottom)

**If no target companies exist:**
> "You haven't added any target companies yet. Start with `/ember:company` to add one."

Stop. Do not proceed.

**If companies exist**, present grouped by status:

---

> **── Active ──**
> - **[Company]** · [Industry] · [Stage] · [HQ City]
>   [N] contact(s) · Last touch: [X days ago] [⚠️ if stale]
>   Applications: [Role] ([Status] · [Priority]) [, Role ([Status]) ...]
>
> **── Targeting ──**
> - **[Company]** · [Industry] · [Stage] · [HQ City]
>   [N] contact(s) · Last touch: [X days ago] [⚠️ if stale]
>   Applications: [Role] ([Status] · [Priority]) [or "None yet"]
>
> **── Researching ──**
> - **[Company]** · [Industry] · [Stage] · [HQ City]
>   [N] contact(s) [or "No contacts yet"] · [⚠️ if stale]
>   Applications: [Role] ([Status]) [or "None yet"]
>
> **── Passed ──**
> - [Company] · [Industry] · [reason passed if in Notes]

Use ⚠️ to flag stale companies inline — do not explain the threshold, just mark it.

---

### Step 3 — Surface the top action

After showing the full pipeline, identify the single most important action:

**Priority logic:**
1. Any application in Final Round with no interaction in 5+ days → most urgent
2. Any application with a passed Follow-Up Date
3. Any Active company that is stale
4. Any Targeting company with no contacts linked after 14 days
5. Any Researching company with no activity after 21 days

> "The most important thing right now: [one-line action]"
> e.g. "Your Final Round application at Zepto has had no activity in 7 days — worth following up."
> e.g. "You've been targeting Razorpay for 16 days with no contacts added."

---

### Step 4 — Offer to act

> "Want to dig into any of these or take action?"

- "Tell me more about [Company]" → hand off to Skill 08 (`/ember:scout`) with company pre-filled
- "Update [Company]'s status" → fetch current Status options from Companies DB schema dynamically, update on confirmation
- "Draft a message to someone at [Company]" → identify which contact, hand off to Skill 02 or 03
- "Log an application at [Company]" → hand off to Skill 07 (`/ember:apply`) with company pre-filled
- "Mark [Company] as Passed" → update Overall Status, ask for brief reason to add to Notes

---

## Tone guidelines
- This is a command centre view — confident, scannable, action-oriented
- Each company entry is 2–3 lines max — no walls of text
- The stale flag (⚠️) should feel like a gentle nudge, not an alarm
- The top action callout should feel like a smart assistant's insight, not a nag

## What NOT to do
- Do not hardcode any Notion URLs — always search dynamically
- Do not show companies where Is Target = false — this is a pipeline view, not a directory
- Do not show Passed companies prominently — they go at the bottom, collapsed
- Do not explain the stale threshold in the output — just flag it with ⚠️
- Do not offer to act on multiple companies at once — one at a time
- Do not skip the top action callout — it is the most valuable part of this skill
- Do not fetch select options from hardcoded lists — always read from the DB schema
