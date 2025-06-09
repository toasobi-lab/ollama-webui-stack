# Architecture Overview

## System Components

### 1. [Open WebUI](https://github.com/open-webui/open-webui)
- Official web interface for Ollama
- Features:
  - Modern, responsive design
  - Multiple model support
  - Chat history and persistence
  - Markdown support
  - Code highlighting
  - System prompts management
  - Model management interface
  - User authentication (optional)
- Runs on port 8080 (mapped to 3000)

### 2. [Ollama API](https://github.com/ollama/ollama)
- Runs as a Docker container
- Manages AI model lifecycle
- Provides REST API endpoints for:
  - Model inference
  - Model management
  - Chat completions
- Default port: 11434

### 3. DeepSeek LLM Model
- Managed by Ollama
- Runs locally on the host machine
- Provides the actual AI capabilities
- Model files are persisted in Docker volume

## Communication Flow

1. User interacts with Open WebUI
2. Open WebUI communicates with Ollama API
3. Ollama processes requests using the loaded model
4. Responses are streamed back to Open WebUI
5. Open WebUI renders the response with proper formatting

## Data Flow

```
User → Open WebUI → Ollama API → LLM Model
   ↑                                    ↓
   └────────────────────────────────────┘
```

## Security Considerations

- All components run locally
- No external API calls required
- Data stays on user's machine
- Docker containers are isolated
- Optional authentication in Open WebUI
- Configurable secret key for additional security

## Scalability

- Open WebUI can handle multiple concurrent users
- Ollama can process multiple requests simultaneously
- Multiple models can be loaded and managed
- Chat history is persisted

## Configuration

### Environment Variables

- `WEBUI_SECRET_KEY`: For securing the web interface
- `OLLAMA_API_BASE_URL`: For connecting to Ollama API

### Ports

- Open WebUI: 3000 (host) → 8080 (container)
- Ollama API: 11434 (host) → 11434 (container)

## Development Workflow

1. Local Development:
   - Use Docker Compose for local testing
   - Easy model switching and testing
   - Persistent storage for models and chat history

2. Production:
   - All services containerized
   - Persistent storage for model data
   - Configurable security settings 