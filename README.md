# 🔥 Ember — Personal Networking CRM for Claude

> Stay on top of every relationship that matters.  
> Add contacts, draft outreach, log interactions, and get nudged before connections go cold — all from a conversation with Claude.

---

## What is Ember?

Ember is a Claude plugin that turns your Notion workspace into a personal networking CRM. Instead of juggling spreadsheets, reminders, and LinkedIn DMs in separate tabs, you talk to Claude in plain English — and Ember handles the rest.

It's built for anyone who is serious about their network: MBA students in recruiting season, founders doing BD, freelancers maintaining client relationships, or anyone who has ever thought *"I should really follow up with that person."*

Everything lives in **your own Notion workspace**. Ember never stores your data externally.

---

## What Ember Can Do

| Command | What it does |
|---|---|
| `/ember setup` | First-time setup — connects your Notion workspace and personalises Ember for you |
| `/ember add` | Add a new contact (conversational or one-liner fast-track) |
| `/ember reach out` | Draft a first-touch outreach message, personalised by channel and context |
| `/ember follow up` | Draft a follow-up using your logged interaction history |
| `/ember log` | Log a call, coffee chat, DM, or email in seconds |
| `/ember nudge` | See who you're overdue to follow up with, ranked by urgency |

---

## Before You Start — What You Need

You need three things. All free, all one-time setup.

1. **A Claude account** — [claude.ai](https://claude.ai) (free tier works)
2. **A Notion account** — [notion.so](https://www.notion.so) (free tier works)
3. **The Notion MCP integration enabled** in Claude — takes about 60 seconds (instructions below)

---

## Setup — Step by Step

### Step 1 — Duplicate the Notion Template

Click the link below and hit **"Duplicate"** in the top-right corner of the Notion page. This copies the pre-built Ember workspace (Contacts database, Interactions database, and Settings page) into your own Notion account.

👉 [Duplicate the Ember Notion Template](https://www.notion.so/Ember-Networking-CRM-Template-3346ced227548125a3f6c0f5a2caa5a3)

You'll end up with three things in your Notion:
- **Contacts** — a database to store everyone you're networking with
- **Interactions** — a database to log every touchpoint (calls, DMs, coffees)
- **Ember Settings** — a page where Ember stores your name, role, and networking goal

You don't need to fill anything in yet. Ember will populate your Settings page during onboarding.

---

### Step 2 — Connect Notion to Claude

Ember reads and writes to your Notion workspace via an official integration called Notion MCP. Here's how to enable it:

1. Open [claude.ai](https://claude.ai) and sign in
2. Click the **plug icon** (⚡) in the bottom-left sidebar — this opens the Integrations menu
3. Find **Notion** in the list and click **Connect**
4. You'll be redirected to Notion to authorise the connection — click **Allow Access**
5. Come back to Claude — the Notion integration will now show as connected

That's it. Claude can now read and write to your Notion workspace. No API keys. No code.

> **Note:** Ember only accesses the pages and databases it needs — your Contacts, Interactions, and Settings. It doesn't touch anything else in your Notion.

---

### Step 3 — Run Ember Onboarding

Open a new Claude conversation and type:

```
/ember setup
```

Ember will:
1. Verify your Notion databases are connected and ready
2. Ask you three quick questions (your name, your role, why you're networking)
3. Save your answers to your Ember Settings page in Notion
4. Unlock the full command set

The whole thing takes under 2 minutes.

---

## Using Ember — Full Command Guide

### `/ember add` — Add a Contact

**Conversational flow** — Ember asks one question at a time:
```
/ember add
```

**Fast-track** — drop everything in one line:
```
/ember add Alex Rivera, PM at Acme Corp, met at a Kellogg networking event
```

Ember will parse whatever you give it, ask for anything missing, and write the contact to your Notion Contacts database. It automatically infers the **Relationship Stage** (Prospect, Connected, Active, etc.) from context — you never have to set it manually.

Fields Ember captures:
- Name, Company, Role
- LinkedIn URL (optional)
- How We Met
- Relationship Stage (inferred)
- Next Follow-Up Date (if you mention one)
- Notes and Tags (if you mention relevant context)

---

### `/ember reach out` — Draft an Outreach Message

```
/ember reach out Alex Rivera
```

Ember pulls Alex's contact record from Notion, reads your Settings (your name, role, and networking goal), figures out the best hook, and drafts a personalised first-touch message.

**Supported channels:**
- LinkedIn DM (hard 300-character cap — Ember counts characters for you and cuts if needed)
- LinkedIn InMail (with subject line)
- Email (with specific subject line)
- WhatsApp

Ember infers tone from context — it won't ask. Every draft comes with a one-line metadata strip so you know exactly what hook and tone were used, and why.

After approving the draft, Ember will offer to:
- Log the outreach in your Interactions database
- Update the contact's Relationship Stage

---

### `/ember follow up` — Draft a Follow-Up Message

```
/ember follow up Alex Rivera
```

Ember pulls the 3 most recent logged interactions with Alex, recaps them for you, confirms what you want to follow up on, and drafts a message. It never re-introduces you (the recipient already knows you) and always references something specific from your last conversation.

Same channels as outreach, same 300-character cap for LinkedIn DM.

After approving, Ember logs the follow-up and suggests a stage update.

---

### `/ember log` — Log an Interaction

**Conversational:**
```
/ember log
```

**Fast-track one-liner:**
```
/ember log — coffee chat with Jamie yesterday, she offered to make an intro to the strategy team, follow up next week
```

Ember parses the one-liner, fills in today's date if you didn't specify one, and writes everything to your Interactions database — including the **Next Step**, which is what powers the follow-up drafting later.

After logging, Ember will offer to:
- Set a follow-up date on the contact record
- Update the contact's Relationship Stage

---

### `/ember nudge` — See Who to Reconnect With

```
/ember nudge
```

Ember scans your entire Contacts and Interactions databases and surfaces up to 10 people you should reconnect with, grouped into three tiers:

**Overdue** — contacts with a past-due follow-up date  
**Stuck** — contacts whose stage hasn't moved in too long (e.g. "Request Sent" for 14+ days with no reply logged)  
**Going Cold** — high-value contacts (VIP, Mentor, Investor tags, or Active stage) you haven't touched in 30–60+ days

Each nudge card is one line: name, reason, last touchpoint. No fluff.

From the nudge list, you can say "draft a message for Alex" and Ember will hand off to the right skill automatically.

---

## How the Relationship Stage Works

Ember tracks every contact's stage through the pipeline. You never set stages manually — Ember infers and updates them.

| Stage | What it means |
|---|---|
| Prospect | Not yet reached out |
| Request Sent | You've sent a message, waiting on a reply |
| Connected | You're connected / have an open channel |
| Active | In active dialogue — recent back-and-forth |
| Dormant | Haven't spoken in a while |

Stages power the nudge logic and shape how Ember drafts your messages. A follow-up to someone at "Request Sent" will be a gentle nudge that acknowledges no reply yet. A follow-up to someone "Active" will pick up right where you left off.

---

## How Ember Personalises Your Messages

Ember reads three things every time it drafts a message:

1. **Your Settings** (name, role, networking goal) — so messages sound like you and reflect your purpose
2. **The contact's record** (company, role, how you met, tags) — so the hook is always relevant
3. **Your interaction history** (the 3 most recent logged touchpoints) — so follow-ups reference real conversations

This is why logging interactions matters. The more you log, the better Ember's drafts get.

---

## Your Data — Privacy and Ownership

- **All your data lives in your own Notion workspace.** Ember never stores anything on external servers.
- Ember connects to Notion via the official [Notion MCP](https://notion.so) integration. You control what it can access.
- To delete your data, just delete the Notion databases. Nothing else to clean up.

---

## Troubleshooting

**"I don't have [Name] in your contacts yet"**  
Ember can't draft messages for contacts that aren't in your Notion. Add them first with `/ember add`, then retry.

**"I couldn't find your Ember Settings page"**  
You likely haven't duplicated the Notion template yet, or duplicated it into a workspace that isn't connected to Claude. Re-duplicate the template and make sure the Notion integration is authorised.

**Ember seems to be reading someone else's data / nothing is showing up**  
Make sure you duplicated the template — don't just view it. Duplicating creates your own private copy. If you're not sure, check that your Notion sidebar shows a "Contacts" and "Interactions" database that you own.

**The Notion integration isn't showing up in Claude**  
Try disconnecting and reconnecting the Notion integration from Claude's integrations menu. If the issue persists, make sure you're on [claude.ai](https://claude.ai) and not a third-party Claude interface.

**LinkedIn DM is being cut off**  
Ember enforces a hard 300-character cap on LinkedIn DMs (LinkedIn's actual limit). If the draft feels too short, ask Ember to make it "warmer" or "more specific" — it'll rewrite within the limit.

---

## Repo Structure

```
ember/
├── README.md
├── ember.plugin.json          # Plugin manifest
└── skills/
    ├── 00-onboarding.md       # First-time setup flow
    ├── 01-contact-intake.md   # Add contacts
    ├── 02-outreach.md         # Draft first-touch messages
    ├── 03-follow-up.md        # Draft follow-up messages
    ├── 04-log.md              # Log interactions
    └── 05-nudge.md            # Surface reconnection nudges
```

---

## License

MIT — free to use, fork, and build on.

---

*Built by [@atharvabhave21](https://github.com/atharvabhave21)*
