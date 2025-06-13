# Ollama WebUI Stack: Local AI Chat

> **Note:** Running large language models (LLMs) locally can use significant CPU and memory resources. Monitor your system temperature and resource usage. For most users, running smaller models or using a dedicated server/cloud is recommended for heavy workloads.

A local AI chat application that runs entirely on your machine using Docker Compose. This project uses [Open WebUI](https://github.com/open-webui/open-webui), a beautiful and feature-rich interface for Ollama, combined with local AI model deployment.

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Configuration](#-configuration)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

## 🚀 Features

- **Modern Web UI**: Real-time chat, Markdown, code highlighting, and theme support
- **Model Management**: Easy model switching, settings management, and updates
- **Rich Chat**: Persistent history, system prompts, custom templates, and conversation branching
- **Data Handling**: Docker volumes for persistence
- **Security**: Local-only deployment with optional authentication

## 🏗️ Architecture

### System Components

1. **Open WebUI**: A modern web interface built with React and FastAPI. Runs on port 8080 (mapped to 3000). Depends on Ollama API for model interactions.
2. **Ollama API**: The Docker-containerized model hosting service that manages AI model lifecycle. Runs on port 11434. Handles model loading, inference, and management.
3. **Docker Volumes**: 
   - `ollama_data`: Stores model files and Ollama configurations
   - `open_webui_data`: Persists chat history and user settings

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

1. **User Request Flow**: 
   - User input → Open WebUI Frontend (React)
   - Frontend → Open WebUI Backend (FastAPI)
   - Backend → Ollama API
   - Ollama API → Model Manager → LLM Model

2. **Response Flow**: 
   - LLM Model → Model Manager → Ollama API
   - Ollama API → Open WebUI Backend
   - Backend → Frontend → User

### Container Configuration

- **Open WebUI**:
  - Port: 3000 (host) → 8080 (container)
  - Environment: 
    - `OLLAMA_API_BASE_URL=http://ollama:11434/api`
    - `WEBUI_SECRET_KEY` (for authentication)
  - Volume: `open_webui_data:/app/backend/data`

- **Ollama**:
  - Port: 11434 (host) → 11434 (container)
  - Volume: `ollama_data:/root/.ollama`
  - Command: `serve`

### Volume Management

#### Ollama Data Volume
- **Location**: Managed by Docker
- **Contents**: Model files, configurations, and cache data
- **Mount Point**: `/root/.ollama` in the Ollama container
- **Purpose**: Persists downloaded models and their configurations

#### WebUI Data Volume
- **Location**: Managed by Docker
- **Contents**: Chat history, user settings, system prompts, and custom configurations
- **Mount Point**: `/app/backend/data` in the Open WebUI container
- **Purpose**: Maintains user data and preferences across container restarts

## 📋 Prerequisites

- **Software**: Docker (20.10.0+), Docker Compose (2.0.0+), Git
- **Hardware**: 
  - CPU: 2+ cores (4+ recommended)
  - RAM: 8GB (16GB+ recommended)
  - Storage: 10GB+ free space (SSD recommended)
  - GPU: NVIDIA GPU with CUDA support (recommended)
- **OS**: Linux, macOS, or Windows with WSL2

## 🚀 Quick Start

1. **Clone Repository**
```bash
git clone https://github.com/toasobi-lab/ollama-webui-stack.git
cd ollama-webui-stack
```

2. **Start Services**
```bash
docker compose up -d
docker compose ps  # Check status
```

3. **Access Web Interface**
- Open http://localhost:3000
- Download a model when prompted

## ⚙️ Configuration

### Ports
- WebUI: 3000 (host) → 8080 (container)
- Ollama API: 11434 (host) → 11434 (container)

### Resource Limits
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
1. **Port Conflicts**: Check ports 3000 and 11434, modify mappings in `docker-compose.yml`
2. **Volume Issues**: Check permissions and disk space
3. **Container Issues**: Check logs (`docker compose logs`), restart/rebuild containers
4. **Performance**: Monitor resources, verify model size matches hardware

### Help Resources
- [Ollama documentation](https://github.com/ollama/ollama)
- [Open WebUI repository](https://github.com/open-webui/open-webui)
- Open an issue in this repository

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add some amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details. 