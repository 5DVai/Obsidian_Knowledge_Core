---
type: brainstem
tier: S
domain: scholarly
source_url: https://www.semanticscholar.org/paper/e90435e1ae06fab4efa272f5f46ed74ca0a8cde0
arxiv_id: "2404.13781"
last_updated: 2024-11-26
tags: [rag, evaluation, retrieval-quality, metrics, erag]
related_projects: []
---

# eRAG: Evaluating Retrieval Quality in Retrieval-Augmented Generation

## Overview

eRAG (Salemi & Zamani, 2024) proposes a novel evaluation approach for retrieval models within RAG systems. Instead of using human annotations or query-document relevance labels (which show low correlation with downstream RAG performance), eRAG uses the LLM in the RAG system itself as the evaluator by testing each retrieved document individually.

## Key Mechanism

- **Per-Document Evaluation**: Each document in the retrieval list is individually fed to the LLM with the query, and the generated output is evaluated using downstream task metrics (accuracy, exact match, ROUGE)
- **Document-Level Annotations**: The downstream performance for each document serves as its relevance label: `G_q[d] = E_M(M(q, {d}), y)` where `E_M` is the evaluation function, `M` is the LLM, `q` is the query, `d` is the document, and `y` is ground truth
- **Aggregation**: Document-level scores are aggregated using set-based or ranking metrics (Precision, Recall, MAP, MRR, NDCG, Hit Rate)
- **Efficiency**: Processes documents individually rather than concatenating all documents, reducing memory from O(lk²d²) to O(lkd²) and consuming up to 50x less GPU memory

## When To Use

- **RAG System Development**: Evaluating and optimizing retrieval models within RAG pipelines
- **Retriever Comparison**: Comparing different retrieval models (sparse vs. dense) on their contribution to downstream performance
- **Resource-Constrained Evaluation**: When end-to-end evaluation is too computationally expensive
- **Iterative Optimization**: When using interleaving or other ranking optimization techniques that require document-level feedback

## Failure Modes

- **LLM Dependency**: Requires the same LLM used in production RAG system; correlation may vary with LLM size
- **Task-Specific**: Evaluation metrics must match the downstream task (EM for QA, Accuracy for classification, F1 for generation)
- **Single-Document Limitation**: Doesn't capture interactions between multiple retrieved documents
- **Correlation Not Perfect**: While better than baselines, correlation with downstream performance ranges from 0.168 to 0.494 improvement, not perfect

## Implementation Hook

- **Python Implementation**: Available at https://github.com/alirezasalemi7/eRAG
- **Key Components**: 
  - Document-level LLM evaluation function
  - Aggregation metrics (MAP, MRR, NDCG, Precision, Recall, Hit Rate)
  - Support for multiple downstream task metrics
- **Integration**: Can be integrated into existing RAG evaluation pipelines as a drop-in replacement for relevance-based evaluation

## Related Notes

- [[RAGBench_Explainable_Benchmark]] - Comprehensive RAG evaluation benchmark
- [[FlashRAG_Modular_Toolkit]] - RAG research toolkit with evaluation components

