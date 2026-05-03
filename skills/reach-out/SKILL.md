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

## Message Frameworks

Two frameworks exist — one for cold outreach, one for warm. Every draft must follow
exactly one of them, in the order listed. Do not blend the two in a single message.

**These frameworks define the structure of every message. Follow the order of elements strictly.**

---

### Framework A — Cold Outreach

Use when there is no prior relationship, shared context, or mutual connection.

```
1. Identity       -> Who you are: brief, one clause.
                    Pull from Ember Settings (Your role field) and condense:
                    e.g. "2nd-year Kellogg MBA exploring roles in B2B agentic AI,
                    returning to India"
2. Company hook   -> Why THIS company specifically: a pain point you lived,
                    a company-specific product observation, or a research insight.
                    Never the industry in general -- always something specific to
                    this company that the recipient can tell was written for them.
                    This is the most important element. Do not skip or generalise it.
3. Credentials    -> What you have done: one clause, pulled from Ember Settings.
                    e.g. "Ex-PwC; recently launched an AI product at a Series B
                    startup to $1.5M+ ARR"
                    Use the canonical phrasing from Settings -- do not rephrase or vary
4. CTA            -> Light ask: "would love to connect" or "hear your perspective"
                    Never ask for a job directly
```

---

### Framework B — Warm Outreach

Use when there is a prior relationship, shared event, referral, dormant connection,
or any shared context that can be referenced.

WhatsApp always uses Framework B -- you only have someone's number if you know them.

```
1. Relationship anchor -> How you know each other or how you met: be specific,
                         not generic. Reference the actual event, program, or
                         mutual person by name
2. Why reaching out now -> What prompted this message: a career update, a question,
                          a company move, or a specific ask. Can include a
                          company-specific observation but only after the anchor
3. Credentials          -> Only if needed for context. One clause, from Settings.
                          Skip entirely if the relationship makes it unnecessary
4. CTA                  -> Light ask: "would love to reconnect" or "catch up"
                          Warmer than cold CTA
```

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
Note internally: this will be warm outreach (Framework B), framed as a reconnection.
Continue.

**All other stages:** continue normally.

---

### Step 3 — Ask warm or cold

Always ask this explicitly. Do not infer it silently.

If the contact's data strongly suggests an answer (e.g. Dormant stage, Referral as
How We Met, Connected stage), suggest it and confirm:
> "This looks like warm outreach — you've got [signal] as context. Does that sound
> right, or is this more of a cold reach-out?"

If the data is ambiguous or cold (LinkedIn / blank How We Met, Prospect stage):
> "Is this a warm or cold outreach?
> - Warm: you've met before, have a mutual connection, or there's shared context
> - Cold: no prior relationship"

Wait for the user's answer. This determines which framework is used for the draft.
Do not proceed until the user has confirmed warm or cold.

**Edge case — Stage = Request Sent:**
Regardless of How We Met, treat as warm. A prior outreach attempt creates context.
Suggest warm and confirm:
> "You've already sent [Name] a request — I'll treat this as warm outreach and
> reference that. Does that work?"

---

### Step 4 — Ask for channel

> "Which channel do you want to use?
> - LinkedIn DM
> - LinkedIn InMail
> - Email
> - WhatsApp"

Wait for response. Never infer the channel.

**WhatsApp note:** WhatsApp always uses Framework B. If the user picks WhatsApp and
said "cold" in Step 3, flag it gently:
> "WhatsApp usually means you have their number — which suggests you know them.
> I'll use warm framing. Let me know if you want to change anything."

**WhatsApp + Prospect stage check:**
If channel = WhatsApp AND Stage = Prospect:
> "WhatsApp usually means you already have their number — but [Name] is marked
> as a Prospect. Just checking — do you have their contact details already?"

Accept whatever the user says and continue.

---

### Step 5 — Infer the hook, confirm before writing

For **warm outreach (Framework B)**, use this priority order:
1. **Referral** (How We Met = Referral) -> suggest leading with the mutual connection by name
2. **Shared event** (How We Met = Networking Event / Career Fair / Club) -> suggest referencing that specific event
3. **Shared context** (How We Met = Class / Cohort / Alumni Network) -> suggest the shared program
4. **Notes contain a reason** -> surface it as the hook
5. **Nothing clear** -> ask the user directly

For **cold outreach (Framework A)**, the hook IS the company-specific observation
(Framework A element 2). Ask the user:
> "What's the specific angle on [Company] you want to lead with? For example: a pain
> point you've lived, something specific about their product, or a research finding.
> The more specific, the better."

If the user provides it, use it. If they're unsure, suggest based on Notes or Tags
(Mentor, Investor) and confirm before writing.

**If a warm hook can be inferred**, suggest it and confirm:
> "I'd lead with [inferred hook] — does that work, or is there a different reason
> you're reaching out to [Name] right now?"

Wait for confirmation or correction before writing anything.

---

### Step 6 — Infer tone silently

Never ask about tone. Determine it from context:

**From warm/cold (Step 3):**
- Cold -> direct, no preamble, assume they don't know who you are
- Warm -> collegial, reference the relationship naturally

**From Relationship Stage:**
- Prospect -> introduce the user clearly
- Request Sent -> brief acknowledgement of prior attempt, keep short
- Connected -> skip intro, reference prior connection
- Dormant -> warm reconnection framing, acknowledge the gap lightly

**From How We Met:**
- LinkedIn (cold) -> acknowledge the cold approach briefly, be direct
- Referral -> lead with the mutual name, warmer tone
- Event / Fair / Club -> reference shared experience, conversational
- Alumni / Class -> lean on shared context, collegial tone

**From User's networking purpose (Settings):**
- Job searching -> curiosity framing — interested in their work, not "please hire me"
- Finding mentors -> learning angle — specific ask for 20 mins of their time
- BD / sales -> value-exchange framing — what's in it for them
- Building network -> genuine interest, light ask, no pressure
- Recruiting -> opportunity framing — what makes this interesting for them

**From Tags:**
- Mentor -> humble, learning-oriented tone
- VIP -> extra polish — review the draft carefully before outputting
- Investor -> business-first framing

---

### Step 7 — Write the draft

Write one message. No placeholders. No [brackets]. A real, copy-paste ready message.

Apply all channel constraints from the table above.

**Apply the correct message framework:**
- Warm outreach confirmed in Step 3 -> use Framework B, follow the four elements in order
- Cold outreach confirmed in Step 3 -> use Framework A, follow the four elements in order
- Do not blend the two frameworks in a single message
- The framework order is not a suggestion — it defines the structure of the message

**Credentials for Framework A:**
Pull the user's credentials from Ember Settings ("Your role" field). Condense to one
clause: "Ex-[past role]; recently [key achievement]". Do not vary the phrasing across
messages — use the same canonical form every time.

**For LinkedIn DM specifically:**
- Count every character including spaces and punctuation
- If over 300 characters -> cut, do not ask the user to shorten it
- Show character count in metadata strip

**For Email and InMail:**
- Always write a subject line first
- Subject line should be specific, not generic ("Quick question" is not acceptable)
- Good subject lines reference something real: the person's role, shared context,
  or the specific ask

---

### Step 8 — Show draft + metadata strip

Present in this order:

**If email or InMail — show subject line first:**
> Subject: [subject line]

**Then the message:**
> [full draft message]

**Then the metadata strip** (always — one line, scannable, 4 items):
> *[Framework A/B] · Channel · [X/300 chars if LinkedIn DM] · [hook used] · [tone applied]*

Example metadata strips:
> *Framework B · LinkedIn DM · 278/300 chars · Led with alumni call · Warm reconnect tone*
> *Framework A · Email · Company hook: ERP integration pain point · Job search framing*
> *Framework B · WhatsApp · Led with referral from Rohan · Casual, collegial tone*

**Then a single adjustment offer:**
> "Want me to make this warmer, shorter, or more formal?"

Wait. If the user asks for an adjustment, make it and re-show the full draft.
If the user approves, proceed to Step 9.

---

### Step 9 — Offer to log the interaction

> "Want me to log this as a [channel] outreach in your Interactions?"

If yes -> search for Interactions database dynamically (never hardcode URL),
write a new record:
- **Title** -> "[Channel] outreach to [Name]"
- **Contact** -> linked to the contact's record
- **Date** -> today's date
- **Type** -> matched to channel (LinkedIn DM / Email / etc.)
- **Direction** -> Outbound
- **Summary** -> the full drafted message text, verbatim — never truncate to the first line
- **Follow-Up Drafted** -> unchecked

If no -> skip and proceed to Step 10.

---

### Step 10 — Suggest stage update

After logging (or if user skipped logging), suggest a stage update if relevant:

| Current stage | Suggest update to |
|---|---|
| Prospect | Request Sent |
| Connected | Request Sent |
| Dormant | Request Sent |
| Request Sent | No suggestion needed |

> "Should I also update [Name]'s stage to **Request Sent**? Keeps your tracker
> current."

If yes -> update Relationship Stage in Contacts database.
If no -> end cleanly.

Do NOT ask both the logging question and the stage question at the same time —
always log first, then suggest stage update as a separate step.

---

## Examples of good drafts — real messages that have worked

These are actual outreach messages that got responses. Note which framework each
follows and how the elements map to the framework structure.

---

### LinkedIn DM — warm, alumni reconnect (Framework B)

> Hello, I had the opportunity to speak with you during the Kellogg International
> alumni networking call last spring. It was very insightful; your inputs helped me
> focus my recruitment on startup operator roles. I'd love to reconnect and continue
> learning from your journey.

*Framework B · LinkedIn DM · ~278 chars · Relationship anchor: alumni call · Warm reconnect tone*

Elements: (1) Relationship anchor: specific alumni call, (2) Why reaching out: valued the insight, (3) Credentials: skipped (relationship makes it unnecessary), (4) CTA: "reconnect"

---

### LinkedIn DM — cold, personal pain point hook (Framework A)

> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, returning to
> India. I faced outreach scaling challenges firsthand at Datasurfr; Rocket SDR
> directly addresses what we struggled with. Ex-PwC; recently launched an AI product
> at a Series B startup to $1.5M+ ARR. Would love to connect.

*Framework A · LinkedIn DM · ~292 chars · Company hook: lived pain point matching product · Direct, credibility brief*

Elements: (1) Identity, (2) Company hook: specific pain point lived, (3) Credentials: one clause, (4) CTA: "would love to connect"

---

### LinkedIn DM — cold, technical fit hook (Framework A)

> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, returning to
> India. At PwC I ran into ERP-agent integration failures repeatedly; Ema is one of
> the few platforms solving this at the integration layer. Ex-PwC; recently launched
> an AI product at a Series B startup to $1.5M+ ARR. Would love to connect.

*Framework A · LinkedIn DM · ~296 chars · Company hook: specific technical experience matching the company · Direct, no fluff*

---

### LinkedIn DM — cold, domain observation hook (Framework A)

> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, returning to
> India. Most AI tools bolt onto financial workflows; Prodigal rebuilds them from the
> ground up, which is a different and harder bet. Ex-PwC; recently launched an AI
> product at a Series B startup to $1.5M+ ARR. Would love to connect.

*Framework A · LinkedIn DM · ~294 chars · Company hook: specific product observation · Clean, no generic ask*

---

### LinkedIn DM — cold, research-backed hook (Framework A)

> Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, returning to
> India. A McKinsey project I contributed to found compute cost is the primary barrier
> to enterprise AI adoption; Sarvam's inference approach directly addresses this.
> Ex-PwC; recently launched an AI product at a Series B startup to $1.5M+ ARR.
> Would love to connect.

*Framework A · LinkedIn DM · ~299 chars · Company hook: proprietary research insight · Shows genuine domain knowledge*

---

### LinkedIn DM — warm, referral intro (Framework B)

> Hi, Rohan Mehta suggested I reach out. He thought our backgrounds in agentic AI
> and B2B SaaS would make for a good conversation. I'm a 2nd-year Kellogg MBA
> returning to India, recently launched an AI product at a Series B startup to
> $1.5M+ ARR. Would love to connect.

*Framework B · LinkedIn DM · ~285 chars · Relationship anchor: named referral · Warm, credentials included as context*

Elements: (1) Relationship anchor: Rohan Mehta named explicitly, (2) Why reaching out: shared context he identified, (3) Credentials: brief, as context, (4) CTA: "would love to connect"

---

## What makes these work

- **Framework followed in order** — cold messages always lead with identity then company hook before credentials; warm messages always lead with the relationship anchor
- **Company hook is specific** — references a pain point lived, a product observation, or a research finding. Never the industry in general
- **Credentials are canonical** — always pulled from Settings in the same condensed form, never rephrased
- **The ask is light** — "would love to connect" or "hear your perspective", never "can we schedule a call to discuss opportunities"
- **No filler openers** — none start with "I hope this message finds you well" or "I wanted to reach out"
- **The recipient can tell it was written for them** — not blasted

---

## What bad looks like (never produce these)

X "Hi [Name], I hope this message finds you well. I wanted to reach out because I came
across your profile and was very impressed by your career trajectory..."
-> Generic, passive opener, no specific hook, could be sent to anyone

X "Dear [Name], My name is Atharva and I am a student at Kellogg. I am very interested
in [Company] and would love to learn more about opportunities there..."
-> Reads like a mass template, asks for a job too directly, no personalisation

X "Hi, I'm a 2nd-year Kellogg MBA exploring roles in B2B agentic AI, returning to India.
Ex-PwC; recently launched an AI product at a Series B startup to $1.5M+ ARR. Impressed
by how [Company] applies AI to [Industry]. Would love to connect."
-> Credentials before company hook — wrong Framework A order. The company hook must
come before credentials, not after them

---

## Tone guidelines
- Messages should sound like the user, not like AI copy
- Never use phrases like "I hope this message finds you well" — too generic
- Never use "I wanted to reach out" — too passive
- Keep it direct — respecting the recipient's time is itself a form of warmth
- Always write in first person as if you are the user

## What NOT to do
- Do not use em dashes in any drafted message or user-facing output; use commas, semicolons, or rewrite the sentence instead
- Do not proceed if the contact is not in Notion — always send to `/ember add` first
- Do not exceed 300 characters for LinkedIn DM under any circumstances
- Do not ask for tone — infer it
- Do not ask for channel — wait for user to pick from the list
- Do not write multiple draft options — one strong draft only
- Do not use hardcoded Notion URLs — always search dynamically
- Do not ask the logging and stage questions together — one at a time
- Do not use generic subject lines for email — always make it specific
- Do not skip the metadata strip — it helps the user understand and adjust the draft
- Do not infer warm or cold silently — always ask the user explicitly in Step 3
- Do not vary the credentials phrasing across messages — always use the canonical form from Settings
- Do not apply Framework A to warm outreach or Framework B to cold outreach
- Do not put credentials before the company hook in a cold message — Framework A order is fixed
