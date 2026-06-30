# AI Grants Classifier — n8n Workflow

An AI-powered automation that collects applicant details through a form, classifies their financial status using an LLM, logs the result to Google Sheets, and automatically sends a grant approval email to applicants who qualify.

## Overview

This n8n workflow automates the grant eligibility process end-to-end:

1. **Form Intake** — Applicants submit their details (Name, City, Email, Salary, Graduation status, Number).
2. **AI Classification** — A Google Gemini-powered AI Agent analyzes the salary and classifies the applicant as `Rich` or `Poor`.
3. **Sheet Logging** — Results are appended/updated in a Google Sheet for record-keeping.
4. **Conditional Notification** — Applicants classified as `Poor` are automatically emailed about their grant aid via Gmail.

## Workflow Diagram

```
Form Trigger → AI Agent (Gemini) → Google Sheets (Append/Update) → Google Sheets (Filter: status = Poor) → Gmail (Send Aid Notification)
```

## Tech Stack

- **n8n** — workflow orchestration
- **Google Gemini (PaLM API)** — LLM for classification
- **Google Sheets API** — data storage
- **Gmail API** — automated notifications

## Form Fields

| Field | Type | Required |
|---|---|---|
| Name | Text | Yes |
| City | Text | Yes |
| Email | Email | Yes |
| Salary | Number | Yes |
| Graduated | Dropdown (yes / no / in progress) | Yes |
| Number | Number | No |

## Classification Logic

The AI Agent receives the applicant's Name, Email, and Salary and returns structured JSON based on this rule:

- Salary **< 50000** → `Status: Poor`
- Salary **>= 50000** → `Status: Rich`

Output format:
```json
{
  "Name": "",
  "Gmail": "",
  "Status": ""
}
```

## Setup Instructions

### 1. Import the Workflow
- Download `AI_powered_classifier_for_Grants.json` from this repo.
- In n8n, go to **Workflows → Import from File** and select the JSON file.

### 2. Set Up Credentials

**Google Gemini (PaLM) API**
- Go to [Google AI Studio](https://aistudio.google.com/) and generate an API key.
- In n8n, create a new credential under **Google Gemini(PaLM) Api**.
- Paste your API key and save.
- Attach this credential to the **Google Gemini Chat Model** node.

**Google Sheets OAuth2**
- In n8n, create a new credential under **Google Sheets OAuth2 API**.
- Follow the OAuth flow to connect your Google account.
- Create a Google Sheet with columns: `Name`, `Gmail`, `status`.
- Update the **documentId** and **sheetName** in the "Append or update row in sheet" and "Get row(s) in sheet" nodes to point to your sheet.
- Attach the credential to both Google Sheets nodes.

**Gmail OAuth2**
- In n8n, create a new credential under **Gmail OAuth2 API**.
- Follow the OAuth flow to authorize your Gmail account.
- Attach this credential to the **Send a message** node.
- Customize the email subject/body in the **Send a message** node as needed.

### 3. Activate the Workflow
- Toggle the workflow to **Active** in n8n.
- Open the form trigger node to get your public form URL.
- Share the form URL with applicants.

## Customization Ideas

- Replace the fixed Rs. 25,000 aid amount with a dynamic value based on salary tier.
- Add more classification tiers (e.g., Low Income, Middle Income, High Income).
- Send a separate notification to `Rich` applicants explaining they don't qualify.
- Swap Google Gemini for another LLM provider (OpenAI, Groq, Claude) by replacing the Chat Model node.

## Notes

- This workflow currently uses Google Gemini for classification; Groq (`llama-3.1-8b-instant`) is a solid free-tier alternative if Gemini reliability becomes an issue.
- Ensure the Google Sheet's matching column (`Name`) is unique per applicant to avoid row overwrites.

## License

MIT
