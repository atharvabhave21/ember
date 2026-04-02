---
description: Surfaces contacts the user should reconnect with, ranked by urgency into Overdue, Stuck, and Going Cold tiers. Triggers when the user runs /ember:nudge, asks who to follow up with, or says things like "who have I been neglecting" or "who should I reconnect with".
---

# Ember Skill 05 — Follow-Up Nudging

## Purpose
Surface the contacts the user should reconnect with — ranked by urgency, grouped
by signal type, capped at 10. Ember does the thinking so the user doesn't have to.
This is the only skill that initiates insight rather than responding to a request.

---

## Trigger
- User types `/ember nudge`
- User says "who should I follow up with?" or "who have I been neglecting?"

---

## Behavior

### Step 1 — Query both databases

Search the Contacts database and Interactions database dynamically.
Never use hardcoded URLs.

Collect the following for every contact:
- Name, Company, Role, Relationship Stage, Tags, Next Follow-Up Date
- Date of most recent logged interaction (from Interactions DB)
- Summary of most recent logged interaction

Calculate today's date. Use it to determine:
- Days since last interaction (for each contact)
- Whether Next Follow-Up Date has passed (and by how many days)

---

### Step 2 — Score and classify each contact

Run every contact through the three nudge tiers in order.
A contact should only appear in the highest tier that applies to them.

---

**Tier 1 — Overdue (highest urgency)**

Condition: Next Follow-Up Date is set AND that date has passed.

Collect all contacts that meet this condition.
Sort by most overdue first (largest gap between due date and today).

---

**Tier 2 — Stuck pipeline**

Condition: Relationship Stage has not progressed and no interaction has been
logged within the stale threshold for that stage.

| Stage | Stale threshold |
|---|---|
| Request Sent | 14 days since last interaction |
| Connected | 45 days since last interaction |
| Active | 30 days since last interaction |

Contacts already captured in Tier 1 are excluded.
Sort by most days overdue threshold first.

---

**Tier 3 — Going cold**

Condition: No Next Follow-Up Date set, not already captured above, but Tags or
Stage signal this is a high-value relationship that hasn't been touched recently.

| Signal | Threshold |
|---|---|
| VIP tag | 30 days since last interaction |
| Investor tag | 45 days since last interaction |
| Mentor tag | 60 days since last interaction |
| Active stage (no tag) | 60 days since last interaction |
| Dormant stage | Always surface — already flagged as cold |

Contacts already captured in Tier 1 or 2 are excluded.
Sort by most days since last interaction first.

---

### Step 3 — Cap and format the list

Take up to 10 contacts total across all three tiers.
Fill Tier 1 first, then Tier 2, then Tier 3 until the cap is reached.

**If zero contacts qualify across all tiers:**
> "You're all caught up — no overdue follow-ups or cold contacts right now."

Stop. Do not proceed.

**If contacts are found**, present them grouped by tier:

> "Here's who you should reconnect with:"
>
> **Overdue**
> - **[Name]** · [Role] at [Company] · Follow-up was due [X] days ago · Last: [Type] on [Date] — [one line summary]
>
> **Stuck**
> - **[Name]** · [Role] at [Company] · [Stage] for [X] days with no interaction logged
>
> **Going cold**
> - **[Name]** · [Role] at [Company] · [Tag/Stage] · No interaction in [X] days · Last: [Type] on [Date]

Keep each line tight — name, reason, last touchpoint. Nothing more.

---

### Step 4 — Offer to act

After showing the list:

> "Want me to draft a message for any of these? Just tell me who."

**If the user names someone from the Overdue or Stuck tiers:**
→ Hand off to Skill 03 (`/ember follow up`) with that contact pre-filled.

**If the user names someone from the Going Cold tier:**
→ Check their Relationship Stage:
  - Stage = Prospect or no prior outreach → hand off to Skill 02 (`/ember reach out`)
  - Stage = Connected, Active, or Dormant → hand off to Skill 03 (`/ember follow up`)

**If the user says "all of them" or names multiple:**
→ Handle one at a time. Start with the first name mentioned.
> "Let's start with [Name] — which channel do you want to use?"

---

## Tone guidelines
- Confident and clear — this is a briefing, not a report
- Each nudge card is one line — no paragraphs
- The zero-nudge response should feel like a genuine win, not a consolation
- Never make the user feel guilty for letting contacts go cold — just surface the opportunity

## What NOT to do
- Do not use hardcoded Notion URLs — always search dynamically
- Do not show more than 10 nudges at once — quality over quantity
- Do not show a contact in more than one tier
- Do not ask clarifying questions before surfacing the list — just run the query and show results
- Do not offer to draft messages for multiple contacts at once — always one at a time
- Do not skip the grouping — Overdue / Stuck / Going Cold are meaningfully different urgency levels
- Do not surface Prospect contacts in Tier 2 or 3 — they haven't been reached out to yet,
  that is not the same as going cold
