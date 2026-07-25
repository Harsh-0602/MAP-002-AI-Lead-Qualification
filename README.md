# MAP-002 – AI Lead Qualification System

## Overview

This AI workflow automatically qualifies incoming leads using Google Gemini, checks for duplicates before writing, and stores validated results inside Google Sheets.

---

## Business Problem

Sales teams waste time manually reviewing every lead before deciding who deserves immediate attention — and duplicate lead submissions (e.g. a form submitted twice) can quietly damage trust in the data.

---

## Solution

This workflow:

- Generates a stable, unique key for each lead based on its core details
- Checks Google Sheets for an existing entry with that key before doing anything else
- Skips processing if the lead is a duplicate
- Uses AI to analyze buying intent, score, and categorize the lead
- Validates that the AI's output is well-formed JSON with expected values before storing it
- Flags invalid AI output separately instead of writing bad data to the sheet
- Stores only validated, non-duplicate leads automatically

---

## Workflow

Webhook
↓
Generate Lead Key
↓
Check Duplicate (Google Sheets lookup)
↓ (new lead) ↓ (duplicate)
Google Gemini Skip (Edit Fields)
↓
Validate AI Output (JSON shape check)
↓ (valid) ↓ (invalid)
Google Sheets Flag Invalid Lead Output

---

## Tech Stack

- n8n
- Google Gemini
- Google Sheets

---

## Sample Input / Output

**Input:**
```json
{
  "name": "Ramesh Sharma",
  "email": "ramesh@company.com",
  "company": "Sharma Textiles",
  "employees": "50-100",
  "budget": "$5000/month",
  "requirement": "Need automation for lead tracking urgently"
}
```

**Output (row added to Google Sheets):**

Lead Score: 88
Category: Warm
Priority: High
Reason: Strong budget and urgency signals, but company size is mid-range.

If the same lead is submitted again, the workflow detects the duplicate via its LeadKey and does not create a second row.

---

## Output Fields

- Lead Score
- Category
- Priority
- Reason
- LeadKey (used internally for duplicate detection)

---

## Use Cases

- SaaS Companies
- AI Startups
- Sales Teams
- Agencies

---

## Recent Updates (based on r/n8n community feedback)

This workflow was reviewed by the n8n community on Reddit, and the following changes were made based on their feedback:

- Added a dedupe/idempotency check using a stable lead key — prevents the same lead from being scored and logged multiple times (e.g. from webhook retries or duplicate form submissions)
- Added JSON shape validation on the AI's output — invalid or malformed responses no longer silently break the workflow or write garbage data
- Invalid outputs are now flagged separately for review instead of being written to the main sheet


---

## Future Improvements

- Use a stable event/submission ID from the webhook as the dedupe key instead of a content-based hash
- Separate raw payload, normalized record, and AI output into distinct log fields for easier debugging
- Add execution ID logging for traceability
- Support-ticket triage flow as the next automation in the series
