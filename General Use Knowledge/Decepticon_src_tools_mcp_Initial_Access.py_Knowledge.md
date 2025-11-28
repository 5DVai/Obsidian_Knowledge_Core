# Initial Access Tooling for Cybersecurity Operations
**Source:** Decepticon\src\tools\mcp\Initial_Access.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `Initial_Access.py` module is a critical component of the Decepticon project, designed to facilitate initial access operations in cybersecurity contexts. It provides a robust framework for executing commands within a controlled Kali Linux environment using Docker containers. The module includes functions for executing brute-force authentication attacks and searching exploit databases, making it a valuable tool for penetration testers and security researchers. By leveraging Docker, the module ensures that operations are conducted in isolated environments, enhancing security and reproducibility.

## Key Concepts & Principles
- **Docker Utilization:** Ensures isolated and reproducible environments for executing potentially destructive commands.
- **Command Execution:** Facilitates the execution of shell commands within a Docker container.
- **Brute-force Attacks:** Implements tools like Patator and Hydra for testing authentication mechanisms.
- **Exploit Database Search:** Uses SearchSploit to find known vulnerabilities for specified services.

## Detailed Technical Analysis

### Docker-Based Command Execution
The module uses Docker to run commands in a Kali Linux environment. This approach ensures that the host system remains unaffected by the operations performed within the container. The `command_execution` function checks for Docker availability, verifies the existence and running state of the container, and executes commands, returning the results or errors encountered.

### Brute-force Attack Tools
- **Patator:** A versatile brute-force tool that supports multiple protocols such as SSH, FTP, HTTP, and more. The module provides a template for constructing Patator commands based on the target service and options.
- **Hydra:** Another tool for performing brute-force attacks, with a simpler interface for specifying target and options.

### Exploit Database Search
The `searchsploit` function allows users to search for known vulnerabilities in the Exploit Database, providing a command-line interface to quickly identify potential exploits for a given service.

## Enterprise Q&A Bank

1. **Q:** How does the module ensure that Docker is available before executing commands?
   **A:** It runs a subprocess to execute `docker ps` and checks the return code to confirm Docker's availability.

2. **Q:** What happens if the specified Docker container is not running?
   **A:** The module attempts to start the container using `docker start`, and returns an error message if it fails.

3. **Q:** Can the module handle multiple command executions in sequence?
   **A:** Yes, each command is executed independently, allowing for sequential execution within the same container environment.

4. **Q:** How does the module handle errors during command execution?
   **A:** It captures standard error output and returns a formatted error message, including the error type if an exception occurs.

5. **Q:** What protocols are supported by the Patator tool in this module?
   **A:** Supported protocols include SSH, FTP, HTTP, SMB, MySQL, RDP, and Telnet.

## Actionable Takeaways
- Ensure Docker is installed and accessible in the system PATH before using this module.
- Verify that the necessary Docker container is created and configured with Kali Linux.
- Use the provided functions to execute commands, perform brute-force attacks, and search for exploits in a controlled environment.
- Regularly update the wordlists and exploit databases to ensure comprehensive testing.
- Consider implementing additional logging and monitoring for executed commands to maintain an audit trail.

---
**Raw Content:**

```python
from mcp.server.fastmcp import FastMCP
from typing_extensions import Annotated
from typing import List, Optional, Union
import subprocess

mcp = FastMCP("initial_access", port=3002)

CONTAINER_NAME = "attacker"

def command_execution(command: Annotated[str, "Commands to run on Kali Linux"]) -> Annotated[str, "Command Execution Result"]:
    """
    Run one command at a time in a kali linux environment and return the result
    """
    try:
        # Docker 사용 가능 여부 확인
        docker_check = subprocess.run(
            ["docker", "ps"], 
            capture_output=True, text=True, encoding="utf-8", errors="ignore"
        )
        
        if docker_check.returncode != 0:
            return f"[-] Docker is not available: {docker_check.stderr.strip()}"
            
        # 컨테이너 존재 여부 확인
        container_check = subprocess.run(
            ["docker", "ps", "-a", "--filter", f"name={CONTAINER_NAME}"],
            capture_output=True, text=True, encoding="utf-8", errors="ignore"
        )
        
        if CONTAINER_NAME not in container_check.stdout:
            return f"[-] Container '{CONTAINER_NAME}' does not exist"
        
        # 컨테이너 실행 상태 확인
        running_check = subprocess.run(
            ["docker", "ps", "--filter", f"name={CONTAINER_NAME}"],
            capture_output=True, text=True, encoding="utf-8", errors="ignore"
        )
        
        # 컨테이너가 실행 중이 아니면 시작
        if CONTAINER_NAME not in running_check.stdout:
            start_result = subprocess.run(
                ["docker", "start", CONTAINER_NAME],
                capture_output=True, text=True, encoding="utf-8", errors="ignore"
            )
            
            if start_result.returncode != 0:
                return f"[-] Failed to start container '{CONTAINER_NAME}': {start_result.stderr.strip()}"
            
        # ✅ Kali Linux 컨테이너에서 명령어 실행
        result = subprocess.run(
            ["docker", "exec", CONTAINER_NAME, "sh", "-c", command],
            capture_output=True, text=True, encoding="utf-8", errors="ignore"
        )
        
        if result.returncode != 0:
            return f"[-] Command execution error: {result.stderr.strip()}"
        
        return f"{result.stdout.strip()}"
    
    except FileNotFoundError:
        return "[-] Docker command not found. Is Docker installed and in PATH?"
    
    except Exception as e:
        return f"[-] Error: {str(e)} (Type: {type(e).__name__})"

@mcp.tool(description="Brute-force authentication attacks using Patator")
def patator(service: str, target: str, options: Optional[Union[str, List[str]]] = None) -> Annotated[str, "Command"]:
    """
    Executes a Patator brute-force attack.

    Args:
        service (str): Service name (e.g., ssh, ftp, mysql)
        target (str): Target IP or URL
        options (Optional[Union[str, List[str]]]): Additional patator options

    Returns:
        str: Executed shell command string
    """
    if options is None:
        args_str = ""
    elif isinstance(options, list):
        args_str = " ".join(options)
    else:
        args_str = options

    module = f"{service}_login"

    # Determine default parameter based on service
    if service in ["ssh", "ftp", "smb", "rdp", "telnet", "mysql"]:
        target_param = f"host={target}"
    elif service in ["http"]:
        target_param = f"url={target}"
    else:
        target_param = target  # fallback

    command = f"patator {module} {target_param} {args_str}"
    return command_execution(command)

@mcp.tool(description="Brute-force authentication attacks")
def hydra(target: str, options: Optional[Union[str, List[str]]] = None) -> Annotated[str, "Command"]:
    if options is None:
        args_str = ""
    elif isinstance(options, list):
        args_str = " ".join(options)
    else:
        args_str = options

    command = f"hydra {args_str} {target}"
    return command_execution(command)

@mcp.tool(description="Search exploit database for vulnerabilities")
def searchsploit(service_name: str, options: Optional[Union[str, List[str]]] = None) -> Annotated[str, "Command"]:
    if options is None:
        args_str = ""
    elif isinstance(options, list):
        args_str = " ".join(options)
    else:
        args_str = options

    command = f"searchsploit {args_str} {service_name}"
    return command_execution(command)

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```