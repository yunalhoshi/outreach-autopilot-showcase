# Outreach Autopilot (showcase)

**AI-assisted B2B lead research and cold-email pipeline with a human approval gate.**
Finds small businesses, reads their websites, scores them with deterministic rules, drafts personalised emails only when a *verified* personalisation hook exists, and manages follow-ups, replies and opt-outs. Nothing is ever sent without approval.

```text
find-leads → research → score → generate-emails → review (human) → send → followups / check-replies → dashboard
```

## Why it is different

| Problem with typical AI outreach | How this pipeline handles it |
|---|---|
| AI invents "I saw on your site…" details | Every quote Claude claims to have seen is searched **verbatim** in the crawled page text. Anything not found is dropped before drafting. |
| AI decides who is a good lead | Scoring is **plain JavaScript** with weighted, explainable signals (`config/scoring.json`). Claude is used only for site summaries and drafts. |
| Bots get blocked or break sites' rules | Respects `robots.txt`, never logs in, never bypasses CAPTCHAs, skips on 403/429 and retries a failed site at most 3 times. |
| Compliance is an afterthought | US CAN-SPAM footer, physical address and instant opt-out; UK PECR rule that blocks sending until a lead is verified as a corporate subscriber (Ltd/LLP). |
| Spreadsheet edits get overwritten | The bot writes only the cells it changed; scraped text is never interpreted as a formula. |

## Features

- **Lead discovery:** Google Places (New) text search or CSV import; deduplicated by domain.
- **Crawler:** Playwright, up to 5 relevant pages per site, keyword-guided page selection.
- **Deterministic extraction:** carriers/tools named, lines of business, team-size evidence, public business emails (free-provider and personal addresses skipped), company LinkedIn link from the site itself.
- **Claude (Haiku):** site summary and email draft only, with JSON output and loose-JSON recovery.
- **Draft rules:** word limits, banned phrases, subject checks, verified hook required.
- **Review CLI:** approve, edit or skip each draft in the terminal.
- **Sending:** SMTP with dry-run mode (writes to `outbox/`), daily limits, business-day follow-up scheduling.
- **Replies:** IMAP reader detects replies, bounces and opt-outs (and ignores the quoted "unsubscribe" line of your own email).
- **Dashboard:** funnel counts, reply rate by priority and pending actions.
- **Multi-service configs:** switch niche by changing one JSON file, no code changes:
  `insurance-form-automation`, `lead-generation`, `order-automation`, `ai-crm`, `weekly-reporting`.
- **Offline selftest:** checks extraction, verification, scoring, compliance and email rules without any API keys.

## Commands

| Command | What it does |
|---|---|
| `npm run setup` | Creates the Google Sheet tabs and tests every connection |
| `npm run find-leads` / `import-leads` | Adds new leads from Places or a CSV |
| `npm run research` | Crawls sites, extracts signals, verifies Claude's claims, scores |
| `npm run generate-emails` | Drafts emails for qualified leads |
| `npm run review` | Human approval step |
| `npm run send` | Sends approved emails (or writes them to `outbox/` in dry-run) |
| `npm run followups` / `check-replies` / `dnc` | Follow-ups, reply handling, do-not-contact list |
| `npm run dashboard` | Pipeline summary |
| `npm run daily` | Runs the whole safe sequence; never sends |
| `npm run selftest` | Offline logic test |

## Architecture

```text
config/      settings, scoring weights, one JSON per service/niche
prompts/     research and email prompts (system + user)
src/lib/     crawler, extract, analyze, scoring, compliance, claude, sheets, mailer, imap, state
src/commands one file per CLI command (13 commands)
```

## Source code

The source code is kept in a **private repository** because the pipeline is the core of a commercial automation service. A walkthrough or live demo is available on request.

## Tech stack

Node.js 20 (ES modules) · Playwright · Claude API (Haiku) · Google Sheets API · Google Places API (New) · Nodemailer (SMTP) · ImapFlow · robots-parser

Running cost at ~300 leads/month is estimated at **$5–8/month** (Claude usage, mailbox and domain).

## Security

All secrets live in `.env` and `secrets/`, both git-ignored. The private repository contains no keys or lead data.

---

© 2026 yunalhoshi. All rights reserved. See [NOTICE](NOTICE).
