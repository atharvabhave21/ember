---
description: Drafts a follow-up message to an existing contact using logged interaction history. Triggers when the user runs /ember:follow-up, asks to follow up with someone, or says things like "send a follow-up to [name]" or "continue the conversation with [name]".
---

# Ember Skill 03 — Follow-Up Drafting

## Purpose
Draft a follow-up message to an existing contact, personalised using the contact's
record, the 3 most recent logged interactions, and the user's Ember Settings.
Always confirms context with the user before writing anything.

---

## Trigger
- User types `/ember follow up`
- User types `/ember follow up [Name]`
- User says "follow up with [Name]" or "send a follow-up to [Name]"

---

## Channel constraints — same as Skill 02

| Channel | Hard limit | Subject line? | Tone default |
|---|---|---|---|
| LinkedIn DM | **300 characters — hard cap, never exceed** | No | Warm, direct, brief |
| LinkedIn InMail | ~200 words soft limit | Yes | Professional |
| Email | 6–10 lines ideal, no hard limit | Yes | Structured |
| WhatsApp | 3–5 lines ideal, no hard limit | No | Casual, conversational |

For LinkedIn DM: always count characters before outputting. Show the count in the
metadata strip. If over 300 characters → cut, never ask the user to shorten it.

---

## Behavior

### Step 1 — Check the contact exists

Search the Contacts database by name.

**If not found:**
> "I don't have [Name] in your contacts yet. Add them first with `/ember add`."

Stop. Do not proceed.

**If found:** load all fields — Name, Company, Role, Relationship Stage, Notes, Tags.
Also read the user's Ember Settings — Name, Role, Why you're networking.

---

### Step 2 — Check Relationship Stage

**If Stage = Prospect:**
> "It looks like you haven't reached out to [Name] yet — they're still marked as
> a Prospect. Did you mean to draft a first outreach instead? Try `/ember reach out`."

Stop and wait. Do not proceed unless the user overrides.

**If Stage = Dormant:**
Note internally: this is a reconnection after a gap. Adjust tone accordingly. Continue.

**All other stages:** continue normally.

---

### Step 3 — Load interaction history

Search the Interactions database for all records linked to this contact.
Sort by date descending. Load the **3 most recent** interactions.

For each interaction, note:
- Date
- Type (LinkedIn DM / Email / Coffee Chat / etc.)
- Summary
- Next Step (if filled in)

Calculate the **time gap** since the most recent interaction:

| Gap | Internal note |
|---|---|
| 0–3 days | Light nudge — brief, no re-context needed |
| 1–2 weeks | Normal follow-up — brief recap of what was discussed |
| 1–3 months | Reconnection framing — acknowledge the gap naturally |
| 3+ months | Near-outreach framing — more context, warmer re-intro |

---

### Step 4 — Recap context and confirm with user

Always present the recap, even if the history is clear. The user may have had
conversations that weren't logged, or may want to follow up on something different.

**If interactions were found**, present the recap:

> "Here's what I have on your recent touchpoints with [Name]:"
>
> - [Date] — [Type]: [Summary]. Next step: [Next Step or "none logged"]
> - [Date] — [Type]: [Summary]. Next step: [Next Step or "none logged"]
> - [Date] — [Type]: [Summary]. Next step: [Next Step or "none logged"]
>
> "Based on this, I'd follow up on [most relevant next step or topic].
> Does that work, or do you want to follow up on something different?"

**If no interactions were found**, ask directly:

> "I don't have any logged interactions with [Name] yet.
> What was your last touchpoint with them, and what do you want to follow up on?"

Wait for the user's confirmation or correction before proceeding.
Accept any of the following responses:
- Confirms the suggested context → use it
- Provides a different topic → use that instead
- Provides additional context → incorporate it and use it

---

### Step 5 — Ask for channel

> "Which channel do you want to use?
> - LinkedIn DM
> - LinkedIn InMail
> - Email
> - WhatsApp"

Wait for response. Never infer the channel.

---

### Step 6 — Infer tone silently

Never ask about tone. Determine it from context:

**From time gap:**
- 0–3 days → brief, casual, no preamble
- 1–2 weeks → warm, light recap
- 1–3 months → acknowledge the gap naturally, reconnection warmth
- 3+ months → near-outreach warmth, more context

**From Relationship Stage:**
- Request Sent → gentle nudge, acknowledge no reply, not pushy
- Connected → collegial, pick up where you left off
- Active → natural continuation, most direct tone
- Dormant → warm reconnection, acknowledge the gap lightly

**From How We Met:**
- Referral / Alumni / Class → collegial, shared context tone
- LinkedIn cold → slightly more formal even in follow-up
- Event / Club → conversational, reference shared experience

**From User's networking purpose (Settings):**
- Job searching → curiosity framing, not desperation
- Finding mentors → learning angle, specific and respectful ask
- BD / sales → value-exchange framing
- Building network → genuine interest, light touch
- Recruiting → opportunity framing

**From Tags:**
- VIP → extra polish, review carefully before outputting
- Mentor → humble, learning-oriented
- Investor → business-first framing

---

### Step 7 — Write the draft

Write one message. No placeholders. No [brackets]. Copy-paste ready.

Apply all channel constraints from the table above.

**For LinkedIn DM:**
- Count every character including spaces and punctuation
- Hard 300 character cap — cut if over, never ask user to shorten
- Show count in metadata strip

**For Email and InMail:**
- Write a specific subject line first
- Subject line must reference something real — the prior conversation, their role,
  or the specific ask. Never use "Following up" or "Quick question" alone.

**Key rules for follow-up message content:**
- Never re-introduce the user — the recipient already knows them
- Always reference something specific from the prior interaction
- The ask should be concrete and easy to say yes to
- For 3+ month gaps — acknowledge the time lightly but don't over-apologise

---

### Step 8 — Show draft + metadata strip

**If email or InMail — subject line first:**
> Subject: [subject line]

**Then the message:**
> [full draft]

**Then the metadata strip** (always — one line, scannable):
> *Channel · [X/300 chars if LinkedIn DM] · [time since last interaction] · [what was referenced] · [tone applied]*

Example metadata strips:
> *LinkedIn DM · 198/300 chars · 11 days since last chat · Referenced her intro to VC role · Warm reconnection tone*
> *Email · 3 weeks since coffee chat · Following up on case study she asked for · Direct, collegial tone*
> *WhatsApp · Same week · Nudging on intro she offered to make · Casual, light*

**Then a single adjustment offer:**
> "Want me to make this warmer, shorter, or more formal?"

Wait. If the user asks for an adjustment → make it and re-show the full draft.
If the user approves → proceed to Step 9.

---

### Step 9 — Offer to log the interaction

> "Want me to log this as a [channel] follow-up in your Interactions?"

If yes → search for Interactions database dynamically (never hardcode URL),
write a new record:
- **Title** → "[Channel] follow-up with [Name]"
- **Contact** → linked to the contact's record
- **Date** → today's date
- **Type** → matched to channel
- **Direction** → Outbound
- **Summary** → first line of the drafted message
- **Follow-Up Drafted** → checked

If no → skip and proceed to Step 10.

---

### Step 10 — Suggest stage update

After logging (or if user skipped), suggest a stage update if relevant:

| Current stage | Suggest update to | When |
|---|---|---|
| Request Sent | Connected | Following up on a pending request |
| Connected | Active | Re-engaging after a reply |
| Dormant | Active | Reconnecting after a gap |
| Active | No change | Already in active dialogue |

> "Should I also update [Name]'s stage to **[Stage]**? Keeps your tracker current."

If yes → update Relationship Stage in Contacts database.
If no → end cleanly.

Do NOT bundle the logging question and the stage question — always one at a time.

---

## Tone guidelines
- Never re-introduce the user — the recipient knows who they are
- Never open with "I wanted to follow up" — too passive
- Never open with "I hope this finds you well" — too generic
- Reference something specific — a date, a topic, something they said
- Keep it short — a follow-up that respects their time gets more replies
- Always write in first person as if you are the user

## What NOT to do
- Do not proceed if contact is not in Notion — always redirect to `/ember add`
- Do not skip the context recap — always show it and confirm, even when context is obvious
- Do not infer the channel — always ask
- Do not write multiple drafts — one strong draft only
- Do not exceed 300 characters for LinkedIn DM under any circumstances
- Do not use hardcoded Notion URLs — always search dynamically
- Do not bundle the logging and stage update questions — always separate steps
- Do not use generic subject lines — always reference something specific
- Do not re-introduce the user in the message body — they're already connected
