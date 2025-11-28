# Target Manipulation Utilities for Hawx Recon Agent
**Source:** Hawx-Recon-Agent\agent\utils\target.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `target.py` module within the Hawx Recon Agent is a utility library designed to facilitate the manipulation and evaluation of network targets. It provides a suite of functions that resolve hostnames to IP addresses, initialize target-related variables, evaluate conditions for command execution, substitute variables in command strings, and update open ports based on network scan outputs. This module is crucial for network reconnaissance tasks, enabling dynamic and flexible target handling, which is essential for automated security assessments and penetration testing.

The utility functions encapsulated in this module are reusable and can be integrated into various network scanning and reconnaissance tools. They abstract complex operations such as DNS resolution, regex-based transformations, and condition evaluations, making them valuable for enterprise-grade applications that require robust and adaptable target manipulation capabilities.

## Key Concepts & Principles
- **Hostname Resolution:** Converting a hostname to its corresponding IP address.
- **Target Variable Initialization:** Setting up target-specific variables using configuration-driven transformations.
- **Condition Evaluation:** Determining the applicability of commands based on predefined conditions.
- **Variable Substitution:** Replacing placeholders in command strings with actual target values.
- **Open Port Detection:** Identifying and updating lists of open ports from network scan outputs.

## Detailed Technical Analysis

### Hostname Resolution
The `resolve_ip` function is responsible for converting a given hostname into an IP address. It strips any URL scheme and path components before attempting DNS resolution. This function handles both direct IP addresses and hostnames, returning an empty string if resolution fails.

### Target Variable Initialization
The `initialize_target_variables` function initializes a dictionary of target-related variables. It uses regex patterns defined in a configuration dictionary to extract and transform target attributes, allowing for flexible and dynamic variable setup.

### Condition Evaluation
The `evaluate_condition` function assesses whether certain conditions are met for executing commands. It supports various condition types, such as checking for subdomains, verifying if a target is an IP address, and confirming if specific ports are open.

### Variable Substitution
The `substitute_variables` function replaces placeholders in command strings with actual values from the target variables dictionary. This allows for dynamic command generation based on the current target context.

### Open Port Detection
The `update_open_ports` function parses nmap scan outputs to identify open TCP ports, updating the target variables accordingly. This function uses regex to extract port numbers from the scan results.

## Enterprise Q&A Bank

1. **Q:** How does the `resolve_ip` function handle invalid hostnames?
   **A:** It returns an empty string if the hostname cannot be resolved to an IP address.

2. **Q:** Can the `initialize_target_variables` function handle multiple transformations?
   **A:** Yes, it applies all transformations specified in the configuration dictionary to the target.

3. **Q:** What happens if a condition type is not recognized in `evaluate_condition`?
   **A:** The function returns `False`, indicating that the condition is not met.

4. **Q:** How are open ports updated in the target variables?
   **A:** The `update_open_ports` function uses regex to extract open port numbers from nmap output and updates the `open_ports` set in the target variables.

5. **Q:** Is it possible to add custom condition types to `evaluate_condition`?
   **A:** Yes, the function can be extended to support additional condition types as needed.

## Actionable Takeaways
- Ensure that all target transformations are defined in the configuration for dynamic variable initialization.
- Regularly update the regex patterns used for condition evaluation to accommodate new target structures.
- Utilize the variable substitution function to dynamically generate commands tailored to specific targets.
- Integrate the open port detection logic with network scanning tools to maintain an up-to-date list of open ports.
- Extend the condition evaluation logic to support additional use cases and target attributes.

---
**Raw Content:**
```python
"""
Target manipulation utilities for Hawx Recon Agent.

Provides functions for target resolution, transformation, and condition evaluation.
"""

import re
import socket
from typing import Dict

def resolve_ip(target: str) -> str:
    """Resolve hostname to IP address."""
    # Remove scheme if present
    hostname = re.sub(r"^https?://", "", target)
    # Remove path and query components
    hostname = hostname.split("/")[0]

    try:
        if re.match(r"^(\d{1,3}\.){3}\d{1,3}$", hostname):
            return hostname
        return socket.gethostbyname(hostname)
    except socket.gaierror:
        return ""

def initialize_target_variables(target: str, layer0_config: Dict) -> Dict[str, str]:
    """Initialize all target-related variables using regex patterns."""
    target_vars = {
        "target": target,
        "ip": resolve_ip(target),
        "open_ports": set(),  # Will be populated during nmap scan
    }

    # Apply all transformations from config
    transformations = layer0_config.get("transformations", {})
    for var_name, transform in transformations.items():
        pattern = transform.get("pattern")
        group = transform.get("group", 1)
        if pattern:
            match = re.search(pattern, target)
            if match and len(match.groups()) >= group:
                target_vars[var_name] = match.group(group)

    return target_vars

def evaluate_condition(condition: Dict, target_vars: Dict[str, str]) -> bool:
    """Evaluate if a command should run based on its conditions."""
    cond_type = condition.get("type")

    if cond_type == "always":
        return True

    elif cond_type == "has_subdomain":
        return bool(target_vars.get("subdomain"))

    elif cond_type == "is_ip":
        target = target_vars.get("target", "")
        return bool(re.match(r"^(\d{1,3}\.){3}\d{1,3}$", target))

    elif cond_type == "port_open":
        port = condition.get("port")
        open_ports = target_vars.get("open_ports", set())
        return port in open_ports

    elif cond_type == "custom_regex":
        pattern = condition.get("pattern")
        if pattern:
            target = target_vars.get("target", "")
            return bool(re.search(pattern, target))

    return False

def substitute_variables(command: str, target_vars: Dict[str, str]) -> str:
    """Substitute all target variables in command string."""
    for var_name, value in target_vars.items():
        if isinstance(value, (str, int)):
            command = command.replace(f"{{{var_name}}}", str(value))
    return command

def update_open_ports(target_vars: Dict[str, str], nmap_output: str) -> None:
    """Update open ports from nmap scan output."""
    port_pattern = re.compile(r"(\d+)/tcp\s+open")
    open_ports = target_vars.get("open_ports", set())
    if isinstance(open_ports, set):
        for match in port_pattern.finditer(nmap_output):
            open_ports.add(int(match.group(1)))
```