# Swarm Planner Agent Design
**Source:** Decepticon\src\agents\swarm\Planner.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `Planner.py` module is a critical component of the Decepticon system, responsible for orchestrating a swarm of agents through a centralized planning mechanism. This module leverages advanced language models and memory management tools to create a robust and adaptable planning agent. The primary purpose of this module is to integrate various tools and models to facilitate complex decision-making processes within a swarm intelligence framework. By utilizing a combination of prebuilt agents, memory tools, and prompt-based configurations, the Planner agent is designed to handle dynamic and evolving scenarios efficiently.

This module exemplifies enterprise-grade software engineering by incorporating reusable components, such as memory management and tool integration, which can be adapted for various applications beyond the current scope. The design patterns and architectural decisions embedded in this module provide valuable insights into building scalable and flexible AI-driven systems.

## Key Concepts & Principles
- **Swarm Intelligence:** A decentralized approach to problem-solving using multiple agents working collaboratively.
- **Language Model Integration:** Utilizing advanced language models to enhance decision-making capabilities.
- **Memory Management:** Tools for managing and searching memory to retain and retrieve information efficiently.
- **Tool Integration:** Combining various tools to extend the functionality and adaptability of the agent.
- **Prompt-based Configuration:** Using prompts to guide the behavior and responses of the agent.

## Detailed Technical Analysis

### Language Model Utilization
The module begins by attempting to load the current language model (LLM) using `get_current_llm()`. If no model is available, it defaults to using the "Claude 3.5 Sonnet" model from ChatAnthropic. This fallback mechanism ensures that the planner agent always has a language model to work with, maintaining operational continuity.

### Memory and Tool Management
The module employs a centralized store for managing state and memory, accessed via `get_store()`. It integrates memory tools for managing and searching memories, which are crucial for maintaining context and historical data across interactions.

### Tool Integration and Agent Creation
The planner agent is constructed using a combination of MCP tools, swarm-specific tools, and memory tools. These tools are dynamically loaded and combined to form a comprehensive toolkit for the agent. The `create_react_agent` function is used to instantiate the agent, incorporating the loaded tools, language model, and a specific prompt tailored for the planner's role in the swarm.

## Enterprise Q&A Bank

1. **Q:** What is the primary role of the Planner agent in the Decepticon system?
   **A:** The Planner agent orchestrates a swarm of agents, integrating various tools and models to facilitate complex decision-making processes.

2. **Q:** How does the module ensure the availability of a language model?
   **A:** It attempts to load the current LLM and defaults to a predefined model if none is available.

3. **Q:** What is the significance of memory tools in this module?
   **A:** Memory tools manage and search memories, enabling the agent to retain and retrieve information efficiently, which is crucial for maintaining context.

4. **Q:** How are tools integrated into the Planner agent?
   **A:** Tools are dynamically loaded and combined into a toolkit, which is then used to create the agent with the `create_react_agent` function.

5. **Q:** What architectural pattern does this module exemplify?
   **A:** The module exemplifies a modular and reusable architecture, integrating various components to build a scalable and flexible system.

## Actionable Takeaways
- Ensure fallback mechanisms for critical components like language models to maintain system continuity.
- Utilize centralized stores for efficient state and memory management.
- Leverage prompt-based configurations to guide agent behavior and responses.
- Integrate diverse tools to extend the functionality and adaptability of agents.
- Design systems with modular and reusable components for scalability and flexibility.

---
**Raw Content:**
```python
from langgraph.prebuilt import create_react_agent
from langchain_mcp_adapters.client import MultiServerMCPClient
from langmem import create_manage_memory_tool, create_search_memory_tool
from src.prompts.prompt_loader import load_prompt
from src.tools.handoff import handoff_to_initial_access, handoff_to_reconnaissance, handoff_to_summary
from src.utils.llm.config_manager import get_current_llm
from src.utils.memory import get_store 
from langchain_anthropic import ChatAnthropic
from src.utils.mcp.mcp_loader import load_mcp_tools

async def make_planner_agent():
    # planner 에이전트에 연결된 mcp_tools가 없을 수도 있으므로 예외처리 가능
    # 메모리에서 LLM 로드 (없으면 기본값 사용)
    llm = get_current_llm()
    if llm is None:
        llm = ChatAnthropic(model_name="claude-3-5-sonnet-latest", temperature=0, timeout=60, stop=None)
        print("Warning: Using default LLM model (Claude 3.5 Sonnet)")
    
    # 중앙 집중식 store 사용
    store = get_store()
    
    mcp_tools = await load_mcp_tools(agent_name=["planner"])

    swarm_tools = [
        handoff_to_reconnaissance, 
        handoff_to_initial_access, 
        handoff_to_summary,
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
        name="Planner",
        prompt=load_prompt("planner", "swarm")
    )
    return agent
```