# AI-Powered Lead Research & Qualification System

An automated n8n workflow that sources decision-makers from Apollo.io, enriches company data, validates leads using strict criteria, and qualifies them with GPT-4 scoring [file:2].

## 🎯 Features

- **Apollo.io Integration**: Searches for European SMB decision-makers (CEOs, Founders, Directors) with verified emails [file:2]
- **Automated Lead Enrichment**: Fetches detailed person and company data via Apollo API [file:2]
- **Strict Data Validation**: Filters leads by employee count (5-50), validates company names, and removes incomplete records [file:2]
- **AI-Powered Qualification**: Uses GPT-4o-mini to score leads as High/Medium/Low fit based on automation potential [file:2]
- **Google Sheets Integration**: Automatically appends qualified leads with deduplication via email matching [file:2]
- **Error Logging**: Tracks failed enrichments in a separate sheet with timestamps and error details [file:2]
- **Pagination Support**: Processes leads in batches with page counter stored in Google Sheets [file:2]
- **Scheduled Execution**: Optional weekly schedule trigger (disabled by default) [file:2]

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Apollo.io account** with API access
3. **Google account** with Google Sheets access
4. **API Keys for**:
   - Apollo.io API key
   - OpenAI API (GPT-4o-mini)
   - Google OAuth2 credentials for Sheets

## 🗄️ Database Setup

### Google Sheets Structure Required

Create a Google Sheet with **3 sheets**:

#### 1. **Leads Sheet** (main output)

| Column Name | Description |
|------------|-------------|
| Company name | Name of the company |
| Company website | Website URL |
| Industry niche | Industry/sector |
| Number of employees | Employee count |
| Country | Country location |
| Contact person full name | Decision maker's name |
| Job title | Their role |
| Business email (verified) | Verified email address |
| LinkedIn profile URL (personal) | Personal LinkedIn URL |
| LinkedIn company page URL | Company LinkedIn page |
| Qualification score | High/Medium/Low |
| Score reasoning | AI explanation of score |

#### 2. **Workflow Config Sheet**

| Setting | Value |
|---------|-------|
| currentpage | 1 |
| lastrundate | [auto-updated] |

This tracks pagination state across runs [file:2].

#### 3. **Error Log Sheet**

| Column Name | Description |
|------------|-------------|
| Timestamp | When error occurred |
| Error Type | Error category |
| Lead Name | Name of affected lead |
| Apollo ID | Apollo record ID |
| Company Name | Company name |
| Error Details | Description |
| Raw Data | Full JSON payload |

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `workflow.json` file from this folder
4. Click **Import**

### 2. Configure Credentials

#### Apollo.io API
- Open the **Apollo - Search Decision Makers** node
- Replace `YOURAPOLLOAPIKEY` with your actual Apollo API key [file:2]
- Do the same in the **Apollo - Enrich Person Details** node

#### OpenAI API
- Go to **Credentials** in n8n
- Create/select OpenAI credential
- Replace `YOUROPENAICREDENTIALID` in the **AI - Generate Short Note & Score** node [file:2]

#### Google Sheets OAuth2
- Set up Google OAuth2 in n8n credentials
- Replace `YOURGOOGLECREDENTIALID` in all Google Sheets nodes [file:2]
- Replace `YOURGOOGLESHEETID` with your actual Google Sheet ID [file:2]
- Update the `documentId` field with your sheet's ID

### 3. Customize Your ICP (Ideal Customer Profile)

Edit the **Apollo - Search Decision Makers** node to match your target criteria [file:2]:

```json
{
  "person_titles": ["CEO", "Founder", "Owner", "Managing Director"],
  "organization_num_employees_ranges": ["5,10", "11,20", "21,50"],
  "organization_locations": ["Europe"],
  "contact_email_status": "verified"
}
4. Adjust AI Qualification Prompt
Edit the AI - Generate Short Note & Score node to customize scoring logic [file:2]:

Modify industry preferences

Adjust scoring thresholds

Change qualification criteria

📊 How It Works
Workflow Stages
Trigger → Manual or scheduled execution [file:2]

Get Page Number → Fetches current pagination state from Google Sheets [file:2]

Apollo Search → Searches for decision-makers matching ICP criteria [file:2]

Split Array → Splits Apollo results for individual processing [file:2]

Enrich Details → Fetches full contact and company data [file:2]

Validation Check → Filters out empty/invalid person objects [file:2]

Extract Fields → Normalizes data into 12 required fields [file:2]

Filter Invalid → Removes leads outside 5-50 employee range or with domain-as-name issues [file:2]

AI Qualification → GPT-4 scores each lead and generates a personalized short note [file:2]

Merge Data → Combines enriched data with AI scores [file:2]

Parse Output → Extracts structured JSON from AI response [file:2]

Append to Sheet → Writes qualified leads to Google Sheets with deduplication [file:2]

Update Counter → Increments page number and updates last run date [file:2]

Lead Qualification Tiers
High: Perfect fit for automation/AI services, strong buying signals [file:2]

Medium: Good fit but may need nurturing [file:2]

Low: Partial fit or missing key criteria [file:2]

Data Validation Rules
The workflow enforces strict validation [file:2]:

Employee count: Must be 5-50 (SMB sweet spot)

Company name: Cannot be a domain/URL (filters out bad Apollo data)

Required fields: All 10 core fields must have valid values

Email verification: Only verified emails pass through

🧪 Testing
Manual Test
Click the When clicking Execute workflow node [file:2]

Click Execute Node

Watch the workflow process 100 leads from Apollo

Check your Google Sheet for results

Expected Output
For each qualified lead, you'll see [file:2]:

All 12 contact/company fields populated

AI-generated short note (60 words max)

Qualification score (High/Medium/Low)

Reasoning for the score

🔧 Customization
Change Target Geography
Edit the Apollo - Search Decision Makers node [file:2]:

json
"organization_locations": ["Germany", "France", "Netherlands"]
Adjust Employee Range
Modify both the Apollo search AND the Filter Invalid Lead node [file:2]:

javascript
// In Filter Invalid Lead code node
if (emp < 10 || emp > 100) { // Now targeting 10-100 employees
  // reject lead
}
Modify AI Scoring Criteria
Update the system prompt in AI - Generate Short Note & Score [file:2]:

text
You are a B2B lead qualification expert specializing in [YOUR NICHE].
Consider:
- [Your custom criteria 1]
- [Your custom criteria 2]
📈 Monitoring
Check Lead Quality
Review the Qualification score column in your Google Sheet to track:

Percentage of High vs Medium vs Low leads

Common industries being sourced

Geographic distribution

Error Tracking
Check the Error Log sheet for [file:2]:

Leads that failed enrichment

Apollo API errors

Malformed data issues

Performance Metrics
Monitor in the workflow execution history:

Total leads processed per run

Valid leads after filtering

Qualification distribution

🐛 Troubleshooting
Issue: No leads being returned

Solution: Check Apollo API quota and search criteria; may be too restrictive [file:2]

Issue: AI qualification failing

Solution: Verify OpenAI API key and check rate limits; ensure GPT-4o-mini access [file:2]

Issue: Leads marked as invalid

Solution: Review Filter Invalid Lead node console logs to see rejection reasons [file:2]

Issue: Duplicates still appearing

Solution: Ensure "Business email" is set as the matching column in Append or update row node [file:2]

Issue: Page counter not incrementing

Solution: Check that "Workflow Config" sheet name matches exactly in Get Current Page Number node [file:2]

📝 License
MIT License - feel free to modify and use for your needs

🤝 Contributing
Contributions welcome! Please open an issue or PR.