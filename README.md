# AI Lead Qualification & Routing — n8n

A tested portfolio demonstration that turns inbound enquiries into structured, prioritised leads. Built with n8n, OpenAI, webhooks, JavaScript, Data Tables and Microsoft Outlook.

**Quick links:** [Workflow JSON](claudePortfolio_01_-_AI_Lead_Qualification_-_PUBLIC.json) · [Test results](#tested-scenarios) · [Setup](#setup) · [Limitations](#current-limitations)

![Overview of the n8n lead qualification and routing workflow](portfolio%20publish.png)

## Business problem

Manually checking every enquiry, judging its intent, recording it and alerting sales takes time. This workflow demonstrates how to automate those steps so high-priority enquiries can receive attention while other leads remain available for follow-up. Business impact has not been measured in a client deployment.

## What it does

1. Receives a lead through a POST webhook.
2. Checks required fields before calling AI; incomplete submissions receive HTTP 400.
3. Uses GPT-4o-mini to produce a lead score, priority, intent and qualification reason.
4. Parses the JSON output and converts budget and score to numbers.
5. Routes the lead into a hot, warm or low-priority branch.
6. Stores structured lead data in an n8n Data Table.
7. Sends an Outlook notification for hot leads and returns a success response for valid submissions.

| Route | Action |
| --- | --- |
| Hot | Store lead → send sales notification → return success |
| Warm | Store for later nurture → return success |
| Low | Store as low priority → return success |
| Invalid | Reject before AI processing → return HTTP 400 |

Warm leads are stored for nurture; automated follow-up messages are not part of this version. CRM integration is a possible extension, not an implemented feature.

## Tested scenarios

The following end-to-end results were recorded on **3 September 2026**. These are the original project results, not a new test run of the public export.

| Test | Recorded result |
| --- | --- |
| Hot lead | Routed as hot, stored successfully, sales email received with expected lead information, success response returned |
| Warm lead | Routed as warm, stored for nurture, success response returned |
| Low lead | Routed as low priority, stored successfully, success response returned |
| Missing required fields | Rejected before AI processing with HTTP 400 |

## Technology and demonstrated skills

- n8n workflow design and conditional routing
- Webhook intake and required-field validation
- OpenAI GPT-4o-mini classification and scoring
- JavaScript, JSON parsing and numeric conversion
- n8n Data Tables for persistent storage
- Microsoft Outlook notifications and webhook responses

**Training:** n8n Foundations certification.

## Repository files

| File | Purpose |
| --- | --- |
| [Public workflow JSON](claudePortfolio_01_-_AI_Lead_Qualification_-_PUBLIC.json) | Importable workflow structure; configure your own connections before testing |
| [Workflow screenshot](portfolio%20publish.png) | Visual overview of the workflow |
| README.md | Case study, recorded tests, setup and limitations |

## Setup

The public export is sanitised: credentials, internal Data Table IDs, webhook IDs, instance metadata and personal email addresses were removed. The notification recipient is a placeholder (`sales@example.com`).

1. Download the workflow JSON linked above and import it into n8n.
2. Connect your own OpenAI and Microsoft Outlook credentials.
3. Create an n8n Data Table called `portfolio_leads` with the columns below.
4. Select your table in `StoreHotLead`, `StoreWarmLead` and `StoreLowLead`, and check the field mappings.
5. Replace `sales@example.com` with your intended test recipient.
6. Use the imported Webhook node’s test URL and inspect the validation node for the required input fields.
7. Test hot, warm, low and missing-field submissions with synthetic data. Confirm the HTTP response, stored row and hot-lead email before publishing.

| Column | Type |
| --- | --- |
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

Testing uses your connected services; OpenAI usage may incur charges and the hot route sends an email to the configured recipient.

## Current limitations

This is a tested portfolio demonstration, not a full production deployment.

- Required-field checks do not fully validate every input type.
- Malformed AI output does not yet have a recovery route.
- Unexpected priority values do not yet have a fallback route.
- Retries and duplicate prevention have not yet been added.
- Load testing has not been performed.
- The sanitised export needs your own credentials, table selection and testing before use.

## Relevant project work

This project demonstrates skills relevant to small, clearly scoped n8n tasks: webhook intake, field mapping, AI classification, lead routing, table storage and email notifications. Extensions such as CRM integration, follow-up sequences and production hardening need separate implementation and testing.
