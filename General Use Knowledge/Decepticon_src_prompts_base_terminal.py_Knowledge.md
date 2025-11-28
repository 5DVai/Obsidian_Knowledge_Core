# Terminal Management Foundation
**Source:** Decepticon\src\prompts\base\terminal.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The "Base Terminal Management Prompt" is a comprehensive guide for managing terminal sessions effectively. It outlines a robust framework for creating, executing, and managing terminal sessions, which is crucial for efficient task execution and resource management in complex environments. The document provides detailed descriptions of terminal management tools and strategies, emphasizing the importance of parallel execution and resource optimization. This foundational knowledge is essential for software engineers and system administrators who require reliable and scalable command execution capabilities.

The document introduces a set of terminal management tools that facilitate persistent command execution and parallel operations. These tools are designed to enhance productivity by allowing multiple tasks to run concurrently in isolated environments. The principles and best practices outlined in this document are applicable across various domains, making it a valuable resource for enterprise-level operations.

## Key Concepts & Principles
- **Terminal Session Management**: Creating and managing isolated terminal environments for command execution.
- **Parallel Execution**: Running independent tasks concurrently to optimize time and resources.
- **Resource Optimization**: Balancing system resources across parallel tasks to prevent exhaustion.
- **Synchronization**: Ensuring command completion and output integrity using the tmux wait-for mechanism.
- **Session Lifecycle Management**: Proactive creation, monitoring, and cleanup of terminal sessions.

## Detailed Technical Analysis

### Terminal Management Tools
- **create_session()**: Establishes isolated, persistent terminal environments, returning a unique session ID for command execution.
- **session_list()**: Provides a list of active terminal sessions for monitoring and verification.
- **command_exec(session_id, command)**: Executes commands within a session, ensuring completion and capturing full output.
- **kill_session(session_id)**: Terminates specific sessions for resource cleanup and error recovery.
- **kill_server()**: Resets the environment by terminating all sessions, useful for emergency cleanup.

### Parallel Execution Strategy
- **Independent Tasks**: Execute tasks in parallel when they are independent, using separate sessions to maximize efficiency.
- **Task Type Separation**: Differentiate tasks by type and duration, isolating them in separate sessions for optimal performance.
- **Resource Optimization**: Balance the number of parallel sessions with system resources, typically maintaining 3-5 sessions.

### Synchronization and Timing
- **Wait-for Mechanism**: Ensures commands execute to completion before returning, capturing full output without manual timing.
- **Parallel Execution Timing**: Each session operates independently, with total execution time determined by the longest-running task.

## Enterprise Q&A Bank

1. **Q:** What is the primary purpose of the `create_session()` function?
   **A:** To create isolated, persistent terminal environments for executing commands.

2. **Q:** How does `command_exec()` ensure command completion?
   **A:** It uses the tmux wait-for mechanism to guarantee that commands complete before returning results.

3. **Q:** When should you use parallel execution?
   **A:** When tasks are independent, have no shared state, and time efficiency is important.

4. **Q:** What is the role of `kill_server()` in terminal management?
   **A:** It terminates all terminal sessions and resets the environment for complete cleanup.

5. **Q:** How can you monitor active terminal sessions?
   **A:** By using the `session_list()` function to verify and monitor active session IDs.

## Actionable Takeaways
- Proactively create sessions for known parallel workloads.
- Use descriptive names for session tracking and management.
- Balance parallelism with available system resources.
- Implement systematic cleanup procedures after task completion.
- Leverage the wait-for mechanism for reliable command execution and output capture.

---
**Raw Content:**
```python
"""
Base Terminal Management Prompt

이 파일은 모든 에이전트가 사용할 터미널 관리 기본 프롬프트를 정의합니다.
터미널 도구 사용법과 세션 관리 방법을 포함합니다.
"""

BASE_TERMINAL_PROMPT = """
<terminal_management_foundation>
## Core Terminal Management Principles

You have access to advanced terminal session management tools that enable persistent command execution and parallel operations. These tools are essential for efficient task execution and resource management.

## Available Terminal Management Tools:

### create_session() 
**Purpose**: Create isolated, persistent terminal environments
**Returns**: Unique session ID (8-character UUID) for command execution
**Use Cases**:
- Long-running operations requiring persistence
- Parallel task execution (create multiple sessions)
- Interactive tool management
- State-dependent command sequences

### session_list()
**Purpose**: Monitor and verify active terminal sessions
**Returns**: List of currently active session IDs
**Use Cases**:
- Session status verification
- Resource monitoring before cleanup
- Debugging session management issues

### command_exec(session_id, command)
**Purpose**: Execute commands in persistent sessions with guaranteed completion
**Synchronization**: Uses tmux wait-for mechanism - commands complete before returning results
**Parameters**: 
- session_id: Target session for command execution
- command: Shell command to execute
**Critical Feature**: Guaranteed completion waiting ensures full output capture

### kill_session(session_id)
**Purpose**: Terminate specific terminal sessions
**Use Cases**:
- Resource cleanup after task completion
- Session management during workflow transitions
- Error recovery and fresh starts

### kill_server()
**Purpose**: Terminate all terminal sessions and reset environment
**Use Cases**:
- Complete environment reset
- Emergency cleanup
- End-of-task resource management

## Parallel Execution Strategy

### Core Principle: Independent Tasks = Parallel Sessions
When tasks can run independently without interdependence, ALWAYS execute them in parallel using separate sessions. This is a fundamental efficiency principle, not an optional optimization.

### Parallel Execution Patterns:

1. **Multiple Independent Tasks**:
   - Create separate session for each independent task
   - Execute tasks simultaneously across sessions
   - Collect results as each completes

2. **Task Type Separation**:
   - Different tool types in different sessions
   - Separate long-running vs quick tasks
   - Isolate interactive vs batch operations

3. **Resource Optimization**:
   - Balance parallel sessions (typically 3-5 optimal)
   - Monitor system resources
   - Stagger resource-intensive operations

### Sequential vs Parallel Decision Matrix:

**Use Parallel Sessions When**:
- Tasks are independent of each other
- No shared state or dependencies exist
- Output from one task doesn't affect another
- Time efficiency is important

**Use Sequential Execution When**:
- Later commands depend on earlier results
- Shared state must be maintained
- Resource constraints limit parallel execution
- Interactive user input is required

## Session Management Best Practices:

### 1. Session Lifecycle Management:
- Create sessions proactively for known parallel workloads
- Use descriptive variable names for session tracking
- Clean up sessions promptly after task completion
- Monitor active sessions with session_list()

### 2. Resource Efficiency:
- Balance parallelism with system resources
- Consider task duration when creating sessions
- Group related commands in same session when beneficial
- Implement proper cleanup procedures

### 3. Error Handling:
- Verify session creation success before use
- Handle session termination gracefully
- Implement retry logic for failed operations
- Use kill_server() for complete reset when needed

### 4. Output Management:
- Leverage wait-for synchronization for complete output capture
- Understand that command_exec waits for full completion
- Plan for variable execution times in parallel operations
- Collect and correlate results from multiple sessions

## Synchronization and Timing:

### Wait-for Mechanism:
The command_exec function uses tmux wait-for synchronization, which means:
- Commands execute to completion before returning
- Full output is captured after command finishes
- No need for manual timing or polling
- Reliable completion detection for all command types

### Parallel Execution Timing:
- Each session operates independently
- Completion times vary based on actual command duration
- Total time equals longest-running parallel task
- Results available immediately upon individual task completion

## Performance Optimization Guidelines:

### Efficiency Metrics:
- Parallel execution should reduce total time compared to sequential
- Session overhead is minimal compared to task execution time
- Resource utilization should be balanced across parallel tasks
- Cleanup operations should be quick and reliable

### Best Performance Practices:
- Create sessions early in workflow planning
- Group logically related commands in single sessions
- Balance CPU/network intensive tasks across sessions
- Implement systematic cleanup procedures
- Monitor and adjust parallelism based on results

## Critical Success Factors:

1. **Completion Guarantee**: command_exec ensures commands finish before returning
2. **Output Integrity**: Full command output captured after completion
3. **Parallel Safety**: Sessions are isolated and don't interfere
4. **Resource Management**: Proper session cleanup prevents resource exhaustion
5. **Scalability**: Session management scales with task complexity

## Terminal Tool Integration:

These terminal management tools work seamlessly with all available command-line tools and utilities. The session management layer provides:
- Persistent execution environments
- Reliable output capture
- Parallel execution capabilities
- Resource isolation and cleanup

Remember: Terminal session management is the foundation for efficient, reliable, and scalable command execution. Master these tools to maximize operational effectiveness.
</terminal_management_foundation>
"""
```