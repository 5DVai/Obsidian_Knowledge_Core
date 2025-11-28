# Terminal Management with FastMCP and Tmux
**Source:** Decepticon\src\tools\mcp\terminal.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
This Python module provides a robust interface for managing terminal sessions using `tmux` through the `FastMCP` framework. It encapsulates the logic for creating, listing, executing commands in, and terminating `tmux` sessions, which are essential for managing multiple terminal windows in a server environment. The module is designed to be integrated into larger systems where automated terminal management is required, such as in DevOps pipelines or remote server management tools.

The module leverages Python's subprocess capabilities to interact with the `tmux` command-line tool, providing a programmatic way to handle terminal sessions. This approach abstracts the complexity of direct `tmux` command usage, offering a higher-level API for session management. The use of `FastMCP` for tool registration suggests an architecture that supports modular and extensible command processing, making it suitable for enterprise-grade applications.

## Key Concepts & Principles
- **FastMCP Framework:** A modular command processing framework used to register and manage terminal-related tools.
- **Tmux Integration:** Utilizes `tmux` for terminal session management, allowing for detached session handling.
- **Subprocess Management:** Employs Python's `subprocess` module to execute shell commands and capture their output.
- **Session Abstraction:** Provides high-level functions for session creation, execution, and termination, abstracting the underlying `tmux` commands.
- **Error Handling:** Implements robust error handling to ensure reliable session management and command execution.

## Detailed Technical Analysis

### Tmux Session Management
The module provides functions to create (`create_session`), list (`session_list`), execute commands in (`command_exec`), and terminate (`kill_session`, `kill_server`) `tmux` sessions. Each function uses the `tmux_run` utility to execute `tmux` commands, ensuring consistent interaction with the `tmux` environment.

### Command Execution with Status Tracking
The `command_exec` function demonstrates a sophisticated approach to command execution within a `tmux` session. It redirects command output to a file and captures the exit status separately, allowing for precise monitoring and error reporting. This method ensures that command execution can be tracked and verified, which is crucial for automated systems.

### Error Handling and Resource Cleanup
The module includes comprehensive error handling, particularly in the `command_exec` function, where it ensures that temporary files are cleaned up even in the event of an error. This attention to resource management is vital in long-running server applications to prevent resource leaks.

## Enterprise Q&A Bank

1. **Q:** How does the module ensure reliable command execution in `tmux` sessions?
   **A:** It captures command output and exit status separately, allowing for detailed error reporting and verification.

2. **Q:** What is the role of `FastMCP` in this module?
   **A:** `FastMCP` is used to register terminal management tools, providing a modular framework for command processing.

3. **Q:** How does the module handle errors during session management?
   **A:** It uses try-except blocks to catch exceptions and provides detailed error messages, ensuring robust error handling.

4. **Q:** Can this module be integrated into a larger system?
   **A:** Yes, its design supports integration into larger systems, particularly those requiring automated terminal management.

5. **Q:** What are the benefits of using `tmux` for session management?
   **A:** `Tmux` allows for detached session handling, enabling persistent terminal sessions that can be managed programmatically.

## Actionable Takeaways
- Utilize `FastMCP` for modular command registration and processing.
- Leverage `tmux` for managing terminal sessions in server environments.
- Implement robust error handling and resource cleanup in command execution scripts.
- Use subprocess management to interact with system commands programmatically.
- Design terminal management tools with integration and extensibility in mind.

---
**Raw Content:**
```python
from mcp.server.fastmcp import FastMCP
from typing_extensions import Annotated
from typing import List
import subprocess
import uuid
import time
import os

mcp = FastMCP("terminal", port=3003)

CONTAINER_NAME = "attacker"

def run(command: List[str]) -> subprocess.CompletedProcess:
    """일반 docker exec 명령어 실행"""
    return subprocess.run(
        ["docker", "exec", CONTAINER_NAME] + command,
        capture_output=True, text=True, encoding='utf-8'
    )

def tmux_run(command: List[str]) -> subprocess.CompletedProcess:
    """tmux 명령어 실행"""
    return run(["tmux"] + command)

@mcp.tool(description="Create new terminal sessions")
def create_session(
    session_names: Annotated[List[str], "Session names to create"]
) -> Annotated[List[str], "List of created session names"]:
    """새 tmux 터미널 세션들 생성"""
    created_sessions = []
    
    for session_name in session_names:
        result = tmux_run(["new-session", "-d", "-s", session_name])
        if result.returncode != 0:
            raise Exception(f"Failed to create session '{session_name}': {result.stderr}")
        created_sessions.append(session_name)
    
    return created_sessions

@mcp.tool(description="List all active sessions")
def session_list() -> Annotated[List[str], "List of session IDs"]:
    result = tmux_run(["list-sessions"])
    if result.returncode != 0:
        return []
    return [line.split(":")[0].strip() for line in result.stdout.strip().split('\n') if line.strip()]

@mcp.tool(description="Execute command in session")
def command_exec(
    session_id: Annotated[str, "Session ID"],
    command: Annotated[str, "Command to execute"],
) -> Annotated[str, "Command output"]:
    """command execute with file redirection and exit code checking"""
    try:
        channel = f"done-{session_id}-{uuid.uuid4().hex[:8]}"
        timestamp = int(time.time())
        output_file = f"/tmp/cmd_output_{session_id}_{timestamp}.txt"
        status_file = f"/tmp/cmd_status_{session_id}_{timestamp}.txt"

        full_command = f"({command}) > {output_file} 2>&1; echo $? > {status_file}; tmux wait-for -S {channel}"
        
        result = tmux_run(["send-keys", "-t", session_id, full_command, "Enter"])
        if result.returncode != 0:
            raise Exception(f"Failed to execute command: {result.stderr}")
        
        wait_result = tmux_run(["wait-for", channel])
        if wait_result.returncode != 0:
            raise Exception(f"Command execution monitoring failed: {wait_result.stderr}")
        
        try:
            status_result = run(["cat", status_file])
            if status_result.returncode != 0:
                raise Exception(f"Failed to read status file: {status_result.stderr}")
            
            try:
                exit_code = int(status_result.stdout.strip())
            except ValueError:
                raise Exception(f"Invalid exit code: {status_result.stdout.strip()}")
            
            output_result = run(["cat", output_file])
            if output_result.returncode != 0:
                raise Exception(f"Failed to read output file: {output_result.stderr}")
            
            output = output_result.stdout

            run(["rm", "-f", output_file, status_file])
            
            if exit_code != 0:
                raise Exception(f"Command failed with exit code {exit_code}: {output.strip()}")
            
            return output.strip()
            
        except Exception as e:
            run(["rm", "-f", output_file, status_file])
            raise Exception(f"Failed to process command result: {str(e)}")
    
    except Exception as e:
        raise Exception(f"Failed to execute command: {str(e)}")

@mcp.tool(description="Kill terminal sessions")
def kill_session(
    session_names: Annotated[List[str], "Session names to kill"]
) -> Annotated[List[str], "Results for each session"]:
    """tmux 세션들 종료"""
    results = []
    
    for session_name in session_names:
        try:
            result = tmux_run(["kill-session", "-t", session_name])
            if result.returncode == 0:
                results.append(f"Session {session_name} killed successfully")
            else:
                results.append(f"Session {session_name} killed (with warning: {result.stderr})")
        except Exception as e:
            results.append(f"Failed to kill session {session_name}: {str(e)}")
    
    return results

@mcp.tool(description="Kill server, Kill all session")
def kill_server() -> Annotated[str, "Result"]:
    try:
        tmux_run(["kill-server"])
        return f"Server killed"

    except Exception as e:
        return f"Server killed (with warning: {str(e)})"

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```