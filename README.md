# SMS AI Advisor API Server

## Overview

The SMS AI Advisor API Server is a FastAPI-based application designed to provide **offline access to an AI advisor for areas without internet connectivity**. It uses SMS as the communication medium, enabling users in remote areas to access AI-powered assistance through simple text messages.

The system leverages Termux (Android terminal emulator) to send and receive SMS messages, while integrating with Google's Gemini AI model to provide intelligent responses. This creates a bridge between AI technology and areas with limited internet infrastructure, where SMS remains a reliable communication method.

## Key Features

- **SMS-based AI Communication**: Send questions via SMS and receive AI-powered responses
- **Offline-First Design**: Operates without requiring constant internet connectivity for end users
- **Automatic Message Handling**: Intelligently chunks long messages and handles SMS encoding
- **Multi-language Support**: Responds in romanized versions of local languages
- **Conversation Memory**: Maintains chat history per phone number
- **Real-time Processing**: Long polling for instant message delivery
- **Google Search Integration**: Optional grounding with Google Search for enhanced accuracy
- **Registration System**: Automatic AI replies for registered phone numbers

## Use Case: AI Advisor for Remote Areas

This system is specifically designed for:

- **Rural/Remote Communities**: Areas with limited or no internet access but cellular coverage
- **Agricultural Advice**: Farmers can ask questions about crops, weather, farming techniques
- **Healthcare Information**: Basic health guidance and information access via SMS
- **Educational Support**: Students and teachers accessing information through text messages
- **Emergency Information**: Quick access to important information during disasters
- **Small Business Support**: Entrepreneurs getting business advice without internet

Users simply send a text message with their question to a registered number, and receive AI-generated responses via SMS, making advanced AI assistance accessible anywhere with basic cellular service.

## System Requirements

- **Android device** with Termux installed
- **Termux API** package for SMS functionality
- **Python 3.7+** environment
- **Google Gemini API key** for AI functionality
- **Cellular connectivity** for SMS sending/receiving

## Installation & Setup

### 1. Install Termux and Termux:API from F-Droid

**Important**: Install Termux from F-Droid, not Google Play Store, for better compatibility.

1. Download and install **F-Droid** from [f-droid.org](https://f-droid.org/)
2. Open F-Droid and search for "Termux"
3. Install **Termux** (main terminal emulator)
4. Install **Termux:API** (provides SMS and system access)

### 2. Initial Termux Setup

Open Termux and run the following commands:

```bash
# Update package lists and upgrade existing packages
pkg update && pkg upgrade
```

When prompted, press `Y` to confirm updates.

### 3. Install Required Packages

```bash
# Install essential packages
pkg install termux-api python git tmux curl wget

# Verify termux-api installation
termux-setup-storage
```

Grant storage permissions when prompted.

### 4. Install Python Dependencies

```bash
# Install required Python packages
pip install fastapi uvicorn python-dotenv google-genai pydantic
```

### 5. Install ngrok for External Access

```bash
# Clone ngrok installer for Termux
git clone https://github.com/Yisus7u7/termux-ngrok
cd termux-ngrok

# Run the installation script
bash install.sh

# Return to home directory
cd ~
```

### 6. Clone the SMS Bot Repository

```bash
# Clone the project repository
git clone https://github.com/AgriAgent-Capital-One-Hackathon/sms-bot
cd sms-bot
```

### 7. Setup ngrok Account and Authentication

#### Create ngrok Account:
1. Visit [ngrok.com](https://ngrok.com) and sign up for a free account
2. After signing up, go to [ngrok dashboard](https://dashboard.ngrok.com/get-started/setup/linux)
3. Copy your authentication token from the dashboard

#### Configure ngrok:
```bash
# Add your authentication token (replace YOUR_TOKEN with actual token)
ngrok config add-authtoken YOUR_TOKEN
```

The token looks like: `2abc123def456ghi789jkl_1MnOpQrStUvWxYz2AbCdEfGhIjKlMnOpQrS`

### 8. Configure Environment Variables

```bash
# Copy environment template
cp env-example .env

# Edit the .env file
nano .env
```

Add your Google Gemini API key and configure other settings as needed.

### 9. Running the Application with tmux

tmux allows you to run multiple processes simultaneously and keep them running even if you close Termux.

#### Start tmux session:
```bash
# Start a new tmux session
tmux new-session -s sms-bot
```

#### Split tmux window:
```bash
# Press Ctrl+B, then press " (quote) to split horizontally
# You'll now have two panes
```

#### tmux Basic Commands:
- `Ctrl+B + "` - Split window horizontally
- `Ctrl+B + %` - Split window vertically  
- `Ctrl+B + arrow keys` - Switch between panes
- `Ctrl+B + d` - Detach from session (keeps running)
- `tmux attach -t sms-bot` - Reattach to session

### 10. Run the Application

#### In the first tmux pane (top):
```bash
# Start the SMS API server
python sms_api_server.py
```

#### In the second tmux pane (bottom):
```bash
# Switch to the bottom pane with Ctrl+B + Down Arrow
# Start ngrok tunnel
ngrok http http://localhost:8000
```

**Note**: The default port is 8000, not 8080. Adjust if you changed the port in your `.env` file.

### 11. Complete Setup Verification

1. **Check API Server**: The Python script should show "SMS API Server is running"
2. **Check ngrok**: You'll see a forwarding URL like `https://abc123.ngrok-free.app`
3. **Test SMS functionality**: 
   ```bash
   # In a third terminal (Ctrl+B + " to split again)
   termux-sms-list
   ```

### 12. Grant Necessary Permissions

When first running the application, grant these permissions:
- **SMS permissions** for Termux:API
- **Phone permissions** for making calls (if needed)
- **Storage permissions** for file access

### Quick Start Summary

Once everything is installed:

```bash
# 1. Start tmux
tmux new-session -s sms-bot

# 2. Split window (Ctrl+B + ")
# 3. In first pane: run the server
python sms_api_server.py

# 4. In second pane: start ngrok
ngrok http http://localhost:8000

# 5. Copy the ngrok URL and use it to access your API
```

## Additional Configuration

### Environment Configuration (.env file)

Create a `.env` file in the project root with the following structure:

```bash
# Required Configuration
GOOGLE_API_KEY=your_actual_gemini_api_key_here
GEMINI_MODEL=gemini-2.5-flash

# Server Configuration
API_HOST=0.0.0.0
API_PORT=8000

# Optional Configuration
DEBUG=false
HISTORY_FILE=chat_history.json
PROCESSED_FILE=processed_sms.json
POLL_INTERVAL=2.0
ENABLE_GROUNDING=true
```

#### Environment Variables Explained:

**Required Variables:**
- `GOOGLE_API_KEY`: Your Google Gemini API key (get from Google AI Studio)
- `GEMINI_MODEL`: AI model to use (default: gemini-2.5-flash)

**Server Variables:**
- `API_HOST`: Server host address (default: 0.0.0.0 for all interfaces)
- `API_PORT`: Server port number (default: 8000)

**Optional Variables:**
- `DEBUG`: Enable debug logging (true/false, default: false)
- `HISTORY_FILE`: File to store chat histories (default: chat_history.json)
- `PROCESSED_FILE`: File to track processed SMS IDs (default: processed_sms.json)
- `POLL_INTERVAL`: SMS polling interval in seconds (default: 2.0)
- `ENABLE_GROUNDING`: Enable Google Search grounding (true/false, default: true)

### Get Google Gemini API Key

1. Visit [Google AI Studio](https://aistudio.google.com/)
2. Sign in with your Google account
3. Generate a new API key
4. Copy the key and add it to your `.env` file

### Run the Server

```bash
# Start the SMS API server
python sms_api_server.py
```

The server will start on `http://localhost:8000` by default.

### Expose Server (Optional)

For external access, use ngrok or similar:
```bash
# Install ngrok
# Start tunnel
ngrok http 8000
```

## API Endpoints

### Base URL
```
http://localhost:8000
```

For external access via ngrok:
```
https://your-ngrok-url.ngrok-free.app
```

### Headers Required for ngrok
```http
ngrok-skip-browser-warning: true
Content-Type: application/json
```

---

## 📍 Endpoints Overview

### 1. **Health Check**
**GET** `/`

Check if the API server is running.

**Response:**
```json
{
  "status": "online",
  "message": "SMS API Server is running"
}
```

---

### 2. **Send SMS**
**POST** `/send`

Send an SMS message to a phone number. Messages are automatically chunked if they exceed SMS limits.

**Request Body:**
```json
{
  "phone_number": "+1234567890",
  "message": "Your message content here"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "SMS queued for sending in 2 chunk(s)"
}
```

**Error Response:**
```json
{
  "detail": "Failed to send SMS: error details"
}
```

**Notes:**
- Messages are automatically chunked based on GSM 7-bit encoding rules
- Single SMS: 160 chars (GSM) / 70 chars (Unicode)
- Multi-part SMS: 153 chars (GSM) / 67 chars (Unicode) per chunk
- Messages are queued and sent asynchronously

---

### 3. **Receive SMS (Long Polling)**
**GET** `/receive`

Wait for incoming SMS messages using long polling (30-second timeout).

**Response (New Message):**
```json
{
  "id": "12345",
  "phone_number": "+1234567890",
  "message": "Incoming message content",
  "timestamp": "2025-01-15T10:30:00Z",
  "direction": "inbound"
}
```

**Response (No Messages):**
```json
{
  "status": "no_new_messages"
}
```

**Implementation Notes:**
- Use this for real-time SMS reception
- Timeout after 30 seconds if no messages
- Ideal for webhook-style integrations

---

### 4. **Register Phone Number**
**POST** `/register/{phone_number}`

Register a phone number for automatic AI-powered replies using Gemini.

**URL Parameters:**
- `phone_number`: Phone number to register (URL-encoded)

**Example:**
```
POST /register/%2B1234567890
```

**Response:**
```json
{
  "status": "success",
  "message": "Phone number +1234567890 registered successfully"
}
```

**Behavior:**
- Registered numbers receive automatic AI replies
- A confirmation SMS is sent to the registered number
- Chat history is initialized for the number

---

### 5. **Get Chat History**
**GET** `/history/{phone_number}?limit={limit}`

Retrieve chat history for a specific phone number.

**URL Parameters:**
- `phone_number`: Phone number (URL-encoded)

**Query Parameters:**
- `limit` (optional): Maximum number of messages to return (default: 100)

**Example:**
```
GET /history/%2B1234567890?limit=50
```

**Response:**
```json
{
  "phone_number": "+1234567890",
  "messages": [
    {
      "role": "user",
      "text": "Hello",
      "ts": 1705312200,
      "direction": "inbound"
    },
    {
      "role": "assistant", 
      "text": "Hi! How can I help you today?",
      "ts": 1705312201,
      "direction": "outbound"
    }
  ],
  "total_count": 25
}
```

**Message Object Fields:**
- `role`: "user", "assistant", or "system"
- `text`: Message content
- `ts`: Unix timestamp
- `direction`: "inbound" or "outbound"

---

### 6. **Clear Chat History**
**DELETE** `/history/{phone_number}`

Clear all chat history for a phone number and remove from registered numbers.

**URL Parameters:**
- `phone_number`: Phone number to clear (URL-encoded)

**Response:**
```json
{
  "status": "success",
  "message": "History cleared for +1234567890"
}
```

**Behavior:**
- Deletes all message history
- Removes from registered numbers list
- Clears Gemini chat context
- Sends confirmation SMS

---

### 7. **System Status**
**GET** `/status`

Get detailed system status and metrics.

**Response:**
```json
{
  "termux_api": true,
  "registered_numbers": 5,
  "active_chats": 3,
  "processed_sms_count": 127,
  "send_queue_size": 2,
  "gemini_queue_size": 0,
  "grounding_enabled": true
}
```

**Field Descriptions:**
- `termux_api`: Whether Termux API is available
- `registered_numbers`: Count of registered phone numbers
- `active_chats`: Number of active Gemini chat sessions
- `processed_sms_count`: Total processed SMS messages
- `send_queue_size`: Pending outgoing messages
- `gemini_queue_size`: Pending AI processing requests
- `grounding_enabled`: Whether Google Search grounding is enabled

---

### 8. **Get Registered Numbers**
**GET** `/numbers`

Get list of all registered phone numbers.

**Response:**
```json
[
  "+1234567890",
  "+0987654321",
  "+1122334455"
]
```

---

## Usage Examples

### Register a Phone Number
```bash
curl -X POST "http://localhost:8000/register/%2B1234567890"
```

### Send an SMS
```bash
curl -X POST "http://localhost:8000/send" \
  -H "Content-Type: application/json" \
  -d '{"phone_number": "+1234567890", "message": "Hello from API!"}'
```

### Check System Status
```bash
curl "http://localhost:8000/status"
```

### Get Chat History
```bash
curl "http://localhost:8000/history/%2B1234567890?limit=10"
```

## Technical Features

### Message Chunking
- Automatic SMS length detection and chunking
- GSM 7-bit encoding support (160 chars single, 153 chars multipart)
- Unicode support (70 chars single, 67 chars multipart)
- Intelligent word boundary splitting

### AI Integration
- Google Gemini 2.5 Flash model integration
- Conversation context maintenance per phone number
- System prompt optimized for SMS communication
- Multi-language support with romanization

### Data Persistence
- Chat histories saved to JSON files
- Processed SMS tracking to prevent duplicates
- Automatic state recovery on server restart

### Queue Management
- Threaded message processing
- Send queue for outgoing messages
- Gemini processing queue
- Rate limiting and error handling

## Troubleshooting

### Termux-Specific Issues

1. **termux-api not working**
   ```bash
   # Reinstall termux-api
   pkg uninstall termux-api
   pkg install termux-api
   
   # Grant permissions again
   termux-setup-storage
   ```

2. **SMS permissions denied**
   - Go to Android Settings → Apps → Termux:API → Permissions
   - Enable SMS and Phone permissions
   - Restart Termux

3. **ngrok authentication failed**
   ```bash
   # Re-add your auth token
   ngrok config add-authtoken YOUR_TOKEN
   
   # Check ngrok config
   ngrok config check
   ```

4. **tmux session lost**
   ```bash
   # List existing sessions
   tmux list-sessions
   
   # Reattach to session
   tmux attach -t sms-bot
   
   # Kill old session if needed
   tmux kill-session -t sms-bot
   ```

5. **Python packages installation failed**
   ```bash
   # Update pip
   pip install --upgrade pip
   
   # Install packages one by one
   pip install fastapi
   pip install uvicorn
   pip install python-dotenv
   pip install google-genai
   pip install pydantic
   ```

6. **Port already in use**
   ```bash
   # Check what's using port 8000
   netstat -tulpn | grep 8000
   
   # Kill process using the port
   pkill -f "python sms_api_server.py"
   
   # Or change port in .env file
   echo "API_PORT=8001" >> .env
   ```

### Common Issues

1. **"termux_api": false in status**
   - Install termux-api package: `pkg install termux-api`
   - Grant SMS permissions to Termux

2. **AI responses not working**
   - Check `GOOGLE_API_KEY` in `.env` file
   - Verify API key has Gemini access
   - Check internet connectivity

3. **SMS not sending/receiving**
   - Verify Termux has SMS permissions
   - Check if device has cellular connectivity
   - Ensure termux-api is properly installed

4. **Server not starting**
   - Check if port 8000 is available
   - Verify all Python dependencies are installed
   - Check `.env` file configuration

### Debug Mode
Enable debug logging by setting `DEBUG=true` in your `.env` file for verbose output.

## Security Considerations

- The server runs locally on the Android device
- No authentication is implemented (suitable for local/demo use)
- Consider adding API keys for production deployment
- Phone numbers are URL-encoded for safety
- Use HTTPS (ngrok) for external access

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is open source. Please check the license file for specific terms.

## Support

For issues and questions:
- Check the troubleshooting section above
- Review the API documentation
- Ensure all prerequisites are properly installed
- Verify environment configuration

---

*This SMS AI Advisor system bridges the digital divide by making AI assistance accessible through SMS, serving communities where internet access is limited but cellular coverage exists.*
