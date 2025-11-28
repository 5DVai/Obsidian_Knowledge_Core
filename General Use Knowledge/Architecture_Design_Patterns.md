# Architecture & Design Patterns Bible

**Source:** 20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingested From:** .source_truth/20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingestion Date:** 2025-11-26

## Summary

This document provides a robust mental model for software architecture, defining it as the "important stuff" that is costly to change. It emphasizes managing complexity through **High Cohesion** and **Low Coupling**, achieved primarily via **Dependency Inversion**. It positions the **Modular Monolith** as the default starting point and treats **Microservices** as an organizational scaling pattern. It also covers modern AI architectures (RAG, Agents) as composite patterns.

## Key Principles

- **Dependency Inversion (DIP):** The core mechanism. High-level policy should not depend on low-level detail. Both should depend on abstractions.
- **Cohesion vs. Coupling:**
  - **Goal:** High Cohesion (things that change together stay together) and Low Coupling (minimal dependencies).
  - **Mechanism:** Dependency Injection (DI) is the tool to achieve DIP.
- **Monolith First:** Start with a Modular Monolith. Microservices are a "premium" architecture for scaling teams, not code.

## Architectural Styles

### 1. Hexagonal (Ports & Adapters)

- **Concept:** Isolate the "Inside" (Domain Core) from the "Outside" (UI, DB, APIs).
- **Components:**
  - **Ports:** Interfaces defined by the Domain (e.g., `IUserRepository`).
  - **Adapters:** Implementations defined by Infrastructure (e.g., `SqlUserRepository`).
- **Benefit:** Testability and technology agility.

### 2. Modular Monolith (The Default)

- **Concept:** Single deployment unit, but code is strictly partitioned into modules (e.g., `Billing`, `Shipping`) with defined public interfaces.
- **Rule:** No cross-module database joins. Communication via public APIs or in-process events.
- **Benefit:** Simplicity of a monolith with the structure of microservices.

### 3. Microservices

- **Context:** Organizational scaling pattern (Conway's Law).
- **Trade-off:** Accepts **high operational complexity** and **eventual consistency** for **independent deployability**.
- **Anti-Pattern:** "Distributed Monolith" (microservices with tight coupling).

## Core Patterns

### 1. CQRS (Command Query Responsibility Segregation)

- **Concept:** Split the system into **Write** (Command) and **Read** (Query) models.
- **Use Case:** High-performance read requirements, complex domains.

### 2. Event-Driven Architecture (EDA)

- **Event Notification:** "Something happened" (minimal payload). Decouples sender/receiver.
- **Event-Carried State Transfer:** "Something happened, here is the new state" (fat payload). Increases resilience, reduces callbacks.
- **Event Sourcing:** Store the *sequence of events*, not just current state. Perfect audit trail.

## AI Architectures

### 1. RAG (Retrieval-Augmented Generation)

- **Nature:** Composite pattern (Retriever + Generator).
- **RAGOps:** Managing the dual lifecycle of **Code** (slow change) and **Data** (fast change).
- **Pipeline:** Ingestion -> Verification -> Chunking/Embedding -> Vector DB -> Retrieval -> Generation.

### 2. AI Agents

- **Nature:** Composite pattern (LLM + Memory + Tools + Planning).
- **Key:** Agents are "loops" that perceive, reason, act, and observe.

## Operational Playbooks

- **Strangler Fig Pattern:** Migrate legacy systems by gradually replacing functionality with new services/modules, routing traffic away from the old system.
- **ADRs (Architectural Decision Records):** Document *why* a decision was made. Template: Title, Status, Context, Decision, Consequences.
