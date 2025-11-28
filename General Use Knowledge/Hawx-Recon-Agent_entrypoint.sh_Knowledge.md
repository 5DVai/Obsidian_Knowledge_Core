# Hawx Recon Agent Entrypoint Script
**Source:** Hawx-Recon-Agent\entrypoint.sh  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `entrypoint.sh` script for the Hawx Recon Agent is a robust initialization script designed to configure and launch a containerized environment for network reconnaissance tasks. It demonstrates several enterprise-grade practices, such as dynamic configuration through environment variables, conditional execution based on available resources, and integration with external services like VPNs and testing frameworks. This script is valuable for its reusable patterns in container initialization, network configuration, and automated testing, making it a strong candidate for inclusion in a software engineering knowledge base.

## Key Concepts & Principles
- **Dynamic Configuration:** Uses environment variables to adapt behavior based on runtime conditions.
- **Conditional Logic:** Implements checks and balances to ensure prerequisites are met before proceeding.
- **Network Configuration:** Integrates VPN setup and network probing to ensure secure and reliable connectivity.
- **Testing Integration:** Supports automated testing within the container environment.
- **Fail-Safe Mechanisms:** Includes logic to handle failures gracefully, ensuring safe exits.

## Detailed Technical Analysis

### Dynamic Configuration and Initialization
The script begins by setting up the environment with `set -e` to ensure that any command failure halts execution. It checks for mounted files and appends custom host entries if provided, showcasing a flexible configuration approach.

### VPN Setup and Validation
A critical feature is the conditional VPN setup using OpenVPN. The script checks for the presence of an `OVPN_FILE` and attempts to establish a VPN connection, verifying its success by checking for the `tun0` interface. This pattern ensures secure network communication, which is essential for enterprise applications.

### Target Resolution and Probing
The script determines the target type (host or website) based on environment variables and performs network probing using Nmap for host targets. This ensures that the target is reachable and responsive, a crucial step in reconnaissance operations.

### Testing and Execution
If `TEST_MODE` is enabled, the script runs unit tests using `pytest`, demonstrating an integrated approach to testing within the deployment pipeline. Finally, it executes the main application logic by invoking `main.py` with resolved parameters, ensuring that all configurations and checks are completed before execution.

## Enterprise Q&A Bank

1. **Q:** How does the script ensure secure network communication?
   **A:** By conditionally setting up a VPN connection using OpenVPN and verifying its success through interface checks.

2. **Q:** What happens if the target host is not reachable?
   **A:** The script performs a safe exit with code 0, indicating no open ports are reachable, possibly due to the host being down or firewalled.

3. **Q:** How does the script handle missing environment variables?
   **A:** It includes checks for required variables like `LLM_API_KEY` and exits with an error message if they are not set.

4. **Q:** What is the purpose of the `STEPS` variable?
   **A:** It controls the number of steps the agent performs, capped at 3 to prevent excessive operations.

5. **Q:** How does the script integrate testing?
   **A:** By checking the `TEST_MODE` variable and running `pytest` if enabled, ensuring the code is tested before execution.

## Actionable Takeaways
- Ensure all required environment variables are set before execution.
- Use conditional logic to adapt script behavior based on runtime conditions.
- Integrate VPN setup for secure network operations.
- Implement automated testing within the deployment pipeline.
- Include fail-safe mechanisms to handle errors gracefully.

---
**Raw Content:**
```bash
#!/bin/bash

set -e

echo "=== 🛰️  HTB Agent Container Started ==="
echo ""

# Checking mounted files
echo "[*] Checking mounted files in /mnt..."
ls -l /mnt
echo ""

# === Append custom hosts file to /etc/hosts if provided ===
if [[ -n "${CUSTOM_HOSTS_FILE:-}" && -f "$CUSTOM_HOSTS_FILE" ]]; then
    echo "[*] Appending contents of \$CUSTOM_HOSTS_FILE to /etc/hosts..."
    cat "$CUSTOM_HOSTS_FILE" >> /etc/hosts
    echo "[+] Custom hosts entries added."
    echo ""
fi

# === Start VPN only if OVPN_FILE is provided ===
if [[ -n "${OVPN_FILE:-}" ]]; then
    echo "[*] Starting OpenVPN using config: $OVPN_FILE"
    openvpn --config "$OVPN_FILE" --daemon

    echo "[*] Waiting for VPN connection (interface tun0)..."
    RETRIES=15
    while ! ip a | grep -q "tun0"; do
        sleep 1
        ((RETRIES--))
        if [[ $RETRIES -eq 0 ]]; then
            echo "[!] ❌ tun0 did not appear. VPN failed to establish."
            exit 1
        fi
    done
    echo "[+] ✅ VPN interface tun0 is now up."
else
    echo "[*] Skipping VPN setup (no OVPN file provided)."
fi

# === Determine target type ===
if [[ -n "${TARGET_HOST:-}" ]]; then
    TARGET_MODE="host"
    RESOLVED_TARGET="$TARGET_HOST"
elif [[ -n "${TARGET_WEBSITE:-}" ]]; then
    TARGET_MODE="website"
    RESOLVED_TARGET="$TARGET_WEBSITE"
else
    echo "[!] Must provide either TARGET_HOST or TARGET_WEBSITE."
    exit 1
fi

# Only probe host if in host mode
if [[ "$TARGET_MODE" == "host" ]]; then
    echo "[*] Probing target: $TARGET_HOST"
    TCP_OK=false

    echo "[*] Running Nmap ping scan to validate host is up..."
    if nmap -sn "$TARGET_HOST" | grep -q "Host is up"; then
        echo "[+] Nmap confirms host is up"
        TCP_OK=true
    else
        echo "[!] Nmap could not confirm host is up"
    fi

    if [[ "$TCP_OK" == true ]]; then
        echo "[+] ✅ VPN and target connectivity confirmed via TCP."
    else
        echo "[!] ❌ No open ports reachable on $TARGET_HOST. Box may be down or firewalled."
        echo "[!] Failing safe: exiting with code 0."
        exit 0
    fi
    echo ""
fi

# Cap STEPS at 3
if [[ -z "${STEPS:-}" ]]; then
    STEPS=1
fi

if [[ "$STEPS" -gt 3 ]]; then
    echo "[!] STEPS capped at 3. Setting to 3."
    STEPS=3
fi

# Check LLM_API_KEY
if [[ -z "${LLM_API_KEY:-}" ]]; then
    echo "[!] LLM_API_KEY environment variable is required."
    exit 1
fi

# Pass INTERACTIVE as third param to agent
INTERACTIVE_FLAG="${INTERACTIVE:-false}"

clear

# Run unit tests if TEST_MODE is enabled
if [[ "${TEST_MODE:-}" == "true" ]]; then
    echo "=== 🧪 Running tests in container ==="
    cd /opt
    export PYTHONPATH=/opt
    echo "PYTHONPATH set to: $PYTHONPATH"
    cd /mnt/tests || exit 1
    pytest -s --disable-warnings -q
    exit $?
fi

# ✅ Call main.py with resolved target and mode
cd /opt
export PYTHONPATH=/opt
python3 -m agent.main "$RESOLVED_TARGET" "$STEPS" "$INTERACTIVE_FLAG" "$TARGET_MODE"
```