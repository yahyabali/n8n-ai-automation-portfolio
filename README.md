# n8n & AI Automation Portfolio

Practical projects using n8n, AI, webhooks, and API integrations to automate business tasks.

## Portfolio 01 — AI Lead Qualification & Routing

An n8n workflow that assesses incoming enquiries, stores qualified lead details, and alerts sales when a lead needs immediate attention.

### What it does

1. Receives a lead through a POST webhook.
2. Checks that name, email, and message are present.
3. Uses GPT-4o-mini to generate a lead score, priority, intent, and reason.
4. Routes the lead as hot, warm, or low priority.
5. Stores the lead in an n8n Data Table.
6. Sends a Microsoft Outlook notification for hot leads.
7. Returns a success response, or rejects incomplete submissions.

### Routing

| Priority | Action | Stored status |
|---|---|---|
| Hot | Store and email sales | sales_follow_up |
| Warm | Store for nurture | nurture |
| Low | Store without a sales notification | low_priority |

Priority is determined by the AI assessment rather than fixed score thresholds.

### Technology

- n8n
- OpenAI GPT-4o-mini
- JavaScript and JSON
- POST webhooks
- n8n Data Tables
- Microsoft Outlook

## Workflow file

Import the PUBLIC.json workflow included in this repository into n8n.

The public export intentionally omits credentials, internal table IDs, webhook IDs, and instance metadata. The notification recipient is a placeholder: sales@example.com.

## Setup

1. Import the workflow into an n8n instance that supports the included node versions.
2. Connect the OpenAI Chat Model to your available OpenAI credential or gateway connection.
3. Connect your Microsoft Outlook credential.
4. Create a Data Table named portfolio_leads with the columns below.
5. Select that table in StoreHotLead, StoreWarmLead, and StoreLowLead. Check that the column mappings remain populated.
6. Replace sales@example.com with your notification recipient.
7. Test the workflow before publishing it.

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

Changing a workflow mapping does not migrate an existing table’s column types. Both budget and lead_score should be numeric in the actual table.

For manual testing, start the full workflow with **Execute workflow**, then send a POST request to its Test URL.

## Manual test results

The following checks passed on 3 September 2026 using an imported copy with credentials connected and a separate test table.

| Test | Observed result |
|---|---|
| Hot lead | Routed as hot, stored, notification email received with populated fields, and success response returned |
| Warm lead | Routed as warm, budget 800 and score 30 shown as numeric, and success response returned |
| Low lead | Routed as low, budget 0 and score 0 shown as numeric, and success response returned |
| Missing message | Returned “Missing required lead fields” through the rejection branch; AI, storage, and email were skipped |

Budgets were submitted as JSON strings to exercise numeric conversion. AI scores and classifications may vary between runs.

## Current limitations

This is a tested portfolio demonstration with basic validation.

- Validation checks required fields for presence; it does not fully validate email format or every input type.
- AI output is parsed directly as JSON. Malformed output does not have a dedicated recovery path.
- Unexpected priority values do not have a fallback route.
- Retries, duplicate prevention, and service-failure handling have not been added.
- Load testing and broader failure testing have not been performed.

## Privacy

The repository contains a sanitized workflow export. Configure credentials and account-specific settings inside your own n8n instance, and keep configured exports and private lead data out of public GitHub commits.
