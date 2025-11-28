# Summary Agent Creation in Swarm System
**Source:** Decepticon\src\agents\swarm\Summary.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `Summary.py` file is a critical component of the Decepticon's swarm agent system, responsible for creating a summary agent. This agent leverages a combination of language models, memory management tools, and multi-server communication protocols to perform its tasks. The file outlines the process of loading or defaulting to a language model, integrating various tools, and constructing an agent capable of executing complex tasks within a swarm architecture.

The value of this file lies in its demonstration of integrating multiple components to create a cohesive agent. It showcases the use of memory tools, language models, and a centralized store to facilitate the agent's operations. This approach is particularly valuable for enterprises looking to implement scalable and adaptable AI-driven systems.

## Key Concepts & Principles
- **Language Model Integration:** Utilizing prebuilt language models and defaulting to a standard model when none is specified.
- **Memory Management:** Employing tools to manage and search memory, ensuring efficient data handling.
- **Tool Aggregation:** Combining multiple tools from different domains to enhance agent capabilities.
- **Centralized Store Usage:** Leveraging a centralized store for consistent data access and management.
- **Swarm Architecture:** Implementing a swarm-based approach for distributed agent operations.

## Detailed Technical Analysis

### Language Model Loading
The file begins by attempting to load a current language model (LLM) from memory. If unavailable, it defaults to a predefined model, `ChatAnthropic`, ensuring the agent always has a functional LLM.

### Tool Integration
The agent is equipped with a variety of tools:
- **MCP Tools:** Loaded asynchronously to support multi-server communication.
- **Swarm Tools:** Include functions for reconnaissance, initial access, and planning, crucial for swarm operations.
- **Memory Tools:** Facilitate memory management and retrieval, enhancing the agent's ability to handle data efficiently.

### Agent Creation
The `create_react_agent` function is used to instantiate the agent, integrating the loaded LLM, tools, and a centralized store. This setup allows the agent to perform its tasks effectively within the swarm system.

## Enterprise Q&A Bank

1. **Q:** What happens if no current LLM is found in memory?
   **A:** The system defaults to using the `ChatAnthropic` model, ensuring continuity in agent operations.

2. **Q:** How are tools integrated into the agent?
   **A:** Tools are aggregated from multiple sources, including MCP, swarm, and memory management, to enhance the agent's functionality.

3. **Q:** What is the role of the centralized store?
   **A:** It provides a consistent data access point for the agent, facilitating efficient data management.

4. **Q:** Why is a swarm architecture used?
   **A:** It allows for distributed and scalable agent operations, suitable for complex environments.

5. **Q:** How does the agent handle memory management?
   **A:** Through specialized tools that manage and search memory, ensuring efficient data handling.

## Actionable Takeaways
- Ensure a default language model is available to prevent operational disruptions.
- Integrate a diverse set of tools to enhance agent capabilities.
- Utilize a centralized store for consistent data management.
- Adopt a swarm architecture for scalable and distributed agent operations.
- Implement robust memory management practices to optimize data handling.

---
**Raw Content:**
```python
from langgraph.prebuilt import create_react_agent
from langchain_mcp_adapters.client import MultiServerMCPClient
from langmem import create_manage_memory_tool, create_search_memory_tool
from src.prompts.prompt_loader import load_prompt
from src.tools.handoff import handoff_to_initial_access, handoff_to_reconnaissance, handoff_to_planner
from src.utils.llm.config_manager import get_current_llm
from src.utils.memory import get_store

from src.utils.mcp.mcp_loader import load_mcp_tools

async def make_summary_agent():
    # 메모리에서 LLM 로드 (없으면 기본값 사용)
    llm = get_current_llm()
    if llm is None:
        from langchain_anthropic import ChatAnthropic
        llm = ChatAnthropic(model_name="claude-3-5-sonnet-latest", temperature=0, timeout=60, stop=None)
        print("Warning: Using default LLM model (Claude 3.5 Sonnet)")
    
    # 중앙 집중식 store 사용
    store = get_store()
    
    mcp_tools = await load_mcp_tools(agent_name=["summary"])

    swarm_tools = [
        handoff_to_reconnaissance, 
        handoff_to_initial_access,
        handoff_to_planner,
    ]

    mem_tools = [
        create_manage_memory_tool(namespace=("memories",)),
        create_search_memory_tool(namespace=("memories",))
    ]

    tools = mcp_tools + swarm_tools + mem_tools

    agent = create_react_agent(
        llm,
        tools=tools,
        store=store,
        name="Summary",
        prompt=load_prompt("summary", "swarm")
    )
    return agent
```