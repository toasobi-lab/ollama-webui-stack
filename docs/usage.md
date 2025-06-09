# Usage Guide

> **Note:** Running large language models (LLMs) locally, especially on laptops, can use significant CPU and memory resources, which may cause your device to get warm or run slower. Monitor your system temperature and resource usage, and consider stopping the service if your device becomes uncomfortable. For most users, running smaller models or using a dedicated server/cloud is recommended for heavy workloads.

## Getting Started

To use the Open WebUI, follow these steps:

1.  **Start the Services:**
    Navigate to the project root directory and run:
    ```bash
    docker compose up -d
    ```
    This command starts both the `open-webui` and `ollama` services in detached mode.

2.  **Access Open WebUI:**
    Open your web browser and navigate to:
    [http://localhost:3000](http://localhost:3000)

3.  **Download a Model:**
    Once the Open WebUI interface loads, you will be prompted to download an Ollama model. You can choose from various models available on the Ollama library.

    *   **Recommendation:** Start with a smaller model like `deepseek-coder:1.3b` or `mistral:7b` to get started quickly and conserve resources.

4.  **Start Chatting!**
    After the model is downloaded, you can start interacting with your local AI assistant.

## 🤖 Model Selection Based on PC Specifications

Choosing the right LLM model for your local setup is crucial for performance and system health. Here's advice based on your PC specifications:

-   **CPU Cores:** Models utilize CPU cores for inference. More cores generally mean faster processing, but also more heat. Balance performance with thermal comfort.
-   **RAM (Memory):** Model size directly correlates with RAM requirements. Larger models (e.g., 13B+) need more RAM. Ensure you have sufficient free RAM beyond what your OS and other applications need.
-   **GPU (if available):** If your machine has a dedicated GPU (like NVIDIA or AMD) or an efficient integrated GPU (like Apple Silicon's M-series), models can leverage it for significant speedups. However, GPU usage also generates heat.
-   **Model Size (Parameters):**
    -   **Small Models (e.g., 1B-7B parameters):** Ideal for laptops and systems with limited RAM (8GB-16GB). They offer good speed and lower resource consumption (e.g., `deepseek-coder:1.3b`, `mistral:7b`, `llama2:7b`).
    -   **Medium Models (e.g., 8B-13B parameters):** Suitable for systems with 16GB+ RAM and decent CPU/GPU. They offer a good balance of capability and performance.
    -   **Large Models (e.g., 30B+ parameters):** Best for powerful desktops or servers with 32GB+ RAM and dedicated GPUs. Running these on laptops can lead to extreme heat and slow performance.
-   **Monitor System:** Always keep an eye on your CPU and GPU temperatures, and memory usage (e.g., using Activity Monitor on macOS). If your device gets too hot or unresponsive, consider pausing the service or switching to a smaller model.
-   **Fine-tuning Parameters:** Experiment with model parameters like `temperature` (lower for more consistent output, less compute), `context window size`, and `batch size` to balance performance and resource usage.

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