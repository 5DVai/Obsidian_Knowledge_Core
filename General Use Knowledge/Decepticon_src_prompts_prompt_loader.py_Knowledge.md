# Prompt Loader for Agent-Based Systems
**Source:** Decepticon\src\prompts\prompt_loader.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `prompt_loader.py` file is a crucial component in the Decepticon system, responsible for dynamically loading prompts based on agent personas and architectural configurations. This module facilitates the integration of various agent types with specific prompts, enabling a flexible and scalable approach to managing agent interactions. The file encapsulates the logic for combining base terminal prompts with persona-specific and architecture-specific prompts, ensuring that agents operate with the correct contextual information.

This file is valuable for its reusable logic in managing agent prompts, which can be adapted to various enterprise applications requiring dynamic configuration of agent behaviors. The design pattern employed here allows for easy extension and modification, making it suitable for systems that need to support multiple agent types and architectures.

## Key Concepts & Principles
- **Agent Persona Mapping:** Associates specific prompts with agent personas to tailor interactions.
- **Architecture-Specific Logic:** Adjusts prompt loading based on the system architecture (e.g., swarm, hierarchical).
- **Dynamic Prompt Composition:** Combines base prompts with persona and architecture-specific prompts.
- **Error Handling:** Provides mechanisms to handle unknown agent requests gracefully.

## Detailed Technical Analysis

### Agent Persona and Architecture Mapping
The file defines two dictionaries, `PERSONA_PROMPTS` and `SWARM_PROMPTS`, which map agent names to their respective prompts. This mapping allows the system to easily retrieve and compose the correct prompts for each agent type.

### Dynamic Prompt Loading
The `load_prompt` function is the core utility that combines the base terminal prompt with persona-specific prompts. It further appends architecture-specific prompts if the architecture is set to "swarm." This function ensures that agents receive a comprehensive prompt tailored to their role and operational context.

### Error Handling and Validation
The function includes validation to ensure that only known agent names are processed. If an unknown agent name is provided, a `ValueError` is raised, listing available agents. This approach prevents runtime errors and guides users towards valid configurations.

### Utility Functions
Additional utility functions, such as `get_available_agents`, `get_supported_architectures`, and `get_terminal_base_prompt`, provide easy access to configuration details, enhancing the module's usability and integration capabilities.

## Enterprise Q&A Bank

1. **Q:** How does the system ensure that agents receive the correct prompts?
   **A:** The system uses a mapping of agent personas to prompts and dynamically composes these with architecture-specific prompts to ensure accuracy.

2. **Q:** What happens if an unknown agent name is provided?
   **A:** The system raises a `ValueError` and provides a list of available agents to guide the user.

3. **Q:** Can this module support new agent types easily?
   **A:** Yes, by adding new entries to the `PERSONA_PROMPTS` and `SWARM_PROMPTS` dictionaries, the system can support new agent types.

4. **Q:** How does the module handle different architectures?
   **A:** The module uses the `architecture` parameter to adjust prompt composition, adding specific prompts for the "swarm" architecture.

5. **Q:** Is it possible to retrieve just the base terminal prompt?
   **A:** Yes, the `get_terminal_base_prompt` function returns the base terminal prompt for debugging or testing purposes.

## Actionable Takeaways
- Ensure all agent types are defined in the `PERSONA_PROMPTS` dictionary.
- Validate agent names before attempting to load prompts.
- Extend the system by adding new prompts to the existing dictionaries.
- Use utility functions to access configuration details and enhance integration.
- Consider architecture-specific logic when designing prompt systems for agents.

---
**Raw Content:**
```python
# base terminal management
from src.prompts.base.terminal import BASE_TERMINAL_PROMPT

# personas
from src.prompts.personas.reconnaissance_persona import RECONNAISSANCE_PERSONA_PROMPT
from src.prompts.personas.initial_access_persona import INITIAL_ACCESS_PERSONA_PROMPT
from src.prompts.personas.planner_persona import PLANNER_PERSONA_PROMPT
from src.prompts.personas.summary_persona import SUMMARY_PERSONA_PROMPT
from src.prompts.personas.supervisor_persona import SUPERVISOR_PERSONA_PROMPT

# swarm coordination
from src.prompts.swarm.summary import SWARM_SUMMARY_PROMPT
from src.prompts.swarm.planner import SWARM_PLANNER_PROMPT
from src.prompts.swarm.recon import SWARM_RECON_PROMPT
from src.prompts.swarm.initaccess import SWARM_INITACCESS_PROMPT

# additional tools
from src.prompts.tools.swarm_handoff_tools import SWARM_HANDOFF_TOOLS_PROMPT

# 에이전트 타입과 프롬프트 매핑
PERSONA_PROMPTS = {
    "reconnaissance": RECONNAISSANCE_PERSONA_PROMPT,
    "initial_access": INITIAL_ACCESS_PERSONA_PROMPT,
    "planner": PLANNER_PERSONA_PROMPT,
    "summary": SUMMARY_PERSONA_PROMPT,
    "supervisor": SUPERVISOR_PERSONA_PROMPT
}

SWARM_PROMPTS = {
    "reconnaissance": SWARM_RECON_PROMPT,
    "initial_access": SWARM_INITACCESS_PROMPT,
    "planner": SWARM_PLANNER_PROMPT,
    "summary": SWARM_SUMMARY_PROMPT,
    "supervisor": ""  # Supervisor는 swarm 전용 프롬프트 없음
}

def load_prompt(agent_name: str, architecture: str = "swarm"):
    """
    통합 프롬프트 로더 - 모든 에이전트의 프롬프트를 로드
    
    Args:
        agent_name: "reconnaissance", "initial_access", "planner", "summary", "supervisor"
        architecture: "swarm", "hierarchical", "standalone"
    
    Returns:
        완성된 에이전트 프롬프트
        
    Examples:
        load_prompt("reconnaissance", "swarm")
        load_prompt("initial_access", "standalone") 
        load_prompt("supervisor", "hierarchical")
    """
    if agent_name not in PERSONA_PROMPTS:
        available_agents = list(PERSONA_PROMPTS.keys())
        raise ValueError(f"Unknown agent: {agent_name}. Available agents: {available_agents}")
    
    # 기본 구조: 터미널 + 페르소나
    prompt = BASE_TERMINAL_PROMPT + PERSONA_PROMPTS[agent_name]
    
    # Swarm 아키텍처인 경우 추가 기능
    if architecture == "swarm" and agent_name != "supervisor":
        swarm_prompt = SWARM_PROMPTS.get(agent_name, "")
        if swarm_prompt:
            prompt += swarm_prompt
        prompt += SWARM_HANDOFF_TOOLS_PROMPT
    
    return prompt

def get_available_agents():
    """사용 가능한 에이전트 목록 반환"""
    return list(PERSONA_PROMPTS.keys())

def get_supported_architectures():
    """지원되는 아키텍처 목록 반환"""
    return ["swarm", "hierarchical", "standalone"]

def get_terminal_base_prompt():
    """터미널 기본 프롬프트만 반환 (디버깅/테스트용)"""
    return BASE_TERMINAL_PROMPT
```