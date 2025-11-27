---
type: brainstem
tier: S
domain: scholarly
canonical_id: "Microsoft Research: From Local to Global"
source_url: https://www.microsoft.com/en-us/research/publication/from-local-to-global-a-graph-rag-approach-to-query-focused-summarization/
created: 2024-11-26
tags: [rag, graph, entity-knowledge-graph, community-summaries, global-queries]
promote_to_brainstem: true
related_notes:
  - [[Retrieval_Strategy_Core]]
  - [[RAPTOR_Hierarchical_Retrieval]]
---

# GraphRAG: From Local to Global - A Graph RAG Approach to Query-Focused Summarization

## Overview

GraphRAG addresses the failure of conventional RAG on global questions directed at entire text corpora (e.g., "What are the main themes in the dataset?") by building a graph-based text index. The approach uses an LLM in two stages: first to derive an entity knowledge graph from source documents, then to pregenerate community summaries for all groups of closely-related entities. Given a question, each community summary generates a partial response, then all partial responses are summarized into a final response. GraphRAG scales with both generality of user questions and quantity of source text, showing substantial improvements over naïve RAG for comprehensiveness and diversity on global sensemaking questions over 1M token datasets.

## Key Mechanism

- **Entity Knowledge Graph Construction**: LLM extracts entities and relationships from source documents to build knowledge graph
- **Community Detection**: Identify groups of closely-related entities (communities) in the graph
- **Community Summary Pre-generation**: For each community, pregenerate summaries that capture relationships and themes within that community
- **Query-Time Processing**: Given a question, use relevant community summaries to generate partial responses, then aggregate partial responses into final answer
- **Global Query Support**: Enables answering questions about entire corpus (themes, patterns, relationships) rather than just local factual lookups

## When To Use

- **Global Sensemaking Questions**: Queries about entire corpus themes, patterns, relationships (e.g., "What are the main themes?", "How are entities connected?")
- **Corpus-Level Analysis**: Applications requiring holistic understanding of large document collections (1M+ tokens)
- **Entity Relationship Queries**: Questions about how entities relate to each other across documents
- **Query-Focused Summarization**: Tasks requiring summarization of relevant information across entire corpus for specific query
- **Intelligence Analysis**: Scenarios where understanding connections among people, places, events is critical

## Failure Modes / Limitations

- **Graph Construction Cost**: Building entity knowledge graph and community summaries requires significant LLM preprocessing
- **Entity Extraction Quality**: Graph quality depends on LLM's ability to accurately extract entities and relationships
- **Community Detection Accuracy**: Poor community detection leads to incoherent or incomplete community summaries
- **Scalability Limits**: May not scale well beyond 1M token range without further optimization
- **Local Query Overhead**: For simple factual queries, graph-based approach may be overkill compared to standard RAG

## Implementation Hook

- Implement two-stage graph construction: (1) extract entities and relationships to build knowledge graph, (2) detect communities and pregenerate summaries
- Use community summaries as retrieval units instead of document chunks for global queries
- Generate partial responses from relevant community summaries, then aggregate into final response
- Combine with local RAG for hybrid approach: use graph-based retrieval for global queries, standard RAG for local queries

## What We Steal (10 Actionable Takeaways)

1. **Entity Knowledge Graph Index**: Build graph-based index with entities as nodes and relationships as edges, instead of flat document chunks
2. **Community Summary Pre-generation**: Precompute summaries for entity communities to enable fast retrieval for global queries
3. **Two-Stage Graph Construction**: First extract entities/relationships, then detect communities and summarize, separating structure from content
4. **Partial Response Generation**: Generate responses from individual community summaries, then aggregate for final answer
5. **Global vs. Local Routing**: Distinguish global queries (thematic, corpus-level) from local queries (factual lookup), route to appropriate retrieval method
6. **Query-Focused Summarization**: Frame global queries as QFS task rather than retrieval task, requiring summarization across corpus
7. **Entity Relationship Modeling**: Capture relationships between entities across documents to enable relationship-based queries
8. **Community Detection**: Use graph algorithms to identify closely-related entity groups for coherent summarization
9. **Hybrid RAG Architecture**: Combine graph-based retrieval (global) with standard RAG (local) for comprehensive coverage
10. **Scalable Preprocessing**: Design graph construction to scale with corpus size, enabling indexing of large document collections

## How This Changes Our Operator Contract

- **Query Type Classification**: Operator must distinguish global queries (thematic, corpus-level) from local queries (factual), route to graph-based or standard retrieval
- **Preprocessing Requirements**: Operator should support graph index construction as preprocessing step for corpora requiring global query support
- **Stop Conditions**: Add graph-specific limits (max communities, max summary length) to prevent excessive preprocessing or context

## Related Notes

- [[Retrieval_Strategy_Core]]: Global query handling, fallback to hierarchical/graph approaches
- [[RAPTOR_Hierarchical_Retrieval]]: Alternative hierarchical approach for long-document and thematic queries

