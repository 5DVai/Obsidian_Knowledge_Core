---
type: brainstem
tier: S
domain: scholarly
source_url: https://arxiv.org/abs/1706.03762
arxiv_id: "1706.03762"
last_updated: 2024-11-26
tags: [transformer, attention, nlp, architecture, machine-translation]
related_projects: []
---

# Attention Is All You Need

## Overview

The Transformer architecture (Vaswani et al., 2017) introduced a novel neural network architecture based solely on attention mechanisms, dispensing with recurrence and convolutions entirely. This paper established the foundation for modern large language models.

## Key Concepts

- **Scaled Dot-Product Attention**: Core attention mechanism using dot products scaled by √dk, enabling efficient parallel computation
- **Multi-Head Attention**: Multiple attention heads running in parallel, allowing the model to attend to different representation subspaces
- **Positional Encoding**: Sine/cosine functions to inject positional information since the model has no recurrence
- **Encoder-Decoder Architecture**: Stacked self-attention and point-wise feed-forward layers
- **Constant Path Length**: O(1) operations to relate signals from arbitrary positions (vs O(n) for RNNs, O(logk(n)) for convolutions)

## Architecture Details

- **Base Model**: 6 layers, 512 hidden units, 8 attention heads
- **Big Model**: 6 layers, 1024 hidden units, 16 attention heads
- **Training**: 8 P100 GPUs, 12 hours (base) to 3.5 days (big)
- **Results**: 28.4 BLEU on WMT 2014 En-De, 41.8 BLEU on En-Fr

## When to Use

- Sequence-to-sequence tasks (translation, summarization)
- Tasks requiring long-range dependencies
- When parallelization is critical
- Foundation for modern LLMs (GPT, BERT, T5)

## Failure Modes

- Requires large amounts of training data
- Quadratic complexity with sequence length (attention over all positions)
- Positional encoding may not generalize well to sequences longer than training
- No inherent recurrence (though this is also a strength for parallelization)

## Related Sources

- [[GPT-3_Paper]] - Scaling transformers to 175B parameters
- [[BERT_Paper]] - Bidirectional encoder using transformer architecture

## Update 2024-11-26

Initial distillation from arXiv paper. Core concepts extracted for RAG retrieval.

