# Swarm Architecture Planner Agent Prompts
**Source:** Decepticon\src\prompts\swarm\planner.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The file `planner.py` is a critical component in the Swarm architecture, specifically designed to enhance the functionality of the Planner agent. This file defines additional prompts that are used to guide the Planner agent in coordinating with other agents within the Swarm system. The primary role of the Planner agent is to act as both a strategist and a coordinator, facilitating efficient and effective communication and task delegation among agents. The prompts outlined in this file are essential for maintaining strategic oversight and ensuring that all agents are aligned with the overall objectives of the operation.

The value of this file lies in its structured approach to agent coordination, which is crucial for the success of Swarm-based systems. By providing clear guidelines for agent handoffs and enhancing the output format, the file ensures that the Planner agent can perform its duties with precision and clarity. This contributes to the overall agility and adaptability of the Swarm architecture, making it a valuable asset for enterprise-grade systems that require dynamic and responsive operations.

## Key Concepts & Principles
- **Swarm Architecture**: A system design that involves multiple agents working collaboratively to achieve common goals.
- **Planner Agent**: An agent responsible for strategizing and coordinating tasks among other agents.
- **Agent Handoff**: The process of transferring tasks and responsibilities from one agent to another.
- **Strategic Oversight**: Maintaining a high-level view of operations to ensure alignment with objectives.
- **Enhanced Output Format**: A structured format for agent communication that includes coordination details and operation status.

## Detailed Technical Analysis

### Swarm Coordination
The Swarm architecture relies on direct coordination between agents, facilitated by the Planner agent. This coordination is achieved through well-defined handoffs, where tasks and responsibilities are transferred from one agent to another. The file provides examples of such handoffs, including transfers to Reconnaissance, Initial Access, and Summary agents. Each handoff is accompanied by guidelines that emphasize the importance of clear objectives, context, and expected deliverables.

### Enhanced Output Format
To improve communication and coordination, the file introduces an enhanced output format. This format includes sections for agent coordination and swarm status, ensuring that all agents have access to the latest operational information. By standardizing the output format, the file enhances the clarity and effectiveness of agent interactions.

## Enterprise Q&A Bank

1. **What is the role of the Planner agent in Swarm architecture?**
   - The Planner agent acts as both a strategist and coordinator, facilitating communication and task delegation among agents.

2. **How does the Planner agent coordinate with other agents?**
   - Coordination is achieved through agent handoffs, where tasks and responsibilities are transferred with clear objectives and context.

3. **What are the key components of the enhanced output format?**
   - The enhanced output format includes sections for agent coordination and swarm status, providing a structured approach to communication.

4. **Why is strategic oversight important in Swarm architecture?**
   - Strategic oversight ensures that all agents are aligned with the overall objectives, contributing to the success of the operation.

5. **How does the file contribute to the adaptability of the Swarm system?**
   - By providing clear guidelines and a standardized output format, the file enhances the system's ability to respond dynamically to changing conditions.

## Actionable Takeaways
- Ensure all agent handoffs include clear objectives, context, and expected deliverables.
- Maintain strategic oversight to align agent activities with overall objectives.
- Utilize the enhanced output format to standardize communication and coordination.
- Regularly review and update prompts to adapt to evolving operational needs.
- Foster a culture of clear and actionable communication among agents.

---
**Raw Content:**
```python
"""
Swarm 아키텍처용 Planner 에이전트 프롬프트

이 파일은 Swarm 아키텍처에서 Planner 에이전트가 사용할 추가 프롬프트를 정의합니다.
기본 프롬프트에 추가로 사용됩니다.
"""

SWARM_PLANNER_PROMPT = """
<swarm_coordination>
In swarm architecture, you coordinate other agents directly through handoffs. You are both strategist and coordinator.

## Agent Handoff Examples:

**To Reconnaissance**: 
`transfer_to_Reconnaissance`

**To Initial Access**:
`transfer_to_Initial_Access`

**To Summary**:
`transfer_to_Summary`

## Handoff Guidelines:
- Provide clear objectives and context
- Include all relevant findings and intelligence
- Specify expected deliverables
- Maintain strategic oversight across handoffs

## Enhanced Output Format:
Add to your standard output:

## AGENT COORDINATION
[Next agent transfer decision and task description]

## SWARM STATUS  
[Current operation state and coordination needs]

Direct coordination enables rapid, adaptive operations. Make handoffs clear and actionable.
</swarm_coordination>
"""
```