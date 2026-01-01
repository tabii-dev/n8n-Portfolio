# AI-Powered WhatsApp Business Automation

An intelligent n8n workflow that transforms WhatsApp into an AI-powered sales assistant for Sylvia Hub, a Nigerian jewellery vendor. The system handles customer inquiries through text, voice notes, and images with real-time inventory lookups.

## 🎯 Features

- **Multi-Format Message Processing**: Handles text messages, voice notes, and product images
- **AI Voice Transcription**: Converts customer voice messages to text using OpenAI Whisper
- **Image Analysis**: Uses GPT-4 Vision to analyze product photos sent by customers
- **Intelligent Sales Assistant**: GPT-4-powered chatbot with business context and personality
- **Real-Time Inventory Access**: Connects to Google Sheets for live product availability
- **Delivery & Operations Automation**: Auto-calculates delivery fees and validates business hours
- **Conversational Memory**: Maintains conversation context for personalized customer experience
- **Automated Order Processing**: Guides customers through product selection to checkout

## 📋 Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **WhatsApp Business API** account (via Meta/Facebook)
3. **Google Sheets** with inventory and operations data
4. **API Keys for**:
   - OpenAI API (GPT-4 access)
   - WhatsApp Business API credentials

## 🗄️ Google Sheets Setup

### Required Spreadsheets

#### 1. Jewellery Inventory Sheet

Create a Google Sheet with columns:

- Product Name
- Price (₦)
- Availability (Available/Sold Out)
- Category
- Description
- Stock Quantity

**Example**:
| Product Name | Price | Availability | Category | Stock |
|-------------|-------|--------------|----------|-------|
| Gold Necklace Set | 35000 | Available | Necklaces | 5 |
| Pearl Earrings | 7500 | Sold Out | Earrings | 0 |

#### 2. Store Operations & Delivery Sheet

Create a sheet with:

- Business Hours
- Delivery Locations
- Delivery Fees
- Payment Methods
- Return Policy

**Example**:
| Location | Delivery Fee (₦) | Estimated Time |
|----------|-----------------|----------------|
| Lagos Mainland | 1500 | 2-3 hours |
| Lagos Island | 2000 | 3-4 hours |
| Outside Lagos | 3500 | 1-2 days |

## 🚀 Installation

### 1. Import the Workflow

1. Open your n8n instance
2. Click on **Workflows** → **Import from File**
3. Select the `AI-Powered-WhatsApp-Business-Automation.json` file
4. Click **Import**

### 2. Configure Credentials

You'll need to set up the following credentials in n8n:

#### WhatsApp Business API

- Go to **Credentials** → **Add Credential** → **WhatsApp**
- Use OAuth2 authentication
- Enter your WhatsApp Business Account credentials
- Note your Phone Number ID: `711479615389938` (update in workflow)

#### OpenAI

- Add OpenAI credential with your API key
- Ensure you have access to:
  - GPT-4 models
  - Whisper (audio transcription)
  - Vision API (image analysis)

#### Google Sheets

- Use OAuth2 authentication
- Grant permissions to read your inventory sheets
- Connect both spreadsheets:
  - Jewellery Inventory
  - Store Operations & Delivery Rules

### 3. Update Configuration

Replace the following in the workflow:

1. **Google Sheet IDs**:

   - Inventory: `1BoesL7V4xw5pgLiTrSo3dICqDqG9M1udffDFJQKfkP4` (update with yours)
   - Operations: `1yUNbvxxxEv5erYj8aFQk0ZRE6P9hDSHNgv7w1mmZ0GA` (update with yours)

2. **WhatsApp Phone Number ID**: Update in "Reply to WhatsApp Customer" node

3. **Business Details**: Customize the AI prompt in "Sylvia Hub Sales Assistant" node:
   - Business name
   - Operating hours
   - Product categories
   - Pricing format

## 📊 How It Works

### Workflow Stages

1. **Message Intake** → WhatsApp trigger receives customer message
2. **Type Detection** → Switch node identifies message type (text/audio/image)
3. **Processing**:
   - **Text**: Direct extraction
   - **Audio**: Fetch → Download → Transcribe with Whisper
   - **Image**: Fetch → Download → Analyze with GPT-4 Vision
4. **Normalization** → Convert all inputs to text format
5. **AI Assistant** → GPT-4 processes query with:
   - Business context
   - Conversation history
   - Real-time inventory data
   - Operations & delivery rules
6. **Response** → Send AI-generated reply back to customer on WhatsApp

### AI Assistant Capabilities

The assistant can:

- Check product availability in real-time
- Provide pricing and delivery estimates
- Validate business hours before taking orders
- Calculate total costs (product + delivery)
- Suggest alternatives for sold-out items
- Process orders with customer details
- Maintain conversation context across messages

## 🧪 Testing

### Test with Text Message

Send this WhatsApp message to your business number:

```
"What jewellery do you have available today?"
```

### Test with Voice Note

Record and send:

```
"Do you have gold necklaces in stock?"
```

### Test with Image

Send a photo of jewellery and ask:

```
"Do you have anything similar to this?"
```

## 🔧 Customization

### Modify AI Personality

Edit the **Sylvia Hub Sales Assistant** node system prompt to change:

- Tone and communication style
- Business policies
- Response templates
- Product recommendations logic

### Adjust Business Rules

Update the prompt to modify:

- Operating hours
- Delivery zones and fees
- Order minimums
- Payment methods

### Add New Product Categories

Update your Google Sheets inventory and the AI will automatically access new products.

## 📈 Monitoring

Track performance through n8n's execution logs:

- Message volume by type
- AI response accuracy
- Transcription success rates
- Inventory lookup patterns
- Customer conversation flows

## 🐛 Troubleshooting

**Issue**: Voice notes not transcribing

- **Solution**: Check OpenAI API key and Whisper API access

**Issue**: Images not being analyzed

- **Solution**: Verify GPT-4 Vision API is enabled in your OpenAI account

**Issue**: Inventory not updating

- **Solution**: Confirm Google Sheets permissions and sheet IDs are correct

**Issue**: WhatsApp not sending replies

- **Solution**: Validate WhatsApp Business API credentials and phone number ID

## 💡 Use Cases

This workflow is perfect for:

- Small retail businesses
- Product-based service providers
- Nigerian businesses using WhatsApp for sales
- Any business needing 24/7 customer support
- Order automation and inquiry handling

## 📝 License

MIT License - feel free to modify and use for your needs

## 🤝 Contributing

Contributions welcome! Please open an issue or PR.

---

**Built with**: n8n, OpenAI GPT-4, WhatsApp Business API, Google Sheets

```

```
