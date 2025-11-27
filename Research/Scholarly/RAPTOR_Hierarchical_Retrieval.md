---
type: brainstem
tier: S
domain: scholarly
canonical_id: "arXiv:2401.18059"
source_url: https://arxiv.org/abs/2401.18059
created: 2024-11-26
tags: [rag, hierarchical, tree-retrieval, summarization, long-documents]
promote_to_brainstem: true
related_notes:
  - [[Retrieval_Strategy_Core]]
  - [[RAG_Diagnostics_Eval]]
  - [[GraphRAG_Global_Summarization]]
---

# RAPTOR: Recursive Abstractive Processing for Tree-Organized Retrieval

## Overview

RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval) addresses the limitation of traditional RAG that retrieves only short contiguous chunks, which fails to capture large-scale discourse structure needed for thematic or multi-hop questions. RAPTOR builds a hierarchical tree structure by recursively clustering text chunks based on semantic similarity, then summarizing clusters using LLMs. This creates multiple levels of abstraction (from granular leaf nodes to high-level root summaries). At query time, RAPTOR uses collapsed tree retrieval to select nodes from appropriate abstraction levels. RAPTOR significantly outperforms BM25 and DPR baselines, achieving state-of-the-art results on NarrativeQA, QASPER, and QuALITY datasets.

## Key Mechanism

- **Tree Construction**: Start with leaf nodes (100-token chunks), embed with SBERT, cluster using Gaussian Mixture Models (GMM) with UMAP dimensionality reduction, summarize clusters with LLM, repeat recursively until root node
- **Soft Clustering**: Nodes can belong to multiple clusters (not hard assignment), capturing multi-topic content
- **Hierarchical Summarization**: Each parent node contains abstractive summary of its child cluster, preserving both high-level themes and granular details
- **Collapsed Tree Retrieval**: Flatten tree into single layer, compute cosine similarity between query and all nodes, select top-k nodes until token budget (e.g., 2000 tokens) is reached
- **Multi-Level Information**: Retrieval can mix nodes from different abstraction levels, matching query's required detail level

## When To Use

- **Long Document QA**: Questions requiring understanding of entire books, papers, or long narratives (e.g., "What are the main themes?")
- **Thematic Queries**: Global questions about corpus themes, patterns, or high-level summaries
- **Multi-Hop Reasoning**: Questions requiring information synthesis across multiple document sections
- **Variable Granularity Needs**: Queries that may need both detailed facts and high-level summaries
- **Corpus-Level Sensemaking**: Applications requiring holistic understanding of large text collections

## Failure Modes / Limitations

- **Tree Construction Cost**: Building tree requires multiple LLM summarization calls, increasing preprocessing time and token costs
- **Summarization Hallucinations**: LLM summarization may introduce minor hallucinations (~4% in study), though they don't propagate to parent nodes
- **Clustering Quality**: GMM clustering quality depends on embedding quality and UMAP parameters; poor clustering leads to incoherent summaries
- **Token Budget Management**: Collapsed tree retrieval requires computing similarity for all nodes, though FAISS can accelerate
- **Fixed Chunk Size**: Initial 100-token chunk size may not be optimal for all document types

## Implementation Hook

- Implement recursive tree construction: chunk documents (100 tokens), embed, cluster with GMM+UMAP, summarize clusters, repeat
- Use collapsed tree retrieval: embed query, compute cosine similarity with all tree nodes, select top-k until token budget
- Prefer collapsed tree over tree traversal (layer-by-layer) for flexibility in selecting appropriate abstraction levels
- Set token budget (e.g., 2000 tokens) to ensure context fits within model limits while maximizing information diversity

## What We Steal (10 Actionable Takeaways)

1. **Hierarchical Tree Index**: Build multi-level tree structure with leaf nodes (chunks) and parent nodes (summaries) for variable-granularity retrieval
2. **Recursive Clustering-Summarization**: Cluster semantically similar chunks, summarize clusters, re-embed and re-cluster summaries to build tree bottom-up
3. **Soft Clustering with GMM**: Use Gaussian Mixture Models (not hard k-means) to allow nodes to belong to multiple clusters
4. **UMAP Dimensionality Reduction**: Apply UMAP before clustering to handle high-dimensional embeddings effectively
5. **Collapsed Tree Retrieval**: Flatten tree for retrieval, select nodes from any level based on query similarity, not constrained to layer-by-layer traversal
6. **Token Budget Management**: Select nodes until reaching token limit (not fixed k), ensuring context fits model constraints
7. **Multi-Level Information Mixing**: Retrieve nodes from different abstraction levels simultaneously to match query granularity needs
8. **Abstractive Summarization**: Use LLM (GPT-3.5-turbo) to generate summaries that preserve key information while compressing text
9. **BIC for Cluster Selection**: Use Bayesian Information Criterion to determine optimal number of clusters automatically
10. **Linear Scaling**: Tree construction scales linearly with document length in both time and tokens, making it feasible for large corpora

## How This Changes Our Operator Contract

- **Global Query Handling**: Operator should recognize global/thematic queries and route to hierarchical retrieval (RAPTOR-style) instead of standard chunk retrieval
- **Multi-Pass Strategy**: For long documents, Operator can implement tree-based indexing as preprocessing step, then use collapsed tree retrieval at query time
- **Stop Conditions**: Add tree depth limits and token budget constraints for hierarchical retrieval to prevent excessive context

## Related Notes

- [[Retrieval_Strategy_Core]]: Multi-pass retrieval ladder, hierarchical approaches for global queries
- [[RAG_Diagnostics_Eval]]: Evaluating retrieval quality across different abstraction levels
- [[GraphRAG_Global_Summarization]]: Alternative graph-based approach for global query-focused summarization

