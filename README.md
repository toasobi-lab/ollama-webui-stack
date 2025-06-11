# Ollama WebUI Stack: Local AI Chat

> **Note:** Running large language models (LLMs) locally, especially on laptops, can use significant CPU and memory resources, which may cause your device to get warm or run slower. Monitor your system temperature and resource usage, and consider stopping the service if your device becomes uncomfortable. For most users, running smaller models or using a dedicated server/cloud is recommended for heavy workloads.

A local AI chat application that runs entirely on your machine using Docker Compose. This project uses [Open WebUI](https://github.com/open-webui/open-webui), a beautiful and feature-rich interface for Ollama, combined with local AI model deployment.

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Model Selection Guide](#-model-selection-guide)
- [Resource Management](#-resource-management)
- [Data Persistence](#-data-persistence)
- [Backup and Restore](#-backup-and-restore)
- [Configuration](#-configuration)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)
- [Acknowledgments](#-acknowledgments)

## 🚀 Features

- **Modern & Responsive Web UI**: Enjoy a beautiful interface with real-time chat, Markdown, code highlighting, and theme support.
- **Comprehensive Model Management**: Easily switch between models, manage their settings, and perform updates or cleanups.
- **Rich Chat Capabilities**: Benefit from persistent chat history, system prompts, custom templates, chat export, and conversation branching.
- **Seamless Data Handling**: Utilizes Docker volumes for secure data persistence, automated backups, and efficient resource monitoring.
- **Enhanced Security**: Local-only deployment with optional authentication, encrypted storage, and resource isolation.

## 🏗️ Architecture

### System Components

1. **Open WebUI**: The modern, feature-rich web interface for interacting with AI models. Runs on port 8080 (mapped to 3000).
2. **Ollama API**: The Docker-containerized model hosting service that manages AI model lifecycle and provides REST API endpoints. Default port: 11434.
3. **AI Models**: The actual AI models served by Ollama, running directly on the host machine (not in a container) for GPU acceleration, with model files persisted in Docker volumes.

### System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              Host Machine                               │
│                                                                         │
│  ┌───────────────────┐    ┌───────────────────┐    ┌─────────────────┐  │
│  │   Open WebUI      │    │    Ollama API     │    │  Docker Engine  │  │
│  │   Container       │    │    Container      │    │                 │  │
│  │                   │    │                   │    │                 │  │
│  │  ┌───────────┐    │    │  ┌───────────┐    │    │  ┌───────────┐  │  │
│  │  │  Frontend │    │    │  │  API      │    │    │  │ Volumes   │  │  │
│  │  │  (React)  │    │    │  │  Server   │    │    │  │           │  │  │
│  │  └───────────┘    │    │  └───────────┘    │    │  │  ┌─────┐  │  │  │
│  │        │          │    │        │          │    │  │  │ollama│  │  │  │
│  │  ┌───────────┐    │    │  ┌───────────┐    │    │  │  │data │  │  │  │
│  │  │  Backend  │    │    │  │  Model    │    │    │  │  └─────┘  │  │  │
│  │  │  (FastAPI)│    │    │  │  Manager  │    │    │  │           │  │  │
│  │  └───────────┘    │    │  └───────────┘    │    │  │  ┌─────┐  │  │  │
│  └─────────┬─────────┘    └─────────┬─────────┘    │  │  │webui│  │  │  │
│            │                        │              │  │  │data │  │  │  │
│            │                        │              │  │  └─────┘  │  │  │
│            │                        │              │  └───────────┘  │  │
│            │                        │              └─────────────────┘  │
│            │                        │                                   │
│            └────────────────────────┴───────────────────────────────────┘
```

### Data Flow

1. **User Request Flow**: User input → Open WebUI Frontend → Backend API → Ollama API → Model Manager → LLM Model.
2. **Response Flow**: LLM Model → Model Manager → Ollama API → Backend → Frontend → User.

## 📋 Prerequisites

### Required Software

- [Docker](https://docs.docker.com/get-docker/) (version 20.10.0 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 2.0.0 or higher)
- [Git](https://git-scm.com/downloads)

### System Requirements

- **CPU**: 2+ cores (4+ recommended for better performance, 8+ for optimal).
- **RAM**: 8GB (16GB+ recommended, 32GB+ for large models).
- **Storage**: 10GB+ free space (SSDs recommended for performance).
- **GPU**: NVIDIA GPU with CUDA support highly recommended for acceleration.
- **OS**: Linux, macOS, or Windows with WSL2.
- **Network**: Internet connection required for initial model downloads.

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/toasobi-lab/ollama-webui-stack.git
cd ollama-webui-stack
```

### 2. Configure Environment
```bash
# Copy the example environment file
cp .env.example .env

# Edit the environment file with your settings
nano .env
```

### 3. Start the Services
```bash
# Start all services in detached mode
docker compose up -d

# Check service status
docker compose ps
```

### 4. Access the Web Interface
- Open your browser and navigate to http://localhost:3000
- The interface should load automatically
- You'll be prompted to download a model if none is available

### 5. Pull Your First Model
```bash
# Pull a small model to start with
docker exec -it local-ai-chat-ollama-1 ollama pull deepseek-coder:1.3b

# Verify the model was pulled
docker exec -it local-ai-chat-ollama-1 ollama list
```

## 🤖 Model and Hardware Selection Guide

- **Small Models (1B-7B params)**: Ideal for laptops (8-16GB RAM). E.g., `deepseek-coder:1.3b`, `mistral:7b`. Fast response, low resource use.
- **Medium Models (8B-13B params)**: For 16GB+ RAM and decent CPU/GPU. E.g., `llama2:13b`, `codellama:13b`. Balanced capability and performance.
- **Large Models (30B+ params)**: Requires powerful desktops/servers (32GB+ RAM, dedicated GPUs). E.g., `llama2:70b`, `mistral:70b`. Best performance for complex tasks.
- **CPU**: More cores boost processing but increase heat.
- **RAM**: Directly relates to model size; ensure 4GB+ free for system.
- **GPU**: NVIDIA with CUDA highly recommended for speed.
- **Storage**: SSDs for performance; models need substantial disk space.

## ⚙️ Resource Management

### Best Practices

#### 1. Set Resource Limits
```yaml
services:
  ollama:
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 4G
  webui:
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 2G
```

#### 2. Model Selection
- Start with smaller models and scale up only if necessary, considering your hardware capabilities and monitoring performance.

#### 3. Performance Optimization
- Adjust model parameters (context window, temperature, batch size) based on available memory and desired response speed.

## 💾 Data Persistence

### Volume Management

#### Ollama Data Volume
- **Location**: Managed by Docker
- **Contents**: Model files, configurations, and cache data.
- **Backup**: Regular backups are highly recommended.

#### WebUI Data Volume
- **Location**: Managed by Docker
- **Contents**: Chat history, user settings, system prompts, and custom configurations.
- **Backup**: Regular backups are highly recommended.

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

## 🔧 Configuration

### Environment Variables

#### WebUI Configuration
- `WEBUI_SECRET_KEY`: Secret key for the web interface. Must be a strong, unique key in production, used for session encryption and authentication security.

#### Ollama Configuration
- `OLLAMA_API_BASE_URL`: URL for the Ollama API (default: `http://ollama:11434/api`). Configurable for different deployments and affects service connectivity.

### Ports

#### Service Ports
- Open WebUI: `3000` (host) → `8080` (container)
- Ollama API: `11434` (host) → `11434` (container)

### Resource Limits

#### Example Configuration
```yaml
services:
  ollama:
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 4G
  webui:
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 2G
```

## 🔍 Troubleshooting

### Common Issues

#### 1. Port Conflicts
- Check for services using ports `3000` and `11434`.
- Modify port mappings in `docker-compose.yml`.
- Ensure no other Ollama instance is running.
- Check firewall settings.

#### 2. Volume Issues
- Verify volume permissions and existence.
- Inspect volume details using `docker volume inspect <volume_name>`.
- Ensure sufficient disk space.

#### 3. Container Issues
- Check container logs (`docker compose logs`).
- Restart containers (`docker compose restart`).
- Rebuild containers (`docker compose up -d --build`).
- Monitor resource usage (`docker stats`).

#### 4. Performance Issues
- Monitor system resources and check for thermal throttling.
- Verify model size matches hardware capabilities.
- Adjust resource limits in `docker-compose.yml` if needed.

### Getting Help

- Check the [Ollama documentation](https://github.com/ollama/ollama).
- Visit the [Open WebUI repository](https://github.com/open-webui/open-webui).
- Open an issue in this repository.

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