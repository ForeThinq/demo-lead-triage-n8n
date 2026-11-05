# Inbound Lead Triage — n8n + AI

**Never lose another lead.** Every enquiry that hits your form is validated, deduped, scored by AI, logged to Google Sheets, and — if it's worth your time — in your inbox within a minute.

Built by [Forethinq Labs](https://github.com/ForeThinq) as a working demo of the lead-flow automation we sell.

## What it does

```
Lead form → Validate → Dedupe check → AI score (HOT / WARM / COLD)
          → Google Sheets row → Instant Gmail alert (HOT & WARM only)
```

1. **Lead Form** — a hosted n8n form (name, email, phone, company, service, budget, message).
2. **Validate & Normalize** — cleans the input, lowercases the email, drops junk submissions.
3. **Dedupe Check** — compares the email against the Leads sheet. A repeat enquiry is skipped: no second row, no second alert.
4. **AI Classify (Gemini)** — scores the lead 0–100 with a category (HOT / WARM / COLD), a one-line reason, and a suggested next step, based on fit, budget, and urgency.
5. **Append to Leads Sheet** — the full record, with score and reasoning, lands in one clean row.
6. **Instant Alert** — HOT and WARM leads trigger an immediate Gmail alert with everything you need to reply. COLD leads are logged silently.
7. **Error workflow** (separate file) — if anything in the chain fails, you get a failure alert with the node and error, so a lead is never lost silently.

## Failure handling (the part most demos skip)

- **Duplicate submissions** are detected by email and skipped.
- **Invalid submissions** (no name, bad email, empty message) are dropped before they touch the sheet.
- **AI unavailable or returns junk** → the lead still gets logged with a safe fallback score and a "review manually" flag.
- **Any node fails** → the companion error workflow emails you the failing node and error message.

## Files

| File | What it is |
|---|---|
| `workflow-lead-triage.json` | The main workflow — import into n8n |
| `workflow-error-handler.json` | The error workflow — import, then select it in the main workflow's Settings |
| `sample-leads.csv` | 5 test leads (hot, warm, cold/spam, a duplicate, a borderline case) with expected results |

## Setup (10 minutes)

1. **Install n8n** locally (`npx n8n`) or use any n8n instance.
2. **Import both JSON files** (Workflows → ⋯ → Import from File).
3. **Create a Google Sheet** with a tab named `Leads` and these headers in row 1:
   `Received At | Name | Email | Phone | Company | Service | Budget | Message | Score | Category | Reason | Next Action | Status`
4. **Connect your Google account** in the two Google Sheets nodes and the Gmail node (OAuth, one click each), and select your sheet in the Sheets nodes.
5. **Get a free Gemini API key** from [Google AI Studio](https://aistudio.google.com/apikey) and replace `YOUR_GEMINI_API_KEY` in the *AI Classify — Gemini* node.
6. **Replace `YOUR_EMAIL@example.com`** in both Gmail nodes with the inbox that should receive alerts.
7. **Error workflow:** open the main workflow → Settings → Error Workflow → select *Error Handler — Lead Triage Alerts*.
8. **Activate** the workflow and open the form URL (`/form/lead-intake`) to submit test leads from `sample-leads.csv`.

## Try it against the samples

Submit the rows from `sample-leads.csv` one at a time. You should see: two strong leads scored HOT/WARM with alerts, the repeat Priya submission skipped as a duplicate, the spam scored COLD with no alert, and the salon enquiry scored WARM despite the tiny budget — because the *need* is real.

## Want this for your business?

This demo is a template of what Forethinq Labs builds for clients — lead capture → AI triage → CRM/Sheets → instant follow-up, inside **your** accounts, at a fixed price.

📧 forethinq@gmail.com · [GitHub](https://github.com/ForeThinq) · [Reddit](https://www.reddit.com/user/ForeThinq/)
