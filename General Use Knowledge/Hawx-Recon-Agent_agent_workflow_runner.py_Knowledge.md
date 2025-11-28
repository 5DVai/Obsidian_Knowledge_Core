# Command Runner for Hawx Recon Agent Workflow Layers
**Source:** Hawx-Recon-Agent\agent\workflow\runner.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `runner.py` file is a critical component of the Hawx Recon Agent, responsible for executing a series of reconnaissance commands within a specified workflow layer. This script not only automates the execution of these commands but also provides an interactive interface for users to modify or skip commands as needed. The primary value of this file lies in its ability to facilitate dynamic command execution, collect recommended next steps, and identify discovered services, making it a reusable and adaptable tool for enterprise-grade reconnaissance workflows.

This script embodies a blend of automation and user interaction, allowing for flexible command execution strategies. It supports both automated and manual intervention, enabling users to tailor the execution process according to real-time requirements. The integration with a language model client (`llm_client`) and the ability to record discovered services further enhance its utility in complex reconnaissance scenarios.

## Key Concepts & Principles
- **Command Execution Workflow:** Automates the execution of a series of commands within a workflow layer.
- **Interactive Command Modification:** Allows users to modify or skip commands interactively.
- **Dynamic Recommendations:** Collects and returns recommended next steps based on command execution results.
- **Service Discovery:** Identifies and records services discovered during command execution.
- **Integration with LLM Client:** Utilizes a language model client for enhanced command processing.

## Detailed Technical Analysis

### Command Execution and Interaction
The script iterates over a list of commands, executing each one while providing an interactive prompt for user input. This interaction allows users to modify commands on-the-fly or skip them entirely, offering flexibility in how the workflow is executed.

### Use of `shlex` and `readline`
- **`shlex.split(cmd)`:** This function is used to safely parse command strings into arguments, ensuring that the command execution is robust against shell injection vulnerabilities.
- **`readline` Module:** Facilitates command modification by allowing users to prefill and edit commands interactively, enhancing user experience during command execution.

### Integration with `execute_command`
The `execute_command` function, imported from the `output` module, is central to executing parsed command parts. It interacts with the `llm_client` and processes the command within the context of the specified workflow layer, returning results that include recommended steps and discovered services.

## Enterprise Q&A Bank

1. **Q:** How does the script handle command modification?
   **A:** The script uses the `readline` module to allow users to prefill and modify commands interactively before execution.

2. **Q:** What is the role of `llm_client` in this script?
   **A:** The `llm_client` is used to enhance command execution by integrating with a language model, potentially for processing or interpreting command results.

3. **Q:** How are discovered services recorded?
   **A:** Discovered services are appended to the `records.services` list, which is updated based on the results returned by `execute_command`.

4. **Q:** Can the script execute commands non-interactively?
   **A:** Yes, by setting the `interactive` parameter to `False`, the script can execute commands without user prompts.

5. **Q:** What happens if a command is skipped?
   **A:** If a command is skipped, it is set to `None` and the script continues to the next command without execution.

## Actionable Takeaways
- Ensure `llm_client` is properly configured for enhanced command processing.
- Utilize the interactive mode for real-time command adjustments during reconnaissance.
- Regularly update the `execute_command` logic to accommodate new command types and execution contexts.
- Leverage the service discovery feature to maintain an up-to-date inventory of network services.
- Consider extending the script to support additional command execution environments or contexts.

---
**Raw Content:**
```python
"""
Command runner for Hawx Recon Agent workflow layers.

Executes a list of recon commands for a given layer, supports interactive modification,
and collects recommended next steps and discovered services.
"""

import shlex
import readline
from .output import execute_command


def run_layer(commands, layer_index, llm_client, base_dir, records, interactive=False):
    """Run all commands for a given workflow layer, optionally interactively."""
    current_recommended = []
    print(
        f"\n\033[1;36m[***] Starting Layer {layer_index} with {len(commands)} command(s)\033[0m\n"
    )
    skip_prompt = False
    for idx, cmd in enumerate(commands):
        print(f"\033[1;34m[>] Command [{idx+1}/{len(commands)}]: {cmd}\033[0m")
        if interactive and not skip_prompt:
            while True:
                user_input = input(
                    "\033[1;33m    Run? [Enter = yes | m = modify | s = skip | Y = yes to all] > \033[0m"
                ).strip()
                if user_input == "":
                    break
                elif user_input.lower() == "s":
                    print("    ⏩ Skipping.\n")
                    cmd = None
                    break
                elif user_input.lower() == "m":

                    def prefill_hook():
                        readline.insert_text(cmd)
                        readline.redisplay()

                    readline.set_pre_input_hook(prefill_hook)
                    try:
                        new_cmd = input("    Modify command: ").strip()
                    finally:
                        readline.set_pre_input_hook()
                    if new_cmd:
                        cmd = new_cmd
                        break
                elif user_input == "Y":
                    skip_prompt = True
                    break
                else:
                    print("    Invalid input. Try again.")
        if not cmd:
            continue
        parts = shlex.split(cmd)
        resp = execute_command(parts, llm_client, base_dir, layer_index)
        if isinstance(resp, dict):
            current_recommended.extend(resp.get("recommended_steps", []))
            records.services.extend(resp.get("services_found", []))
        print(f"\033[1;32m[✓] Done: {cmd}\033[0m\n")
    return current_recommended
```