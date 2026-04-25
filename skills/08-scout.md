---
description: Shows a full profile for a company or a contact — all details, linked records, and recent activity in one place. Triggers when the user runs /ember:scout, asks to see a company or contact profile, or says things like "tell me about [name]" or "what do I know about [company/person]".
---

# Ember Skill 08 — Scout

## Purpose
Show a complete briefing for a specific company or contact.
Detects automatically whether the user is asking about a company or a person.
Pulls together all linked data — contacts at a company, applications, interaction
history — so the user has everything they need in one view.

---

## Trigger
- User types `/ember:scout [name]`
- User says "tell me about [name]" or "what do I know about [company/person]"
- User says "show me [name]'s profile"

---

## Behavior

### Step 1 — Detect entity type

Search both Companies DB and Contacts DB simultaneously for the name provided.

**If only found in Companies DB** → run **Company Profile** flow.
**If only found in Contacts DB** → run **Contact Profile** flow.
**If found in both** → ask:
> "Did you mean the company [Name] or the person [Name]?"

**If found in neither:**
> "I couldn't find [Name] in your tracker. Want to add them as a contact (`/ember:contact`) or a company (`/ember:company`)?"

---

## Company Profile Flow

### Step 2C — Load all company data

Fetch from Companies DB:
- Name, Is Target, Industry, Stage, Size, HQ City, Website, LinkedIn
- Why Attractive, Notes, Overall Status, Source
- All linked Contacts (from Contacts DB relation)
- All linked Applications (from Applications DB relation)

For each linked contact, fetch: Name, Role, Relationship Stage, Last Interaction date + type + summary
For each linked application, fetch: Role Title, Status, Priority, Applied Date, Follow-Up Date

---

### Step 3C — Present company briefing

> **[Company Name]** · [Industry] · [Stage] · [Size] · [HQ City]
> [Overall Status badge] · [Is Target: Targeting / Directory only]
>
> **Why attractive:** [Why Attractive field]
> [Notes if present]
>
> **Your contacts there ([count]):**
> - [Name] · [Role] · [Stage] · Last: [Type] on [Date] — [summary]
> - [Name] · [Role] · [Stage] · Last: [Type] on [Date] — [summary]
> *(If no contacts: "No contacts linked yet.")*
>
> **Open applications ([count]):**
> - [Role Title] · [Status] · [Priority] priority · Applied [Date]
> *(If no applications: "No applications logged yet.")*
>
> **Links:** [Website if present] · [LinkedIn if present]

---

### Step 4C — Offer to act

> "What do you want to do?"
> - Draft a message to one of your contacts there → `/ember:reach-out` or `/ember:follow-up`
> - Log a new application → `/ember:apply`
> - Update the company's status

Handle whichever the user picks. If updating status → fetch current Status options
from Companies DB schema dynamically, present them, update on confirmation.

---

## Contact Profile Flow

### Step 2P — Load all contact data

Fetch from Contacts DB:
- Name, Role, LinkedIn URL, How We Met, Relationship Stage, Tags, Next Follow-Up Date, Notes
- Linked Company record (from Companies DB relation) → fetch: Name, Industry, Overall Status
- All interactions from Interactions DB linked to this contact, sorted by date descending

For interactions, load up to 5 most recent: Date, Type, Direction, Summary, Next Step

---

### Step 3P — Present contact briefing

> **[Name]** · [Role] at [Company] · [Relationship Stage]
> *Met via [How We Met]* [· Tags if present]
>
> **Company:** [Company Name] · [Industry] · [Overall Status if Is Target, else omit]
> [LinkedIn URL if present]
>
> **Interaction history:**
> - [Date] · [Type] · [Direction] — [Summary] → Next: [Next Step]
> - [Date] · [Type] · [Direction] — [Summary] → Next: [Next Step]
> *(If no interactions: "No interactions logged yet.")*
>
> **Next follow-up:** [Next Follow-Up Date if set, else "Not set"]
> [Notes if present]

---

### Step 4P — Offer to act

> "Want to draft a message or log an interaction?"
> - Draft outreach → `/ember:reach-out` (if stage is Prospect or Request Sent)
> - Draft follow-up → `/ember:follow-up` (if stage is Connected, Active, or Dormant)
> - Log an interaction → `/ember:log`

Hand off to the relevant skill with contact pre-filled.

---

## Tone guidelines
- This is a briefing, not a data dump — present it like a smart summary
- Keep each line tight — no padding or filler text
- If a field is empty, say so briefly or omit the section entirely
- The goal is to give the user everything they need to act in the next 30 seconds

## What NOT to do
- Do not hardcode any Notion URLs — always search dynamically
- Do not ask the user what type of entity they want before searching — always search both and infer
- Do not show all interactions — cap at 5 most recent
- Do not show empty sections with no data — omit or collapse them
- Do not ask clarifying questions before showing the profile — load first, then offer actions
- Do not show raw Notion field names — use plain English labels
