# Local AI Chat

A local AI chat application that runs entirely on your machine using Docker Compose. This project uses [Open WebUI](https://github.com/open-webui/open-webui), a beautiful and feature-rich interface for Ollama, combined with local AI model deployment.

## 🚀 Features

- Modern web interface powered by Open WebUI
- Local AI model deployment using [Ollama](https://github.com/ollama/ollama)
- Docker-based setup for easy deployment
- Real-time chat interactions
- Model management through Ollama
- Multiple model support
- Chat history and persistence
- Markdown support
- Code highlighting
- System prompts management
- Data persistence using Docker volumes
- Easy backup and restore capabilities

## 🏗️ Architecture

The project consists of three main components:

1. **Open WebUI**: A modern, feature-rich web interface for interacting with AI models
2. **Ollama API**: The model hosting service that manages the AI model
3. **DeepSeek LLM Model**: The actual AI model being served

## 📋 Prerequisites

- [Docker](https://docs.docker.com/get-docker/) (version 20.10.0 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 2.0.0 or higher)
- [Git](https://git-scm.com/downloads)

## 🚀 Quick Start

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/local-ai-chat.git
   cd local-ai-chat
   ```

2. Start the services:
   ```bash
   docker compose up -d
   ```

3. Access the web interface at http://localhost:3000

4. Pull a model (example):
   ```bash
   docker exec -it local-ai-chat-ollama-1 ollama pull deepseek-coder
   ```

## 📁 Project Structure

```
local-ai-chat/
├── docker-compose.yml    # Docker Compose configuration
├── README.md            # Project documentation
├── .gitignore          # Git ignore file
└── docs/               # Documentation
    ├── architecture.md # Architecture details
    ├── setup.md       # Setup guide
    └── usage.md       # Usage guide
```

## 💾 Data Persistence

The application uses Docker volumes for data persistence:

1. `ollama_data`: Stores AI models and their data
   - Model files
   - Model configurations
   - Cache data

2. `open_webui_data`: Stores application data
   - Chat history
   - User settings
   - System prompts
   - Custom configurations

## 📚 Documentation

Detailed documentation can be found in the `docs/` directory:
- [Architecture Overview](docs/architecture.md)
- [Setup Guide](docs/setup.md)
- [Usage Guide](docs/usage.md)

## 🔧 Configuration

### Environment Variables

The following environment variables can be configured in `docker-compose.yml`:

- `WEBUI_SECRET_KEY`: Secret key for the web interface (change this in production)
- `OLLAMA_API_BASE_URL`: URL for the Ollama API (default: http://ollama:11434/api)

### Ports

- Open WebUI: 3000 (host) → 8080 (container)
- Ollama API: 11434 (host) → 11434 (container)

## 🔄 Backup and Restore

### Backup
```bash
# Backup Ollama data
docker run --rm -v local-ai-chat_ollama_data:/source -v $(pwd):/backup alpine tar -czf /backup/ollama_backup.tar.gz -C /source .

# Backup Open WebUI data
docker run --rm -v local-ai-chat_open_webui_data:/source -v $(pwd):/backup alpine tar -czf /backup/webui_backup.tar.gz -C /source .
```

### Restore
```bash
# Restore Ollama data
docker run --rm -v local-ai-chat_ollama_data:/target -v $(pwd):/backup alpine sh -c "cd /target && tar -xzf /backup/ollama_backup.tar.gz"

# Restore Open WebUI data
docker run --rm -v local-ai-chat_open_webui_data:/target -v $(pwd):/backup alpine sh -c "cd /target && tar -xzf /backup/webui_backup.tar.gz"
```

## 🤝 Contributing

Contributions are welcome! Please read our contributing guidelines before submitting pull requests.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Open WebUI](https://github.com/open-webui/open-webui) for the beautiful web interface
- [Ollama](https://github.com/ollama/ollama) for the AI model hosting 