Multi-Source Prospect Discovery & Data Collection System
An intelligent, multi-platform prospecting automation that discovers, scores, and organizes qualified leads from Instagram, LinkedIn, and Google Maps into a centralized database.

🎯 What This Solves
Businesses waste countless hours manually searching for prospects across different platforms, copying data into spreadsheets, and trying to determine which leads are worth pursuing. This workflow automates the entire prospect discovery pipeline—scraping data from multiple sources, intelligently scoring each prospect, filtering out low-quality leads, and organizing everything in Airtable with real-time notifications.

Perfect for agencies, sales teams, and anyone running outreach campaigns who needs a constant flow of qualified prospects.

✨ Key Features
Multi-Platform Scraping: Automatically discovers prospects from Instagram hashtags, LinkedIn companies, and Google Maps businesses

Intelligent Scoring System: Custom algorithms score each prospect based on followers, engagement, company size, ratings, and industry relevance

Quality Filtering: Automatically routes high-quality prospects vs low-quality leads

Duplicate Prevention: Removes duplicate entries across all three sources

Automated Database Sync: Upserts all qualified prospects to organized Airtable tables

Real-Time Notifications: Slack alerts with pipeline summaries and metrics

Scheduled Execution: Runs automatically on schedule (8 AM, 9 AM, 10 AM daily)

Data Validation: Ensures all prospects have required fields before saving

🛠️ Tech Stack
n8n - Workflow orchestration and automation

Apify - Web scraping for Instagram, LinkedIn, and Google Maps

Airtable - Centralized prospect database with separate tables per source

Slack - Real-time pipeline notifications and metrics

JavaScript - Custom scoring algorithms and data transformation

📋 What You'll Need
Before setting this up, make sure you have:

n8n instance (cloud or self-hosted)

Apify account with:

Instagram Hashtag Scraper task configured

LinkedIn Company Scraper task configured

Google Maps Email Extractor task configured

Airtable account with:

Base created (e.g., "Outreach")

Three tables: Instagram Prospects, LinkedIn Prospects, Google Maps Prospects

Slack workspace with:

Bot or OAuth app configured

Channel or user for notifications

API credentials for all services

🗄️ Airtable Database Setup
Instagram Prospects Table
Create a table with these fields:

Field Name	Type	Description
Handle	Single line text	Instagram username
Name	Single line text	Full name from profile
Bio	Long text	Profile biography
Followers	Number	Follower count
Recent Posts URLs	URL	Latest post URL
Profile URL	URL	Instagram profile link
Engagement Rate	Number	Calculated engagement
Status	Single select	"Discovered", "Contacted", etc.
LinkedIn Prospects Table
Field Name	Type	Description
Company name	Single line text	Company name
Website	URL	Company website
Description	Long text	Company description
Industry	Single line text	Industry category
Company Size	Number	Employee count
Profile URL	URL	LinkedIn company page
Status	Single select	"Discovered", "Contacted", etc.
Google Maps Prospects Table
Field Name	Type	Description
Name	Single line text	Business name
Category	Single line text	Business type
Phone	Phone number	Contact phone
Date Discovered	Date	Scrape date
Total Score	Number	Google rating
🚀 How to Set This Up
Step 1: Import the Workflow
Download the Prospect-Discovery-Data-Collection.json file from this folder

Open your n8n instance

Click Workflows → Import from File

Select the downloaded JSON file

Click Import

Step 2: Configure Apify Tasks
You need to create three Apify tasks:

Instagram Hashtag Scraper:

Go to Apify console

Find "Instagram Hashtag Scraper" actor

Create a task with your target hashtags (e.g., #entrepreneur, #startup)

Copy the task ID

Update in n8n's "Scrape Instagram Hashtags" node

LinkedIn Company Scraper:

Create task with LinkedIn company search URLs

Copy task ID

Update in "Scrape LinkedIn Companies" node

Google Maps Scraper:

Create task with location and business category

Copy task ID

Update in "Scrape Google Maps Businesses" node

Step 3: Add Your Credentials
In n8n, configure credentials for:

Apify:

Click "Scrape Instagram Hashtags" node → Credentials → Create New

Add your Apify API token

Airtable:

Click "Save to Instagram Prospects" node → Credentials → Create New

Connect your Airtable account

Select your base and tables

Slack:

Click any "Send Success Notification" node → Credentials → Create New

Connect your Slack workspace

Select notification channel/user

Step 4: Update Configuration
Replace these placeholder values:

Apify Task IDs:

Instagram Scraper Task ID: YOUR_INSTAGRAM_SCRAPER_TASK_ID

LinkedIn Scraper Task ID: YOUR_LINKEDIN_SCRAPER_TASK_ID

Maps Scraper Task ID: YOUR_MAPS_SCRAPER_TASK_ID

Airtable IDs:

Base ID: YOUR_AIRTABLE_BASE_ID

Instagram Table ID: YOUR_INSTAGRAM_TABLE_ID

LinkedIn Table ID: YOUR_LINKEDIN_TABLE_ID

Maps Table ID: YOUR_MAPS_TABLE_ID

Slack User:

User/Channel ID: YOUR_SLACK_USER_ID

Step 5: Customize Scoring Logic
Open the scoring nodes and adjust thresholds:

Instagram Scoring (Code node):

Adjust follower count thresholds

Modify business keywords in bio detection

Change score weights

LinkedIn Scoring:

Update ideal company size range

Modify target industries list

Adjust score weights

Google Maps Scoring:

Change rating thresholds

Update target business categories

Modify score weights

Step 6: Test the Workflow
Test Instagram Pipeline:

Click "Schedule Trigger" node

Click "Execute Node"

Watch data flow through the pipeline

Check Airtable for new records

Verify Slack notification

Test LinkedIn Pipeline:

Click "Schedule Trigger1" node

Execute and verify

Test Google Maps Pipeline:

Click "Schedule Trigger2" node

Execute and verify

Step 7: Enable Scheduling
Once tested:

Activate the workflow (toggle in top-right)

The workflow will run automatically:

Instagram: 8:00 AM daily

LinkedIn: 9:00 AM daily

Google Maps: 10:00 AM daily

🔧 Customization Options
Change Scraping Frequency
Edit the Schedule Trigger nodes:

Current: Daily at 8 AM, 9 AM, 10 AM

To change: Click node → Modify "triggerAtHour"

Modify Scoring Criteria
Instagram:

javascript
// In "Score Instagram Prospects" node
if (data.followersCount >= 50000) score += 50;  // Adjust threshold
LinkedIn:

javascript
// In "Score LinkedIn Prospects" node
if (size >= 50 && size <= 500) score += 40;  // Adjust ideal company size
Google Maps:

javascript
// In "Score Maps Prospects" node
if (rating >= 4.5) score += 40;  // Adjust rating threshold
Add New Target Industries/Categories
Edit the arrays in scoring nodes:

javascript
const targetIndustries = ['Technology', 'Software', 'SaaS', 'Marketing'];
const targetCategories = ['Restaurant', 'Retail', 'Salon', 'Gym'];
Change Notification Format
Edit Slack nodes to customize message:

javascript
✅ *Pipeline Complete*
📊 Summary:
- Scraped: {{ records }}
- High Quality: {{ highQualityCount }}
📊 How It Works
Instagram Pipeline (8:00 AM)
Schedule Trigger → Runs daily at 8 AM

Scrape Instagram Hashtags → Apify scrapes posts from target hashtags

Get Profile Details → Fetches full profile data for each user

Format Instagram Data → Extracts relevant fields

Validate Instagram Data → Ensures required fields exist

Score Instagram Prospects → Applies scoring algorithm (0-100)

Filter Qualified Prospects → Routes high quality (≥30) vs low quality (<30)

Remove Duplicates → Prevents duplicate entries

Save to Instagram Prospects → Upserts to Airtable

Log Instagram Metrics → Records pipeline performance

Send Success Notification → Slack alert with summary

LinkedIn Pipeline (9:00 AM)
Schedule Trigger → Runs daily at 9 AM

Scrape LinkedIn Companies → Apify scrapes company profiles

Format LinkedIn Data → Extracts company details

Validate LinkedIn Data → Checks required fields

Score LinkedIn Prospects → Scores based on size, industry, website

Switch → Routes qualified (≥35) vs low score (<35)

Deduplicate → Removes duplicates

Save to LinkedIn Prospects → Upserts to Airtable

Log Metrics → Records performance

Send Notification → Slack summary

Google Maps Pipeline (10:00 AM)
Schedule Trigger → Runs daily at 10 AM

Scrape Google Maps Businesses → Apify extracts business data

Format Maps Data → Structures data fields

Validate Maps Data → Ensures completeness

Score Maps Prospects → Scores based on rating, category, phone

Switch → Routes high quality (≥60) vs low (<40)

Deduplicate → Prevents duplicates

Save to Maps Prospects → Upserts to Airtable

Log Metrics → Records pipeline stats

Send Notification → Slack alert

🐛 Troubleshooting
Problem: Apify scraper returns no data

Solution: Check that your Apify task is configured correctly and has input parameters set

Problem: "Credential not found" error

Solution: Make sure you've added credentials for Apify, Airtable, and Slack in n8n

Problem: Duplicate prospects appearing in Airtable

Solution: Verify the "matchingColumns" in Airtable nodes are set correctly (Handle for Instagram, Company name for LinkedIn, Name for Maps)

Problem: Slack notifications not sending

Solution: Check Slack credentials and verify the user/channel ID is correct

Problem: Scoring is too strict/lenient

Solution: Adjust score thresholds in the scoring JavaScript nodes

Problem: Workflow timing out

Solution: Reduce the number of items being scraped per run or increase n8n timeout settings

💡 Who This Is For
This workflow is perfect for:

Lead generation agencies building prospect lists for clients

Sales teams needing a constant pipeline of qualified leads

Marketing agencies researching potential clients across platforms

Business development reps prospecting on multiple channels

Recruitment agencies finding companies hiring in specific industries

Influencer marketers discovering brand partnership opportunities

Real-world example: A B2B SaaS sales team uses this to automatically discover tech companies on LinkedIn (50-500 employees), Instagram influencers in the startup space (10K+ followers), and local businesses on Google Maps (4+ star rating). Every morning, their Airtable is populated with fresh, scored prospects ready for outreach.

📈 Metrics Tracked
Each pipeline logs:

Total records scraped

Records after validation

High quality prospect count

Medium quality prospect count

Records saved to Airtable

Execution duration

Pipeline status (Success/Error)

Average prospect score

Top industry/category


📝 License
MIT License - Feel free to use and modify for your needs.

Questions or improvements? Open an issue or submit a PR!