---
type: brainstem
tier: S
domain: scholarly
source_url: https://arxiv.org/abs/1810.04805
arxiv_id: "1810.04805"
last_updated: 2024-11-26
tags: [bert, bidirectional, transformer, pre-training, nlp]
related_projects: []
---

# BERT: Pre-training of Deep Bidirectional Transformers

## Overview

BERT (Devlin et al., 2018) introduces bidirectional pre-training for language representations using Masked Language Modeling (MLM) and Next Sentence Prediction (NSP), enabling state-of-the-art results on 11 NLP tasks with minimal task-specific architecture modifications.

## Key Concepts

- **Masked Language Model (MLM)**: Randomly mask 15% of tokens and predict them using bidirectional context (vs unidirectional LMs)
- **Next Sentence Prediction (NSP)**: Binary classification task predicting if sentence B follows sentence A
- **Bidirectional Context**: Unlike GPT, BERT can attend to both left and right context in all layers
- **Fine-tuning Approach**: Add task-specific output layer and fine-tune all parameters (vs feature-based like ELMo)
- **WordPiece Tokenization**: 30K vocabulary with subword units

## Architecture

- **BERT BASE**: 12 layers, 768 hidden, 12 attention heads, 110M parameters
- **BERT LARGE**: 24 layers, 1024 hidden, 16 attention heads, 340M parameters
- **Pre-training Data**: BooksCorpus (800M words) + Wikipedia (2.5B words)
- **Training**: 1M steps, batch size 256, sequence length 512

## When to Use

- Tasks requiring bidirectional understanding (QA, NLI, NER)
- When fine-tuning on labeled data is feasible
- Sentence-pair tasks (paraphrasing, entailment)
- Token-level tasks (named entity recognition, question answering spans)

## Failure Modes

- Not designed for text generation (encoder-only, no decoder)
- Requires fine-tuning for each task (not few-shot like GPT-3)
- Masking creates pre-train/fine-tune mismatch (15% masking vs no masking at inference)
- Quadratic attention complexity limits sequence length

## Notable Results

- **GLUE**: 80.5% (7.7% improvement over previous SOTA)
- **SQuAD v1.1**: 93.2 F1 (1.5 point improvement)
- **SQuAD v2.0**: 83.1 F1 (5.1 point improvement)
- **MultiNLI**: 86.7% (4.6% improvement)

## Related Sources

- [[Attention_Is_All_You_Need]] - Transformer architecture
- [[GPT-3_Paper]] - Unidirectional alternative with few-shot learning

## Update 2024-11-26

Initial distillation from arXiv paper. Bidirectional pre-training methodology extracted.

