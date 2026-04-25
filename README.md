# 🔥 Ember — Personal Networking CRM for Claude

> Stay on top of every relationship and opportunity that matters.  
> Track contacts, companies, and job applications. Draft outreach, log interactions, and get nudged before connections go cold — all from a conversation with Claude.

---

## What is Ember?

Ember is a Claude plugin that turns your Notion workspace into a personal networking CRM and job search pipeline. Instead of juggling spreadsheets, reminders, and LinkedIn DMs in separate tabs, you talk to Claude in plain English — and Ember handles the rest.

It's built for anyone serious about their network and job search: MBA students in recruiting season, founders doing BD, freelancers maintaining client relationships, or anyone who has ever thought *"I should really follow up with that person."*

Everything lives in **your own Notion workspace**. Ember never stores your data externally.

---

## What Ember Can Do

### Contacts & Outreach
| Command | What it does |
|---|---|
| `/ember:setup` | First-time setup — connects your Notion workspace and personalises Ember |
| `/ember:contact` | Add a new contact (conversational or one-liner fast-track) |
| `/ember:reach-out` | Draft a first-touch outreach message, personalised by channel and context |
| `/ember:follow-up` | Draft a follow-up using your logged interaction history |
| `/ember:log` | Log a call, coffee chat, DM, or email in seconds |
| `/ember:nudge` | See who and what needs your attention, ranked by urgency |

### Companies & Applications
| Command | What it does |
|---|---|
| `/ember:company` | Add a target company to your job search pipeline |
| `/ember:apply` | Log a job application or update an existing one |
| `/ember:pipeline` | Bird's eye view of all target companies grouped by status |

### Search & Explore
| Command | What it does |
|---|---|
| `/ember:scout` | Full profile — company or contact — in one view |
| `/ember:search` | Search across your entire network using natural language |

---

## Before You Start — What You Need

1. **A Claude account on a paid plan** — [claude.ai](https://claude.ai) (Pro or Max)
2. **A Notion account** — [notion.so](https://www.notion.so) (free tier works)
3. **Claude Cowork** — the desktop app from Anthropic ([download here](https://claude.ai/download))

---

## Setup — Step by Step

### Step 1 — Duplicate the Notion Template

Click the link below and hit **"Duplicate"** in the top-right corner of the Notion page.

👉 [Duplicate the Ember Notion Template](https://languid-drip-947.notion.site/Ember-Personal-Networking-CRM-Template-3346ced227548125a3f6c0f5a2caa5a3)

You'll get five things in your Notion — rename all of them by removing "(Template)" from the name:

| Rename this | To this |
|---|---|
| Contacts (Template) | Contacts |
| Interactions (Template) | Interactions |
| Companies (Template) | Companies |
| Applications (Template) | Applications |
| Ember Settings (Template) | Ember Settings |

---

### Step 2 — Connect Notion to Claude

1. Open **Claude Cowork** and sign in
2. Click the **plug icon** (⚡) in the sidebar
3. Find **Notion** and click **Connect**
4. Authorise access in Notion, then return to Cowork

---

### Step 3 — Install the Ember Plugin

1. Download `ember.plugin` from the [Ember GitHub repo](https://github.com/atharvabhave21/ember)
2. In Claude Cowork, run:

```
claude plugin install ~/Downloads/ember.plugin
```

---

### Step 4 — Run Setup

```
/ember:setup
```

Ember will verify your Notion databases, ask three quick questions, and unlock the full command set. Takes under 2 minutes.

---

## Using Ember — Full Command Guide

### `/ember:contact` — Add a Contact

```
/ember:contact
/ember:contact Alex Rivera, PM at Zepto, met at a Kellogg networking event
```

Ember captures Name, Company (linked to Companies DB), Role, LinkedIn URL, How We Met, and infers Relationship Stage automatically. If the company doesn't exist in your tracker, Ember asks whether it's a target — one question, then handles everything silently.

---

### `/ember:company` — Add a Target Company

```
/ember:company
/ember:company Zepto, quick commerce, Series D, Bangalore, found on LinkedIn
```

Adds a company to your pipeline with Is Target = true. Ember captures Name, Why Attractive, Industry, Stage, HQ City, and Source. All select options are read dynamically from your Notion schema — add new options in Notion and they appear automatically.

---

### `/ember:apply` — Log a Job Application

```
/ember:apply
/ember:apply PM Growth at Zepto, high priority, applied today
/ember:apply update Zepto PM to Phone Screen
```

Links the application to the company in your Companies DB. Infers Status from context (Applied, Phone Screen, Final Round, etc.). Offers to set a follow-up reminder and update the company's overall status.

---

### `/ember:pipeline` — View Your Company Pipeline

```
/ember:pipeline
```

Shows all target companies grouped by status — Active → Targeting → Researching → Passed. For each company: contacts there, open applications, last interaction date, and a stale flag (⚠️) if no activity recently. Surfaces the single most important action to take.

---

### `/ember:scout` — Full Profile

```
/ember:scout Zepto
/ember:scout Sarah Chen
```

Automatically detects whether you're asking about a company or a person.

- **Company profile:** status, why attractive, all contacts there, open applications, links
- **Contact profile:** relationship stage, company info, full interaction history, next step

---

### `/ember:search` — Search Your Network

```
/ember:search Series B companies in Bangalore
/ember:search contacts I met at networking events
/ember:search high priority applications in Final Round
/ember:search VIP contacts I haven't spoken to in a while
```

Searches across all four databases using natural language. Infers filters, runs the query, shows results with what was filtered by.

---

### `/ember:reach-out` — Draft Outreach

```
/ember:reach-out Alex Rivera
```

Pulls Alex's record, reads your Settings, finds the best hook, and drafts a personalised first-touch message. Supported channels: LinkedIn DM (hard 300-char cap), LinkedIn InMail, Email, WhatsApp.

---

### `/ember:follow-up` — Draft a Follow-Up

```
/ember:follow-up Alex Rivera
```

Loads the 3 most recent interactions, recaps for confirmation, then drafts a message that references your actual last conversation. Never re-introduces you.

---

### `/ember:log` — Log an Interaction

```
/ember:log
/ember:log coffee chat with Jamie yesterday, she offered an intro to strategy team, follow up next week
```

Writes to your Interactions DB including Next Step — which powers follow-up drafting later.

---

### `/ember:nudge` — See What Needs Attention

```
/ember:nudge
```

Surfaces up to 7 people (Overdue / Stuck / Going Cold) plus company and application nudges (stalled pipeline, Final Round with no activity). Offers to draft a message or take action for any of them.

---

## Your Notion Databases

Ember uses four linked databases in your Notion workspace:

| Database | What it stores |
|---|---|
| **Contacts** | Everyone in your network, linked to their company |
| **Interactions** | Every touchpoint — calls, DMs, coffees, emails |
| **Companies** | Every employer — targets (Is Target = true) and directory entries |
| **Applications** | Job applications, linked to companies and referral contacts |

Plus one settings page: **Ember Settings** — your name, role, and networking goal.

### About select field options
All select options (Industry, Stage, Status, etc.) are read dynamically from your Notion schema at runtime. To add a new option, just edit the field in Notion — no skill files need updating.

---

## How the Relationship Stage Works

Ember tracks every contact's stage automatically — you never set it manually.

| Stage | What it means |
|---|---|
| Prospect | Not yet reached out |
| Request Sent | You've sent a message, waiting on a reply |
| Connected | You're connected / have an open channel |
| Active | In active dialogue — recent back-and-forth |
| Dormant | Haven't spoken in a while |

---

## Your Data — Privacy and Ownership

- **All your data lives in your own Notion workspace.** Ember never stores anything externally.
- To delete your data, just delete the Notion databases.

---

## Troubleshooting

**"I don't have [Name] in your contacts yet"**
Add them first with `/ember:contact`, then retry.

**"I couldn't find your Ember Settings page"**
You likely haven't duplicated the Notion template, or it's in a workspace that isn't connected to Claude.

**Ember seems to be reading someone else's data**
Make sure you duplicated the template — don't just view it.

**The Notion integration isn't showing up in Claude**
Try disconnecting and reconnecting from Claude's integrations menu.

**LinkedIn DM is being cut off**
Ember enforces a hard 300-character cap (LinkedIn's actual limit). Ask Ember to rewrite within the limit.

---

## Repo Structure

```
ember/
├── README.md
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── commands/
│   ├── setup.md                 # /ember:setup
│   ├── contact.md               # /ember:contact
│   ├── company.md               # /ember:company
│   ├── apply.md                 # /ember:apply
│   ├── reach-out.md             # /ember:reach-out
│   ├── follow-up.md             # /ember:follow-up
│   ├── log.md                   # /ember:log
│   ├── scout.md                 # /ember:scout
│   ├── search.md                # /ember:search
│   ├── pipeline.md              # /ember:pipeline
│   └── nudge.md                 # /ember:nudge
└── skills/
    ├── 00-onboarding.md         # First-time setup flow
    ├── 01-contact-intake.md     # Add contacts + company linking
    ├── 02-outreach.md           # Draft first-touch messages
    ├── 03-follow-up.md          # Draft follow-up messages
    ├── 04-log.md                # Log interactions
    ├── 05-nudge.md              # People + company nudges
    ├── 06-company.md            # Add target companies
    ├── 07-apply.md              # Log job applications
    ├── 08-scout.md              # Company + contact profiles
    ├── 09-search.md             # Natural language search
    └── 10-pipeline.md           # Company pipeline view
```

---

## License

MIT — free to use, fork, and build on.

---

*Built by [@atharvabhave21](https://github.com/atharvabhave21)*
