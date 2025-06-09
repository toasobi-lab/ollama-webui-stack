# Setup Guide

> **Note:** Running large language models (LLMs) locally, especially on laptops, can use significant CPU and memory resources, which may cause your device to get warm or run slower. Monitor your system temperature and resource usage, and consider stopping the service if your device becomes uncomfortable. For most users, running smaller models or using a dedicated server/cloud is recommended for heavy workloads.

## Prerequisites

Before you begin, ensure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/) (version 20.10.0 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 2.0.0 or higher)
- [Git](https://git-scm.com/downloads)

## Installation Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/toasobi-lab/ollama-webui-stack.git
   cd ollama-webui-stack
   ```

2. **Configure Environment**
   - The default configuration in `docker-compose.yml` should work out of the box
   - For production use, change the `WEBUI_SECRET_KEY` in `docker-compose.yml`

3. **Start the Services**
   ```bash
   docker compose up -d
   ```

4. **Verify Installation**
   - Open WebUI should be available at http://localhost:3000
   - Ollama API should be accessible at http://localhost:11434

## Pulling Models

After the services are running, you can pull AI models using Ollama:

```bash
# Pull a model (example with deepseek-coder)
docker exec -it local-ai-chat-ollama-1 ollama pull deepseek-coder

# List available models
docker exec -it local-ai-chat-ollama-1 ollama list
```

## Data Persistence

The application uses two Docker volumes for data persistence:

1. `ollama_data`: Stores AI models and their data
   - Location: Managed by Docker
   - Contains: Model files, configurations

2. `open_webui_data`: Stores chat history and settings
   - Location: Managed by Docker
   - Contains: Chat logs, user settings, system prompts

## Backup and Restore

### Backup Volumes
```bash
# Backup Ollama data
docker run --rm -v local-ai-chat_ollama_data:/source -v $(pwd):/backup alpine tar -czf /backup/ollama_backup.tar.gz -C /source .

# Backup Open WebUI data
docker run --rm -v local-ai-chat_open_webui_data:/source -v $(pwd):/backup alpine tar -czf /backup/webui_backup.tar.gz -C /source .
```

### Restore Volumes
```bash
# Restore Ollama data
docker run --rm -v local-ai-chat_ollama_data:/target -v $(pwd):/backup alpine sh -c "cd /target && tar -xzf /backup/ollama_backup.tar.gz"

# Restore Open WebUI data
docker run --rm -v local-ai-chat_open_webui_data:/target -v $(pwd):/backup alpine sh -c "cd /target && tar -xzf /backup/webui_backup.tar.gz"
```

## Troubleshooting

### Common Issues

1. **Port Conflicts**
   - If port 3000 is already in use, modify the port mapping in `docker-compose.yml`
   - If port 11434 is already in use, ensure no other Ollama instance is running

2. **Volume Issues**
   - If data persistence isn't working, check volume permissions
   - Use `docker volume ls` to verify volumes exist
   - Use `docker volume inspect <volume_name>` to check volume details

3. **Container Issues**
   - Check container logs: `docker compose logs`
   - Restart containers: `docker compose restart`
   - Rebuild containers: `docker compose up -d --build`

### Getting Help

- Check the [Ollama documentation](https://github.com/ollama/ollama)
- Visit the [Open WebUI repository](https://github.com/open-webui/open-webui)
- Open an issue in this repository 