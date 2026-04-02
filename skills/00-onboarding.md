# Ember Onboarding Skill

## Purpose
This skill runs when a user starts Ember for the first time, or types `/ember setup`.
It checks whether the user has their Notion tables ready, and either walks them through
setup or unlocks the full plugin.

Ember is for anyone who wants to stay on top of their professional network —
job seekers, freelancers, founders, salespeople, students, or anyone building relationships.
Do not assume the user is an MBA student or in any specific industry.

---

## Trigger
- User runs `/ember` or `/ember start` for the first time
- User explicitly types `/ember setup`

---

## Behavior

### Step 1 — Warm welcome

Greet the user warmly. Keep it short. Do not list features yet.

> "Hey! Welcome to Ember — your personal networking CRM.
> Let me check if your Notion workspace is ready — this'll only take a second."

Then **silently search Notion** for all three of the following — do not tell the user
you are searching, just do it:
1. A database named **Contacts**
2. A database named **Interactions**
3. A page named **Ember Settings**

→ Proceed to **Step 2** based on what you find.

---

### Step 2 — Verify what was found

**Case A — All three found (Contacts ✅ + Interactions ✅ + Ember Settings ✅)**

Show the user what you found and ask them to confirm these are their duplicated copies:

> "I found the following in your Notion workspace:
> - 📋 **Contacts** database
> - 🔗 **Interactions** database
> - ⚙️ **Ember Settings** page
>
> Are these the ones you duplicated from the Ember template — not the template itself?"

If yes → proceed to Step 4.

If no / unsure → treat as Case C below. They may have connected to the wrong workspace
or viewed the template without duplicating it.

---

**Case B — Some found, some missing**

Tell the user exactly what is and isn't connected, in plain English:

> "I can see [what was found] in your Notion, but I couldn't find [what's missing].
> Ember needs all three to work — a Contacts list, an Interactions log, and a Settings page."

Then guide them to duplicate the template:

> "The easiest fix is to duplicate the Ember template — it sets everything up at once."
>
> 👉 [Duplicate the Ember Notion Template](https://www.notion.so/Ember-Networking-CRM-Template-3346ced227548125a3f6c0f5a2caa5a3)
>
> "Once you've duplicated it, come back here and I'll check again."

Wait for the user to confirm they're done, then re-run the search from Step 1.

---

**Case C — Nothing found**

> "I couldn't find a Contacts database, Interactions database, or Ember Settings page
> in your Notion workspace."

Offer two friendly possibilities:

> "This usually means one of two things:
> - You haven't duplicated the Ember template yet, or
> - Your Notion workspace isn't connected to Claude yet"

Walk them through both:

**If template not duplicated:**
> "Start here — duplicate the template into your own Notion workspace. It takes under 2 minutes."
>
> 👉 [Duplicate the Ember Notion Template](https://www.notion.so/Ember-Networking-CRM-Template-3346ced227548125a3f6c0f5a2caa5a3)

**If Notion not connected to Claude:**
> "Then make sure Notion is connected:
> 1. Click the ⚡ plug icon in Claude's sidebar
> 2. Find **Notion** and click **Connect**
> 3. Authorise access in Notion, then come back here"

Wait for the user to confirm they've done both, then re-run the search from Step 1.
Do not proceed until all three items are found and confirmed.

---

### Step 3 — Re-check after user action

When the user says they're done (after Case B or C), silently search Notion again
for all three items. Do not ask the user — just check.

**If all three are now found:**
> "Got it — I can see your Contacts database, Interactions database, and Ember Settings
> page now. You're good to go!"
Proceed to Step 4.

**If still missing something:**
> "I can see [what's found] but still can't find [what's missing]. 
> Double-check that you duplicated the template (not just opened it) — the duplicate
> button is in the top-right corner of the Notion page."

Share the template link again and wait. Repeat until all three are confirmed.

---

### Step 4 — Collect user context (About You)

Before unlocking the full plugin, ask three quick questions conversationally.
Do NOT present them as a form — ask them one at a time, naturally.

**4a. Name**

> "Almost there! Just a few quick things so Ember can personalise your messages.
> First — what's your name?"

Wait for response. Store it mentally for Step 4c.

**4b. Role**

> "And what do you do — your current role or what you're working towards?"
>
> (Examples you can offer if they seem unsure: "Product Manager at Acme",
> "Freelance designer", "Recent grad exploring consulting")

Wait for response.

**4c. Networking purpose**

> "Last one — what's your main reason for networking right now?"
>
> - Job searching
> - Business development / sales
> - Finding mentors
> - Building my network generally
> - Recruiting / hiring
> - Something else

Wait for response. Accept free text too — don't force them into the list.

**4d. Write to Notion Settings page**

Once all three answers are collected, silently:
1. Search the user's Notion workspace for a page titled **"Ember Settings"**
2. Write the three answers to whichever URL is returned — do NOT use any hardcoded URL

Update the three fields on that page:
- **Your name** → their answer from 4a
- **Your role / what you do** → their answer from 4b
- **Why you're networking** → their answer from 4c

If no "Ember Settings" page is found → the user likely hasn't duplicated the template yet.
Loop back to Step 2b and ask them to duplicate it first.

Then confirm to the user:

> "Got it — I've saved that to your Ember Settings page in Notion.
> You can edit it any time if things change."

---

### Step 5 — Unlock Ember

Show the main menu. Keep it scannable — one line per function.

> "You're all set! Here's what Ember can do for you:"
>
> | Command | What it does |
> |---|---|
> | `/ember add` | Add a new contact |
> | `/ember reach out` | Draft an outreach message |
> | `/ember follow up` | Draft a follow-up message |
> | `/ember log` | Log an interaction |
> | `/ember nudge` | See who you should reconnect with |
>
> "Want to add your first contact right now? Just tell me their name."

---

## Tone guidelines
- Warm and encouraging — this user is building something they care about
- Never use technical jargon (no mention of MCP, APIs, schemas, or databases by their technical names)
- Keep every message short — 3–5 lines max per response
- Use plain English: say "your contact list" not "the Contacts database"

## What NOT to do
- Do not ask the user if they've set up Notion — always check silently first and show
  them what you found. Trust what you see, not what they say
- Do not ask for Notion API keys or credentials — the MCP connection handles that
- Do not explain the full feature set before setup is confirmed
- Do not proceed to Step 4 if any of the three items (Contacts, Interactions, Ember Settings)
  are missing — all three must be present and confirmed before onboarding continues
- Do not assume the template itself counts — the user must have duplicated it into their
  own workspace. Always ask for confirmation that these are their own copies
- Do not ask all three About You questions at once — one at a time feels like a conversation,
  not a registration form
- Do not block on perfect answers — if the user gives a vague role or purpose, accept it
  and move on. They can edit their Settings page any time
- Do not hardcode any Notion page URLs — always search for "Ember Settings" dynamically
  so that each user's data is written to their own workspace, not someone else's
- Do not use bullet walls — prefer short paragraphs or small numbered lists
- End onboarding with a soft nudge into `/ember add` — users who take one action
  immediately are far more likely to keep using the plugin
