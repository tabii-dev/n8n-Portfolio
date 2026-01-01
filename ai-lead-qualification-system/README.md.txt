# AI-Powered Lead Qualification & Routing System

An intelligent n8n workflow that automatically qualifies, enriches, and routes sales leads using AI, with seamless CRM integration.

## 🎯 Features

- **Multi-Source Lead Intake**: Accepts leads from web forms, LinkedIn, Calendly, and HubSpot
- **Intelligent Data Normalization**: Automatically standardizes lead data from different sources
- **Email Validation**: Validates email deliverability and detects disposable emails
- **Duplicate Detection**: Prevents duplicate leads from entering your pipeline
- **Company Enrichment**: Enriches leads with company data using Clearbit and Apollo APIs
- **AI-Powered Qualification**: Uses GPT-4 to score and tier leads (A/B/C/Disqualified)
- **Smart Sales Rep Assignment**: Automatically assigns leads to available sales reps based on workload
- **HubSpot CRM Integration**: Creates contacts and deals automatically
- **Slack Notifications**: Real-time alerts for high-priority leads

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Supabase account** (for lead database)
3. **API Keys for**:
   - Clearbit API
   - Apollo.io API
   - Emailable API
   - OpenAI API (GPT-4)
   - HubSpot API
   - Slack API

## 🗄️ Database Setup

### Supabase Tables Required

#### 1. `leads` table
```sql
CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id TEXT UNIQUE NOT NULL,
  source TEXT,
  first_name TEXT,
  last_name TEXT,
  email TEXT UNIQUE,
  phone TEXT,
  company_name TEXT,
  job_title TEXT,
  company_website TEXT,
  company_domain TEXT,
  company_size INTEGER,
  company_industry TEXT,
  company_employees_range TEXT,
  company_founded_year INTEGER,
  company_revenue_range TEXT,
  company_funding_total TEXT,
  company_location TEXT,
  company_description TEXT,
  company_linkedin_url TEXT,
  company_twitter_url TEXT,
  tech_stack JSONB,
  buying_signals JSONB,
  seniority_level TEXT,
  is_decision_maker BOOLEAN,
  decision_maker_likelihood DECIMAL,
  email_valid BOOLEAN,
  email_deliverable BOOLEAN,
  email_type TEXT,
  email_quality_score INTEGER,
  enrichment_data JSONB,
  enrichment_source TEXT,
  enrichment_confidence DECIMAL,
  enrichment_completed_at TIMESTAMP,
  ai_score INTEGER,
  ai_tier TEXT,
  ai_tier_reasoning TEXT,
  ai_confidence DECIMAL,
  ai_component_scores JSONB,
  ai_key_strengths JSONB,
  ai_concerns JSONB,
  ai_talking_points JSONB,
  ai_suggested_angle TEXT,
  ai_predicted_deal_size TEXT,
  ai_predicted_close_timeline TEXT,
  ai_qualified_at TIMESTAMP,
  ai_model_used TEXT,
  assigned_to_rep_id UUID,
  assigned_at TIMESTAMP,
  assignment_reason TEXT,
  hubspot_contact_id TEXT,
  hubspot_deal_id TEXT,
  hubspot_deal_stage TEXT,
  hubspot_deal_amount DECIMAL,
  hubspot_synced_at TIMESTAMP,
  status TEXT,
  is_duplicate BOOLEAN DEFAULT false,
  duplicate_of UUID,
  raw_payload JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### 2. `sales_reps` table
```sql
CREATE TABLE sales_reps (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  territory TEXT,
  current_load INTEGER DEFAULT 0,
  max_capacity INTEGER DEFAULT 50,
  status TEXT DEFAULT 'active',
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### 3. `lead_audit_log` table
```sql
CREATE TABLE lead_audit_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  action TEXT NOT NULL,
  details TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `AI-Powered Lead Qualification & Routing System.json` file
4. Click **Import**

### 2. Configure Credentials

You'll need to set up the following credentials in n8n:

#### Supabase
- Go to **Credentials** → **Add Credential** → **Supabase**
- Enter your Supabase URL and API Key

#### Clearbit
- Create HTTP Header Auth credential
- Header Name: `Authorization`
- Header Value: `Bearer YOUR_CLEARBIT_API_KEY`

#### Apollo.io
- Create HTTP Header Auth credential
- Header Name: `x-api-key`
- Header Value: `YOUR_APOLLO_API_KEY`

#### OpenAI
- Add OpenAI credential with your API key

#### HubSpot
- Use OAuth2 authentication
- Follow n8n's HubSpot OAuth setup guide

#### Slack
- Use OAuth2 authentication
- Grant required permissions: `chat:write`, `channels:read`

### 3. Update Configuration

Replace the following in the workflow:

1. **Webhook node**: Note your webhook URL for lead intake
2. **Slack channel ID**: Update in "Slack - Notify Sales Team" node
3. **API keys**: Ensure all API keys are properly configured

## 📊 How It Works

### Workflow Stages

1. **Lead Intake** → Webhook receives lead data
2. **Normalization** → Standardizes data from different sources
3. **Validation** → Validates email and checks for duplicates
4. **Enrichment** → Fetches company data (Clearbit/Apollo)
5. **AI Qualification** → GPT-4 scores and tiers the lead
6. **Routing** → Assigns to sales rep based on tier and availability
7. **CRM Sync** → Creates HubSpot contact and deal
8. **Notification** → Sends Slack alert to sales team

### Lead Tiers

- **A-Tier (85-100)**: Perfect fit, immediate outreach required
- **B-Tier (65-84)**: Good fit, standard qualification process
- **C-Tier (45-64)**: Partial fit, long-term nurture
- **Disqualified (<45)**: Poor fit, no action

## 🧪 Testing

Test the workflow with this sample payload:
```json
{
  "source": "web_form",
  "data": {
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "phone": "+1234567890",
    "company": "Acme Corp",
    "jobTitle": "VP of Sales",
    "website": "https://acme.com"
  }
}
```

Send it to your webhook URL using curl:
```bash
curl -X POST https://your-n8n-instance.com/webhook/YOUR_WEBHOOK_ID \
  -H "Content-Type: application/json" \
  -d '{
    "source": "web_form",
    "data": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "company": "Acme Corp",
      "jobTitle": "VP of Sales"
    }
  }'
```

## 🔧 Customization

### Adjust ICP Criteria

Edit the **OpenAI - Qualify Lead** node to modify your Ideal Customer Profile:

- Company size range
- Target industries
- Revenue requirements
- Decision maker levels

### Modify Tier Thresholds

Update scoring in the **OpenAI - Qualify Lead** prompt to adjust tier boundaries.

## 📈 Monitoring

Check the **lead_audit_log** table to monitor:
- Lead intake volume
- Enrichment success rates
- AI qualification accuracy
- Assignment patterns

## 🐛 Troubleshooting

**Issue**: Leads not being enriched
- **Solution**: Check Clearbit/Apollo API quotas and credentials

**Issue**: AI qualification failing
- **Solution**: Verify OpenAI API key and check rate limits

**Issue**: Duplicates still getting through
- **Solution**: Ensure email field is indexed in Supabase

## 📝 License

MIT License - feel free to modify and use for your needs

## 🤝 Contributing

Contributions welcome! Please open an issue or PR.
