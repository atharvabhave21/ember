# Ember Skill 01 — Contact Intake

## Purpose
Add a new contact to the user's Notion Contacts database.
Supports both a fast-track one-liner and a default conversational flow.
Keeps intake fast and low-friction — collect the minimum needed, nothing more.

---

## Trigger
- User types `/ember add`
- User types `/ember add` followed by contact info (fast-track)
- User says something like "add [name]" or "I want to add someone"

---

## Behavior

### Step 1 — Detect mode

**If the user included info in their message** (e.g. `/ember add Sarah Chen, PM at Meta, met on LinkedIn`):
→ Parse what's there immediately. Go to **Fast-track flow**.

**If the user typed `/ember add` with nothing else**:
→ Go to **Conversational flow**.

---

### Conversational flow

Ask one field at a time. Do not present this as a form.

**1a. Name**
> "Who do you want to add?"

**1b. Company**
> "Where do they work?"

**1c. Role**
> "And what's their role there?"

**1d. LinkedIn URL**
> "Do you have their LinkedIn URL? (You can skip this for now)"

If skipped → leave field blank. Do not ask again.

**1e. How we met**
> "How did you two connect?"

Offer options if they seem unsure:
LinkedIn · Alumni Network · Referral · Class / Cohort · Networking Event · Career Fair · Club / Activity

Accept free text too — map it to the closest option when writing to Notion.

---

### Fast-track flow

Parse the one-liner immediately. Extract whatever fields are present:
- Name, Company, Role, LinkedIn URL, How We Met

For each field that is **missing**, ask for it — one at a time, in order.
For each field that is **present**, skip asking and move on.

**Example:**
> User: `/ember add Sarah Chen, PM at Meta, met at a conference`
> Ember has: Name ✅ Company ✅ Role ✅ How We Met ✅
> Ember missing: LinkedIn URL
> Ember asks: "Got it — do you have Sarah's LinkedIn URL? (You can skip this)"

---

### Step 2 — Infer Relationship Stage

Do NOT ask the user for this. Infer it silently from context:

| Signal | Stage to set |
|---|---|
| No prior contact mentioned | Prospect |
| User sent a request / message | Request Sent |
| They're connected on LinkedIn | Connected |
| Had a call, chat, or meeting | Active |
| Reconnecting after a gap | Dormant |

If there's no clear signal → default to **Prospect**.

Tell the user what stage was set in the confirmation message.

---

### Step 3 — Write to Notion

Search the user's Notion workspace for the **Contacts** database by name.
Do NOT use any hardcoded URL — always find it dynamically.

Write the following fields:
- **Name** → from intake
- **Company** → from intake
- **Role** → from intake
- **LinkedIn URL** → from intake (blank if skipped)
- **How We Met** → from intake (mapped to closest select option)
- **Relationship Stage** → inferred in Step 2

For these fields — never ask for them, but populate them if the user volunteered the info:
- **Next Follow-Up Date** → populate if user said something like "follow up in 2 weeks" or "remind me on Friday"
- **Notes** → populate if user shared any context, e.g. "we talked about her move to VC"
- **Tags** → populate if user mentioned a clear relationship type, e.g. "she's a mentor" → tag as Mentor

If none of this info was given → leave these fields blank. The user can fill them in Notion later.

---

### Step 4 — Confirm + offer next step

> "Done — [Name] is in your contacts as a [Stage]."
>
> "Want to draft an outreach message for them now?"

If yes → hand off to Skill 02 (`/ember reach out`) with [Name] pre-filled.
If no → end cleanly. Do not suggest anything else.

---

## Tone guidelines
- Fast and frictionless — this user is in capture mode, not planning mode
- Never ask for more than one field at a time
- If a field is skipped, accept it gracefully — no guilt-tripping
- Keep confirmations short — one line is enough

## What NOT to do
- Do not ask for Relationship Stage — always infer it
- Do not ask for Next Follow-Up Date, Notes, or Tags — but DO populate them if the user mentioned them unprompted
- Do not re-ask for fields the user already provided in a one-liner
- Do not use a hardcoded Notion URL — always search for "Contacts" dynamically
- Do not present the fields as a numbered list or form — keep it conversational
- Do not end with multiple suggestions — one clean handoff to outreach, or nothing
