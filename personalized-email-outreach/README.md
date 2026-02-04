# Personalized Email Outreach & Follow-Up System

A sophisticated n8n workflow that sends personalized cold emails with AI-generated messaging, manages a 3-email sequence with 5-day intervals, detects replies with sentiment analysis, and respects business hours and GDPR compliance [file:1].

## 🎯 Features

- **Automated Daily Campaigns**: Sends 10 personalized emails daily at 9 AM CET, Monday-Friday only [file:1]
- **AI-Powered Personalization**: GPT-4.1-mini generates unique email copy for each lead based on industry and context [file:1]
- **3-Email Sequence**: Email 1 (initial) → Email 2 (5 days later) → Email 3 (10 days total) with automatic timing [file:1]
- **Business Hours Validation**: Only sends emails between 9 AM - 4 PM CET to respect recipient time zones [file:1]
- **Random Delays**: 5-10 minute delays between emails to avoid spam filters [file:1]
- **Reply Detection**: Gmail trigger monitors replies in real-time [file:1]
- **AI Sentiment Analysis**: Categorizes replies as POSITIVE, NEUTRAL, NEGATIVE, UNSUBSCRIBE, or OUT-OF-OFFICE [file:1]
- **Auto-Unsubscribe Handling**: Stops emailing leads who request removal [file:1]
- **GDPR-Compliant Footers**: Every email includes unsubscribe instructions [file:1]
- **Google Sheets Tracking**: Updates contacted status, sent dates, reply sentiment, and summaries [file:1]
- **Error Handling**: Monitors workflow errors and sends detailed notifications [file:1]

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Google account** with Gmail and Google Sheets access
3. **Google Sheet** from Phase 1 (Lead Research & Qualification)
4. **API Keys for**:
   - OpenAI API (GPT-4.1-mini)
   - Gmail OAuth2 credentials
   - Google Sheets OAuth2 credentials

## 🗄️ Database Setup

### Google Sheets Structure Required

This workflow **extends** the Google Sheet from Phase 1 by adding columns [file:1]:

| Additional Column | Description |
|------------------|-------------|
| Contacted | "Yes" or "No" |
| Email 1 Sent Date | Timestamp of first email |
| Email 2 Sent Date | Timestamp of follow-up 1 |
| Email 3 Sent Date | Timestamp of follow-up 2 |
| Reply Date | When they replied |
| Reply Sentiment | POSITIVE/NEUTRAL/NEGATIVE/UNSUBSCRIBE/OUTOFOFFICE |
| Reply Summary | AI-generated 1-sentence summary of reply |

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `workflow.json` file from this folder
4. Click **Import**

### 2. Configure Credentials

#### OpenAI API
- Replace `YOUROPENAICREDENTIALID` in all 3 AI nodes [file:1]:
  - Generate Personalized Email 1
  - Generate Personalized Email 2
  - Generate Personalized Email 3
  - Message a model (sentiment analysis)

#### Gmail OAuth2
- Set up Gmail OAuth2 in n8n credentials
- Replace `YOURGOOGLECREDENTIALID` in [file:1]:
  - Send Email 1
  - Send Email 2
  - Send Email 3
  - New Email Received (trigger)
  - All notification nodes

#### Google Sheets OAuth2
- Replace `YOURGOOGLECREDENTIALID` in all Google Sheets nodes [file:1]
- Update the `documentId` with your Google Sheet ID (from Phase 1)
- Ensure all sheet names match your setup

### 3. Update Email Settings

#### Change Sender Information

In all 3 **Send Email** nodes [file:1]:

```json
{
  "senderName": "Your Name",
  "replyTo": "your-email@example.com"
}
Customize Email Templates
Edit the AI prompt in Generate Personalized Email 1 node [file:1]:

text
You are a B2B cold email expert writing for [YOUR NAME], 
an [YOUR TITLE] at [YOUR COMPANY].

[YOUR POSITIONING STATEMENT]
Do the same for Email 2 and Email 3 nodes.

4. Set Your Daily Limits
Edit the Cap at 10 Daily node to change the daily email limit [file:1]:

javascript
maxItems: 20  // Change from 10 to 20
📊 How It Works
Email 1: Initial Outreach
Trigger: Daily at 9 AM CET, Monday-Friday [file:1]

Process:

Fetches all leads where Contacted = "No" [file:1]

Caps at 10 leads per day [file:1]

Randomizes order to look natural [file:1]

Generates personalized email using GPT-4.1-mini [file:1]

Adds GDPR unsubscribe footer [file:1]

Waits 5-10 minutes (random) [file:1]

Validates business hours (9 AM - 4 PM CET) [file:1]

Sends email via Gmail [file:1]

Updates Google Sheet: Contacted = "Yes", Email 1 Sent Date = [timestamp] [file:1]

Email Template [file:1]:

Positions you as a consultant who identifies problems, not a salesperson

Mentions specific operational bottlenecks for their industry

Offers a 15-minute problem-discovery call

Under 150 words

Email 2: Soft Follow-Up (5 Days Later)
Trigger: Daily at 10 AM CET [file:1]

Process:

Fetches all contacted leads [file:1]

Identifies leads where Email 1 was sent exactly 5 days ago [file:1]

Skips leads who already received Email 2, replied, or unsubscribed [file:1]

Generates personalized follow-up [file:1]

Sends with same validation and tracking as Email 1 [file:1]

Email Template [file:1]:

Brief check-in under 100 words

Acknowledges they may be busy

Offers easy opt-out

Provides one concrete example if interested

Email 3: Value-Add Follow-Up (10 Days Total)
Trigger: Same as Email 2 [file:1]

Process:

Identifies leads where Email 2 was sent exactly 5 days ago [file:1]

Same filtering logic (no reply, no unsubscribe) [file:1]

Final value-based email [file:1]

Email Template [file:1]:

Shares a specific, relevant automation example

Gives clear next steps

Respects their decision

Under 120 words

Reply Detection & Sentiment Analysis
Trigger: Real-time Gmail monitoring [file:1]

Process:

New Email Received trigger fires when Gmail gets a reply [file:1]

Extracts sender email, subject, full body [file:1]

Looks up sender in Google Sheet by email [file:1]

Sends lead data + reply to GPT-4.1-mini for sentiment analysis [file:1]

AI categorizes as: POSITIVE, NEUTRAL, NEGATIVE, UNSUBSCRIBE, or OUTOFOFFICE [file:1]

Updates Google Sheet with reply date, sentiment, and 1-sentence summary [file:1]

If POSITIVE: Sends notification email to you with full reply details [file:1]

If UNSUBSCRIBE: Flags lead to skip future emails [file:1]

🧪 Testing
Test Email 1 Campaign
In your Google Sheet, set 2-3 test leads to Contacted = "No"

Use your own email address in the Business email column

Run the Daily 9AM Campaign manually

Verify you receive the personalized email

Test Reply Detection
Reply to one of your test emails

The New Email Received trigger should fire within 1 minute [file:1]

Check Google Sheet for updated reply sentiment and summary

If sentiment is POSITIVE, you should receive a notification email

Test Follow-Up Sequence
Manually set Email 1 Sent Date to 5 days ago for a test lead

Run the Daily 10AM Follow-up Check manually

Verify Email 2 is sent

🔧 Customization
Change Email Intervals
Currently set to 5-day intervals [file:1]. To change:

Edit Check Who Needs Email 2 and Check Who Needs Email 3 code nodes:

javascript
if (daysDiff === 7) { // Changed from 5 to 7 days
  // send email
}
Modify Business Hours
Edit the three Validate Business Hours nodes [file:1]:

javascript
if (hour >= 8 && hour <= 17) { // Changed from 9-16 to 8-17
  // send email
}
Adjust Random Delay
Edit Random Delay 5-10 minutes nodes [file:1]:

javascript
Math.floor(Math.random() * 11) + 10 // Now 10-20 minutes
Change Daily Email Cap
Edit Cap at 10 Daily node [file:1]:

json
{
  "maxItems": 25
}
Customize Unsubscribe Footer
Edit the three Add Unsubscribe Footer nodes [file:1]:

javascript
const unsubscribeFooter = "\n\n---\n\nYour custom unsubscribe message here.";
📈 Monitoring
Campaign Performance
Check Daily Stats Logger and Email 3 Stats Logger output [file:1]:

Date of campaign

Number of emails sent

List of companies contacted

Reply Metrics
Review Log Reply Stats output [file:1]:

Company name

Contact name

Reply date

Sentiment category

Summary

Whether follow-up is needed

Error Tracking
The Error Handling & Monitoring section tracks [file:1]:

Failed nodes

Error messages

Affected leads

Error categories (QUOTA_EXCEEDED, AUTH_FAILED, NETWORK_ERROR, DATA_PARSING)

Severity levels (HIGH, MEDIUM)

🐛 Troubleshooting
Issue: Emails not sending

Solution: Check Gmail OAuth2 credentials and daily sending limits (500/day for free Gmail) [file:1]

Issue: AI failing to generate emails

Solution: Verify OpenAI API key, check rate limits, ensure GPT-4.1-mini access [file:1]

Issue: Reply detection not working

Solution: Ensure Gmail trigger is active and OAuth2 has gmail.readonly scope [file:1]

Issue: Follow-ups sending too early/late

Solution: Check Check Who Needs Email 2/3 nodes for date calculation logic; verify timestamps in Google Sheet [file:1]

Issue: Business hours validation not working

Solution: Verify timezone in Validate Business Hours nodes matches your target region (currently Europe/Amsterdam) [file:1]

Issue: Unsubscribe requests not being honored

Solution: Check that Check Who Needs Email 2/3 nodes correctly filter out Reply Sentiment = "UNSUBSCRIBE" [file:1]

🔒 GDPR Compliance
This workflow includes [file:1]:

Unsubscribe footer on every email

Auto-removal when leads request unsubscribe

Sentiment detection for unsubscribe requests

Opt-out language in all follow-ups

📝 License
MIT License - feel free to modify and use for your needs

🤝 Contributing
Contributions welcome! Please open an issue or PR.