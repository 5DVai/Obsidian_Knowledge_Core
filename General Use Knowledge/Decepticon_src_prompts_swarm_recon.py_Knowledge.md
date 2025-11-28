# Swarm Reconnaissance Agent Prompt for Swarm Architecture
**Source:** Decepticon\src\prompts\swarm\recon.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The file "recon.py" defines a prompt for a Reconnaissance agent within a Swarm architecture. This architecture is designed to facilitate the coordination and intelligence gathering among multiple agents. The prompt outlines how reconnaissance data should be communicated to other agents, emphasizing the importance of proactive sharing of critical findings. The document provides structured examples of agent handoffs and highlights the need for enhanced output formats to ensure effective communication and decision-making.

This prompt is valuable for its structured approach to agent communication and coordination in a distributed system. It encapsulates best practices for intelligence sharing and prioritization, which are crucial for maintaining an efficient and responsive swarm of agents. The principles outlined can be applied to various domains where autonomous agents operate collaboratively.

## Key Concepts & Principles
- **Swarm Architecture:** A system design where multiple agents work collaboratively, sharing intelligence and coordinating actions.
- **Agent Handoff:** The process of transferring information and tasks between agents to ensure continuity and efficiency.
- **Proactive Intelligence Sharing:** The practice of disseminating critical findings immediately to enable swift action.
- **Enhanced Output Format:** A structured format for reporting findings that prioritize actionable intelligence.

## Detailed Technical Analysis

### Swarm Coordination
The prompt emphasizes the need for agents to gather intelligence and coordinate directly with others. This is crucial in a swarm architecture where the collective action of agents leads to successful outcomes. The coordination involves sharing critical findings proactively, which is a key aspect of maintaining an agile and responsive system.

### Agent Handoff Examples
The file provides specific examples of how reconnaissance findings should be communicated to different agents:
- **To Planner:** When a strategy is needed, findings are transferred to the Planner agent.
- **To Initial Access:** When vulnerabilities are ready to be exploited, information is passed to the Initial Access agent.
- **To Summary:** For documentation purposes, findings are sent to the Summary agent.

### Enhanced Output Format
The prompt suggests an enhanced output format that includes coordination notes and discovery alerts. This format ensures that critical findings are highlighted and prioritized, facilitating immediate attention from relevant agents.

### Autonomous Decision-Making
The prompt outlines principles for autonomous decision-making, such as sharing critical vulnerabilities immediately and prioritizing actionable findings. This approach ensures that intelligence is packaged for easy exploitation and that the focus remains on actionable data rather than comprehensive data collection.

## Enterprise Q&A Bank

1. **What is the primary purpose of the Swarm Reconnaissance agent prompt?**
   - To define how reconnaissance data should be communicated and coordinated among agents in a swarm architecture.

2. **How does the prompt facilitate agent coordination?**
   - By providing structured examples of agent handoffs and emphasizing proactive intelligence sharing.

3. **What is the significance of the enhanced output format?**
   - It ensures that critical findings are highlighted and prioritized for immediate attention.

4. **Why is proactive intelligence sharing important in swarm architecture?**
   - It enables swift action by other agents, maintaining the efficiency and responsiveness of the system.

5. **What are the key principles of autonomous decision-making outlined in the prompt?**
   - Sharing critical vulnerabilities immediately, packaging intelligence for easy exploitation, and prioritizing actionable findings.

## Actionable Takeaways
- Implement structured communication protocols for agent handoffs.
- Emphasize proactive sharing of critical findings to enable swift action.
- Utilize enhanced output formats to prioritize actionable intelligence.
- Focus on autonomous decision-making principles to maintain system efficiency.
- Apply these principles to other domains where autonomous agents operate collaboratively.

---
**Raw Content:**
```python
"""
Swarm 아키텍처용 Reconnaissance 에이전트 프롬프트

이 파일은 Swarm 아키텍처에서 Reconnaissance 에이전트가 사용할 추가 프롬프트를 정의합니다.
기본 프롬프트에 추가로 사용됩니다.
"""

SWARM_RECON_PROMPT = """
<swarm_coordination>
In swarm architecture, you gather intelligence and coordinate directly with other agents. Share critical findings proactively.

## Agent Handoff Examples:

**To Planner** (strategy needed):
`transfer_to_Planner("Reconnaissance complete. Found 5 critical vulnerabilities including Apache path traversal. Need exploitation strategy.")`

**To Initial Access** (ready to exploit):
`transfer_to_Initial_Access("Apache 2.4.49 vulnerable to CVE-2021-41773 on 192.168.1.100. Also found weak SSH on port 22.")`

**To Summary** (documentation needed):
`transfer_to_Summary("Reconnaissance phase complete. Please document findings and prioritize vulnerabilities.")`

## Enhanced Output Format:
Add to your standard REACT output:

## COORDINATION NOTES
[Critical findings requiring immediate agent attention]

## DISCOVERY ALERTS
[High-priority vulnerabilities or opportunities found]

## Autonomous Decision-Making:
- Share critical vulnerabilities immediately  
- Package intelligence for easy exploitation
- Prioritize actionable findings over comprehensive data

Provide intelligence that enables swift action by other agents.
</swarm_coordination>
"""
```