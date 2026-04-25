---
description: Logs a new job application or updates an existing one in the Notion Applications database. Supports conversational flow or a fast-track one-liner. Triggers when the user runs /ember:apply, wants to log an application, or says things like "I applied to [role] at [company]" or "track my application at [company]".
---

# Ember Skill 07 — Application Intake

## Purpose
Log a new job application to the user's Notion Applications database.
Links the application to the Companies database and optionally to a contact (referral).
Supports both fast-track and conversational flows.
All select field options (Status, Priority) must be fetched dynamically from the
Applications database schema — never hardcode them.

---

## Trigger
- User types `/ember:apply`
- User types `/ember:apply` followed by application info (fast-track)
- User says "I applied to [role] at [company]" or "track my application at [company]"
- User says "update my [company] application to [status]"

---

## Behavior

### Step 1 — Detect mode

**Update mode** — if user mentions updating an existing application status:
→ Search Applications DB for matching record → update Status field → confirm → exit.
Example: "Mark my Zepto PM application as Phone Screen"

**New application mode** — all other cases:
→ Check if user included info. If yes → Fast-track. If no → Conversational.

---

### Conversational flow

**1a. Role Title**
> "What role are you applying for?"

**1b. Company**
> "At which company?"

Silently search Companies DB for this company.
- If found and Is Target = true → link the application to that record silently
- If found and Is Target = false → link silently, then ask: "Want to mark [Company] as a target in your tracker?"
- If not found → create a Companies DB record (Is Target = true, Overall Status = Targeting) and link

**1c. Priority**
Fetch Priority options from Applications DB schema dynamically.
> "How would you prioritise this one — High, Medium, or Low?"

**1d. Job URL**
> "Do you have the job posting URL? (You can skip this)"

If skipped → leave blank.

**1e. Applied Date**
> "Did you apply today, or on a different date?"

Default to today if they say "today" or don't specify.

**1f. Referral Contact**
> "Do you have a contact at [Company] who referred you or is helping you? (You can skip this)"

If yes → search Contacts DB for that person and link via Referral Contact relation.
If no / skipped → leave blank.

---

### Fast-track flow

Parse the one-liner. Extract whatever fields are present:
Role Title, Company, Priority, Job URL, Applied Date, Referral Contact

Check if company exists in Companies DB before writing.

For each **missing essential field** (Role Title, Company, Priority), ask one at a time.
For each **present field**, skip and move on.
Default Applied Date to today if not mentioned.
Default Status to **Saved** if user is tracking but hasn't applied yet,
or **Applied** if user says "I applied".

---

### Step 2 — Infer Status silently

Do NOT ask for Status during intake. Infer from context:

| Signal | Status |
|---|---|
| "I applied", "submitted", "sent in" | Applied |
| "I want to track", "saving this", "found a role" | Saved |
| "I have a phone screen", "call scheduled" | Phone Screen |
| "technical round", "case study", "assessment" | Technical |
| "final round", "last stage", "panel interview" | Final Round |

---

### Step 3 — Write to Notion

Search the user's Notion workspace for the **Applications** database by name.
Do NOT use any hardcoded URL — always find it dynamically.
Fetch the database schema to get current select options before writing select fields.

Write the following fields:
- **Role Title** → from intake
- **Company** → relation link to Companies DB (always populated)
- **Status** → inferred in Step 2
- **Priority** → from intake
- **Job URL** → from intake (blank if skipped)
- **Applied Date** → from intake (today if not specified)
- **Referral Contact** → relation to Contacts DB (blank if skipped)
- **Notes** → populate if user shared any context unprompted

---

### Step 4 — Update company Overall Status

After creating the application, silently check the linked company's Overall Status.

| Current Status | Suggest update | When |
|---|---|---|
| Researching | Targeting | Application is Saved |
| Researching or Targeting | Active | Application is Applied or beyond |
| Any | No change | Status already Active or beyond |

Ask once:
> "Should I update [Company]'s status to [suggested status]?"

If yes → update Companies DB record.
If no → leave as is.

---

### Step 5 — Confirm + offer next step

> "Logged — [Role Title] at [Company] · [Status] · [Priority] priority."

If a Follow-Up Date makes sense (e.g. applied status → follow up in 1 week):
> "Want me to set a follow-up reminder for this application?"

If yes → set Follow-Up Date to 7 days from today (or ask for specific date).
If no → end cleanly.

---

## Tone guidelines
- Efficient and action-oriented — the user is in job-search mode
- Keep confirmations tight — role, company, status, priority in one line
- Never make the user feel like they're filling a form

## What NOT to do
- Do not hardcode select options — always fetch from Applications DB schema dynamically
- Do not hardcode any Notion URLs — always search dynamically
- Do not ask for Status — always infer it from context
- Do not ask for Notes — populate only if volunteered
- Do not create a duplicate application — if the same role + company already exists, ask if they want to update it instead
- Do not skip the company resolution step — always link to Companies DB
- Do not ask more than one question at a time
