# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

### Running the Bot
```bash
python3 chatbot.py
```

### Docker Development
```bash
# Build Docker image
docker build -t chatgptmattermostchatbot .

# Run with environment variables
docker run -d --name chatbotgpt \
  -e AI_API_KEY="your_ai_api_key" \
  -e AI_MODEL="gpt-4o" \
  -e MATTERMOST_URL="mattermostinstance.example.com" \
  -e MATTERMOST_TOKEN="your_mattermost_token" \
  ghcr.io/elehiggle/chatgptmattermostchatbot:latest
```

### Dependencies
```bash
# Install Python dependencies
pip3 install -r requirements.txt
```

## Architecture

This is a Python-based Mattermost chatbot that integrates OpenAI's API with comprehensive multimodal capabilities.

### Core Components

- **chatbot.py**: Main application entry point containing the bot logic, message handling, and OpenAI API integration
- **config.py**: Configuration management using environment variables for all bot settings (AI parameters, Mattermost connection, feature toggles)
- **helpers.py**: Utility functions for image processing, URL validation, caching, YouTube handling, and background removal
- **mattermostdriver_patched.py**: Patched version of the Mattermost driver to handle SSL certificate issues
- **websocket_patched.py**: WebSocket patches for improved connection handling

### Key Features Architecture

**Multimodal Processing Pipeline**:
- File attachment extraction (images, PDFs, documents)
- URL content extraction with optional FlareSolverr bypass for CAPTCHA/JS protection
- YouTube transcript fetching and metadata extraction
- Vision API integration for image analysis
- PDF parsing with text and image extraction

**Function Calling System**:
- DALL-E-3 image generation
- Custom emoji creation with background removal
- HTML-to-image screenshot generation using Chromium
- Live data fetching (stock prices via yfinance, crypto data, exchange rates)
- Screenshot capability for raw HTML or URLs

**Threading and Concurrency**:
- Thread pool executor for handling multiple concurrent requests
- Typing indicators sent during processing
- Context preservation within message threads

### Configuration System

All configuration is environment-variable based with sensible defaults. Key categories:

- **AI Settings**: Model selection, temperature, max tokens, reasoning effort, system prompts
- **Mattermost Connection**: URL, authentication (token or username/password), SSL settings
- **Feature Controls**: Tool usage, specific function disabling, content size limits
- **Image Processing**: Size limits, quality settings for generation and emoji creation

### Message Processing Flow

1. WebSocket connection receives Mattermost messages
2. Filter for mentions or direct messages to bot
3. Extract and process attachments (images, PDFs, files)
4. Extract and fetch content from URLs in message
5. Build context from thread history
6. Send to OpenAI API with appropriate tools/functions
7. Process function calls if requested
8. Send response back to Mattermost

### Security Considerations

- Local IP/hostname filtering for URL extraction
- SSL certificate verification with certifi
- Content size limits for extracted data
- Optional user ID ignoring to prevent bot loops
- Defused XML parsing for security

### Dependencies Note

This project uses specific pinned versions in requirements.txt to maintain stability, especially for nodriver (≤0.36) and rembg (≤2.0.57) which have known breaking changes in newer versions.