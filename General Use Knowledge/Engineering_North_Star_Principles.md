# Engineering North Star & Principles Bible

**Source:** 20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingested From:** .source_truth/20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingestion Date:** 2025-11-26

## Summary

This document serves as the "engineering constitution" for the 5thDimensionVibesCoding project. It defines the core values, decision heuristics, and strict rules for software engineering, security, and AI integration. It establishes a "stable taste" for engineering trade-offs (e.g., safety vs. speed) and provides concrete guidelines for building maintainable, secure, and AI-native systems.

## Key Points

- **Core Values:** Simplicity (KISS), Safety & Security, Maintainability, Automation-First, Transparency, User-centricity.
- **Decision Matrix:** When trade-offs are ambiguous, prioritize: **Safety & Compliance > Longevity > Time-to-Market > Complexity**.
- **No-Hallucination Policy:** Strict adherence to factual accuracy; AI agents must not invent features or APIs.
- **AI as Teammate:** AI agents are treated as teammates with limited scope and guardrails, not autonomous "gods".
- **Security First:** "Never trust inputs", "Least Privilege", and "Privacy-by-Design" are non-negotiable.
- **Monolith First:** Default to Modular Monolith; use Microservices only when strictly necessary for scale or isolation.

## Detailed Content

### 1. Core Values & Philosophies

* **Simplicity (KISS):** Prefer the simplest solution. Complexity is a bug.
- **Safety & Security:** Includes reliability, resilience, and AI safety (robustness, bias mitigation).
- **Maintainability:** High cohesion, low coupling. Code is read more than written.
- **Automation-First:** Automate any task done more than N times/quarter.
- **Transparency:** Document decisions; logs/metrics are first-class signals.
- **User-centricity:** Privacy-by-design, data minimization.

### 2. Decision Heuristics

* **YAGNI (You Ain't Gonna Need It):** Don't build for hypothetical futures.
- **DRY (Don't Repeat Yourself):** Single source of truth for logic and data.
- **SOLID + Law of Demeter:** Design for loose coupling.
- **SRE Error Budgets:** Reliability governs feature velocity.
- **Decision Matrix:**
    1. **Safety & Compliance** (Weight ~10)
    2. **Longevity & Evolvability**
    3. **Time-to-Market**
    4. **Cognitive Load**

### 3. Secure Software Development (SSDL)

* **Input Validation:** Validate ALL external data server-side.
- **Least Privilege:** Minimal permissions for users, services, and AI agents.
- **Secrets Management:** Centralized control plane (e.g., Vault), never in code/logs.
- **AI Safety:** Assume prompt injection is possible; use layered defenses (classifiers, sanitization).

### 4. Code Quality & Design

* **SRP (Single Responsibility Principle):** One reason to change.
- **Composition over Inheritance:** Avoid brittle class hierarchies.
- **Small Functions:** Long functions hide responsibilities.
- **No Global Mutable State:** Use dependency injection.

### 5. Documentation

* **Docstrings:** Mandatory for all public modules/classes/functions.
- **"Why, not What":** Comments explain intent, not syntax.
- **Docs-as-Code:** Versioned, reviewed, and built (Sphinx/MkDocs).

### 6. Reliability & Concurrency

* **12-Factor App:** Stateless processes, logs as event streams.
- **Resilience Patterns:** Circuit Breakers, Bulkheads, Timeouts with Backoff.
- **Observability:** SLIs (Indicators) and SLOs (Objectives) for every production service.

### 7. Automation & CI/CD

* **Pipeline:** Build -> Lint -> Test (Unit/Int/Sec) -> SAST -> Deploy.
- **IaC (Infrastructure as Code):** Immutable infrastructure defined in code (Terraform/Pulumi).
- **"Push on Green":** Automate deployments where possible.

### 8. Testing Strategy

* **Test Pyramid:** Heavy on Unit, moderate on Integration, light on E2E.
- **Metrics:** Monitor Cyclomatic Complexity (CC < 10) and Change Failure Rate (CFR < 5%).

### 9. AI & Agentic Engineering

* **Trustworthy AI:** Valid, reliable, secure, explainable.
- **Model Governance:** Version data, models, and pipelines.
- **Agent Guardrails:** Prompt sanitization, human-in-the-loop for high-stakes actions.

### 10. Operational Playbooks

* **Greenfield:** Start with Modular Monolith, CI/CD, and strict standards from Day 1.
- **Brownfield:** Use Strangler Fig pattern to refactor; measure tech debt reduction.
- **AI Integration:** Monitor AI coding assistants; treat AI-generated bugs as standard defects.

### 11. Governance

* **Scorecards:** Track Code Health, Delivery Velocity, Security Incidents, and AI Performance.
- **Living Document:** This Bible is version-controlled and evolves via PRs.
