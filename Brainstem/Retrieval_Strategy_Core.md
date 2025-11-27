---
type: brainstem
tier: S
domain: retrieval
last_updated: 2024-11-26
tags: [retrieval, strategy, multi-pass, correction, fallback]
related_notes:
  - [[Operator_Contract]]
  - [[Self-RAG_Adaptive_Retrieval]]
  - [[CRAG_Corrective_RAG]]
  - [[RAPTOR_Hierarchical_Retrieval]]
  - [[GraphRAG_Global_Summarization]]
  - [[RAGCHECKER_Diagnostics]]
---

# Retrieval Strategy Core

## When to Retrieve

**Decision Rules:**
- **Query Type Classification**: Distinguish between local (factual lookup) vs. global (thematic, multi-hop) queries
- **Self-Knowledge Assessment**: Use LLM self-reflection tokens (as in [[Self-RAG_Adaptive_Retrieval]]) to determine if retrieval is necessary
- **Confidence Thresholds**: Set adaptive thresholds (e.g., 0.2-0.6) based on query complexity and domain
- **Context Dependency**: Retrieve when query requires information beyond parametric knowledge or when temporal/domain-specific facts are needed
- **Stop Condition**: Skip retrieval for creative tasks, personal experiences, or queries where LLM's internal knowledge is sufficient

## Multi-Pass Retrieval Ladder

**Conceptual Pipeline (lexical → vector → rerank → compress):**

1. **Lexical Retrieval (BM25/TF-IDF)**: Fast, keyword-based initial retrieval for broad coverage
   - Use case: First-pass filtering, handling out-of-vocabulary terms
   - Output: Top-k candidate chunks (typically k=20-50)

2. **Vector Retrieval (Dense Embeddings)**: Semantic similarity search using learned embeddings
   - Use case: Capturing semantic relationships, handling paraphrasing
   - Models: E5-Mistral, Contriever, or domain-specific encoders
   - Output: Re-ranked candidate set

3. **Reranking (Cross-Encoder)**: Fine-grained relevance scoring of top candidates
   - Use case: Precision optimization, reducing noise in final context
   - Models: bge-reranker, cross-encoder architectures
   - Output: Top-k final chunks (typically k=5-10)

4. **Compression/Refinement**: Extract key information strips from retrieved chunks
   - Use case: Token efficiency, noise reduction, focus on relevant segments
   - Methods: Decompose-then-recompose (as in [[CRAG_Corrective_RAG]]), extractive summarization
   - Output: Refined context ready for generation

**Note**: This ladder is conceptual; actual implementations may combine steps or use hierarchical structures (see [[RAPTOR_Hierarchical_Retrieval]] for tree-based approaches).

## "Retrieval Went Wrong" Correction Playbook

**Error Detection:**
- **Retrieval Evaluator**: Lightweight model (e.g., T5-based) assesses relevance scores for each retrieved document
- **Confidence Degrees**: Classify retrieval as {Correct, Incorrect, Ambiguous} based on relevance thresholds
- **Claim-Level Validation**: Check if retrieved chunks contain claims needed for ground-truth answer

**Corrective Actions:**

1. **Correct Retrieval**: Apply knowledge refinement (decompose → filter → recompose) to extract key strips
   - Filter irrelevant segments using retrieval evaluator
   - Recombine relevant strips in order

2. **Incorrect Retrieval**: Discard retrieved chunks, trigger fallback mechanisms
   - Web search augmentation (for static corpus limitations)
   - Query rewriting for better retrieval
   - Alternative retrieval strategies (different embedding models, hybrid search)

3. **Ambiguous Retrieval**: Combine both internal (refined) and external (web search) knowledge
   - Merge refined chunks with web search results
   - Use retrieval evaluator to filter both sources

**Implementation Reference**: See [[CRAG_Corrective_RAG]] for detailed corrective framework.

## Stop Conditions + Fallback Behavior

**Stop Conditions:**
- **Quality Threshold**: If all retrieved chunks score below relevance threshold, mark as Incorrect
- **Token Budget**: Stop retrieval when context window is filled (typically 2K-4K tokens)
- **Iteration Limit**: Maximum retrieval cycles (e.g., 3-5) to prevent infinite loops
- **Confidence Saturation**: Stop when additional retrieval doesn't improve confidence scores

**Fallback Behavior:**
- **Web Search**: When static corpus fails, use web search APIs (prefer authoritative sources like Wikipedia)
- **Self-Knowledge Generation**: If retrieval consistently fails, allow LLM to generate from parametric knowledge (with appropriate disclaimers)
- **Query Reformulation**: Rewrite query with keywords, expand with synonyms, or decompose into sub-queries
- **Hierarchical Fallback**: For global queries, fall back to hierarchical summarization (see [[RAPTOR_Hierarchical_Retrieval]] or [[GraphRAG_Global_Summarization]])

## Links to Ingested Sources

- [[Self-RAG_Adaptive_Retrieval]]: On-demand retrieval with reflection tokens, adaptive thresholds
- [[CRAG_Corrective_RAG]]: Retrieval evaluator, corrective actions (Correct/Incorrect/Ambiguous), knowledge refinement
- [[RAPTOR_Hierarchical_Retrieval]]: Tree-based retrieval with multi-level summarization for long documents
- [[GraphRAG_Global_Summarization]]: Entity knowledge graphs, community summaries for global queries
- [[RAGCHECKER_Diagnostics]]: Fine-grained retrieval metrics (claim recall, context precision)

## Related to Operator Contract

See [[Operator_Contract]] for:
- Hard caps on ingestion (MAX 5 sources per run)
- Tool routing for retrieval operations
- Stop conditions for ingestion workflows

