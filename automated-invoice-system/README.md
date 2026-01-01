# Automated Invoice Generation & Payment Tracking System

A complete n8n workflow that automates invoice creation, PDF generation, client notifications, QuickBooks sync, and payment tracking for freelancers and consulting businesses.

## 🎯 Features

- **Webhook API**: REST endpoint to generate invoices programmatically
- **Input Validation**: Ensures required fields and correct data types
- **Client Data Fetching**: Retrieves client information from Supabase
- **Automatic Calculations**: Computes subtotal, tax, and total amounts
- **Smart Invoice Numbering**: Generates unique invoice IDs (INV-2026-XXXXXX format)
- **PDF Generation**: Creates professional PDFs using PDFMonkey
- **Email Automation**: Sends invoices to clients via Mailjet
- **QuickBooks Integration**: Syncs invoices to QuickBooks Online
- **Dashboard Tracking**: Updates Google Sheets for real-time reporting
- **Payment Tracking**: Creates records for payment reminders
- **Audit Logging**: Tracks all invoice creation events

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Supabase account** for database
3. **API Keys for**:
   - PDFMonkey (PDF generation)
   - Mailjet (email sending)
   - QuickBooks Online OAuth2
4. **Google Sheets** for dashboard

## 🗄️ Supabase Database Setup

### Required Tables

#### 1. `clients` table

```sql
CREATE TABLE clients (
  id SERIAL PRIMARY KEY,
  client_name TEXT NOT NULL,
  contact_email TEXT NOT NULL,
  billing_email TEXT NOT NULL,
  phone TEXT,
  address TEXT,
  city TEXT,
  state TEXT,
  zip_code TEXT,
  payment_terms_days INTEGER DEFAULT 30,
  created_at TIMESTAMP DEFAULT NOW()
);
invoices table
CREATE TABLE invoices (
  id SERIAL PRIMARY KEY,
  invoice_number TEXT UNIQUE NOT NULL,
  client_id INTEGER REFERENCES clients(id),
  project_id TEXT,
  service_description TEXT NOT NULL,
  invoice_date DATE NOT NULL,
  due_date DATE NOT NULL,
  subtotal DECIMAL(10,2) NOT NULL,
  tax_rate DECIMAL(4,2) DEFAULT 0.00,
  tax_amount DECIMAL(10,2) DEFAULT 0.00,
  total_amount DECIMAL(10,2) NOT NULL,
  status TEXT DEFAULT 'SENT',
  sent_date TIMESTAMP,
  qb_invoice_id TEXT,
  created_by TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
payment_tracking table
CREATE TABLE payment_tracking (
  id SERIAL PRIMARY KEY,
  invoice_id INTEGER REFERENCES invoices(id),
  status TEXT DEFAULT 'SENT',
  amount_due DECIMAL(10,2),
  amount_paid DECIMAL(10,2) DEFAULT 0.00,
  balance_due DECIMAL(10,2),
  next_reminder_date DATE,
  created_at TIMESTAMP DEFAULT NOW()
);
audit_log table
CREATE TABLE audit_log (
  id SERIAL PRIMARY KEY,
  event_type TEXT NOT NULL,
  invoice_id INTEGER,
  client_id INTEGER,
  user_id TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
🚀 Installation
1. Import the Workflow
Open your n8n instance

Click on Workflows → Import from File

Select the Invoice-Generation-Workflow.json file

Click Import

2. Configure Credentials
You'll need to set up the following credentials in n8n:

Supabase
Go to Credentials → Add Credential → Supabase API

Enter your Supabase URL and service role key

PDFMonkey
Create account at pdfmonkey.io

Create invoice template

Add PDFMonkey API credential with your API key

Update documentTemplateId in workflow

Mailjet
Create account at mailjet.com

Add Mailjet Email API credential

Update fromEmail to your verified sender email

QuickBooks Online
Go to Credentials → Add Credential → QuickBooks OAuth2

Follow OAuth setup process

Connect your QuickBooks Online account

Google Sheets
Add Google Sheets OAuth2 credential

Authenticate your Google account

3. Update Configuration
Replace the following in the workflow:

PDFMonkey Template ID: Update 6292FF59-8AFE-4099-839B-A6D0CBC493DD with your template ID

Google Sheet ID: Update 1XzKxLOfWbJYc1JZi59STPJysRXN-vY2DxG3gHpJXiJs with yours

Sender Email: Update billing@debasantaconsulting.com in Mailjet node

Company Name: Replace "Debasanta Consulting LLC" throughout the workflow

📊 How It Works
Workflow Stages
API Request → Webhook receives invoice data

Validation → Checks required fields and data types

Client Lookup → Fetches client info from Supabase

Calculations → Computes subtotal, tax, total, and due date

Invoice Number → Generates unique invoice ID

Database Record → Creates invoice in Supabase

PDF Creation → Generates professional PDF via PDFMonkey

Email Sending → Sends invoice to client via Mailjet

QuickBooks Sync → Creates invoice in QuickBooks Online

Dashboard Update → Logs invoice in Google Sheets

Payment Tracking → Creates payment tracking record

Audit Log → Records invoice creation event

Invoice Number Format
Generated as: INV-YYYY-XXXXXX

YYYY: Current year

XXXXXX: Random 6-digit number

🧪 Testing
Test via Webhook
Send POST request to your webhook URL:
curl -X POST https://your-n8n-instance.com/webhook/invoice-generate \
  -H "Content-Type: application/json" \
  -d '{
    "client_id": 1,
    "project_id": "PROJ-2026-001",
    "service_description": "Web Development - 40 hours",
    "hours": 40,
    "rate": 100,
    "invoice_date": "2026-01-01",
    "payment_terms_days": 30,
    "tax_rate": 0.075
  }'
Required Fields
client_id (number): Client ID from database

service_description (string): Description of services

hours (number): Hours worked

rate (number): Hourly rate

Optional Fields
project_id (string): Defaults to PROJ-{timestamp}

invoice_date (string): Defaults to today

payment_terms_days (number): Defaults to 30

tax_rate (decimal): Defaults to 0.00

🔧 Customization
Adjust Tax Rate
In "Validate Input" node, change default:
tax_rate: input.tax_rate || 0.075  // 7.5% tax
Modify Payment Terms
Change default payment terms:
payment_terms_days: input.payment_terms_days || 45
Customize Email Template
In "Send an email" node, modify the email body to match your brand voice and payment methods.

Change Reminder Schedule
In "next_reminder_date calculation" node:
reminderDate.setDate(reminderDate.getDate() - 7); // 7 days before due
Required Fields
client_id (number): Client ID from database

service_description (string): Description of services

hours (number): Hours worked

rate (number): Hourly rate

Optional Fields
project_id (string): Defaults to PROJ-{timestamp}

invoice_date (string): Defaults to today

payment_terms_days (number): Defaults to 30

tax_rate (decimal): Defaults to 0.00

🔧 Customization
Adjust Tax Rate
In "Validate Input" node, change default:

javascript
tax_rate: input.tax_rate || 0.075  // 7.5% tax
Modify Payment Terms
Change default payment terms:

javascript
payment_terms_days: input.payment_terms_days || 45
Customize Email Template
In "Send an email" node, modify the email body to match your brand voice and payment methods.

Change Reminder Schedule
In "next_reminder_date calculation" node:

javascript
reminderDate.setDate(reminderDate.getDate() - 7); // 7 days before due
📈 Monitoring
Track invoice performance:

Supabase Dashboard: View all invoices and payment status

Google Sheets: Real-time invoice tracking

QuickBooks: Financial reporting and accounting

Audit Log: Complete activity history

Monitor n8n execution logs for:

API request success rate

PDF generation errors

Email delivery status

QuickBooks sync issues

🐛 Troubleshooting
Issue: Webhook not responding

Solution: Check webhook path is /invoice-generate and method is POST

Issue: Client not found

Solution: Verify client_id exists in Supabase clients table

Issue: PDF generation failing

Solution: Check PDFMonkey template ID and API credentials

Issue: Email not sending

Solution: Verify Mailjet credentials and sender email is verified

Issue: QuickBooks sync error

Solution: Reauthorize QuickBooks OAuth2 credentials

Issue: Invalid date format

Solution: Use YYYY-MM-DD format for invoice_date

💡 Use Cases
This workflow is perfect for:

Freelancers: Automated client billing

Consulting Firms: Project-based invoicing

Agencies: Multi-client invoice management

Nigerian Businesses: Naira-based invoicing (update currency)

Service Providers: Hourly billing automation

📝 License
MIT License - feel free to modify and use for your needs

🤝 Contributing
Contributions welcome! Please open an issue or PR.
```
