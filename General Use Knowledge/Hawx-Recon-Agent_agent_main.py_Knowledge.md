# Hawx Recon Agent: Main Entry Point
**Source:** Hawx-Recon-Agent\agent\main.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `main.py` file serves as the primary entry point for the Hawx Recon Agent, a tool designed for automated reconnaissance using a Large Language Model (LLM). This script is responsible for initializing the LLM client, loading necessary configurations, and executing a reconnaissance workflow on a specified target. The script is intended to be executed within a Docker container, ensuring a controlled and consistent environment for operations. The Hawx Recon Agent is tailored for cybersecurity professionals seeking to leverage AI for efficient and effective reconnaissance tasks.

## Key Concepts & Principles
- **LLM Client Initialization:** Establishes a connection with a language model provider using API keys and configuration settings.
- **Configuration Management:** Utilizes external configuration files (`config.yaml` and `.env`) to manage settings, promoting flexibility and security.
- **Reconnaissance Workflow:** Executes a series of steps to gather information about a target, which can be an IP/domain or a full URL.
- **Interactive Mode:** Offers an option for user interaction during the reconnaissance process, enhancing adaptability.
- **Docker Integration:** Designed to run within a Docker container, ensuring environment consistency and ease of deployment.

## Detailed Technical Analysis

### Initialization and Configuration
The script begins by ensuring that the necessary command-line arguments are provided, which include the target, number of steps, interactive mode flag, and target mode. It then loads configuration settings from external files, allowing for dynamic adjustments without altering the codebase.

### LLM Client Setup
The LLM client is initialized with parameters such as API key, provider, and model type. This setup is crucial for establishing a secure and efficient connection to the language model, which is central to the agent's functionality.

### Workflow Execution
The `ReconExecutor` class is instantiated with the LLM client and target details. It orchestrates the reconnaissance process, executing a predefined number of steps to gather intelligence on the target. This modular approach allows for scalability and customization of the workflow.

### User Interaction and Feedback
The script includes a function to display an ASCII art banner, providing branding and user feedback. This enhances the user experience by clearly indicating the start of the process.

## Enterprise Q&A Bank

1. **Q:** What is the primary purpose of the `main.py` script in the Hawx Recon Agent?
   **A:** It initializes the LLM client, loads configurations, and starts the reconnaissance workflow for a specified target.

2. **Q:** How does the script ensure configuration flexibility?
   **A:** By loading settings from external files like `config.yaml` and `.env`, allowing changes without modifying the code.

3. **Q:** What role does the LLM client play in the Hawx Recon Agent?
   **A:** It connects to a language model provider to leverage AI capabilities for the reconnaissance process.

4. **Q:** Why is Docker integration important for this script?
   **A:** It ensures a consistent and controlled environment, simplifying deployment and execution.

5. **Q:** How does the script handle user interaction during the reconnaissance process?
   **A:** It offers an interactive mode that allows users to engage with the process, providing flexibility and control.

## Actionable Takeaways
- Ensure all necessary command-line arguments are provided before execution.
- Regularly update configuration files to maintain security and adapt to new requirements.
- Leverage Docker for consistent deployment and execution environments.
- Consider the interactive mode for scenarios requiring user input or decision-making.
- Utilize the LLM client effectively by keeping API keys and model configurations up to date.

---
**Raw Content:**
```python
"""
Main entry point for the Hawx Recon Agent.

This script initializes the LLM client, loads configuration, and starts the reconnaissance workflow
for a given target machine IP. It is intended to be run inside the Docker container as the main process.
"""

from agent.utils.config import load_config
from agent.llm.llm_client import LLMClient
from agent.workflow.executor import ReconExecutor
import sys
import os


def print_banner():
    """Print the ASCII art banner for branding and user feedback."""
    os.system("clear" if os.name == "posix" else "cls")  # Clears terminal
    print(
        r"""
██╗  ██╗ █████╗ ██╗    ██╗██╗  ██╗
██║  ██║██╔══██╗██║    ██║╚██╗██╔╝
███████║███████║██║ █╗ ██║ ╚███╔╝ 
██╔══██║██╔══██║██║███╗██║ ██╔██╗ 
██║  ██║██║  ██║╚███╔███╔╝██╔╝ ██╗
╚═╝  ╚═╝╚═╝  ╚═╝ ╚══╝╚══╝ ╚═╝  ╚═╝

 H A W X | LLM-based Autonomous Recon Agent ⚡
    """
    )


def main():
    """Main entry point for the Hawx Recon Agent."""
    # Ensure the script is called with at least the target and mode
    if len(sys.argv) < 4:
        print("Usage: main.py <target> <steps> <interactive> <target_mode>")
        print("  <target>: IP/domain (for host) or full URL (for website)")
        print("  <target_mode>: 'host' or 'website'")
        sys.exit(1)

    # Load configuration from config.yaml and .env
    config = load_config()
    target = sys.argv[1]  # Target machine IP or domain
    # Number of workflow steps/layers
    steps = int(sys.argv[2]) if len(sys.argv) > 2 else 3
    interactive = (
        sys.argv[3].lower() == "true" if len(sys.argv) > 3 else False
    )  # Interactive mode flag
    target_mode = sys.argv[4] if len(sys.argv) > 4 else "host"  # Target mode

    # Initialize the LLM client with config values
    llm_client = LLMClient(
        api_key=config["api_key"],
        provider=config["provider"],
        model=config["model"],
        ollama_host=config.get("host"),
        context_length=config.get("context_length", 8192),
    )

    # Create the workflow executor and start the recon workflow
    executor = ReconExecutor(llm_client, target, interactive, target_mode)
    executor.workflow(steps)


if __name__ == "__main__":
    print_banner()  # Show banner at startup
    main()  # Start the main workflow
```