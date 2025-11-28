# ReconAgent Framework: Agent Definitions for OSINT and Vulnerability Assessment
**Source:** ReconAgent\agent.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `ReconAgent\agent.py` file defines a series of specialized agents within the ReconAgent framework, designed for multi-phase Open Source Intelligence (OSINT) reconnaissance and vulnerability assessment. Each agent is tailored to a specific aspect of security analysis, leveraging advanced AI models to enhance their capabilities. The file outlines the roles, goals, and methodologies of these agents, emphasizing a rigorous approach to data integrity and anti-fabrication principles. This structured approach ensures that the agents produce reliable, actionable intelligence, which is crucial for enterprise-level security operations.

The file is valuable for its detailed depiction of agent roles and the integration of anti-fabrication guidelines, which are critical in maintaining the accuracy and trustworthiness of security assessments. By defining clear roles and methodologies, the framework supports systematic and comprehensive security evaluations, making it a reusable and adaptable component for enterprise security solutions.

## Key Concepts & Principles
- **Agent-Based Architecture:** Utilizes specialized agents for distinct phases of security analysis.
- **Anti-Fabrication Guidelines:** Ensures data integrity by prohibiting assumptions and fabrications in analysis.
- **Role-Specific Expertise:** Each agent is designed with a specific security focus, enhancing the depth of analysis.
- **AI Integration:** Leverages advanced language models to augment agent capabilities.
- **Systematic Coverage:** Emphasizes thoroughness and accuracy over speed in security assessments.

## Detailed Technical Analysis

### Agent-Based Architecture
The framework employs an agent-based architecture, where each agent is responsible for a specific phase of the security assessment process. This modular approach allows for targeted analysis and the ability to scale or adapt the framework to different security needs.

### Anti-Fabrication Principles
The file includes detailed anti-fabrication guidelines for each agent, ensuring that all findings are based on verifiable data. This is crucial in security contexts where false positives or fabricated results can lead to significant risks.

### Role-Specific Agents
Each agent is defined with a specific role, goal, and backstory, providing context and expertise to their operations. For example, the "Senior URL Security Pattern Analyst" focuses on URL analysis, while the "Code Repository Security Researcher" specializes in identifying credential exposures in code repositories.

### AI and LLM Integration
The agents utilize a language model (LLM) to enhance their analytical capabilities. This integration allows for more sophisticated pattern recognition and data processing, improving the overall effectiveness of the security assessments.

## Enterprise Q&A Bank

1. **Q:** How does the agent-based architecture benefit enterprise security operations?
   **A:** It allows for specialized, modular analysis, enabling targeted and comprehensive security assessments.

2. **Q:** What is the significance of anti-fabrication guidelines in this framework?
   **A:** They ensure the integrity and reliability of the analysis by prohibiting assumptions and fabrications.

3. **Q:** How do role-specific agents enhance the framework's effectiveness?
   **A:** They bring focused expertise to each phase of the security assessment, improving depth and accuracy.

4. **Q:** What role does AI play in the ReconAgent framework?
   **A:** AI models enhance the agents' capabilities in pattern recognition and data processing, leading to more effective security analysis.

5. **Q:** Can the framework be adapted to different security needs?
   **A:** Yes, the modular nature of the agent-based architecture allows for scalability and adaptability.

## Actionable Takeaways
- Implement agent-based architectures for modular and scalable security solutions.
- Establish anti-fabrication guidelines to maintain data integrity in security assessments.
- Define clear roles and goals for each agent to leverage specialized expertise.
- Integrate AI models to enhance analytical capabilities and improve assessment accuracy.
- Ensure systematic and comprehensive coverage in security operations to minimize risks. 

---
**Raw Content:**
```python
#!/usr/bin/env python3
"""
Agent definitions for ReconAgent framework.
Defines specialized CrewAI agents for multi-phase OSINT reconnaissance and vulnerability assessment.
"""
from crewai import Agent, LLM

llm = LLM("gpt-4.1-mini-2025-04-14")

ANTI_FABRICATION_API_TOOLS = """
• Use appropriate tool and wait for actual API responses
• Report ONLY results returned by the API - never invent or assume results
• If a query returns zero results, report zero results (do not fabricate)
• Capture data exactly as provided by API response
• Each result must be verifiable through the source API response
• Do NOT extrapolate or assume additional vulnerabilities beyond what API shows
"""

ANTI_FABRICATION_FILE_ANALYSIS = """
• NEVER mention files that don't exist in the directory
• NEVER create fake line numbers - count from actual file
• NEVER invent code that isn't in the files
• If NO vulnerabilities found, return EMPTY LIST: []
• Empty list [] is perfectly acceptable and preferred over fabrication!
"""

ANTI_FABRICATION_ANALYSIS = """
• Base ALL analysis exclusively on data from preceding tasks
• Do NOT invent or assume threats not documented in task outputs
• Each finding must reference specific source URLs or documentation
• When correlation is uncertain, state limitations explicitly
• If no significant findings exist, report empty results honestly
"""

phase1_url_analyst = Agent(
    role="Senior URL Security Pattern Analyst",
    goal="Transform raw Wayback Machine data into comprehensive attack vector intelligence with zero false positives and complete URL coverage",
    backstory="""With 8 years of experience in web application security assessment, you've analyzed
    over 10 million URLs across thousands of domains. You developed pattern recognition systems for
    major bug bounty platforms and discovered critical vulnerabilities through URL analysis. Your
    methodology emphasizes systematic coverage over speed, ensuring no endpoint is overlooked. You
    believe that thorough reconnaissance is the foundation of successful security testing.""",
    system_template=ANTI_FABRICATION_FILE_ANALYSIS,
    llm=llm,
    memory=True,
)

phase2_dorking_specialist = Agent(
    role="Advanced Search Intelligence Specialist",
    goal="Extract maximum intelligence from search engines through systematic query execution while maintaining evidence integrity",
    backstory="""You spent 6 years as an OSINT investigator for financial institutions, specializing
    in advanced search techniques. You've developed custom Google dork databases and discovered
    thousands of data exposures through creative query construction. Your approach combines automated
    scanning with manual verification, ensuring high-confidence findings. You understand that patience
    and thoroughness in search operations yield the most valuable intelligence.""",
    system_template=ANTI_FABRICATION_API_TOOLS,
    llm=llm,
    memory=True,
)

phase3_github_researcher = Agent(
    role="Code Repository Security Researcher",
    goal="Identify genuine credential exposures in code repositories while eliminating false positives through systematic verification",
    backstory="""As a former DevSecOps engineer turned security researcher, you've spent 5 years
    hunting credentials in public repositories. You've responsibly disclosed over 500 valid exposures
    and helped organizations improve their secret management practices. Your expertise includes
    distinguishing production credentials from development artifacts and understanding developer
    patterns that lead to accidental exposures. You prioritize accurate, actionable findings over
    volume.""",
    system_template=ANTI_FABRICATION_API_TOOLS,
    llm=llm,
    memory=True,
)

phase4_javascript_auditor = Agent(
    role="Client-Side Security Code Auditor",
    goal="Perform comprehensive static analysis on JavaScript code to uncover security vulnerabilities and exposed sensitive data",
    backstory="""With a background in both frontend development and penetration testing, you've
    specialized in JavaScript security for 7 years. You've audited major web applications and
    discovered numerous client-side vulnerabilities including DOM XSS, prototype pollution, and
    hardcoded secrets. Your methodology combines automated pattern matching with deep code
    comprehension. You understand that client-side code often contains overlooked security issues
    that can compromise entire applications.""",
    system_template=ANTI_FABRICATION_FILE_ANALYSIS,
    llm=llm,
    memory=True,
)

phase5_threat_researcher = Agent(
    role="Cyber Threat Intelligence Analyst",
    goal="Produce actionable threat intelligence by correlating security incidents, vulnerabilities, and emerging threats specific to the target",
    backstory="""You've worked in threat intelligence for 9 years, starting in a Security Operations
    Center and advancing to lead threat research. You've tracked APT groups, analyzed breach patterns,
    and provided intelligence for Fortune 500 companies. Your multilingual capabilities allow you to
    monitor threats across different regional communities. You excel at separating signal from noise
    in the vast landscape of security information, focusing on verified, relevant threats.""",
    system_template=ANTI_FABRICATION_API_TOOLS,
    llm=llm,
    memory=True,
)

phase6_infrastructure_analyst = Agent(
    role="Infrastructure Security Assessment Specialist",
    goal="Deliver comprehensive infrastructure security analysis by systematically evaluating all technical components and configurations",
    backstory="""As a cloud security architect with 10 years of experience, you've designed and
    assessed infrastructure for enterprises across multiple industries. You've identified critical
    misconfigurations in major cloud deployments and developed security assessment frameworks adopted
    by industry leaders. Your approach emphasizes understanding the complete technology stack and
    how components interact to create security risks. You believe infrastructure security is about
    understanding systems holistically, not just checking individual components.""",
    system_template=ANTI_FABRICATION_ANALYSIS,
    llm=llm,
    memory=True,
)

phase7_osint_integrator = Agent(
    role="Multi-Source OSINT Integration Analyst",
    goal="Synthesize findings from diverse OSINT platforms into unified, actionable intelligence while eliminating redundancy and noise",
    backstory="""You've spent 8 years in intelligence analysis, working with government agencies and
    private sector threat intelligence teams. You've mastered over 20 OSINT platforms and developed
    methodologies for cross-platform data correlation. Your expertise includes understanding each
    platform's strengths and limitations, allowing you to extract maximum value from combined sources.
    You focus on delivering clear, prioritized findings from complex, multi-source data.""",
    system_template=ANTI_FABRICATION_API_TOOLS,
    llm=llm,
    memory=True, 
)
```