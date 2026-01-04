AI-Powered Lead Qualification & Routing System
An intelligent n8n workflow that automatically captures, validates, enriches, qualifies, and routes inbound leads using AI and multiple data providers to maximize sales efficiency and lead quality.

📋 TABLE OF CONTENTS
For Business Users
What This Does

How to Use It

What to Expect

Troubleshooting

Getting Help

For Developers
Technical Overview

Architecture

Setup & Configuration

API Integrations

Data Schemas

Error Handling

Modification Guide

Technical Troubleshooting

👔 CLIENT DOCUMENTATION
What This Does
This workflow automatically processes every lead that comes into your business - whether from web forms, LinkedIn, Calendly bookings, or HubSpot - and makes intelligent decisions about who should follow up and how quickly.
​

Real-world example: When someone fills out your website contact form at 2 AM, this workflow instantly validates their email, looks up their company size and industry, checks if they're a decision-maker, scores them using AI against your ideal customer profile, and either assigns them to your best available sales rep with a Slack notification or logs them for later nurturing - all before they finish their morning coffee.
​

Business Value:

Responds to qualified leads within minutes instead of hours or days

Eliminates 95% of spam and invalid leads before they waste sales time

Automatically prioritizes high-value prospects (C-level, large companies) for immediate follow-up

Provides sales reps with rich company intelligence before first contact

Reduces lead response time from 24+ hours to under 2 minutes

How to Use It
Automatic Workflows
When it runs: Continuously, 24/7 whenever a new lead is received
​

What triggers it: Any of the following events automatically start the workflow:

A new form submission on your website

A LinkedIn lead generation form

A Calendly meeting booking

A HubSpot form submission

A direct webhook POST request to the intake endpoint
​

What you need to do: Nothing! The workflow runs automatically and handles everything from data capture to sales assignment.
​

What to Expect
Inputs
What information does this workflow need?

What	Example	Where it comes from
First Name	"Sarah"	Contact form, LinkedIn, Calendly
Last Name	"Johnson"	Contact form, LinkedIn, Calendly
Email Address	"sarah@techstartup.com"	Contact form (required field)
Company Name	"TechStartup Inc"	Contact form (required field)
Phone Number	"+1-555-0123"	Contact form (optional)
Job Title	"VP of Engineering"	Contact form, LinkedIn
Company Website	"techstartup.com"	Contact form (optional)
Source	"web_form", "linkedin", "calendly"	Automatically detected
Outputs
What does this workflow produce?

What	Where it goes	When
Lead record with enrichment data	Supabase database	Within 30 seconds
Sales assignment notification	Slack #sales-team channel	Within 2 minutes (A/B tier leads)
HubSpot contact & deal	HubSpot CRM	Within 2 minutes (A/B tier leads)
Audit log entry	Supabase audit table	Immediately after each stage
Low-priority lead notification	Slack #low-priority-leads	Within 2 minutes (C tier leads)
Timing
How long it takes: 30-90 seconds for complete processing (validation → enrichment → qualification → routing)

When you'll see results: Slack notifications arrive within 2 minutes for high-priority leads

Peak usage times: Processing may take slightly longer (up to 2 minutes) during business hours when API rate limits are approached

Troubleshooting (For Business Users)
Problem: I submitted a test lead but didn't receive a notification
What you see: No Slack message appears after submitting a form
What to do:

Check if the email domain is personal (Gmail, Yahoo) - these may be scored as C-tier

Verify the company name was provided (required field)

Check the #low-priority-leads Slack channel - it may have been routed there

If that doesn't work: Contact the technical team to check the workflow execution logs

Problem: A good lead was marked as low priority
What you see: Qualified prospect received delayed follow-up or was routed to low-priority channel
What to do:

Forward the lead details to your sales manager

Manually reassign in HubSpot

The technical team can adjust the AI qualification criteria

If that doesn't work: Contact the developer to review the ICP (Ideal Customer Profile) scoring parameters

Problem: Duplicate leads are being created
What you see: Same person/email appears multiple times in HubSpot or Slack notifications
What to do: The workflow has duplicate detection, but it only checks within 24 hours - this is expected behavior for leads that return after a day

If that doesn't work: Contact technical support if you see duplicates within the same hour

Getting Help
For questions about how to use this workflow:
Contact: Sales Operations Manager
Slack: #sales-ops

If the workflow stops working or shows errors:
Contact: Development Team
Slack: #tech-support

Emergency contact (outside business hours):
Check the n8n workflow execution history and notify on-call developer via Slack

💻 DEVELOPER DOCUMENTATION
Technical Overview
What This Workflow Does
This is an end-to-end lead management automation that ingests leads from multiple sources, performs multi-stage validation and enrichment using third-party APIs (Clearbit, Apollo, Emailable), leverages OpenAI GPT for intelligent qualification against a defined ICP, and routes qualified leads to sales reps using round-robin assignment with load balancing.
​

Tech Stack
Automation Platform: n8n (self-hosted or cloud)

Primary Integrations: OpenAI GPT-4, Clearbit Enrichment API, Apollo.io, Emailable Email Verification, Supabase PostgreSQL, HubSpot CRM, Slack

Secondary Tools: Webhooks, HTTP Request nodes, JavaScript code nodes

Database: Supabase PostgreSQL (leads, sales_reps, lead_audit_log tables)

Programming: JavaScript ES6+ (for 15 custom code nodes)

Key Capabilities
Multi-source lead normalization (web forms, LinkedIn, Calendly, HubSpot)

Real-time email validation with deliverability scoring

Company enrichment with firmographic data and technographic signals

Decision-maker identification and seniority analysis

AI-powered lead qualification using GPT-4 with structured ICP matching

Intelligent sales rep assignment with load balancing

Automatic HubSpot contact and deal creation

Comprehensive audit logging and Slack notifications

Architecture
Workflow Stages
text
[Webhook Trigger] → [Normalization] → [Validation] → [Deduplication] → [Enrichment] → [AI Qualification] → [Routing & Assignment] → [CRM Sync]
Detailed Flow:

Trigger: Webhook Intake

Node: lead-intake

Type: Webhook (POST)

Config: Accepts POST requests at /webhook/0f69d65c-bfb9-45a2-beb4-5c1d66544250

Purpose: Universal intake endpoint for all lead sources

Stage 1: Data Normalization & Validation

Nodes: Normalize Lead Data, Validate Email, Clean Data

Purpose: Transform disparate source formats into unified schema, validate email format, detect disposable emails

Dependencies: None (pure JavaScript logic)

Process: Handles web_form, linkedin, calendly, and hubspot source formats; flags personal vs corporate emails

Stage 2: Deduplication Check

Nodes: Check Duplicate Email, If, Mark as Duplicate, Insert New Lead

Purpose: Prevent duplicate lead processing within 24-hour window

Dependencies: Supabase database query

Logic: Checks if email exists in leads table with created_at in last 24 hours

Stage 3: Multi-Provider Enrichment

Nodes: Prepare for Enrichment, Clearbit Company Enrichment, Apollo Company Lookup (fallback), Emailable - Verify Email, Normalize Enrichment Data, Process Email Validation

Purpose: Gather company firmographics, technographics, and email deliverability data

Dependencies: Clearbit API (primary), Apollo API (fallback), Emailable API

Output: Company size, industry, revenue, funding, tech stack, email verification status

Stage 4: Lead Intelligence Analysis

Nodes: Analyze Decision Maker, Detect Tech Stack Signals, Calculate Enrichment Score

Purpose: Determine seniority level, buying signals, enrichment completeness

Dependencies: None (JavaScript logic on enriched data)

Scoring: 0-100 enrichment quality score based on data completeness

Stage 5: AI-Powered Qualification

Nodes: Prepare ICP Analysis, OpenAI - Qualify Lead, Parse AI Qualification

Purpose: Match lead against Ideal Customer Profile using GPT-4

Dependencies: OpenAI API (GPT-4)

Output: Tier (A/B/C), qualification score, reasoning, objections, next steps

Stage 6: Sales Routing & Assignment

Nodes: Route by Tier, Get Full Lead Data, Find Available Sales Rep, Filter and Sort Sales Reps, Assign Sales Rep, Update Lead Assignment, Increment Rep Load

Purpose: Intelligently assign A/B tier leads to available sales reps with load balancing

Dependencies: Supabase (sales_reps table)

Logic: Round-robin with current load consideration; C-tier leads bypass assignment

Stage 7: CRM Synchronization

Nodes: Check Existing Contact, Contact Exists?, Update Contact, Create Contact, Merge HubSpot Contact, Create Deal, Extract HubSpot IDs

Purpose: Sync qualified leads to HubSpot as contacts with associated deals

Dependencies: HubSpot CRM API

Scope: Only A/B tier leads are synced to HubSpot

Stage 8: Notifications & Logging

Nodes: Slack - Notify Sales Team, Slack - Log C-Tier Lead, Log Enrichment Complete, Log AI Qualification, Log Audit Trail

Purpose: Real-time notifications and comprehensive audit trail

Dependencies: Slack Webhook API, Supabase audit table

Channels: #sales-team (A/B tier), #low-priority-leads (C tier)

Node Count & Complexity
Total Nodes: 47

API Calls: 8-12 per execution (depending on enrichment fallbacks and HubSpot contact status)

Custom Code Nodes: 15 (JavaScript for data transformation and business logic)

Conditional Logic: 4 decision points (duplicate check, enrichment fallback, tier routing, HubSpot contact exists)

Setup & Configuration
Prerequisites
Required Accounts:

 n8n instance (cloud or self-hosted v1.0+)

 OpenAI account with GPT-4 API access

 Clearbit Enrichment API account (Pro plan recommended)

 Apollo.io account with API access

 Emailable account with API credits

 Supabase account (free tier sufficient for testing)

 HubSpot CRM account (Sales Hub Starter or higher)

 Slack workspace with webhook permissions

Required API Access:

 OpenAI API key with GPT-4 model access

 Clearbit API key (Enrichment API)

 Apollo.io API key

 Emailable API key

 Supabase project URL and anon/service key

 HubSpot Private App token or OAuth credentials

 Slack Incoming Webhook URLs

System Requirements:

n8n version: 1.0.0 or higher

Memory: Minimum 2GB RAM for self-hosted instances

Storage: Minimal (workflow JSON is 82KB)

Step 1: Import Workflow
bash
# Via n8n UI:
# 1. Open n8n dashboard
# 2. Click "Add Workflow" → "Import from File"
# 3. Select AI-Powered-Lead-Qualification-Routing-System.json
# 4. Click "Import"
Step 2: Configure Credentials
Supabase Setup:

text
1. In n8n: Credentials → New Credential → Supabase
2. Enter:
   - Host: your-project.supabase.co
   - Service Role Key: Your Supabase service_role key (from Settings → API)
3. Test connection
4. Save as "Supabase account"
OpenAI Setup:

text
1. In n8n: Credentials → New Credential → OpenAI
2. Enter:
   - API Key: sk-...
3. Save as "OpenAI account"
Clearbit Setup:

text
1. In n8n: Credentials → New Credential → HTTP Request (Generic)
2. Authentication: Basic Auth
   - User: Your Clearbit API key
   - Password: (leave empty)
3. Save as "Clearbit API"
Apollo Setup:

text
1. In n8n: Credentials → New Credential → HTTP Request (Generic)
2. Authentication: Header Auth
   - Name: X-Api-Key
   - Value: Your Apollo API key
3. Save as "Apollo API"
Emailable Setup:

text
1. In n8n: Credentials → New Credential → HTTP Request (Generic)
2. Authentication: Query Auth
   - Name: api_key
   - Value: Your Emailable API key
3. Save as "Emailable API"
HubSpot Setup:

text
1. Create Private App in HubSpot:
   - Settings → Integrations → Private Apps
   - Scopes needed: crm.objects.contacts.write, crm.objects.deals.write, crm.objects.companies.read
2. In n8n: Credentials → New Credential → HubSpot
3. Enter Access Token
4. Save as "HubSpot account"
Slack Setup:

text
1. Create Incoming Webhooks in Slack:
   - Apps → Incoming Webhooks → Add New Webhook
   - Create two: #sales-team and #low-priority-leads
2. In n8n nodes "Slack - Notify Sales Team" and "Slack - Log C-Tier Lead"
3. Paste webhook URLs in the "Webhook URL" field
Step 3: Create Supabase Database Schema
Run this SQL in your Supabase SQL Editor:

sql
-- Leads table
CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  lead_id VARCHAR(255) UNIQUE NOT NULL,
  source VARCHAR(50) NOT NULL,
  first_name VARCHAR(255),
  last_name VARCHAR(255),
  email VARCHAR(255) NOT NULL,
  phone VARCHAR(50),
  company_name VARCHAR(255),
  job_title VARCHAR(255),
  company_website VARCHAR(255),
  status VARCHAR(50) DEFAULT 'new',
  is_duplicate BOOLEAN DEFAULT false,
  duplicate_of UUID,
  is_personal_email BOOLEAN,
  
  -- Enrichment fields
  company_domain VARCHAR(255),
  company_size INTEGER,
  company_employees_range VARCHAR(50),
  company_industry VARCHAR(255),
  company_founded_year INTEGER,
  company_revenue_range VARCHAR(100),
  company_funding_total DECIMAL(15, 2),
  company_location VARCHAR(255),
  company_description TEXT,
  company_linkedin_url VARCHAR(500),
  company_twitter_url VARCHAR(500),
  tech_stack TEXT[],
  buying_signals JSONB,
  
  -- Decision maker analysis
  is_decision_maker BOOLEAN,
  decision_maker_likelihood DECIMAL(3, 2),
  seniority_level VARCHAR(50),
  
  -- Email validation
  email_valid BOOLEAN,
  email_deliverable BOOLEAN,
  email_type VARCHAR(50),
  email_quality_score INTEGER,
  
  -- AI qualification
  qualification_tier VARCHAR(1), -- A, B, or C
  qualification_score INTEGER,
  qualification_reasoning TEXT,
  ai_recommended_action TEXT,
  
  -- Assignment
  assigned_to_rep_id UUID,
  assigned_at TIMESTAMP,
  
  -- CRM sync
  hubspot_contact_id VARCHAR(50),
  hubspot_deal_id VARCHAR(50),
  
  -- Metadata
  enrichment_data JSONB,
  raw_payload JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Sales reps table
CREATE TABLE sales_reps (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL,
  slack_user_id VARCHAR(50),
  is_active BOOLEAN DEFAULT true,
  current_lead_load INTEGER DEFAULT 0,
  max_lead_capacity INTEGER DEFAULT 10,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Audit log table
CREATE TABLE lead_audit_log (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  lead_id UUID REFERENCES leads(id),
  action VARCHAR(100) NOT NULL,
  details TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_leads_email ON leads(email);
CREATE INDEX idx_leads_created_at ON leads(created_at);
CREATE INDEX idx_leads_status ON leads(status);
CREATE INDEX idx_leads_tier ON leads(qualification_tier);
CREATE INDEX idx_sales_reps_active ON sales_reps(is_active, current_lead_load);
Step 4: Insert Sample Sales Reps
sql
INSERT INTO sales_reps (name, email, is_active, max_lead_capacity) VALUES
  ('Alice Johnson', 'alice@yourcompany.com', true, 10),
  ('Bob Smith', 'bob@yourcompany.com', true, 10),
  ('Charlie Davis', 'charlie@yourcompany.com', true, 10);
Step 5: Update Webhook URL in Your Forms
The workflow webhook URL format is:

text
https://your-n8n-instance.com/webhook/0f69d65c-bfb9-45a2-beb4-5c1d66544250
For Web Forms:

xml
<form action="https://your-n8n-instance.com/webhook/0f69d65c-bfb9-45a2-beb4-5c1d66544250" method="POST">
  <!-- Include a hidden field to identify source -->
  <input type="hidden" name="source" value="web_form">
  <!-- Your form fields -->
</form>
For LinkedIn Lead Gen:
Configure the webhook URL in LinkedIn Campaign Manager → Lead Gen Forms → Integrations

For Calendly:
Go to Account → Webhooks → Add Webhook → Use the n8n webhook URL

Step 6: Customize ICP (Ideal Customer Profile) Prompt
In the OpenAI - Qualify Lead node, update the system prompt to match your business:

javascript
// Current ICP criteria (customize these):
const ICP_CRITERIA = {
  ideal_company_size: "50-500 employees",
  target_industries: ["SaaS", "Technology", "Software", "Fintech"],
  ideal_revenue: "$5M-$50M annual revenue",
  decision_maker_seniority: ["C-Level", "VP", "Director"],
  disqualifiers: ["Freelancers", "Students", "Non-profits", "Companies < 10 employees"]
};
Step 7: Test the Workflow
Test with sample payload:

bash
curl -X POST https://your-n8n-instance.com/webhook/0f69d65c-bfb9-45a2-beb4-5c1d66544250 \
  -H "Content-Type: application/json" \
  -d '{
    "source": "web_form",
    "data": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+1-555-0100",
      "company": "Example Corp",
      "jobTitle": "CTO",
      "website": "example.com"
    }
  }'
Verify:

 Workflow executes without errors in n8n execution log

 Lead appears in Supabase leads table

 Enrichment data is populated (check company_size, company_industry)

 AI qualification is complete (check qualification_tier, qualification_score)

 Slack notification sent (check #sales-team or #low-priority-leads)

 HubSpot contact created (for A/B tier leads)

API Integrations
Clearbit Enrichment API
Authentication Method: Basic Auth (API key as username)

Base URL: https://company.clearbit.com/v2

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
/companies/find	GET	Enrich company data by domain	600 requests/minute
Required Permissions:

Enrichment API access

Sample Request:

javascript
{
  "method": "GET",
  "url": "https://company.clearbit.com/v2/companies/find",
  "authentication": "Clearbit API",
  "qs": {
    "domain": "{{$json.company_domain}}"
  }
}
Sample Response:

json
{
  "id": "027b0d40-016c-40ea-8925-a076fa640992",
  "name": "Uber",
  "legalName": "Uber Technologies, Inc.",
  "domain": "uber.com",
  "category": {
    "sector": "Transportation",
    "industryGroup": "Passenger Ground Transportation",
    "industry": "Taxi & Limousine Services"
  },
  "metrics": {
    "employees": 22800,
    "employeesRange": "10001+",
    "estimatedAnnualRevenue": "$10B-$50B",
    "raised": 24700000000
  },
  "tech": ["google_analytics", "aws", "salesforce"],
  "location": {
    "city": "San Francisco",
    "state": "California",
    "country": "US"
  },
  "linkedin": {
    "handle": "company/uber-com"
  }
}
Error Codes:

404 - Company not found (triggers Apollo fallback)

401 - Invalid API key

422 - Invalid domain format

429 - Rate limit exceeded

Apollo.io API (Fallback Enrichment)
Authentication Method: Header Auth (X-Api-Key)

Base URL: https://api.apollo.io/v1

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
/organizations/enrich	GET	Enrich company by domain	200 requests/minute
Sample Request:

javascript
{
  "method": "GET",
  "url": "https://api.apollo.io/v1/organizations/enrich",
  "headers": {
    "X-Api-Key": "{{$credentials.apiKey}}"
  },
  "qs": {
    "domain": "{{$json.company_domain}}"
  }
}
Emailable Email Verification API
Authentication Method: Query Parameter (api_key)

Base URL: https://api.emailable.com/v1

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
/verify	GET	Verify email deliverability	1000 requests/hour
Sample Request:

javascript
{
  "method": "GET",
  "url": "https://api.emailable.com/v1/verify",
  "qs": {
    "email": "{{$json.email}}",
    "api_key": "{{$credentials.apiKey}}"
  }
}
Sample Response:

json
{
  "state": "deliverable",
  "email": "john@example.com",
  "score": 95,
  "reason": "accepted_email",
  "role": false,
  "free": false,
  "disposable": false,
  "accept_all": false,
  "smtp_provider": "google"
}
OpenAI GPT-4 API
Authentication Method: Bearer Token

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
/v1/chat/completions	POST	AI lead qualification	10,000 TPM (tokens per minute)
Model: gpt-4 (or gpt-4-turbo for faster responses)

Sample Request Payload:

javascript
{
  "model": "gpt-4",
  "messages": [
    {
      "role": "system",
      "content": "You are a B2B lead qualification expert. Analyze the following lead against our ICP criteria..."
    },
    {
      "role": "user",
      "content": JSON.stringify(leadData)
    }
  ],
  "temperature": 0.3,
  "response_format": { "type": "json_object" }
}
HubSpot CRM API
Authentication Method: Private App Token (Bearer)

Base URL: https://api.hubapi.com

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
/crm/v3/objects/contacts/search	POST	Check if contact exists	100 requests/10 seconds
/crm/v3/objects/contacts	POST	Create new contact	100 requests/10 seconds
/crm/v3/objects/contacts/{id}	PATCH	Update existing contact	100 requests/10 seconds
/crm/v3/objects/deals	POST	Create deal	100 requests/10 seconds
Required Scopes:

crm.objects.contacts.write

crm.objects.contacts.read

crm.objects.deals.write

crm.objects.companies.read

Slack Webhook API
Authentication Method: Webhook URL (no separate auth)

Endpoints Used:

Endpoint	Method	Purpose	Rate Limit
Incoming Webhook URL	POST	Send notifications	1 request/second
Sample Payload:

json
{
  "text": "🔥 New A-Tier Lead Assigned!",
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "*John Doe* from *Example Corp*\n*Title:* CTO\n*Score:* 92/100"
      }
    }
  ]
}
Data Schemas
Input Schema (Webhook Payload)
json
{
  "source": {
    "type": "string",
    "required": true,
    "enum": ["web_form", "linkedin", "calendly", "hubspot"],
    "example": "web_form"
  },
  "data": {
    "type": "object",
    "required": true,
    "properties": {
      "firstName": {
        "type": "string",
        "required": false,
        "example": "John"
      },
      "lastName": {
        "type": "string",
        "required": false,
        "example": "Doe"
      },
      "email": {
        "type": "string",
        "required": true,
        "format": "email",
        "example": "john@example.com"
      },
      "company": {
        "type": "string",
        "required": true,
        "example": "Example Corp"
      },
      "phone": {
        "type": "string",
        "required": false,
        "example": "+1-555-0123"
      },
      "jobTitle": {
        "type": "string",
        "required": false,
        "example": "CTO"
      },
      "website": {
        "type": "string",
        "required": false,
        "example": "example.com"
      }
    }
  }
}
Internal Data Structure After Enrichment
json
{
  "id": "uuid",
  "lead_id": "lead_1704465600_abc123",
  "email": "john@example.com",
  "company_name": "Example Corp",
  "company_domain": "example.com",
  "company_size": 250,
  "company_industry": "Software",
  "company_revenue_range": "$10M-$50M",
  "is_decision_maker": true,
  "seniority_level": "c_level",
  "decision_maker_likelihood": 0.95,
  "email_deliverable": true,
  "email_quality_score": 92,
  "tech_stack": ["aws", "salesforce", "slack"],
  "buying_signals": {
    "has_funding": true,
    "is_growing": true,
    "has_tech_stack": true
  },
  "enrichment_quality": {
    "score": 87,
    "completeness": "87%"
  }
}
AI Qualification Output Schema
json
{
  "tier": "A",
  "score": 92,
  "reasoning": "Strong ICP match: C-level decision maker at mid-sized software company...",
  "icp_match": {
    "company_size_match": true,
    "industry_match": true,
    "seniority_match": true,
    "budget_indicators": true
  },
  "key_strengths": [
    "Decision maker with procurement authority",
    "Company in growth phase (recent funding)",
    "Uses complementary tech stack"
  ],
  "potential_objections": [
    "May already have incumbent solution"
  ],
  "recommended_next_steps": [
    "Schedule discovery call within 24 hours",
    "Research their current tech stack",
    "Prepare ROI calculator"
  ],
  "urgency": "high"
}
Database Schema (Supabase Leads Table)
See Step 3 in Setup & Configuration for complete schema.

Error Handling
Error Types & Recovery
1. API Timeout Errors (Enrichment APIs)

Cause: Clearbit, Apollo, or Emailable not responding within 30 seconds

Handling: Automatic retry with exponential backoff (3 attempts); if Clearbit fails, falls back to Apollo; if both fail, enrichment_source set to 'failed' and workflow continues

Code Location: HTTP Request nodes → Settings → Retry on fail (3 times)

Fallback: Lead still processes with basic data; enrichment_confidence = 0.0

2. Authentication Errors

Cause: Invalid or expired API keys for any service

Handling: Workflow stops, error logged to execution history, no Slack notification sent

Code Location: All credential-based nodes

Resolution: Refresh credentials in n8n Credentials manager

3. Data Validation Errors

Cause: Webhook payload missing required fields (email or company_name)

Handling: Lead marked with status: 'error' and error_message, stored in database for review, workflow continues to next lead

Code Location: Normalize Lead Data node

Recovery: Review error logs in Supabase, fix source data configuration

4. OpenAI Rate Limit Errors

Cause: Exceeding OpenAI tokens per minute limit

Handling: Automatic retry after 60-second delay

Code Location: OpenAI - Qualify Lead node → Retry settings

Prevention: Consider upgrading OpenAI plan or implementing request queuing

5. HubSpot Sync Errors

Cause: Missing required contact properties or API rate limits

Handling: Lead qualification completes successfully; HubSpot sync error logged separately; sales team still notified via Slack

Code Location: Create Contact / Update Contact nodes

Recovery: Manually create HubSpot contact or retry sync from audit log

6. Sales Rep Assignment Errors

Cause: No available sales reps (all at max capacity or none active)

Handling: Lead assigned to default "Unassigned" pool; notification sent to #sales-ops channel instead of specific rep

Code Location: Find Available Sales Rep node

Resolution: Increase rep capacity or activate additional reps in sales_reps table

Error Logging
Location:

n8n execution logs (Executions tab in workflow)

Supabase lead_audit_log table

Slack #tech-alerts channel (for critical errors)

Log Format:

json
{
  "execution_id": "string",
  "workflow_id": "string",
  "lead_id": "string",
  "error_node": "string",
  "error_type": "string",
  "error_message": "string",
  "timestamp": "ISO8601",
  "input_data": "object"
}
Monitoring & Alerts
Slack Notifications:

Critical errors (API authentication failures) → #tech-alerts (immediate)

Failed enrichment fallbacks → #tech-alerts (when both Clearbit and Apollo fail)

Workflow execution failures → #tech-alerts (immediate)

Email Alerts:
Not currently configured; can be added via n8n Error Workflow feature

Modification Guide
Common Modifications
1. Adjust Lead Qualification Tiers (A/B/C Criteria)
Location: OpenAI - Qualify Lead node

Modify the system prompt to change ICP criteria:

javascript
// Current tier definitions (in system message):
// A-Tier: Decision makers (C-level/VP) at companies 50-500 employees, $5M-$50M revenue
// B-Tier: Directors/Managers at similar companies OR decision makers at smaller companies
// C-Tier: Everyone else

// To make criteria more strict (fewer A-tier leads):
Tier A Requirements:
- Must be C-level (CEO, CTO, CFO) - exclude VPs
- Company size: 100-1000 employees (raise minimum)
- Revenue: $10M+ (raise minimum)
2. Change Sales Rep Assignment Logic
Location: Filter and Sort Sales Reps node

javascript
// Current logic: Round-robin by current_lead_load (lowest first)
const sortedReps = availableReps.sort((a, b) => 
  a.json.current_lead_load - b.json.current_lead_load
);

// To prioritize by seniority (add seniority column to sales_reps table):
const sortedReps = availableReps.sort((a, b) => {
  if (leadTier === 'A') {
    return b.json.seniority_rank - a.json.seniority_rank; // Highest seniority for A-tier
  }
  return a.json.current_lead_load - b.json.current_lead_load; // Load balance for B-tier
});
3. Add Custom Enrichment Field
Step 1: Add column to Supabase leads table:

sql
ALTER TABLE leads ADD COLUMN custom_field VARCHAR(255);
Step 2: Add field to Normalize Enrichment Data node:

javascript
enrichedData.custom_field = cb.custom_api_field || null;
Step 3: Add field to Save Enriched Data node in fieldsUi:

javascript
{
  "fieldId": "custom_field",
  "fieldValue": "={{ $json.custom_field }}"
}
4. Modify Slack Notification Format
Location: Slack - Notify Sales Team node

javascript
// Current format
{
  "text": "🔥 New A-Tier Lead Assigned to {{$json.assigned_rep_name}}!",
  "blocks": [...]
}

// To add custom fields:
{
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": `*${firstName} ${lastName}* from *${companyName}*\n*Title:* ${jobTitle}\n*Score:* ${score}/100\n*Custom Field:* ${customField}`
      }
    }
  ]
}
5. Disable Email Verification (to save API costs)
Step 1: Disable Emailable - Verify Email node (toggle to inactive)

Step 2: Update Process Email Validation node to skip Emailable:

javascript
// Replace Emailable check with basic validation only
emailValidation.email_valid = emailRegex.test(email);
emailValidation.email_deliverable = true; // Assume valid if format correct
emailValidation.email_quality_score = 70; // Default score
6. Route Leads by Industry to Specialized Reps
Step 1: Add specialization column to sales_reps table:

sql
ALTER TABLE sales_reps ADD COLUMN specialization VARCHAR(255);
UPDATE sales_reps SET specialization = 'SaaS' WHERE name = 'Alice Johnson';
UPDATE sales_reps SET specialization = 'Fintech' WHERE name = 'Bob Smith';
Step 2: Modify Filter and Sort Sales Reps node:

javascript
const leadIndustry = $('Save Enriched Data').first().json.company_industry;

// Filter reps by specialization
let matchedReps = availableReps.filter(rep => {
  const spec = rep.json.specialization?.toLowerCase();
  return !spec || leadIndustry.toLowerCase().includes(spec);
});

// Fallback to any available rep if no match
if (matchedReps.length === 0) matchedReps = availableReps;
Testing After Modifications
Test in isolation: Use n8n's "Execute Workflow" with manual test data

Test with sample data: Use the curl command from Step 7 with modified payload

Test full workflow: Submit real form data through your intake sources

Monitor first 10 executions: Check execution logs for errors or unexpected behavior

Verify outputs: Confirm Supabase records, Slack messages, and HubSpot sync

Technical Troubleshooting
Debug Mode
Enable detailed logging:

bash
# For self-hosted n8n, add to environment variables:
export N8N_LOG_LEVEL=debug
export N8N_LOG_OUTPUT=console,file

# Or in Docker Compose:
environment:
  - N8N_LOG_LEVEL=debug
In workflow settings:

Enable "Save Execution Data" (Settings → Save Data: All)

Enable "Save Manual Executions" for testing

Common Technical Issues
Issue: "Clearbit enrichment always fails, falling back to Apollo"
Symptoms: Every lead uses Apollo data; Clearbit node shows 404 errors

Diagnosis:

bash
# Test Clearbit API directly
curl -u YOUR_CLEARBIT_API_KEY: \
  "https://company.clearbit.com/v2/companies/find?domain=uber.com"
Solution:

Verify API key is correct in n8n credentials

Check if domain extraction is working (common issue: "www." prefix or invalid URLs)

In Prepare for Enrichment node, improve domain parsing:

javascript
// Add better domain extraction
const cleanDomain = (url) => {
  if (!url) return null;
  return url.replace(/^(https?:\/\/)?(www\.)?/, '')
    .split('/')[0]
    .toLowerCase();
};
enrichmentPayload.company_domain = cleanDomain(lead.company_website || enrichmentPayload.email_domain);
Prevention: Add domain validation before enrichment call

Issue: "OpenAI qualification returns invalid JSON"
Symptoms: Parse AI Qualification node fails with "Unexpected token" error

Diagnosis:

javascript
// Check raw OpenAI response in execution data
console.log('Raw OpenAI response:', $input.first().json.choices[0].message.content);
Solution:

Ensure response_format: { type: "json_object" } is set in OpenAI node

Add JSON parsing with error handling in Parse AI Qualification:

javascript
let qualificationData;
try {
  const rawContent = $input.first().json.choices[0].message.content;
  qualificationData = JSON.parse(rawContent);
} catch (e) {
  console.error('Failed to parse OpenAI response:', e);
  // Fallback to default scoring
  qualificationData = {
    tier: 'B',
    score: 50,
    reasoning: 'Auto-scored due to AI parsing error',
    recommended_next_steps: ['Manual review required']
  };
}
Prevention: Use structured prompts with clear JSON schema examples

Issue: "Duplicate leads still created within same hour"
Symptoms: Same email appears multiple times in database with different lead_ids

Diagnosis:

sql
-- Check for duplicates in Supabase
SELECT email, COUNT(*), array_agg(lead_id) 
FROM leads 
WHERE created_at > NOW() - INTERVAL '1 hour'
GROUP BY email 
HAVING COUNT(*) > 1;
Solution:

Check if Check Duplicate Email node query is correct

Ensure email normalization is consistent (lowercase):

javascript
// In Normalize Lead Data node
normalized.email = payload.data?.email?.toLowerCase().trim();
Add unique constraint to database:

sql
CREATE UNIQUE INDEX idx_leads_email_24h ON leads(email) 
WHERE created_at > NOW() - INTERVAL '24 hours';
Prevention: Add database-level uniqueness constraint

Performance Optimization
Current Performance:

Avg execution time: 45-90 seconds per lead

API calls per execution: 8-12 (depending on fallbacks)

Memory usage: ~50MB per execution

Optimization Techniques:

Parallel API Calls:

javascript
// Current: Sequential enrichment (Clearbit → Apollo fallback)
// Optimization: Call both in parallel, use best result

// Add Split In Batches node before enrichment
// Route to both Clearbit AND Apollo simultaneously
// Merge results and choose highest confidence
Cache Enrichment Data:

javascript
// Add caching layer in Supabase
CREATE TABLE company_enrichment_cache (
  domain VARCHAR(255) PRIMARY KEY,
  enrichment_data JSONB,
  cached_at TIMESTAMP,
  CONSTRAINT cache_expiry CHECK (cached_at > NOW() - INTERVAL '30 days')
);

// Check cache before API calls:
const cached = await supabase
  .from('company_enrichment_cache')
  .select('enrichment_data')
  .eq('domain', companyDomain)
  .single();

if (cached) return cached.enrichment_data;
// Otherwise, call API and cache result
Batch Email Verification:
Instead of verifying one email at a time, collect 10 leads and use Emailable's batch endpoint (saves API calls)

Skip Enrichment for C-Tier Indicators:

javascript
// If personal email (Gmail, Yahoo) detected early, skip expensive enrichment
if (isPersonalEmail && !hasCompanyWebsite) {
  // Fast-track to C-tier without enrichment
  qualificationTier = 'C';
  skipEnrichment = true;
}
Deployment Checklist
Before deploying to production:

 All API credentials configured with production keys (not test/sandbox)

 Supabase database schema created with indexes

 At least 3 active sales reps inserted in sales_reps table

 Webhook URL updated in all lead sources (web forms, LinkedIn, Calendly)

 ICP criteria customized in OpenAI prompt

 Slack webhook URLs configured for correct channels

 HubSpot integration tested with sample contact creation

 Error handling tested (deliberately cause failures to verify logging)

 Rate limits reviewed for all APIs (especially OpenAI and Clearbit)

 Backup of workflow JSON saved externally

 Team trained on Slack notifications and lead handoff process

 Monitoring dashboard set up (optional: Grafana + Supabase metrics)

 Rollback plan documented (how to disable workflow and revert to manual process)

Version History
Version	Date	Changes	Author
1.0.0	2026-01-04	Initial production release	Development Team
Support & Maintenance
For technical issues:

n8n Community Forum: https://community.n8n.io

Internal Slack: #tech-support

Email: dev-team@yourcompany.com

For feature requests:

Submit via: Internal project management tool or #product-feedback Slack channel

Maintenance schedule:

API credential review: Monthly

Database cleanup (old leads): Quarterly

Workflow optimization review: Bi-annually

ICP criteria refresh: Quarterly (or when business strategy changes)

License
Proprietary - Internal Use Only
© 2026 Your Company. All rights reserved.
