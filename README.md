# AI Ticket Routing & SLA Escalation for a Barbershop Chain

**n8n · OpenAI (GPT-4o-mini) · Gmail · Google Sheets · Telegram**

Critical customer complaints escalated within 5 minutes - every email classified, prioritized and routed automatically.

> Training project (GoIT AI Automator) based on a realistic scenario.

![n8n workflow](screenshots/workflow.png)

## Problem

A chain of 4 men's barbershops received all customer emails in one shared inbox, sorted by hand.

- No priority queue - critical complaints (rude staff, ruined haircut) got lost among questions about prices and booking
- No SLA control - tickets could stay unanswered for an undefined time
- Spam was mixed with real requests and wasted managers' time

## Solution

A two-track AI workflow in n8n.

**Track 1 - Ticket classification**
Gmail Trigger → collect context → AI Classifier (GPT-4o-mini, Chain of Thought, strict JSON) → category / priority P1-P4 / owner / SLA → spam filter → log to Google Sheets + instant Telegram alert to the owner.

**Track 2 - SLA Watcher (every 5 minutes)**
Reads open tickets → detects missed deadlines → 3-level escalation:

| Level | Overdue | Action |
|---|---|---|
| L0 | 0-30 min | Telegram reminder to the owner |
| L1 | 30-60 min | Telegram alert to the team lead |
| L2 | 60+ min | Email to the director with full context |

**Categories:** booking, complaint, pricing_services, b2b_partnership, spam
**Priorities:** P1 (15 min) · P2 (120 min) · P3 (1440 min) · P4 (2880 min)

## Results (real test runs)

- 19 tickets processed automatically - 100% correct classification (category + priority + owner + SLA)
- ~5-8 seconds per ticket instead of manual sorting
- ~$0.0002 per ticket (GPT-4o-mini) → 1,000 tickets ≈ $0.20
- All 3 escalation levels confirmed; breach detected within 5 minutes
- Spam filtering: 2/2 spam emails filtered out after a bug fix

## Bug found & fixed

The "not spam?" router checked `{{ $json.category }}` instead of `{{ $json.parsed.category }}` after a data-structure change, so spam passed through and once triggered a false escalation. Fixed the expression and re-tested - spam now goes to the false branch.

## How to use

1. In n8n: **Workflows → Import from file** → select `ai-ticket-routing-sla.json`
2. Connect your own credentials: Gmail, Google Sheets, OpenAI, Telegram
3. Replace placeholders:
   - `YOUR_GOOGLE_SHEET_ID` - sheet with the columns used in "Log → Tickets Sheet"
   - `YOUR_TELEGRAM_CHAT_ID` - owner chat
   - `YOUR_TEAM_LEAD_GROUP_CHAT_ID` - team lead group
   - `director@company.example` - director email
4. Adapt categories and SLA minutes in the AI Classifier prompt to your business

No API keys or personal data are included in this repository.

## Author

**Petro Volkov** - AI Automation for Logistics & Sales
[Upwork](https://www.upwork.com/freelancers/~01bf694cbbacdc6c7d) · [Portfolio](https://app.notion.com/p/Petro-Volkov-AI-Automator-39aa3189d4e1803ab7ece06f01930819) · [LinkedIn](https://linkedin.com/in/petro-volkov-5a576b87)
