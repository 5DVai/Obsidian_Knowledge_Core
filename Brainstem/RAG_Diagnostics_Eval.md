---
type: brainstem
tier: S
domain: evaluation
last_updated: 2024-11-26
tags: [evaluation, diagnostics, metrics, trace, erag, logging]
related_notes:
  - [[Brainstem_Golden_Questions]]
  - [[RAGBench_Explainable_Benchmark]]
  - [[eRAG_Evaluating_Retrieval_Quality]]
  - [[RAGCHECKER_Diagnostics]]
  - [[Self-RAG_Adaptive_Retrieval]]
  - [[CRAG_Corrective_RAG]]
---

# RAG Diagnostics & Evaluation

## TRACe-Style Rubric (Adapted for Golden Questions)

**Framework**: Based on [[RAGBench_Explainable_Benchmark]] TRACe metrics, adapted for our [[Brainstem_Golden_Questions]] evaluation harness.

**Four Core Dimensions:**

1. **Context Relevance (CR)**: Fraction of retrieved context that is relevant to the query
   - Formula: `Len(Relevant_Tokens) / Len(Total_Context_Tokens)`
   - Golden Questions Application: Measure how much retrieved context actually addresses the question
   - Threshold: >0.7 for high-quality retrieval

2. **Context Utilization (CU)**: Fraction of retrieved context actually used by generator
   - Formula: `Len(Utilized_Tokens) / Len(Total_Context_Tokens)`
   - Golden Questions Application: Assess if generator leverages provided context effectively
   - Threshold: >0.6 indicates good utilization

3. **Completeness**: How well response incorporates all relevant information from context
   - Formula: `Len(Relevant_and_Utilized_Tokens) / Len(Relevant_Tokens)`
   - Golden Questions Application: Check if all expected answer components are present
   - Threshold: >0.8 for comprehensive answers

4. **Adherence (Faithfulness/Groundedness)**: Detects hallucinations by checking if all response parts are grounded in context
   - Boolean: Full support (all claims entailed) vs. Partial/No support
   - Golden Questions Application: Verify that answers match expected internal note links
   - Threshold: Must be 1.0 (fully supported) for Brainstem queries

**Scoring for Golden Questions:**
- Each question evaluated on all four dimensions
- Aggregate scores across question set
- Compare against expected internal note links to validate retrieval accuracy

## eRAG-Style "Doc Contribution Test"

**Procedure**: Based on [[eRAG_Evaluating_Retrieval_Quality]] methodology.

**Steps:**
1. **Individual Document Evaluation**: Feed each retrieved document individually to the LLM (same LLM used in RAG system)
2. **Downstream Task Metrics**: Evaluate LLM output for each document using task metrics (accuracy, exact match, ROUGE) against ground truth
3. **Document-Level Labels**: Performance score for each document serves as its relevance label
4. **Aggregation**: Aggregate document-level annotations using set-based or ranking metrics (MAP, MRR, NDCG)

**Application to Golden Questions:**
- For each question, retrieve top-k documents
- Score each document's contribution to answering the question
- Identify which documents are most useful (high contribution scores)
- Flag documents with low/negative contribution (noise)

**Efficiency**: Reduces computational cost from O(l*k^2*d^2) to O(l*k*d^2) compared to end-to-end evaluation.

## Minimal Logging Fields

**Required Fields for Each Query:**
- `query`: Original user query text
- `retrieved_ids`: List of document/chunk IDs returned by retriever
- `answer`: Generated response text
- `citations`: List of source document IDs used in answer (if available)
- `trace_scores`: Dictionary with TRACe metrics (CR, CU, Completeness, Adherence)

**Optional Diagnostic Fields:**
- `retrieval_confidence`: Confidence score from retrieval evaluator (if using [[CRAG_Corrective_RAG]])
- `doc_contribution_scores`: Per-document contribution scores (from eRAG-style test)
- `retrieval_action`: Action taken (Correct/Incorrect/Ambiguous) if using corrective framework
- `fallback_triggered`: Boolean indicating if fallback mechanisms were used
- `token_usage`: Context tokens, generation tokens, total tokens

**Log Format (JSON):**
```json
{
  "query": "What are the hard caps for Brainstem ingestion?",
  "retrieved_ids": ["Operator_Contract.md", "Retrieval_Strategy_Core.md"],
  "answer": "MAX 5 sources per run, MAX 2 Brainstem modules per cycle...",
  "citations": ["Operator_Contract.md"],
  "trace_scores": {
    "context_relevance": 0.85,
    "context_utilization": 0.72,
    "completeness": 0.90,
    "adherence": 1.0
  },
  "retrieval_confidence": 0.92,
  "doc_contribution_scores": {"Operator_Contract.md": 0.95, "Retrieval_Strategy_Core.md": 0.65}
}
```

## Failure Modes + Detection

**Retrieval Failures:**
- **Low Context Relevance (<0.5)**: Retriever returning irrelevant chunks
  - Detection: TRACe Context Relevance metric
  - Mitigation: Improve retriever (better embeddings, reranking), expand query

- **Low Context Utilization (<0.4)**: Generator ignoring retrieved context
  - Detection: TRACe Context Utilization metric
  - Mitigation: Improve prompts, use context-aware generation, check context formatting

- **Low Completeness (<0.6)**: Missing key information in response
  - Detection: TRACe Completeness metric, compare against expected note links
  - Mitigation: Increase retrieval k, improve chunk selection, use hierarchical retrieval

**Generation Failures:**
- **Adherence < 1.0**: Hallucinations or unsupported claims
  - Detection: TRACe Adherence metric, claim-level entailment checking
  - Mitigation: Use faithfulness-focused prompts, implement claim verification, reduce noise sensitivity

- **High Noise Sensitivity**: Generator trusting irrelevant information
  - Detection: [[RAGCHECKER_Diagnostics]] metrics (relevant/irrelevant noise sensitivity)
  - Mitigation: Improve retrieval precision, implement knowledge refinement, use retrieval evaluator

- **Low Faithfulness**: Claims not grounded in retrieved context
  - Detection: Faithfulness metric from [[RAGCHECKER_Diagnostics]]
  - Mitigation: Enforce citation requirements, use claim-level verification

**System-Level Failures:**
- **Retrieval-Answer Mismatch**: Retrieved docs don't support generated answer
  - Detection: Compare doc contribution scores with answer claims
  - Mitigation: Implement corrective RAG ([[CRAG_Corrective_RAG]]), improve retrieval-generator alignment

- **Global Query Failures**: Cannot answer thematic/multi-hop questions
  - Detection: Low scores on global questions in [[Brainstem_Golden_Questions]]
  - Mitigation: Use hierarchical retrieval ([[RAPTOR_Hierarchical_Retrieval]]) or graph-based approaches ([[GraphRAG_Global_Summarization]])

## Links to Ingested Sources

- [[RAGBench_Explainable_Benchmark]]: TRACe framework (Utilization, Relevance, Adherence, Completeness)
- [[eRAG_Evaluating_Retrieval_Quality]]: Document-level contribution testing methodology
- [[RAGCHECKER_Diagnostics]]: Fine-grained diagnostic metrics (faithfulness, noise sensitivity, context utilization, hallucination)
- [[Self-RAG_Adaptive_Retrieval]]: Reflection tokens for retrieval and generation quality assessment
- [[CRAG_Corrective_RAG]]: Retrieval evaluator and corrective action framework

## Related to Golden Questions

See [[Brainstem_Golden_Questions]] for:
- Evaluation question set for testing retrieval and understanding
- Expected internal note links for validation
- Operator Contract and RAG Basics question categories

