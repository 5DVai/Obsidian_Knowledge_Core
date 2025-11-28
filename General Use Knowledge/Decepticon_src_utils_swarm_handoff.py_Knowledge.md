# Handoff Tool Creation and Management in Multi-Agent Systems
**Source:** Decepticon\src\utils\swarm\handoff.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `handoff.py` module is a critical component in the Decepticon system, designed to facilitate seamless control transfer between agents in a multi-agent architecture. This module provides the functionality to create tools that manage the handoff process, ensuring that agents can delegate tasks efficiently. The primary utility of this module lies in its ability to normalize agent names, create handoff tools with customizable names and descriptions, and retrieve handoff destinations from a compiled state graph. This functionality is essential for systems that rely on dynamic agent interactions and task delegation, making it a valuable asset for enterprise-grade multi-agent systems.

## Key Concepts & Principles
- **Agent Name Normalization:** Ensures consistency in agent naming conventions by converting names to a standardized format.
- **Handoff Tool Creation:** Provides a mechanism to create tools that facilitate control transfer between agents.
- **Metadata Utilization:** Uses metadata to store and retrieve handoff destination information.
- **Multi-Agent Graph Integration:** Integrates with a multi-agent graph to manage agent interactions and task delegation.

## Detailed Technical Analysis

### Agent Name Normalization
The `_normalize_agent_name` function standardizes agent names by replacing whitespace with underscores and converting the string to lowercase. This ensures that agent names are consistent and compatible with system requirements.

### Handoff Tool Creation
The `create_handoff_tool` function is the core utility for creating tools that manage the handoff process. It allows for optional customization of tool names and descriptions, defaulting to a format that includes the agent name. The function uses decorators to define the tool's behavior, ensuring that the handoff process is encapsulated within a well-defined command structure.

### Metadata and Handoff Destinations
The `get_handoff_destinations` function retrieves a list of destinations from an agent's handoff tools by accessing the metadata associated with each tool. This function is crucial for understanding the potential paths of control transfer within the multi-agent system.

## Enterprise Q&A Bank

1. **Q:** How does the module ensure agent name consistency?
   **A:** By using the `_normalize_agent_name` function to standardize names to lowercase and replace spaces with underscores.

2. **Q:** What is the purpose of the `create_handoff_tool` function?
   **A:** To create tools that facilitate the handoff of control to specified agents, with customizable names and descriptions.

3. **Q:** How are handoff destinations retrieved?
   **A:** Through the `get_handoff_destinations` function, which accesses metadata from the agent's tools.

4. **Q:** What role does metadata play in this module?
   **A:** Metadata is used to store and retrieve information about handoff destinations, enabling dynamic control transfer.

5. **Q:** Can the handoff tool names be customized?
   **A:** Yes, the `create_handoff_tool` function allows for optional customization of tool names and descriptions.

## Actionable Takeaways
- Ensure agent names are normalized for consistency across the system.
- Utilize the `create_handoff_tool` function to manage control transfers between agents.
- Leverage metadata to track and retrieve handoff destinations.
- Integrate handoff tools within a multi-agent graph for dynamic task delegation.
- Customize tool names and descriptions to enhance clarity and usability.

---
**Raw Content:**
```python
import re

from langchain_core.messages import ToolMessage
from langchain_core.tools import BaseTool, InjectedToolCallId, tool
from langgraph.graph.state import CompiledStateGraph
from langgraph.prebuilt import InjectedState, ToolNode
from langgraph.types import Command
from typing_extensions import Annotated

WHITESPACE_RE = re.compile(r"\s+")
METADATA_KEY_HANDOFF_DESTINATION = "__handoff_destination"

def _normalize_agent_name(agent_name: str) -> str:
    """Normalize an agent name to be used inside the tool name."""
    return WHITESPACE_RE.sub("_", agent_name.strip()).lower()

def create_handoff_tool(
    *, agent_name: str, name: str | None = None, description: str | None = None
) -> BaseTool:
    """Create a tool that can handoff control to the requested agent.

    Args:
        agent_name: The name of the agent to handoff control to, i.e.
            the name of the agent node in the multi-agent graph.
            Agent names should be simple, clear and unique, preferably in snake_case,
            although you are only limited to the names accepted by LangGraph
            nodes as well as the tool names accepted by LLM providers
            (the tool name will look like this: `transfer_to_<agent_name>`).
        name: Optional name of the tool to use for the handoff.
            If not provided, the tool name will be `transfer_to_<agent_name>`.
        description: Optional description for the handoff tool.
            If not provided, the tool description will be `Ask agent <agent_name> for help`.
    """
    if name is None:
        name = f"transfer_to_{_normalize_agent_name(agent_name)}"

    if description is None:
        description = f"Ask agent '{agent_name}' for help"

    @tool(name, description=description)
    def handoff_to_agent(
        state: Annotated[dict, InjectedState],
        tool_call_id: Annotated[str, InjectedToolCallId],
    ):
        tool_message = ToolMessage(
            content=f"Successfully transferred to {agent_name}",
            name=name,
            tool_call_id=tool_call_id,
        )
        return Command(
            goto=agent_name,
            graph=Command.PARENT,
            update={"messages": state["messages"] + [tool_message], "active_agent": agent_name},
        )

    handoff_to_agent.metadata = {METADATA_KEY_HANDOFF_DESTINATION: agent_name}
    return handoff_to_agent

def get_handoff_destinations(agent: CompiledStateGraph, tool_node_name: str = "tools") -> list[str]:
    """Get a list of destinations from agent's handoff tools."""
    nodes = agent.get_graph().nodes
    if tool_node_name not in nodes:
        return []

    tool_node = nodes[tool_node_name].data
    if not isinstance(tool_node, ToolNode):
        return []

    tools = tool_node.tools_by_name.values()
    return [
        tool.metadata[METADATA_KEY_HANDOFF_DESTINATION]
        for tool in tools
        if tool.metadata is not None and METADATA_KEY_HANDOFF_DESTINATION in tool.metadata
    ]
```