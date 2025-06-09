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
- **Resource limits:**
  - It is recommended to set CPU and memory limits for this service to prevent it from using excessive system resources, especially on laptops or shared machines.

### 2. [Ollama API](https://github.com/ollama/ollama)
- Runs as a Docker container
- Manages AI model lifecycle
- Provides REST API endpoints for:
  - Model inference
  - Model management
  - Chat completions
- Default port: 11434
- **Important**: While the API runs in a container, the actual model inference happens on the host machine
- Can utilize host machine's GPU for acceleration (if available)
- **Resource limits:**
  - It is strongly recommended to set CPU and memory limits for this service to avoid overheating and ensure your system remains responsive. Adjust these limits based on your hardware and workload.

### 3. DeepSeek LLM Model
- Managed by Ollama
- **Runs directly on the host machine (not in container)**
- Can utilize host machine's GPU for acceleration
- Model files are persisted in Docker volume
- Benefits from direct hardware access for better performance
- **Resource usage:**
  - Choose model size and settings appropriate for your hardware. Monitor system temperature and performance, and adjust as needed.

## Resource Management

### CPU & Memory Management
- Always set reasonable CPU and memory limits for both Open WebUI and Ollama API in your `docker-compose.yml`.
- The right values depend on your hardware and how much you want to dedicate to AI workloads.
- Limiting resources helps prevent overheating, system slowdowns, and ensures other applications remain usable.

### GPU Management
- If your hardware supports GPU acceleration, consider limiting GPU usage if possible, especially on laptops.
- Monitor GPU temperature and performance.

### Heat Management Strategies
- Use smaller models for less heat and resource usage.
- Adjust model parameters (context window, batch size, temperature) to reduce load.
- Monitor your system temperature and resource usage regularly.
- If your device becomes hot or slow, reduce resource limits or stop the service.

### Best Practices
- Start with conservative resource limits and increase only if your system remains cool and responsive.
- Avoid running large models on laptops for extended periods.
- Consider using a dedicated server or cloud environment for heavy workloads.

## Configuration

### Resource Configuration

- Set resource limits in `docker-compose.yml` under each service's `deploy.resources` section.
- Avoid leaving services unlimited, especially on laptops or shared computers.
- Example (do not copy exact values, adjust for your hardware):
  ```yaml
  deploy:
    resources:
      limits:
        cpus: '...'    # Set a reasonable CPU limit
        memory: ...    # Set a reasonable memory limit
  ```
- Monitor your system and adjust as needed.

## Detailed System Architecture

> **Note on Diagrams:** We've attempted to use Mermaid for the architecture diagram below. However, complex Mermaid diagrams can sometimes have rendering inconsistencies or syntax challenges across different Markdown viewers. If you encounter rendering issues or wish to refine the diagram, we recommend using an online Mermaid Live Editor (e.g., [https://mermaid.live/](https://mermaid.live/)). You can copy the code block below, paste it into the editor, make adjustments, and then replace this block with your validated Mermaid code.

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
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                    Host Machine Resources                       │    │
│  │                                                                 │    │
│  │  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐         │    │
│  │  │    CPU      │    │    GPU      │    │   Memory    │         │    │
│  │  │             │    │  (if avail) │    │             │         │    │
│  │  └─────────────┘    └─────────────┘    └─────────────┘         │    │
│  │                                                                 │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘

Data Flow:
1. User Request → Open WebUI Frontend
2. Frontend → Backend API
3. Backend → Ollama API
4. Ollama API → Model Manager
5. Model Manager → LLM Model (runs on host machine, can use GPU)
6. Response flows back through the same path

Note: The LLM model runs directly on the host machine, not in a container,
allowing it to access and utilize the host's GPU for acceleration.
```

## Docker Isolation and Security

### Container Isolation

1. **Network Isolation**
   - Each container runs in its own network namespace
   - Custom bridge network for inter-container communication
   - Port mappings for external access
   - Network policies and access controls
   - DNS resolution between containers

2. **Process Isolation**
   - Each container has its own process tree
   - PID namespace isolation
   - Process resource limits
   - CPU and memory constraints
   - Process monitoring and control

3. **Filesystem Isolation**
   - Each container has its own root filesystem
   - Read-only base layers
   - Copy-on-write layers for modifications
   - Volume mounts for persistent data
   - Filesystem permissions and ownership

4. **Resource Isolation**
   - CPU quota and period limits
   - Memory limits and reservations
   - I/O bandwidth controls
   - Network bandwidth limits
   - Resource monitoring and alerts

### Volume Management

1. **Ollama Data Volume**
   - Persistent storage for model files
   - Model configurations and cache
   - Access control and permissions
   - Backup and restore capabilities
   - Size monitoring and management

2. **WebUI Data Volume**
   - Chat history persistence
   - User settings and preferences
   - System prompts storage
   - Data encryption options
   - Backup strategies

### Security Measures

1. **Container Security**
   - Non-root user execution
   - Capability restrictions
   - Seccomp profiles
   - AppArmor/SELinux policies
   - Regular security updates

2. **Network Security**
   - Internal network isolation
   - TLS encryption for API
   - Rate limiting
   - Access control lists
   - Network policy enforcement

3. **Data Security**
   - Volume encryption
   - Secure secret management
   - Regular backups
   - Access logging
   - Data integrity checks

## Communication Flow

1. User interacts with Open WebUI
   - HTTP/HTTPS requests from browser
   - WebSocket connections for real-time updates
   - REST API calls for model management

2. Open WebUI communicates with Ollama API
   - Internal Docker network communication
   - REST API calls for model operations
   - Streaming responses for real-time chat
   - Error handling and retry mechanisms

3. Ollama processes requests using the loaded model
   - Model loading and unloading
   - Request queuing and processing
   - Resource management
   - Response generation
   - **Direct access to host machine's GPU (if available)**

4. Responses are streamed back to Open WebUI
   - Chunked transfer encoding
   - Real-time token streaming
   - Progress updates
   - Error handling

5. Open WebUI renders the response with proper formatting
   - Markdown parsing
   - Code syntax highlighting
   - Real-time updates
   - Error display

## Configuration

### Environment Variables

- `WEBUI_SECRET_KEY`: For securing the web interface
  - Must be a strong, unique key in production
  - Used for session encryption
  - Affects authentication security

- `OLLAMA_API_BASE_URL`: For connecting to Ollama API
  - Internal Docker network communication
  - Configurable for different deployments
  - Affects service connectivity

### Resource Configuration

Resource limits can be adjusted in `docker-compose.yml`:

```yaml
deploy:
  resources:
    limits:
      cpus: '2.0'    # Adjust CPU limit
      memory: 4G     # Adjust memory limit
    reservations:
      cpus: '0.5'    # Adjust guaranteed CPU
      memory: 1G     # Adjust guaranteed memory
```

### Ports

- Open WebUI: 3000 (host) → 8080 (container)
- Ollama API: 11434 (host) → 11434 (container)

## Development Workflow

1. Local Development:
   - Use Docker Compose for local testing
   - Easy model switching and testing
   - Persistent storage for models and chat history
   - Resource limits can be adjusted for development

2. Production:
   - All services containerized
   - Persistent storage for model data
   - Configurable security settings
   - Resource limits based on server capacity 