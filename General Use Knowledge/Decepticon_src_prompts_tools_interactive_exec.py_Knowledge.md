# Interactive Shell Execution Tools Prompt
**Source:** Decepticon\src\prompts\tools\interactive_exec.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The "Interactive Shell Execution Tools Prompt" provides a structured approach for managing persistent terminal sessions in software systems. This document outlines the use of interactive shell commands and tools, such as SSH, FTP, and others, to maintain persistent environments for executing multi-step operations. The guidance is particularly valuable for scenarios where state persistence is crucial, such as directory navigation, environment setup, and background process management. By defining when to use persistent sessions versus standard execution, this document serves as a decision-making framework for developers and system administrators.

The document is a valuable asset for enterprise environments where complex workflows require stateful command execution. It ensures that interactive sessions are used judiciously, optimizing resource usage and maintaining system integrity. The inclusion of specific tools and commands, along with a decision guide, makes it a practical reference for managing terminal sessions effectively.

## Key Concepts & Principles
- **Interactive Shell Commands**: Commands executed in a terminal that require a persistent session.
- **Persistent Session Management**: Maintaining a continuous session for executing multi-step operations.
- **State Persistence**: The ability to retain the state of a session across multiple commands.
- **Decision Guide**: A framework for determining when to use interactive sessions versus standard execution.

## Detailed Technical Analysis

### Persistent Session Management Tools
- **create_session()**: Initiates a persistent session, returning a session ID for subsequent commands. This is crucial for operations that require a stable environment across multiple steps.
- **session_list**: Provides a list of active sessions, allowing users to manage and monitor ongoing processes.
- **command_exec(command, session_id)**: Executes commands within a specified session, ensuring that the state is preserved. This is particularly useful for tasks like directory navigation and environment setup.
- **kill_session(session_id)**: Terminates a session when it is no longer needed, freeing up resources and maintaining system efficiency.

### Decision Guide
The document provides a clear decision guide for using interactive sessions:
- **Use Interactive Sessions For**: Tasks that require state persistence, such as directory changes, environment variable setup, tool installation, and multi-command workflows.
- **Use Standard Execution For**: Simple, independent operations like information gathering and one-time status checks.

## Enterprise Q&A Bank

1. **Q: When should I use `create_session()`?**
   - A: Use `create_session()` when you need a persistent environment for executing multi-step operations that depend on each other.

2. **Q: What is the purpose of `command_exec(command, session_id)`?**
   - A: It allows you to execute commands within a persistent session, ensuring that the session's state is maintained.

3. **Q: How do I know which sessions are active?**
   - A: Use the `session_list` command to view all active sessions.

4. **Q: When should I terminate a session?**
   - A: Use `kill_session(session_id)` to terminate a session when it is no longer needed to free up resources.

5. **Q: What are the benefits of using interactive sessions?**
   - A: Interactive sessions are beneficial for tasks that require state persistence, such as multi-command workflows and environment setup.

## Actionable Takeaways
- Use `create_session()` to initiate a persistent session for complex workflows.
- Execute commands within a session using `command_exec(command, session_id)` to maintain state.
- Regularly check active sessions with `session_list` to manage resources.
- Terminate sessions with `kill_session(session_id)` when they are no longer needed.
- Follow the decision guide to determine when to use interactive sessions versus standard execution.

---
**Raw Content:**
```
Interactive Shell Execution Tools Prompt

This file defines prompts for agents to understand when and how to use 
interactive shell execution tools for persistent terminal session management.
```

INTERACTIVE_EXEC_TOOLS_PROMPT = """
<terminal_tools>
## important
If interactive shell commands such as ssh, ftp, nc or msfconsole are required to be executed, the commands should be executed using the following tools.
Use the `-o HostKeyAlgoriths=+ssh-rsa` option when connecting to the ssh

## Persistent Session Management Tools:
### create_session()
**When to use**: Need persistent environment for multi-step operations
**Returns**: Session ID for subsequent commands

### session_list
session list

### command_exec(command, session_id)
**When to use**: Execute commands that require state persistence
**Examples**: Directory navigation, environment setup, background processes

### kill_session(session_id)
**When to use**: Clean up when session no longer needed

## Decision Guide:
**Use Interactive Sessions For**:
- Directory changes affecting subsequent commands
- Environment variable setup
- Tool installation and configuration  
- Background process management
- Multi-command workflows requiring state

**Use Standard Execution For**:
- Simple information gathering
- One-time status checks
- Independent file operations

Use interactive sessions when commands build upon each other. Use standard execution for simple, independent operations.
</interactive_shell_tools>
```