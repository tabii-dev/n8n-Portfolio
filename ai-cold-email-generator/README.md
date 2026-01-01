# AI-Powered Cold Email Generator & Sender

An intelligent n8n workflow that automates personalized cold email outreach for content creators and influencers. Features AI-generated emails with human-in-the-loop approval and automatic revision based on feedback.

## 🎯 Features

- **Google Sheets Integration**: Automatically triggers when new brand details are added
- **AI Email Generation**: GPT-4 crafts personalized, authentic outreach emails
- **Human Approval Loop**: Sends draft to your email for review before sending
- **Intelligent Classification**: AI determines if feedback is "Approved" or "Needs Revision"
- **Auto-Revision System**: GPT-4 revises emails based on your feedback
- **Direct Sending**: Automatically sends approved emails via Gmail
- **Brand-Specific Personalization**: Tailors each email to the target brand's products/services
- **Spam-Free Formatting**: Professional structure without marketing red flags

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Google account** with:
   - Google Sheets access
   - Gmail account for sending
3. **API Keys for**:
   - OpenAI API (GPT-4 access)

## 🗄️ Google Sheets Setup

### Create Brand Collaboration Tracker

Create a Google Sheet with these columns:

| Column Name                                            | Description           | Example                                          |
| ------------------------------------------------------ | --------------------- | ------------------------------------------------ |
| Timestamp                                              | Auto-filled by form   | 1/1/2026 14:05:00                                |
| Name of the brand                                      | Target company name   | Beauty Line by Didi                              |
| Email of the brand                                     | Contact email         | hello@beautybydidi.com                           |
| What is the Brand's Product or Service?                | Brief description     | Luxury lip glosses and lip stains                |
| Have you used the product or the service of the brand? | Yes/No                | Yes                                              |
| If yes, what did you like about the product/service?   | Your experience       | Amazing pigmentation and long-lasting            |
| Your audience reception to the product/service         | Feedback you received | Tons of compliments, high engagement             |
| Any info that would make you stand out to the brand?   | Unique angle          | 80% of my audience are Nigerian women aged 18-35 |

### Optional: Create a Google Form

To easily add new brands:

1. Go to Google Forms
2. Create a form with the same questions
3. Link responses to your Google Sheet
4. Share the form link for easy data entry

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `Cold-Emails-Generator-and-Sender.json` file
4. Click **Import**

### 2. Configure Credentials

#### Google Sheets Trigger

- Go to **Credentials** → **Add Credential** → **Google Sheets Trigger OAuth2 API**
- Authenticate your Google account
- Grant permissions to read sheets

#### Gmail

- Add Gmail OAuth2 credential
- Authenticate your Gmail account
- Grant permissions to send and read emails
- **Important**: You'll need this for both receiving drafts and sending final emails

#### OpenAI

- Add OpenAI API credential
- Ensure you have GPT-4 access
- The workflow uses GPT-4.1 model

### 3. Update Configuration

#### Update Your Google Sheet ID

1. Open your Google Sheet
2. Copy the Sheet ID from the URL:
   https://docs.google.com/spreadsheets/d/[THIS_IS_YOUR_SHEET_ID]/edit
3. In the workflow, click on the **"Get Brand Info"** node
4. Replace the document ID with yours:

- Current: `1rYkHNo0Bl6KJFhBkPdZH9BxzIT4PW9KWcoYyVg6vS-E`
- New: `YOUR_SHEET_ID`

#### Update Your Email Address

In the **"Human Response"** node:

- Change `sendTo` from `oluwatamilorefamakinwa@gmail.com` to **your email**

#### Customize the AI Prompt (Optional)

In the **"Email Generator"** node, update the system prompt to match your:

- Personal brand name
- Instagram/social media links
- Brand values and voice
- Target audience demographics
- Typical collaboration style

**Example customization**:
You are an email copywriter for [YOUR NAME], a [YOUR NICHE] content creator.
The creator's Instagram is: [YOUR INSTAGRAM LINK]
The [YOUR BRAND NAME] is a brand that centers on [YOUR VALUES].

## 📊 How It Works

### Workflow Process

1. **Trigger** → New row added to Google Sheets (manual or via Google Form)
2. **Data Formatting** → Extracts and structures brand information
3. **Email Generation** → GPT-4 creates personalized email using:
   - Brand name and product details
   - Your past experience with the brand (if any)
   - Audience fit and demographics
   - Collaboration value proposition
4. **Human Review** → Email draft sent to your Gmail with subject "Is this good to go?"
5. **Reply with Feedback**:
   - If satisfied: Reply "Approved" or "Looks good!"
   - If needs changes: Reply with specific feedback (e.g., "Make it shorter and more casual")
6. **AI Classification** → Determines if response is approval or revision request
7. **Decision Point**:
   - **Approved** → Sends email directly to brand's email address
   - **Disapproved** → Revision Model uses your feedback to improve the email → Loops back to step 4

### Email Structure

Generated emails follow this structure:

- **Greeting**: Personal address to the brand
- **Introduction**: Brief creator bio with brand values
- **Connection**: Specific experience or admiration for their product
- **Audience Fit**: Why your audience needs their product
- **Collaboration Intent**: Clear paid partnership proposal
- **Closing**: Warm, professional sign-off

## 🧪 Testing

### Test with Sample Data

1. Add a test row to your Google Sheet:

| Name of the brand | Email              | Product/Service   | Used? | What you liked  | Audience Reception | Additional Info     |
| ----------------- | ------------------ | ----------------- | ----- | --------------- | ------------------ | ------------------- |
| Test Brand Co     | test@youremail.com | Skincare products | Yes   | Amazing results | Great engagement   | 70% female audience |

2. Wait 1 minute (trigger polls every minute)
3. Check your email for the draft
4. Reply with "Approved" or provide feedback
5. If approved, check your sent emails to confirm delivery

## 🔧 Customization

### Adjust Email Tone

Edit the **Email Generator** system prompt to change:

- Formality level (casual vs professional)
- Email length (current default: 200 words)
- Specific phrases or brand voice
- Sign-off style

### Change Trigger Frequency

In the **"Get Brand Info"** node:

- Adjust polling time from every minute to:
  - Every 5 minutes (less frequent checks)
  - Every 30 seconds (faster response)

### Add Email Templates

Create multiple email generator nodes for:

- Different collaboration types (sponsored posts, affiliate, gifted)
- Different industries (beauty, tech, fashion)
- Different audience sizes

## 📈 Monitoring

### Track Performance

Monitor through n8n execution logs:

- Number of emails generated
- Approval vs revision rate
- Average revision cycles per email
- Successfully sent emails

### Measure Results

Track in your Google Sheet:

- Add columns: "Email Sent Date", "Response Received", "Collaboration Status"
- Monitor response rates
- Identify which brands respond positively

## 🐛 Troubleshooting

**Issue**: Workflow not triggering

- **Solution**: Check Google Sheets Trigger credentials and polling time

**Issue**: Not receiving draft emails

- **Solution**: Verify email address in "Human Response" node is correct

**Issue**: Revision loop not working

- **Solution**: Ensure your reply contains clear approval/disapproval language

**Issue**: Emails going to spam

- **Solution**: Warm up your Gmail account by manually sending a few emails first

**Issue**: AI generating inaccurate content

- **Solution**: Provide more detailed info in Google Sheet, especially in "Additional Info" column

## 💡 Use Cases

This workflow is perfect for:

- **Content creators**: Brand collaboration outreach
- **Influencers**: Sponsorship proposals
- **Freelancers**: Service offering emails
- **Sales teams**: Personalized cold outreach
- **Agencies**: Client prospecting campaigns

## 🎓 Best Practices

### Email Effectiveness Tips

1. **Be specific**: Include actual product names and features
2. **Show proof**: Mention past results with similar products
3. **Know your audience**: Share demographic data that matters to brands
4. **Be authentic**: Don't oversell or exaggerate
5. **Follow up**: Add brands to a follow-up sheet if no response in 7 days

### Data Management

- Keep your Google Sheet organized with status tracking
- Archive sent emails monthly
- Note response rates to refine your approach

## 📝 License

MIT License - feel free to modify and use for your needs

## 🤝 Contributing

Contributions welcome! Please open an issue or PR.

---

**Built with**: n8n, OpenAI GPT-4, Gmail, Google Sheets  
**Use Case**: Content creator brand outreach automation
