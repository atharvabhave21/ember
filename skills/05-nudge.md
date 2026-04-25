---
description: Surfaces contacts and companies the user should reconnect with or follow up on, ranked by urgency. Triggers when the user runs /ember:nudge, asks who to follow up with, or says things like "who have I been neglecting" or "what needs my attention".
---

# Ember Skill 05 — Follow-Up Nudging

## Purpose
Surface who and what needs the user's attention — ranked by urgency, grouped
by signal type. Covers both people (contacts) and companies (pipeline + applications).
Ember does the thinking so the user doesn't have to.

---

## Trigger
- User types `/ember nudge`
- User says "who should I follow up with?" or "what needs my attention?"

---

## Behavior

### Step 1 — Query all databases

Search Contacts, Interactions, Companies, and Applications databases dynamically.
Never use hardcoded URLs.

**From Contacts + Interactions:**
- Name, Employer, Role, Relationship Stage, Tags, Next Follow-Up Date
- Date and summary of most recent logged interaction per contact

**From Companies (where Is Target = true):**
- Name, Overall Status, HQ City, Industry
- Count of linked contacts
- Date of most recent interaction with any linked contact

**From Applications:**
- Role Title, Company (linked), Status, Priority, Follow-Up Date
- Date of most recent interaction with Referral Contact (if linked)

Calculate today's date. Use it to determine days overdue and days since last interaction.

---

## Part A — People Nudges

### Step 2A — Score and classify each contact

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

### Step 3A — Cap and format people list

Take up to 7 contacts total across all three tiers.
Fill Tier 1 first, then Tier 2, then Tier 3 until the cap is reached.

---

## Part B — Company & Application Nudges

### Step 2B — Score and classify companies and applications

Run target companies and open applications through these tiers:

---

**Tier 1 — Application urgency**

Condition: Application Status is Final Round OR Follow-Up Date has passed.

| Signal | Urgency |
|---|---|
| Final Round, no interaction in 5+ days | Critical |
| Follow-Up Date passed | Overdue |
| Phone Screen / Technical, no interaction in 10+ days | Stale |

Sort by most urgent first (Final Round > overdue date > stale).
Cap at 3 applications.

---

**Tier 2 — Company pipeline stuck**

Condition: Is Target = true, Overall Status = Targeting or Active, but no progress signal.

| Signal | Threshold |
|---|---|
| Targeting status, no contacts linked | 14 days |
| Active status, no interaction with any contact | 21 days |
| Active status, contacts exist but none interacted with recently | 30 days |

Sort by most days stuck first.
Cap at 3 companies.

---

### Step 3B — Format company list

---

## Step 4 — Present combined output

Show people nudges first, then company/application nudges. Use clear section headers.

**If zero nudges across both sections:**
> "You're all caught up — no overdue follow-ups, cold contacts, or stalled applications right now."

Stop. Do not proceed.

**If nudges are found:**

> "Here's what needs your attention:"
>
> **── People ──**
>
> *Overdue*
> - **[Name]** · [Role] at [Employer] · Follow-up was due [X] days ago · Last: [Type] on [Date] — [one line summary]
>
> *Stuck*
> - **[Name]** · [Role] at [Employer] · [Stage] for [X] days with no interaction logged
>
> *Going cold*
> - **[Name]** · [Role] at [Employer] · [Tag/Stage] · No interaction in [X] days
>
> **── Companies & Applications ──**
>
> *Applications*
> - **[Role Title]** at [Company] · [Status] · [X days since last interaction / follow-up date passed]
>
> *Pipeline*
> - **[Company]** · [Status] · [Reason flagged — e.g. "Targeting for 18 days, no contacts added"]

Keep each line tight — name, reason, last touchpoint. Nothing more.

---

### Step 5 — Offer to act

After showing the list:

> "Want me to draft a message or take action on any of these? Just tell me who or what."

**For people nudges:**
- Overdue or Stuck → hand off to Skill 03 (`/ember:follow-up`) with that contact pre-filled
- Going Cold + Prospect/no prior outreach → hand off to Skill 02 (`/ember:reach-out`)
- Going Cold + Connected/Active/Dormant → hand off to Skill 03 (`/ember:follow-up`)

**For application nudges:**
- User wants to follow up on an application → hand off to Skill 03 (`/ember:follow-up`) with the Referral Contact pre-filled if one exists
- User wants to update application status → hand off to Skill 07 (`/ember:apply`) update mode

**For pipeline nudges:**
- User wants to add contacts at a company → hand off to Skill 01 (`/ember:contact`)
- User wants to view a company in full → hand off to Skill 08 (`/ember:scout`) with company pre-filled

**If the user says "all of them" or names multiple:**
→ Handle one at a time. Start with the first name mentioned.
> "Let's start with [Name/Company] — what do you want to do?"

---

## Tone guidelines
- Confident and clear — this is a briefing, not a report
- Each nudge card is one line — no paragraphs
- The zero-nudge response should feel like a genuine win, not a consolation
- Never make the user feel guilty for letting contacts go cold — just surface the opportunity

## What NOT to do
- Do not use hardcoded Notion URLs — always search dynamically
- Do not show more than 7 people nudges + 6 company/application nudges
- Do not show a contact in more than one tier
- Do not ask clarifying questions before surfacing the list — just run the query and show results
- Do not offer to draft messages for multiple contacts at once — always one at a time
- Do not skip the grouping — People / Companies & Applications are meaningfully different
- Do not surface Prospect contacts in people Tier 2 or 3 — they haven't been reached out to yet
- Do not surface companies where Is Target = false in company nudges
