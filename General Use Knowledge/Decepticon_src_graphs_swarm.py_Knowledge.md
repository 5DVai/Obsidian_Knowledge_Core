# Dynamic Swarm Creation in Asynchronous Systems
**Source:** Decepticon\src\graphs\swarm.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `swarm.py` module is a critical component of the Decepticon project, focusing on the dynamic creation and management of agent-based workflows. This file leverages asynchronous programming to instantiate and manage a collection of agents, which are integral to the system's operation. The agents are created dynamically based on user selections, allowing for flexible and adaptive workflows. This approach is particularly valuable in environments where tasks need to be distributed and managed efficiently, such as in large-scale data processing or AI-driven applications.

The module employs a centralized persistence mechanism using an in-memory checkpointer and store, ensuring that the state of the workflow can be maintained and retrieved efficiently. This design choice supports high-performance operations and facilitates the development of robust, scalable systems. The use of asynchronous functions ensures that the system remains responsive and can handle multiple tasks concurrently, which is essential for enterprise-grade applications.

## Key Concepts & Principles
- **Asynchronous Programming:** Utilizes Python's `asyncio` to manage concurrent operations, improving system responsiveness.
- **Dynamic Agent Creation:** Agents are instantiated based on user input, allowing for customizable workflows.
- **Centralized Persistence:** In-memory checkpointer and store are used for efficient state management.
- **Workflow Compilation:** Agents are compiled into a workflow, which can be executed and managed dynamically.

## Detailed Technical Analysis

### Asynchronous Agent Creation
The module defines an asynchronous function `create_agents` that dynamically creates agents based on user selections. This function uses `await` to ensure that each agent is created in a non-blocking manner, allowing other operations to proceed concurrently.

### Workflow Management
The `create_dynamic_swarm` function orchestrates the creation of a swarm, which is a collection of agents working together. It logs the process of creating the swarm and compiles the workflow using the `create_swarm` utility. The compiled workflow is equipped with an in-memory checkpointer and store, ensuring that the system's state is preserved and can be accessed efficiently.

### Persistence Strategy
The use of an in-memory checkpointer and store is a strategic choice for high-performance applications. This approach minimizes latency and maximizes throughput, making it suitable for environments where speed and efficiency are paramount.

## Enterprise Q&A Bank

1. **Q:** What is the advantage of using asynchronous functions in this module?
   **A:** Asynchronous functions allow the system to handle multiple tasks concurrently, improving responsiveness and efficiency.

2. **Q:** How does dynamic agent creation benefit the system?
   **A:** It allows for customizable workflows that can adapt to user needs, enhancing flexibility and scalability.

3. **Q:** Why is an in-memory checkpointer used?
   **A:** It provides fast access to the system's state, reducing latency and improving performance.

4. **Q:** What role does the `create_swarm` function play?
   **A:** It compiles the agents into a cohesive workflow that can be executed and managed dynamically.

5. **Q:** How does this module support enterprise-grade applications?
   **A:** By providing a flexible, efficient, and scalable framework for managing complex workflows.

## Actionable Takeaways
- Implement asynchronous programming to enhance system responsiveness.
- Use dynamic agent creation to allow for flexible and adaptive workflows.
- Employ in-memory persistence strategies for high-performance applications.
- Ensure that workflows are compiled and managed efficiently to support scalability.
- Leverage logging to monitor and debug the workflow creation process.

---
**Raw Content:**
```python
from src.agents.swarm.Recon import make_recon_agent
from src.agents.swarm.InitAccess import make_initaccess_agent
from src.agents.swarm.Planner import make_planner_agent
from src.agents.swarm.Summary import make_summary_agent
from src.utils.swarm.swarm import create_swarm
from src.utils.memory import get_checkpointer, get_store
import asyncio
import logging

logger = logging.getLogger(__name__)

# 중앙 집중식 persistence 인스턴스
checkpointer = get_checkpointer()
store = get_store() 

# 비동기 함수로 workflow 선언하면 langgraph dev 는 실행안될수도있음

# 에이전트를 즉시 생성하지 않고 동적으로 생성하는 함수들로 변경
# recon = asyncio.run(make_recon_agent()) 
# initaccess = asyncio.run(make_initaccess_agent())
# planner = asyncio.run(make_planner_agent())   
# summary = asyncio.run(make_summary_agent())        

# agents = [recon, initaccess, planner, summary]

# workflow = create_swarm(
#     agents=agents,
#     default_active_agent="Planner",
# )

# swarm = workflow.compile(checkpointer=checkpointer)

# 동적 에이전트 생성 함수
async def create_agents():
    """사용자가 모델을 선택한 후 에이전트들을 동적으로 생성"""
    recon = await make_recon_agent()
    initaccess = await make_initaccess_agent()
    planner = await make_planner_agent()
    summary = await make_summary_agent()
    return [recon, initaccess, planner, summary]

async def create_dynamic_swarm():
    """동적으로 swarm 생성 - 모델 선택 후 호출"""
    logger.info("Creating dynamic swarm with InMemory persistence")
    
    agents = await create_agents()
    workflow = create_swarm(
        agents=agents,
        default_active_agent="Planner",
    )
    
    compiled_workflow = workflow.compile(
        checkpointer=checkpointer,  # ✅ InMemory 체크포인터 활성화
        store=store  # ✅ InMemory 스토어 활성화
    )
    
    logger.info("Swarm compiled with InMemory checkpointer and store")
    return compiled_workflow
```