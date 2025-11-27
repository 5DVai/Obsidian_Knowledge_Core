---
type: brainstem
tier: S
domain: operator
last_updated: 2024-11-26
tags: [operator, contract, retrieval, routing, stop-conditions]
---

# Operator Contract

## Operator Quickstart (10 lines)

The Operator is the retrieval and routing layer for the Brainstem knowledge core. It enforces hard caps: MAX 5 sources per ingestion run, MAX 2 Brainstem modules per update cycle. All other requests queue in `Research/_inbox/queue.md`. The Operator prioritizes RAG control, evaluation, and memory architecture papers over general LLM history. Each distilled note must include: Overview, Key Mechanism, When To Use, Failure Modes, Implementation Hook, Related Notes. The Operator maintains blueprint-first, markdown-first methodology—Stage 1 is conversational blueprinting, Stage 2 is AI-assisted code generation.

## Retrieval Rules

- **Source Selection**: Prioritize Tier S sources (official docs, canonical papers, authoritative specs) over Tier 1 (curated secondary sources)
- **Domain Focus**: RAG control + evaluation + memory architecture papers take precedence over general LLM papers
- **Distillation Only**: No long raw dumps; compress to 1-2 page notes with structured sections
- **Hard Caps**: MAX 5 sources per ingestion run; MAX 2 Brainstem modules per update cycle
- **Queue Management**: Anything exceeding caps goes to `Research/_inbox/queue.md` for future processing

## Tool Routing (MCP)

- **Paper Search**: Use `mcp_Paper_Search_search_arxiv` and `mcp_Paper_Search_search_semantic` for scholarly sources
- **Paper Reading**: Use `mcp_Paper_Search_read_arxiv_paper` and `mcp_Paper_Search_read_semantic_paper` for content extraction
- **Web Sources**: Use `mcp_Exa_Search_web_search_exa` + `mcp_MCP_DOCKER_webpage-to-markdown` for official docs/specs only
- **Knowledge Graph**: Use `mcp_MCP_DOCKER_create_entities` and `mcp_MCP_DOCKER_create_relations` for optional entity extraction
- **Obsidian**: Use `mcp_MCP_DOCKER_obsidian_*` tools for vault operations if available

## Stop Conditions

- **Source Limit Reached**: Stop when 5 sources have been ingested
- **Module Limit Reached**: Stop when 2 Brainstem modules have been created/updated
- **Queue Full**: Redirect to `Research/_inbox/queue.md` when limits are reached
- **Quality Threshold**: If a source cannot be meaningfully distilled (too shallow, irrelevant, or duplicate), skip and move to next
- **Time/Token Budget**: If approaching limits, prioritize highest-value sources and queue the rest

## Promotion Rules (Tier S/1 only)

- **Tier S Criteria**: Official documentation, canonical academic papers (highly cited, foundational), authoritative specifications, ground-truth references
- **Tier 1 Criteria**: High-quality curated sources, expert community resources, well-maintained learning platforms, reputable aggregators
- **No Promotion Below Tier 1**: General blog posts, tutorials, opinion pieces, or unverified sources remain outside Brainstem
- **Review Process**: Before promoting, verify source authority, recency (if applicable), and alignment with Brainstem domain focus

