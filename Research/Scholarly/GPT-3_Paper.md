---
type: brainstem
tier: S
domain: scholarly
source_url: https://arxiv.org/abs/2005.14165
arxiv_id: "2005.14165"
last_updated: 2024-11-26
tags: [gpt-3, language-model, few-shot, in-context-learning, scaling]
related_projects: []
---

# GPT-3: Language Models are Few-Shot Learners

## Overview

GPT-3 (Brown et al., 2020) demonstrates that scaling autoregressive language models to 175 billion parameters enables strong few-shot, one-shot, and zero-shot performance across diverse NLP tasks without task-specific fine-tuning.

## Key Concepts

- **In-Context Learning**: Model learns tasks from demonstrations provided in the context window (few-shot) or instructions (zero-shot)
- **Scaling Laws**: Performance improves smoothly with model size across 3 orders of magnitude (125M to 175B parameters)
- **Few-Shot > One-Shot > Zero-Shot**: More demonstrations generally improve performance
- **Closed-Book QA**: Model answers questions using only knowledge stored in parameters (no retrieval)
- **Task-Agnostic**: Single model architecture for all tasks, no task-specific fine-tuning required

## Architecture & Training

- **175B Parameters**: 96 layers, 12,288 hidden size, 96 attention heads
- **Training Data**: 300B tokens from Common Crawl (filtered), WebText, Books, Wikipedia
- **Context Window**: 2,048 tokens
- **Training**: 3.2M batch size, 0.6e-4 learning rate, 300K steps

## When to Use

- Tasks requiring few-shot adaptation without fine-tuning
- Closed-book question answering
- Text generation and completion
- Translation (especially into English)
- Tasks where labeled data is scarce

## Failure Modes

- Struggles with comparison tasks (WiC, ANLI) - determining if two sentences have same meaning or if one implies another
- Reading comprehension on some datasets (RACE, QuAC)
- Arithmetic beyond 3-4 digits
- Can generate biased or harmful content (reflects training data biases)
- Expensive inference (large model size)

## Notable Results

- **TriviaQA**: 71.2% (few-shot) vs 60.5% (fine-tuned T5-11B)
- **LAMBADA**: 86.4% (few-shot) vs 68.0% (previous SOTA)
- **CoQA**: 85.0 F1 (few-shot) vs 90.7 (fine-tuned SOTA)
- **News Generation**: 52% human accuracy at detection (barely above chance)

## Related Sources

- [[Attention_Is_All_You_Need]] - Transformer architecture foundation
- [[BERT_Paper]] - Bidirectional alternative approach

## Update 2024-11-26

Initial distillation from arXiv paper. Key scaling and few-shot learning insights extracted.

