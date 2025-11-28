# Layer 0 Initial Scan Configuration for Hawx-Recon-Agent
**Source:** Hawx-Recon-Agent\configs\layer0.yaml  
**Ingestion Date:** 2025-11-28

## Executive Summary
The "Layer 0 Initial Scan Configuration" file is a critical component of the Hawx-Recon-Agent, designed to facilitate initial reconnaissance and scanning operations. This configuration file outlines the dynamic variables and transformation patterns necessary for parsing and analyzing target inputs, such as IP addresses, hostnames, and URLs. It defines a set of commands for host and website scanning, leveraging tools like nmap and whatweb to gather comprehensive information about the target's network and web technologies.

This configuration is valuable for enterprises seeking to automate and streamline their reconnaissance processes. By defining reusable patterns and commands, it ensures consistent and efficient data collection, which is crucial for security assessments and vulnerability management.

## Key Concepts & Principles
- **Dynamic Variables:** Placeholders for target-specific data, enabling flexible command execution.
- **Transformation Patterns:** Regular expressions used to extract specific components from target inputs.
- **Command Definitions:** Predefined scanning commands with descriptions, timeouts, and conditions.
- **Condition Types:** Criteria that determine when specific commands should be executed.
- **Global Configuration:** Settings that affect the overall behavior of the scanning process.

## Detailed Technical Analysis

### Dynamic Variables and Transformation Patterns
The configuration file defines several dynamic variables, such as `{target}`, `{domain}`, and `{ip}`, which are used to customize command execution based on the target's characteristics. Transformation patterns utilize regular expressions to extract components like the domain, root domain, subdomain, and top-level domain from URLs. These patterns ensure that the scanning commands are accurately tailored to the target's structure.

### Command Definitions
The file specifies commands for both host and website modes. For instance, the `nmap_full_scan` command performs a comprehensive port scan with service version detection, while the `whatweb_scan` identifies web technologies and CMS platforms. Each command includes a description, execution command, timeout, and conditions, ensuring that the scans are thorough and contextually appropriate.

### Condition Types
Conditions dictate when specific commands should be executed. The configuration supports various condition types, such as "always," "has_subdomain," and "is_ip," allowing for conditional execution based on the target's attributes. This flexibility enables more targeted and efficient scanning operations.

### Global Configuration
Global settings, such as `max_retries` and `dns_resolver`, provide overarching control over the scanning process. These settings ensure reliability and adaptability, allowing the system to handle failures gracefully and customize DNS resolution.

## Enterprise Q&A Bank

1. **What is the purpose of dynamic variables in this configuration?**
   Dynamic variables allow for the customization of commands based on the specific attributes of the target, ensuring accurate and relevant scanning.

2. **How do transformation patterns enhance the scanning process?**
   Transformation patterns extract key components from target inputs, enabling precise command tailoring and improving the accuracy of the scans.

3. **What role do condition types play in command execution?**
   Condition types determine the circumstances under which specific commands are executed, allowing for context-sensitive scanning operations.

4. **Why is the `nmap_full_scan` command critical in host mode?**
   It performs a comprehensive port scan with service version detection, providing detailed insights into the target's network services.

5. **How does the global configuration impact the scanning process?**
   Global settings, such as retry limits and DNS resolvers, ensure the scanning process is robust, adaptable, and capable of handling various scenarios.

## Actionable Takeaways
- Define dynamic variables to customize command execution based on target attributes.
- Utilize transformation patterns to extract and analyze key components from target inputs.
- Implement condition types to enable context-sensitive command execution.
- Configure global settings to enhance the reliability and adaptability of the scanning process.
- Regularly update and review command definitions to ensure they remain effective and relevant.

---
**Raw Content:**
```yaml
# Layer 0 Initial Scan Configuration
# Dynamic Variables:
# {target}     - Raw target input (IP, hostname, or URL)
# {domain}     - Extracted domain from target (e.g., example.com from https://www.example.com)
# {root_domain} - Root domain (e.g., example.com from sub.example.com)
# {subdomain}  - Subdomain part only (e.g., www from www.example.com)
# {ip}         - IP address if target is an IP, or resolved IP if hostname
# {port_80}    - Include command only if port 80 is open
# {port_443}   - Include command only if port 443 is open
# {tld}        - Top level domain (e.g., com from example.com)
# {no_scheme}  - URL without scheme (e.g., www.example.com from https://www.example.com)

# Target transformation patterns
transformations:
  domain:
    pattern: "^(?:https?://)?(?:www\\.)?([^/]+)"
    group: 1
  root_domain:
    pattern: "(?:[^.]+\\.)*([^.]+\\.[^.]+)$"
    group: 1
  subdomain:
    pattern: "^(?:https?://)?([^.]+)\\."
    group: 1
  tld:
    pattern: "\\.([^.]+)$"
    group: 1
  no_scheme:
    pattern: "^(?:https?://)?(.*)"
    group: 1

host_mode:
  commands:
    - name: "nmap_full_scan"
      description: "Full port scan with service version detection and default scripts"
      command: "nmap -sC -sV -p- {target}"
      timeout: 7200  # 2 hours
      required: true # This scan must complete for the workflow to continue
      conditions:
        - type: "always"  # Always run this command

website_mode:
  commands:
    - name: "whatweb_scan"
      description: "Web technology and CMS detection"
      command: "whatweb {target}"
      timeout: 300  # 5 minutes
      required: true
      conditions:
        - type: "always"

    - name: "subfinder_root_domain"
      description: "Subdomain enumeration on root domain"
      command: "subfinder -d {root_domain}"
      timeout: 600  # 10 minutes
      required: false
      conditions:
        - type: "always"  # Always run to discover subdomains

# Condition types supported:
# - "always": Always run the command
# - "has_subdomain": Run only if target has a subdomain
# - "is_ip": Run only if target is an IP address
# - "port_open": Run only if specified port is open
# - "custom_regex": Run only if target matches custom regex pattern
#
# Example custom_regex usage:
# conditions:
#   - type: "custom_regex"
#     pattern: "^https?://[^/]+\\.edu\\." # Only run on .edu domains
#   - type: "custom_regex"
#     pattern: "^192\\.168\\." # Only run on 192.168.* IP addresses
#   - type: "custom_regex"
#     pattern: "^(?!www\\.).*\\.com$" # Run on .com domains except www.

# Global configuration
global:
  max_retries: 2  # Maximum number of retries for failed commands
  parallel: false # Whether to run commands in parallel (future feature)
  dns_resolver: "8.8.8.8"  # Default DNS resolver for lookups
  timeout_multiplier: 1.5  # Multiplier for timeouts on retry
```