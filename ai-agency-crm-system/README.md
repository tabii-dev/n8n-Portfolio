# AI-Powered Agency CRM & Lead Management System

A complete n8n CRM workflow that automates lead capture, qualification, assignment, follow-ups, proposal tracking, and deal closure for digital agencies.

## 🎯 Features

- **Dual Lead Sources**: Captures leads from Gmail and Google Sheets automatically
- **AI Lead Validation**: GPT-4 determines if emails are genuine leads
- **Smart Data Extraction**: AI extracts company name and service requirements
- **Round-Robin Assignment**: Assigns leads to account manager with lowest workload
- **Auto-Reply System**: Instant confirmation emails to leads
- **Daily Follow-Up Reminders**: 8 AM consolidated email with today's tasks
- **Proposal Workflow**: Automates client confirmation and finance alerts
- **Deal Won Process**: Creates projects and sends congratulations emails
- **Manager Load Balancing**: Tracks and distributes workload automatically

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Airtable account** with CRM base
3. **Google account** for Gmail and Sheets
4. **API Keys for**:
   - OpenAI API (GPT-4 access)

## 🗄️ Airtable Base Setup

### Required Tables

#### 1. Lead Capture Table

Fields:

Lead Order (Auto-number)

Name (Single line text)

Email (Email)

Phone number (Phone)

Service Needed (Single select)

Source (Single select)

Date Received (Date & time)

Follow-up Date (Date & time)

Status (Single select: New, Contacted, Won)

Account Manager (Linked record)

#### 2. Account Managers Table

Fields:

Account Manager (Single line text)

Email (Email)

Phone (Phone)

Active (Single select: Active, Inactive)

Current Load (Number)

#### 3. Lead Qualification Table

Fields:

Name, Phone, Email, Company

Service Interested (Single select)

Budget Range, Timeline, Status

Assigned To (Linked record)

Follow-up Date (Date & time)

Proposal Amount (Currency)

Proposal Workflow Triggered (Single select)

Proposal Won Triggered (Single select)

#### 4. Projects Table

Fields:

Project Name, Client, Service

Start Date, Duration, Budget

Status (Single select)

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `CRM-Workflow.json` file
4. Click **Import**

### 2. Configure Credentials

You'll need to set up the following credentials in n8n:

#### Gmail OAuth2

- Go to **Credentials** → **Add Credential** → **Gmail OAuth2**
- Authenticate and grant email permissions

#### Google Sheets Trigger OAuth2

- Add Google Sheets Trigger OAuth2 credential
- Authenticate your Google account

#### Airtable Personal Access Token

- Create token in Airtable Developer Hub
- Grant scopes: `data.records:read`, `data.records:write`

#### OpenAI

- Add OpenAI credential with your API key

### 3. Update Configuration

Replace the following in the workflow:

1. **Airtable Base ID**: Update `app1Bq7PpPmCrHRlI` with your base ID
2. **Google Sheet ID**: Update `1Jrxu61CbV09dtw52iPqQnfkLCZ4hbUf2PAKeflS7KhI` with yours
3. **Email addresses**: Update finance and PM email addresses
4. **Services list**: Update to match your agency offerings

## 📊 How It Works

### Workflow Stages

1. **Lead Capture** → Gmail/Sheets trigger receives lead
2. **AI Validation** → Confirms if email is a real lead (Gmail only)
3. **Data Extraction** → AI extracts company name and services needed
4. **Airtable Creation** → Creates lead record
5. **Assignment** → Finds manager with lowest load and assigns
6. **Notifications** → Sends auto-reply to lead + alert to manager
7. **Daily Follow-Ups** → 8 AM reminder email with today's tasks
8. **Proposal Tracking** → Monitors status changes and sends confirmations
9. **Deal Won** → Creates project record and onboards client

### Manager Assignment Logic

- Searches for managers with status = "Active"
- Sorts by "Current Load" (ascending)
- Assigns lead to manager with lowest load
- Increments manager's load by 1
- Marks manager as "Inactive" when at capacity

## 🧪 Testing

### Test Lead Capture via Email

Send an email to your Gmail:
Subject: Website Development Inquiry

Hi, I'm interested in web development services for my company, TechStart.
Can you send pricing?

Best,
John Doe
john@techstart.com

### Test Lead Capture via Google Sheets

Add a row:
| Name | Email | Service Interested | Source |
|------|-------|-------------------|--------|
| Jane Smith | jane@company.com | Social Media | Website Form |

### Test Proposal Workflow

1. Change lead status to "Proposal Sent" in Airtable
2. Wait 1 hour or manually trigger workflow
3. Verify emails sent to client and finance team

### Test Deal Won Workflow

1. Change lead status to "Won" in Airtable
2. Fill in Actual Deal Value and Duration
3. Verify project created and emails sent

## 🔧 Customization

### Adjust Manager Capacity

In **"Update Manager Status"** node, modify capacity threshold:

```javascript
"Active": $json['Current Load'] >= 10 ? "Inactive" : "Active"
Change Follow-Up Time
In "8 AM Daily Trigger" node, adjust trigger hour or frequency.

Modify Services
Update service list in "Validate Lead Email" and "Extract Lead Data" nodes to match your offerings.

📈 Monitoring
Track performance in Airtable:

Total leads captured by source

Manager workload distribution

Average assignment time

Proposal-to-won conversion rate

Monitor n8n execution logs for:

Workflow success rate

AI validation accuracy

Email delivery status

🐛 Troubleshooting
Issue: Leads not captured from Gmail

Solution: Check Gmail credentials and permissions

Issue: AI not extracting data correctly

Solution: Review and refine OpenAI prompts with examples

Issue: Manager assignment failing

Solution: Ensure Account Managers table has Active managers

Issue: Duplicate emails sent

Solution: Verify "Workflow Triggered" fields are updating correctly

Issue: Follow-ups not sent

Solution: Check Follow-up Date format matches Airtable date format

📝 License
MIT License - feel free to modify and use for your needs

🤝 Contributing
Contributions welcome! Please open an issue or PR.
```
