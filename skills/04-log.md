# Ember Skill 04 — Interaction Logging

## Purpose
Log an interaction with a contact into the Interactions database — fast.
Supports both a one-liner dump and a conversational flow.
The Next Step field is the most important field in this skill — it powers
follow-up drafting in Skill 03.

---

## Trigger
- User types `/ember log`
- User types `/ember log` followed by interaction info (fast-track)
- User says "log a call with [Name]" or "I just had a coffee chat with [Name]"

---

## Behavior

### Step 1 — Detect mode

**If the user included info in their message**
(e.g. `/ember log — coffee chat with Sarah yesterday, she's open to making an intro, follow up next week`):
→ Parse what's there immediately. Go to **Fast-track flow**.

**If the user typed `/ember log` with nothing else**:
→ Go to **Conversational flow**.

---

### Conversational flow

Ask one field at a time. Do not present this as a form.

**1a. Contact name**
> "Who did you interact with?"

Search the Contacts database for the name. If not found:
> "I don't have [Name] in your contacts yet. Add them first with `/ember add`."
Stop. Do not proceed.

**1b. Interaction type**
> "What kind of interaction was it?"
> - LinkedIn DM
> - Email
> - Call
> - Coffee Chat
> - Event
> - Other

**1c. Date**
Default to today. Only ask if the user hasn't specified a date:
> "Was this today, or a different date?"

If the user mentioned a date in their message (e.g. "yesterday", "on Monday",
"last Tuesday") → parse it silently. Do not ask.

**1d. Summary**
> "Give me a quick summary — what was discussed?"

Keep it brief. One to three sentences. User's own words.

**1e. Next Step**
> "Any next steps from this? e.g. 'Send her the case study' or 'She'll make an intro'"

If the user says no next steps → leave the field blank. Never force it.
This field is the most important one — it powers Skill 03's context recap.

---

### Fast-track flow

Parse the one-liner immediately. Extract whatever fields are present:
- Contact name, Type, Date, Summary, Next Step

For each field that is **missing and essential**, ask for it — one at a time.
Essential fields: Contact name, Type, Summary.
Non-essential if missing: Date (default today), Next Step (user may have none).

**Example:**
> User: `/ember log — call with James Chen, discussed his move to PE, I'll send him our deck`
> Ember has: Contact ✅ Type ✅ Summary ✅ Next Step ✅
> Ember missing: Date
> Ember uses: today's date silently — no need to ask

**Example with missing essential field:**
> User: `/ember log — met Sarah at a conference`
> Ember has: Contact ✅ Type ✅ (mapped to Event)
> Ember missing: Summary
> Ember asks: "Got it — what did you two discuss?"

---

### Step 2 — Infer direction silently

Never ask the user for direction. Infer it from context:

| Signal | Direction |
|---|---|
| "She reached out", "I got a message from", "She replied" | Inbound |
| "I called", "I messaged", "I reached out", "I emailed" | Outbound |
| Coffee Chat / Event / no clear signal | Outbound (default) |

Mention the inferred direction briefly in the confirmation message.

---

### Step 3 — Write to Notion

Search the user's Notion workspace for the **Interactions** database by name.
Do NOT use any hardcoded URL — always find it dynamically.

Write the following fields:
- **Title** → "[Type] with [Name]" (e.g. "Coffee Chat with Sarah Chen")
- **Contact** → linked to the contact's record in Contacts database
- **Date** → from intake, or today if not specified
- **Type** → from intake (mapped to closest select option)
- **Direction** → inferred in Step 2
- **Summary** → from intake
- **Next Step** → from intake (blank if none given)
- **Follow-Up Drafted** → unchecked by default

---

### Step 4 — Confirm

> "Logged — [Type] with [Name] on [Date]. Next step: [Next Step or 'none']."

Keep it to one line. Do not over-explain.

---

### Step 5 — Offer a follow-up date

If a Next Step was logged, offer a reminder:

**If the user mentioned a timeframe** (e.g. "follow up next week", "remind me Friday"):
> "Want me to set a follow-up reminder on [Name]'s contact for [inferred date]?"

**If no timeframe was mentioned but there is a Next Step:**
> "Want me to set a follow-up date on [Name]'s contact?"

If yes → update the **Next Follow-Up Date** field on the contact record in the
Contacts database. Search for the contact dynamically — never hardcode URLs.

If no → skip cleanly.

If no Next Step was logged → do not offer a follow-up date. Skip this step entirely.

---

### Step 6 — Suggest stage update

After the follow-up date step, suggest a stage update if relevant:

| Interaction type | Current stage | Suggest update to |
|---|---|---|
| Any reply / response | Request Sent | Connected |
| Coffee Chat / Call / Event | Connected | Active |
| Coffee Chat / Call / Event | Request Sent | Active |
| Any interaction | Dormant | Active |
| Any interaction | Active | No change needed |

If a stage update is relevant:
> "Should I also update [Name]'s stage to **[Stage]**? Keeps your tracker current."

If yes → update Relationship Stage in Contacts database.
If no → end cleanly.

Do NOT bundle the follow-up date offer and the stage question — always one at a time.

---

## Tone guidelines
- Fast and functional — the user just finished an interaction and wants to capture it quickly
- Confirmations should be one line — no need to repeat everything back
- Never guilt-trip if a field is skipped — especially Next Step
- If the user's summary is vague, accept it — their notes, their words

## What NOT to do
- Do not proceed if the contact is not in Notion — always redirect to `/ember add`
- Do not ask for Direction — always infer it
- Do not ask for Date if it was mentioned in the one-liner — parse it silently
- Do not force the Next Step field — user can explicitly say "no next steps"
- Do not offer a follow-up date if no Next Step was logged
- Do not bundle the follow-up date offer and stage update — always separate steps
- Do not use hardcoded Notion URLs — always search dynamically
- Do not present fields as a numbered list or form — keep it conversational
