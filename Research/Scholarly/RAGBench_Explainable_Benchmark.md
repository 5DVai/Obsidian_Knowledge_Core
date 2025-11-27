---
type: brainstem
tier: S
domain: scholarly
source_url: https://www.semanticscholar.org/paper/1b0aba023d7aa5fb9853f9e942efb5c243dc1201
arxiv_id: "2407.11005"
last_updated: 2024-11-26
tags: [rag, benchmark, evaluation, trace, hallucination-detection]
related_projects: []
---

# RAGBench: Explainable Benchmark for Retrieval-Augmented Generation Systems

## Overview

RAGBench (Friel et al., 2024) is the first comprehensive, large-scale RAG benchmark dataset of 100k examples covering five industry-specific domains. It introduces the TRACe evaluation framework (Utilization, Relevance, Adherence, Completeness) for explainable and actionable RAG evaluation metrics applicable across all RAG domains.

## Key Mechanism

- **TRACe Framework**: Four metrics measuring RAG system quality:
  - **Utilization**: Fraction of retrieved context used by generator (new metric)
  - **Relevance**: Fraction of retrieved context relevant to query (existing)
  - **Adherence**: Whether response is grounded in context, detects hallucinations (synonymous with faithfulness/groundedness)
  - **Completeness**: Fraction of relevant information incorporated into response (new metric)
- **Dataset Construction**: 100k examples from 12 component datasets across 5 domains (biomedical, general knowledge, legal, customer support, finance)
- **LLM Annotation**: GPT-4 used to generate ground-truth labels with chain-of-thought prompting, achieving 93-95% agreement with human judgements
- **Fine-Tuned Evaluator**: 400M-parameter DeBERTa-v3-Large model fine-tuned on RAGBench outperforms billion-parameter LLM judges

## When To Use

- **RAG System Evaluation**: Comprehensive evaluation of both retriever and generator components
- **Hallucination Detection**: Identifying when LLM responses are not grounded in retrieved context
- **System Optimization**: Understanding which components (retriever, generator, prompt) need improvement
- **Cross-Domain Benchmarking**: Comparing RAG systems across different domains and task types
- **Production Monitoring**: Continuous evaluation of RAG systems in production settings

## Failure Modes

- **Annotation Cost**: LLM annotation is expensive; human validation required for critical applications
- **Domain Specificity**: Fine-tuned models may not generalize well to domains not in RAGBench
- **Relevance Difficulty**: Context relevance prediction is harder than utilization (higher RMSE), as it requires understanding what information is needed to answer the question
- **Label Quality**: Despite high human agreement, some edge cases (partially supported sentences) require manual resolution

## Implementation Hook

- **Dataset**: Available at https://huggingface.co/datasets/rungalileo/ragbench
- **Code**: https://github.com/rungalileo/ragbench
- **TRACe Metrics**: 
  - Utilization = Len(U_i) / Len(d_i) where U_i is utilized tokens, d_i is document
  - Relevance = Len(R_i) / Len(d_i) where R_i is relevant tokens
  - Completeness = Len(R_i ∩ U_i) / Len(R_i)
  - Adherence = boolean indicating all response parts are grounded
- **Fine-Tuned Model**: DeBERTa-v3-Large with multi-head architecture for all TRACe metrics in single forward pass

## Related Notes

- [[eRAG_Evaluating_Retrieval_Quality]] - Alternative retrieval evaluation approach
- [[FlashRAG_Modular_Toolkit]] - RAG research toolkit

