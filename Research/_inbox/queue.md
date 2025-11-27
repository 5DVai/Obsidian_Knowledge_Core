# Brainstem Ingestion Queue

**Last Updated:** 2024-11-26

This file contains sources that exceeded the hard caps during SAFE MODE ingestion runs.

## Queued Sources (Not Yet Ingested)

### RAG Papers (Potential Future Ingestion)

- "Adaptive Hybrid Retrieval-Augmented Generation with Context-Aware Confidence Control" (Sun, 2025) - IEEE EIT 2025
- "RAG-RLRC-LaySum" (Ji et al., 2024) - Biomedical layman summarization with RAG
- "LLM-Empowered Embodied Agent for Memory-Augmented Task Planning" (Glocker et al., 2025) - Memory-augmented RAG for robotics

### Notes

- These sources were identified during SAFE MODE run but not ingested due to MAX 5 source limit
- Prioritize RAG control, evaluation, and memory architecture papers when processing queue
- Each source should be distilled into 1-2 page notes with required structure: Overview / Key Mechanism / When To Use / Failure Modes / Implementation Hook / Related Notes

---

## Next SAFE MODE Queue Suggestions

**Priority for Next Run (v3):**

1. **Memory-Augmented RAG**: Papers on long-term memory, episodic memory, and memory architectures for RAG systems
2. **RAG Optimization**: Papers on retrieval optimization, embedding fine-tuning, and hybrid retrieval strategies
3. **RAG for Specific Domains**: Domain-specific RAG applications (medical, legal, code) that reveal specialized patterns
4. **RAG System Architectures**: End-to-end RAG system designs, training strategies, and architectural innovations
5. **RAG Evaluation Benchmarks**: Additional evaluation frameworks and benchmark datasets beyond RAGBench and RAGCHECKER

**Notes:**
- Focus on papers that complement existing retrieval strategy and diagnostics modules
- Prioritize sources that provide actionable implementation patterns
- Consider papers that address failure modes identified in current Brainstem modules

