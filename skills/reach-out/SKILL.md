---
name: reach-out
description: "Drafts a first-touch outreach message to a contact, personalised by channel and context. Triggers when the user runs /ember:reach-out, asks to reach out to someone, or says things like 'draft a message to [name]' or 'write an outreach for [name]'."
argument-hint: "[Contact Name]"
---

# Ember Skill 02 — Outreach Drafting

## Purpose
Draft a first-touch outreach message to a contact, personalised using everything
Ember knows about the contact and the user. Supports LinkedIn DM, Email,
LinkedIn InMail, and WhatsApp. Always one strong draft — never a menu of options.

---

## Trigger
- User types `/ember reach out`
- User types `/ember reach out [Name]`
- User says "draft a message to [Name]" or "reach out to [Name]"

---

## Channel constraints — memorise these

| Channel | Hard limit | Subject line? | Tone default |
|---|---|---|---|
| LinkedIn DM | **300 characters — hard cap, never exceed** | No | Warm, direct, brief |
| LinkedIn InMail | ~200 words soft limit | Yes | Professional, stranger context |
| Email | 6–10 lines ideal, no hard limit | Yes | Structured, more context allowed |
| WhatsApp | 3–5 lines ideal, no hard limit | No | Casual, conversational |

For LinkedIn DM: always count characters before outputting. Show the count in the
metadata strip (e.g. "287/300 chars"). If a draft exceeds 300 characters, cut it
down — never ask the user to edit for length themselves.

---

## Behavior

### Step 1 — Check the contact exists

Search the Contacts database by name.

**If not found:**
> "I don't have [Name] in your contacts yet. Add them first with `/ember add` and
> I'll have everything I need to draft a great message."

Stop. Do not proceed.

**If found:** load all fields — Name, Company, Role, LinkedIn URL, How We Met,
Relationship Stage, Notes, Tags.

Also read the user's Ember Settings — Name, Role, Why you're networking.

---

### Step 2 — Check Relationship Stage

**If Stage = Active:**
> "Looks like you're already in conversation with [Name]. Did you mean to send
> a follow-up instead? Try `/ember follow up`."

Stop and wait. Do not proceed unless the user overrides.

**If Stage = Dormant:**
Note internally: frame this as a reconnection, not a cold intro. Continue.

**All other stages:** continue normally.

---

### Step 3 — Ask for channel

> "Which channel do you want to use?
> - LinkedIn DM
> - LinkedIn InMail
> - Email
> - WhatsApp"

Wait for response. This is the only field always asked — never infer it.

**WhatsApp + Prospect stage check:**
If channel = WhatsApp AND Stage = Prospect, flag it:
> "WhatsApp usually means you already have their number — but [Name] is marked
> as a Prospect. Just checking — do you have their contact details already?"

Accept whatever the user says and continue.

---

### Step 4 — Infer the hook, confirm before writing

Use this priority order to infer the best reason for reaching out:

1. **Referral** (How We Met = Referral) → suggest leading with the mutual connection
2. **Shared event** (How We Met = Networking Event / Career Fair / Club) → suggest referencing that
3. **Shared context** (How We Met = Class / Cohort / Alumni Network) → suggest the shared program
4. **Notes contain a reason** → surface it as the hook
5. **Tags = Mentor** → suggest a learning/guidance angle
6. **Tags = Investor** → suggest a business/opportunity angle
7. **Nothing clear** → ask the user directly

**If a hook can be inferred**, suggest it and confirm:
> "I'd lead with [inferred hook] — does that work, or is there a different reason
> you're reaching out to [Name] right now?"

**If nothing can be inferred**, ask directly:
> "What's your main reason for reaching out to [Name] right now?"

Wait for confirmation or correction before writing anything.

---

### Step 5 — Infer tone silently

Never ask about tone. Determine it from context:

**From Relationship Stage:**
- Prospect → introduce the user clearly
- Request Sent → brief acknowledgement of prior attempt, keep short
- Connected → skip intro, reference prior connection
- Dormant → warm reconnection framing, acknowledge the gap lightly

**From How We Met:**
- LinkedIn (cold) → acknowledge the cold approach briefly, be direct
- Referral → lead with the mutual name, warmer tone
- Event / Fair / Club → reference shared experience, conversational
- Alumni / Class → lean on shared context, collegial tone

**From User's networking purpose (Settings):**
- Job searching → curiosity framing — interested in their work, not "please hire me"
- Finding mentors → learning angle — specific ask for 20 mins of their time
- BD / sales → value-exchange framing — what's in it for them
- Building network → genuine interest, light ask, no pressure
- Recruiting → opportunity framing — what makes this interesting for them

**From Tags:**
- Mentor → humble, learning-oriented tone
- VIP → extra polish — review the draft carefully before outputting
- Investor → business-first framing

---

### Step 6 — Write the draft

Write one message. No placeholders. No [brackets]. A real, copy-paste ready message.

Apply all channel constraints from the table above.

**For LinkedIn DM specifically:**
- Count every character including spaces and punctuation
- If over 300 characters → cut, do not ask the user to shorten it
- Show character count in metadata strip

**For Email and InMail:**
- Always write a subject line first
- Subject line should be specific, not generic ("Quick question" is not acceptable)
- Good subject lines reference something real: the person's role, shared context,
  or the specific ask

---

### Step 7 — Show draft + metadata strip

Present in this order:

**If email or InMail — show subject line first:**
> Subject: [subject line]

**Then the message:**
> [full draft message]

**Then the metadata strip** (always — 3 to 4 items, one line, scannable):
> *Channel · [X/300 chars if LinkedIn DM] · [hook used] · [tone applied]*

Example metadata strips:
> *LinkedIn DM · 241/300 chars · Led with alumni connection · Curiosity tone*
> *Email · Led with referral from James · Job search framing · Warm but professional*
> *WhatsApp · Referenced conference · Casual tone · Light ask*

**Then a single adjustment offer:**
> "Want me to make this warmer, shorter, or more formal?"

Wait. If the user asks for an adjustment, make it and re-show the full draft.
If the user approves, proceed to Step 8.

---

### Step 8 — Offer to log the interaction

> "Want me to log this as a [channel] outreach in your Interactions?"

If yes → search for Interactions database dynamically (never hardcode URL),
write a new record:
- **Title** → "[Channel] outreach to [Name]"
- **Contact** → linked to the contact's record
- **Date** → today's date
- **Type** → matched to channel (LinkedIn DM / Email / etc.)
- **Direction** → Outbound
- **Summary** → the full drafted message text, verbatim — never truncate to the first line
- **Follow-Up Drafted** → unchecked

If no → skip and proceed to Step 9.

---

### Step 9 — Suggest stage update

After logging (or if user skipped logging), suggest a stage update if relevant:

| Current stage | Suggest update to |
|---|---|
| Prospect | Request Sent |
| Connected | Request Sent |
| Dormant | Request Sent |
| Request Sent | No suggestion needed |

> "Should I also update [Name]'s stage to **Request Sent**? Keeps your tracker
> current."

If yes → update Relationship Stage in Contacts database.
If no → end cleanly.

Do NOT ask both the logging question and the stage question at the same time —
always log first, then suggest stage update as a separate step.

---


---

## Examples of good drafts — real messages that have worked

These are actual outreach messages that got responses. Use them to calibrate tone, specificity, and length.

---

### LinkedIn DM — alumni reconnect (warm, specific hook)
> Hello, I had the opportunity to speak with you during the Kellogg International alumni
> networking call last spring — it was very insightful. Your inputs helped me focus my
> recruitment on startup operator roles. I'd love to connect and continue learning from
> your journey.

*LinkedIn DM · ~270 chars · Led with shared alumni event · Warm reconnect tone*

---

### LinkedIn DM — cold, product-company fit hook (B2B AI, startup background)
> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, planning a return to
> India. Ex-PwC; recently launched AI product at a Series B startup with $1.5M+ ARR. I
> faced outreach scaling problems while working at Datasurfr and was inspired by Rocket
> SDR's product, would love to connect.

*LinkedIn DM · ~290 chars · Led with personal pain point matching the product · Credibility + genuine interest*

---

### LinkedIn DM — cold, technical fit hook (ERP / agent integrations)
> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, planning a return to
> India. Recently launched an AI product at Series B startup to $1.5M+ ARR. Having faced
> ERP–agent integration challenges via UiPath in PwC, I'm excited by Ema Agent's deep
> integrations. Would love to connect.

*LinkedIn DM · ~290 chars · Led with specific technical experience matching the company · Direct, no fluff*

---

### LinkedIn DM — cold, domain-specific fit (fintech AI)
> Hi, I'm a 2nd-year Kellogg MBA exploring employment opportunities in B2B agentic AI,
> planning a return to India. Ex-PwC; recently helped launch an AI product at a Series B
> startup to $1.5M+ ARR. Impressed by how Prodigal applies AI to automate consumer
> financial workflows.

*LinkedIn DM · ~285 chars · Led with company-specific observation · Clean, no generic ask*

---

### LinkedIn DM — cold, research-backed hook (consulting project insight)
> Hi,
> I'm a 2nd-year Kellogg MBA exploring PM/strategy roles in India. I'm working on a
> McKinsey research project where we found ballooning costs are the primary barrier for
> enterprise AI adoption. Sarvam's low-cost inference directly addresses this. Would love
> to connect and hear your perspective.

*LinkedIn DM · ~290 chars · Led with proprietary research insight · Shows genuine domain knowledge*

---

## What makes these work

- **Specific hook in every message** — references a shared event, a personal pain point, a company observation, or a research finding. Never generic.
- **Credibility signals are brief** — "Ex-PwC, $1.5M+ ARR" in one clause, not a paragraph
- **The ask is light** — "would love to connect" or "hear your perspective", never "can we schedule a call to discuss opportunities"
- **No filler openers** — none start with "I hope this message finds you well" or "I wanted to reach out"
- **Company-specific observation** — the recipient can tell the message was written for them, not blasted

---

## What bad looks like (never produce these)

❌ "Hi [Name], I hope this message finds you well. I wanted to reach out because I came
across your profile and was very impressed by your career trajectory..."
→ Generic, passive opener, no specific hook, could be sent to anyone

❌ "Dear [Name], My name is Atharva and I am a student at Kellogg. I am very interested
in [Company] and would love to learn more about opportunities there..."
→ Reads like a mass template, asks for a job too directly, no personalisation

---

## Tone guidelines
- Messages should sound like the user, not like AI copy
- Never use phrases like "I hope this message finds you well" — too generic
- Never use "I wanted to reach out" — too passive
- Keep it direct — respecting the recipient's time is itself a form of warmth
- Always write in first person as if you are the user

## What NOT to do
- Do not use em dashes (—) in any drafted message or user-facing output — use commas, semicolons, or rewrite the sentence instead
- Do not proceed if the contact is not in Notion — always send to `/ember add` first
- Do not exceed 300 characters for LinkedIn DM under any circumstances
- Do not ask for tone — infer it
- Do not ask for channel — wait for user to pick from the list
- Do not write multiple draft options — one strong draft only
- Do not use hardcoded Notion URLs — always search dynamically
- Do not ask the logging and stage questions together — one at a time
- Do not use generic subject lines for email — always make it specific
- Do not skip the metadata strip — it helps the user understand and adjust the draft
