---
type: brainstem
tier: S
domain: scholarly
canonical_id: "arXiv:2401.15884"
source_url: https://arxiv.org/abs/2401.15884
created: 2024-11-26
tags: [rag, corrective, retrieval-evaluator, web-search, knowledge-refinement]
promote_to_brainstem: true
related_notes:
  - [[Retrieval_Strategy_Core]]
  - [[RAG_Diagnostics_Eval]]
  - [[Self-RAG_Adaptive_Retrieval]]
---

# CRAG: Corrective Retrieval-Augmented Generation

## Overview

Corrective RAG (CRAG) improves RAG robustness by designing a lightweight retrieval evaluator to assess retrieved document quality and trigger corrective actions. The evaluator classifies retrieval as Correct, Incorrect, or Ambiguous based on confidence scores. For Correct retrieval, CRAG applies knowledge refinement (decompose-then-recompose) to extract key information strips. For Incorrect retrieval, it discards retrieved chunks and uses web search as fallback. For Ambiguous cases, it combines both refined internal knowledge and external web search results. CRAG is plug-and-play and significantly improves standard RAG and Self-RAG across short- and long-form generation tasks.

## Key Mechanism

- **Lightweight Retrieval Evaluator**: T5-large model fine-tuned to assess relevance of each retrieved document to the query, outputting confidence scores (-1 to 1)
- **Three-Action Framework**: Classify retrieval as Correct (high confidence, relevant docs found), Incorrect (low confidence, all docs irrelevant), or Ambiguous (intermediate confidence)
- **Knowledge Refinement**: For Correct retrieval, decompose documents into strips, filter irrelevant strips using evaluator, recompose relevant strips in order
- **Web Search Fallback**: For Incorrect retrieval, rewrite query into keywords, search web (prefer Wikipedia), apply same refinement to web results
- **Hybrid Knowledge**: For Ambiguous retrieval, combine refined internal knowledge with external web search results

## When To Use

- **Robustness-Critical Applications**: When retrieval failures must be handled gracefully without degrading generation quality
- **Static Corpus Limitations**: When knowledge base is incomplete or outdated, requiring web search augmentation
- **Noise-Rich Retrieval**: When retrieved chunks contain significant irrelevant information that needs filtering
- **Plug-and-Play Enhancement**: When wanting to improve existing RAG systems without retraining generator models
- **Multi-Source Knowledge**: Scenarios requiring combination of internal (corpus) and external (web) knowledge sources

## Failure Modes / Limitations

- **Evaluator Accuracy Dependency**: System performance heavily depends on retrieval evaluator accuracy; poor evaluator leads to incorrect action triggers
- **Web Search Quality**: Web search results may introduce biases or unreliable information, even with Wikipedia preference
- **Computational Overhead**: Additional evaluator inference and web search API calls increase latency and cost
- **Threshold Sensitivity**: Action trigger thresholds (upper/lower bounds) need empirical tuning per dataset
- **Query Rewriting Quality**: Web search effectiveness depends on query rewriting quality; poor rewrites yield irrelevant results

## Implementation Hook

- Integrate T5-based retrieval evaluator as lightweight quality check before using retrieved chunks
- Implement decompose-then-recompose refinement: split chunks into strips (few sentences), score each strip, filter low-scoring strips, concatenate high-scoring strips
- Add web search API integration (Google Search API, Wikipedia preference) as fallback when retrieval evaluator marks retrieval as Incorrect
- Use three-action framework (Correct/Incorrect/Ambiguous) to route retrieval results to appropriate processing pipeline

## What We Steal (10 Actionable Takeaways)

1. **Retrieval Evaluator Pattern**: Use lightweight model (T5-large) to assess retrieved document relevance before generation
2. **Confidence-Based Action Triggers**: Classify retrieval quality into three actions (Correct/Incorrect/Ambiguous) based on confidence thresholds
3. **Decompose-Then-Recompose**: Split retrieved documents into smaller strips, filter irrelevant strips, recompose relevant ones for cleaner context
4. **Web Search Fallback**: When static corpus retrieval fails, use web search (prefer authoritative sources) as corrective mechanism
5. **Query Rewriting for Search**: Convert natural language queries into keyword-based search queries for better web search results
6. **Hybrid Knowledge Combination**: For ambiguous cases, merge refined internal knowledge with external web search results
7. **Strip-Level Filtering**: Apply retrieval evaluator at fine-grained strip level (not just document level) for better noise reduction
8. **Plug-and-Play Architecture**: Design corrective framework as separate component that can enhance any RAG system without generator retraining
9. **Threshold Tuning Strategy**: Set upper threshold (Correct) and lower threshold (Incorrect) empirically, with Ambiguous as intermediate zone
10. **Knowledge Refinement Pipeline**: Standardize refinement process (decompose → score → filter → recompose) for consistent noise reduction

## How This Changes Our Operator Contract

- **Retrieval Quality Check**: Operator must evaluate retrieved documents before passing to generator, not assume all retrieved chunks are useful
- **Fallback Mechanisms**: Operator should implement web search fallback when retrieval evaluator marks results as Incorrect
- **Stop Conditions**: Add evaluator-based stop conditions—if retrieval is Incorrect, trigger corrective actions instead of proceeding with bad context

## Related Notes

- [[Retrieval_Strategy_Core]]: "Retrieval went wrong" correction playbook, fallback behavior
- [[RAG_Diagnostics_Eval]]: Retrieval evaluator as diagnostic tool, confidence scoring
- [[Self-RAG_Adaptive_Retrieval]]: Complementary adaptive retrieval framework

