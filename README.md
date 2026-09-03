# AI Lead Qualification & Routing Automation — n8n

A production-style n8n automation that receives inbound leads, validates them, uses AI to qualify and score them, routes them by priority, stores structured lead data, and alerts sales when a high-value lead arrives.

## Business Problem

Businesses often receive leads through website forms, contact pages, or enquiry systems, but someone still has to manually:

- check whether the lead information is complete
- read the enquiry
- judge how serious the lead is
- decide whether sales should respond immediately
- store the lead details
- notify the right person

This workflow automates that process.

## What the Automation Does

1. Receives a new lead through a POST webhook
2. Validates required fields before using AI
3. Sends the lead details to GPT-4o-mini
4. Produces structured qualification data:
   - lead score
   - priority
   - intent
   - qualification reason
5. Routes the lead automatically:
   - **Hot** → store + immediate sales notification
   - **Warm** → store for nurture
   - **Low** → store without interrupting sales
6. Saves the structured lead data to an n8n Data Table
7. Sends a Microsoft Outlook notification for hot leads
8. Returns a success response to valid submissions
9. Rejects incomplete leads with a clear HTTP 400 response before AI processing

## Business Outcome

The workflow removes the need for someone to manually review every inbound enquiry.

High-intent leads can be surfaced immediately, while lower-priority enquiries are still captured and stored for later follow-up.

This architecture could be adapted for:

- website lead qualification
- CRM lead routing
- sales inbox automation
- consultation enquiries
- customer support triage
- appointment leads
- AI-assisted intake forms

## Tested Scenarios

The workflow was tested end-to-end on 3 September 2026.

| Test | Result |
|---|---|
| Hot lead | Routed as hot, stored successfully, sales email received, success response returned |
| Warm lead | Routed as warm, stored for nurture, success response returned |
| Low lead | Routed as low priority, stored successfully, success response returned |
| Missing required fields | Rejected before AI processing with HTTP 400 response |

The hot-lead test also confirmed that the notification email contained the expected lead information.

## Reliability Features

- Required-field validation before AI processing
- Structured JSON AI output
- Separate hot, warm, and low routing paths
- Numeric conversion for budget and lead score
- Persistent lead storage
- Separate invalid-lead handling
- Dedicated webhook responses
- Sanitized public workflow with no private credentials or account IDs

## Technology

- n8n
- OpenAI GPT-4o-mini
- Webhooks
- JavaScript
- JSON
- n8n Data Tables
- Microsoft Outlook
- Conditional routing

## Workflow Logic

**Webhook → Validation → AI Qualification → JSON Parsing → Priority Routing**

### Hot
Store lead → Send immediate sales notification → Return success response

### Warm
Store for nurture → Return success response

### Low
Store as low priority → Return success response

### Invalid
Reject before AI processing → Return HTTP 400 response

## Public Workflow

The sanitized n8n workflow JSON is included in this repository.

The public export intentionally removes:

- credentials
- internal Data Table IDs
- webhook IDs
- instance metadata
- personal email addresses

The notification recipient is replaced with:

`sales@example.com`

## Setup

1. Import the public workflow JSON into n8n
2. Connect an OpenAI credential
3. Connect a Microsoft Outlook credential
4. Create an n8n Data Table called `portfolio_leads`
5. Use these columns:

| Column | Type |
|---|---|
| name | String |
| email | String |
| company | String |
| budget | Number |
| lead_score | Number |
| priority | String |
| intent | String |
| reason | String |
| status | String |
| created_at | Date |

6. Select that table in the StoreHotLead, StoreWarmLead, and StoreLowLead nodes
7. Replace `sales@example.com` with the desired notification recipient
8. Test the workflow before publishing

## Current Limitations

This is a tested portfolio demonstration rather than a full production deployment.

Current limitations include:

- validation checks required fields but does not fully validate every input type
- malformed AI output does not yet have a recovery route
- unexpected priority values do not yet have a fallback route
- retries and duplicate prevention have not yet been added
- load testing has not been performed

## Why I Built This

The goal was to demonstrate a complete business automation flow rather than simply connecting nodes together:

**receive data → validate it → use AI where useful → structure the result → route it → store it → trigger the correct business action**

This is the same approach I would use when designing a client automation around leads, CRM workflows, customer enquiries, or internal operations.
