---
description: Searches across all Ember databases using natural language filters. Triggers when the user runs /ember:search, asks an exploratory question like "show me Series B companies in Bangalore" or "contacts I met at events", or wants to filter across their network.
---

# Ember Skill 09 — Search

## Purpose
Answer open-ended, filter-based queries across all four Ember databases.
Parse natural language into structured filters, run the query, and return a
ranked list of results. This is exploratory — the user knows what they're looking
for but doesn't have a specific name in mind.

---

## Trigger
- User types `/ember:search [query]`
- User asks a filter-style question: "show me all Series B companies in Bangalore"
- User asks: "who did I meet at events?" or "high priority applications"
- User asks: "contacts at FinTech companies" or "people I haven't talked to in a while"

---

## Behavior

### Step 1 — Parse the query

Identify which database(s) the query targets and what filters apply.

**Database signals:**
| Signal in query | Target DB |
|---|---|
| Company, startup, stage, industry, city | Companies DB |
| Contact, person, people, met, connection | Contacts DB |
| Application, applied, role, interview, round | Applications DB |
| Interaction, conversation, call, message | Interactions DB |
| Ambiguous | Search all relevant DBs |

**Filter types to extract:**
- Industry (FinTech, SaaS, etc.)
- Stage (Seed, Series A, etc.)
- Size (<50, 50–200, etc.)
- HQ City / location
- Overall Status (Researching, Targeting, Active, Passed)
- Is Target (true/false)
- Relationship Stage (Prospect, Connected, Active, etc.)
- Tags (VIP, Mentor, etc.)
- How We Met (LinkedIn, Event, etc.)
- Application Status (Applied, Final Round, etc.)
- Application Priority (High, Medium, Low)
- Time-based (haven't talked in X days, last interaction before date)

If the query is ambiguous or missing a clear filter, **do not ask for clarification —
make a reasonable inference and run the search**. Show the user what you assumed.

---

### Step 2 — Run the query

Search the target database(s) dynamically. Never use hardcoded URLs.

Apply all extracted filters. If time-based filters are involved, calculate using today's date.

---

### Step 3 — Format results

**If zero results:**
> "Nothing matched '[query]' in your tracker."
Suggest a broader version of the search or a related command.

**If results found**, present as a clean list. Format depends on the target DB:

**Companies results:**
> **[N] companies matched:**
> - **[Name]** · [Industry] · [Stage] · [HQ City] · [Overall Status]

**Contacts results:**
> **[N] contacts matched:**
> - **[Name]** · [Role] at [Company] · [Relationship Stage] · Last: [Date]

**Applications results:**
> **[N] applications matched:**
> - **[Role Title]** at [Company] · [Status] · [Priority] priority · Applied [Date]

**Mixed results (multiple DBs):**
Group by database with a header for each section.

Cap results at **15 per database**. If more exist:
> "Showing top 15 of [N] matches. Narrow your search for more specific results."

Always show what filters were applied:
> *Filtered by: [list of applied filters]*

---

### Step 4 — Offer to act

After showing results:

- If companies → "Want a full profile on any of these? Try `/ember:scout [name]`"
- If contacts → "Want to draft a message to someone? Just tell me who."
  - Hand off to Skill 02 or 03 depending on Relationship Stage
- If applications → "Want to update any of these?" → update mode in Skill 07
- If mixed → offer the most relevant action for the dominant result type

---

## Example queries and how to handle them

| Query | Filters applied | Target DB |
|---|---|---|
| "Series B companies in Bangalore" | Stage = Series B, HQ City = Bangalore | Companies |
| "all contacts at FinTech companies" | Company.Industry = FinTech | Contacts + Companies |
| "high priority applications in Final Round" | Priority = High, Status = Final Round | Applications |
| "people I met at networking events" | How We Met = Networking Event | Contacts |
| "companies I'm actively targeting" | Is Target = true, Overall Status = Active | Companies |
| "contacts I haven't talked to in 60 days" | Last interaction > 60 days ago | Contacts + Interactions |
| "VIP contacts" | Tags includes VIP | Contacts |

---

## Tone guidelines
- Crisp and informative — the user wants data, not prose
- Always show what you filtered by — transparency builds trust
- If you made an inference, say so briefly: *"Interpreted as: [filter]"*
- Never ask for clarification before running — search first, refine after

## What NOT to do
- Do not hardcode any Notion URLs — always search dynamically
- Do not ask for clarification before running the search — infer and go
- Do not return more than 15 results per database
- Do not show raw Notion field names — use plain English
- Do not offer to act on multiple results at once — one at a time
- Do not run an unfiltered full-table scan if no filters can be inferred — ask for at least one filter
