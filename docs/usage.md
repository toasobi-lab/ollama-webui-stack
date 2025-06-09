# Usage Guide

## Getting Started

1. **Access the Web Interface**
   - Open your browser and navigate to http://localhost:3000
   - You'll see the Open WebUI interface

2. **Select a Model**
   - Click on the model selector in the top right
   - Choose from available models
   - If no models are available, pull one using:
     ```bash
     docker exec -it local-ai-chat-ollama-1 ollama pull <model-name>
     ```

## Chat Interface

### Basic Usage

1. **Starting a Chat**
   - Type your message in the input box at the bottom
   - Press Enter or click the send button
   - Wait for the AI's response

2. **Chat Features**
   - [Markdown](https://www.markdownguide.org/) support for formatted text
   - Code highlighting for programming languages
   - Message history persistence
   - Copy responses to clipboard

### Advanced Features

1. **System Prompts**
   - Access system prompts from the settings menu
   - Create custom prompts for different use cases
   - Save and reuse prompts

2. **Model Management**
   - Pull new models
   - Switch between models
   - View model information
   - Delete unused models

3. **Chat Management**
   - Create new chat sessions
   - Rename chat sessions
   - Delete chat history
   - Export chat logs

## Best Practices

1. **Model Selection**
   - Use smaller models for faster responses
   - Use larger models for complex tasks
   - Consider your hardware capabilities

2. **Prompt Engineering**
   - Be specific in your requests
   - Use system prompts for consistent behavior
   - Break complex tasks into smaller steps

3. **Resource Management**
   - Monitor system resources
   - Close unused chat sessions
   - Remove unused models to free space

## Keyboard Shortcuts

- `Ctrl + Enter`: Send message
- `Ctrl + K`: New chat
- `Ctrl + L`: Clear chat
- `Ctrl + S`: Save chat
- `Ctrl + /`: Show keyboard shortcuts

## Tips and Tricks

1. **Efficient Usage**
   - Use [Markdown](https://www.markdownguide.org/) for better formatting
   - Utilize code blocks for programming
   - Save important conversations

2. **Troubleshooting**
   - If responses are slow, try a smaller model
   - If memory usage is high, close unused chats
   - If the interface is unresponsive, refresh the page

3. **Customization**
   - Adjust theme settings
   - Configure default model
   - Set up custom system prompts

## Security Considerations

1. **Data Privacy**
   - All data is stored locally
   - No data is sent to external servers
   - Chat history is persisted in Docker volumes

2. **Access Control**
   - Change the default `WEBUI_SECRET_KEY`
   - Use authentication if needed
   - Regular backups of important data 