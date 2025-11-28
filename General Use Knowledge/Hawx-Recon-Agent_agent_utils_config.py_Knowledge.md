# Configuration Loader for Hawx Recon Agent
**Source:** Hawx-Recon-Agent\agent\utils\config.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `config.py` file in the Hawx Recon Agent project is a critical component responsible for loading and managing configuration settings. It integrates environment variables and structured YAML configuration files to provide a centralized configuration management solution. This approach ensures that sensitive information, such as API keys, is securely managed while allowing for flexible configuration through YAML files. The file exemplifies best practices in configuration management by separating sensitive data from structured configuration and providing a fail-fast mechanism to ensure critical configurations are present.

This configuration loader is valuable for enterprise applications that require secure and flexible configuration management. By leveraging environment variables and YAML files, it supports a wide range of deployment scenarios, from local development to production environments. The design promotes security, maintainability, and scalability, making it a reusable pattern for other projects.

## Key Concepts & Principles
- **Environment Variable Management:** Securely load sensitive information using environment variables.
- **YAML Configuration:** Use structured YAML files for flexible and human-readable configuration.
- **Fail-Fast Principle:** Immediately raise errors when critical configuration is missing to prevent runtime failures.
- **Separation of Concerns:** Distinguish between sensitive data and general configuration settings.

## Detailed Technical Analysis

### Environment Variable Loading
The `load_dotenv()` function is used to load environment variables from a `.env` file. This is a common practice to manage sensitive information like API keys, ensuring they are not hardcoded in the source code.

### YAML Configuration Parsing
The file uses the `yaml.safe_load()` function to parse a YAML configuration file. This method is preferred for its ability to safely deserialize YAML content, preventing potential security risks associated with arbitrary code execution.

### Configuration Composition
The configuration loader composes a dictionary from both environment variables and YAML file contents. This dictionary includes critical settings such as the API key, provider, model, and optional parameters like base URL and host.

### Error Handling
The loader employs a fail-fast strategy by raising exceptions if the API key is missing or if the YAML configuration file is not found in expected locations. This ensures that the application does not proceed with incomplete configurations, reducing the risk of runtime errors.

## Enterprise Q&A Bank

1. **Q:** Why is it important to load sensitive values from environment variables?
   **A:** Loading sensitive values from environment variables enhances security by keeping them out of source code and version control systems.

2. **Q:** What is the advantage of using YAML for configuration files?
   **A:** YAML provides a human-readable format that is easy to edit and supports complex data structures, making it ideal for configuration files.

3. **Q:** How does the fail-fast principle benefit configuration management?
   **A:** It ensures that configuration issues are detected early, preventing the application from running with incomplete or incorrect settings.

4. **Q:** What are the risks of not handling missing configuration files properly?
   **A:** It can lead to runtime errors, application crashes, or unexpected behavior due to missing critical settings.

5. **Q:** How can this configuration loader pattern be reused in other projects?
   **A:** By abstracting the loading logic into a utility function, it can be adapted to different projects with similar configuration needs.

## Actionable Takeaways
- Always separate sensitive information from general configuration settings.
- Use environment variables to manage sensitive data securely.
- Employ YAML files for flexible and human-readable configuration management.
- Implement a fail-fast strategy to catch configuration errors early.
- Ensure configuration files are located in predictable and secure locations.

---
**Raw Content:**
```python
"""
Configuration loader for Hawx Recon Agent.

Loads sensitive values from .env and structured configuration from config.yaml.
"""

import os
import yaml
from dotenv import load_dotenv


def load_config(config_path=None):
    """Load config.yaml and .env (for API key only)"""
    load_dotenv()  # Load environment variables from .env file

    # Load sensitive values (API key)
    api_key = os.getenv("LLM_API_KEY")
    if not api_key:
        # Fail fast if API key is missing
        raise ValueError("LLM_API_KEY must be set in the .env file.")

    # Load structured config from YAML file
    # Try default locations
    if config_path is None:
        if os.path.exists("/opt/agent/configs/config.yaml"):
            config_path = "/opt/agent/configs/config.yaml"
        else:
            raise FileNotFoundError("config.yaml not found in known locations")

    with open(config_path, "r", encoding="utf-8") as f:
        raw = yaml.safe_load(f)

    # Compose config dictionary from loaded values
    config = {
        "api_key": api_key,
        "provider": raw["llm"]["provider"],
        "model": raw["llm"]["model"],
        "base_url": raw["llm"].get("base_url"),
        "host": raw.get("ollama", {}).get("host"),
        "context_length": raw["llm"].get("context_length", 8192),
    }

    return config
```