# OpenRouter LLM Models – Knowledge Bible
**Snapshot: November 2025** | *Updated: Latest model releases and reasoning models*

This is a practical reference guide for developers choosing and prompting LLM models through OpenRouter. It covers 500+ models across 60+ providers, focusing on architecture, use cases, pricing tiers, and concrete prompting strategies.

**How to use this guide:**
- Start with the **Quick Model Selection Guide** to identify candidates for your task.
- Jump to the relevant **Model Family** section for detailed prompting strategies.
- Refer to **Global Prompting Principles** for cross-model consistency.
- Use the **Prompt Templates** as starting points, customizing placeholders for your task.

---

## Quick Model Selection Guide

| Use Case | Recommended Models | Notes |
|----------|-------------------|-------|
| **Deep Reasoning & Analysis** | `openai/o1`, `openai/o3`, `deepseek/deepseek-r1` | Internal chain-of-thought; keep prompts simple |
| **Fast & Cheap General Chat** | `meta-llama/llama-3.1-70b-instruct`, `deepseek/deepseek-chat` | Budget-friendly; solid quality for simple tasks |
| **Best-in-Class Coding** | `anthropic/claude-opus-4-1`, `anthropic/claude-3.5-sonnet` | Superior software engineering; tool calling |
| **Balanced Speed+Quality** | `anthropic/claude-3.5-sonnet`, `google/gemini-2.5-flash` | Sweet spot for most production workloads |
| **Long-Context RAG (1M+)** | `google/gemini-2.5-pro`, `google/gemini-1.5-pro` | Million-token context; best for document understanding |
| **Vision & Multimodal** | `google/gemini-2.5-pro`, `anthropic/claude-3.5-sonnet` | Image, video, PDF understanding |
| **Math & Logic Puzzles** | `openai/o3`, `deepseek/deepseek-r1`, `qwen/qwen-2.5-72b-instruct` | Specialized reasoning for STEM tasks |
| **Multilingual (120+ languages)** | `qwen/qwen-2.5-32b-instruct`, `qwen/qwen-2.5-72b-instruct` | Alibaba's strength; strong non-English support |
| **Image Generation** | `openai/dall-e-3` (via OpenRouter) | Use dedicated image generation models |
| **Open-Source Preference** | `meta-llama/llama-3.1-405b-instruct`, `mistralai/mistral-large-latest` | Weights available; can self-host |

---

## Provider & Family Overview

### **OpenAI**
*Frontier reasoning and general-purpose models with strong multimodal support.*

**Positioning:** Leads in reasoning capability (o-series), reliable general-purpose performance (GPT-4 series), and audio understanding.

**Key Families:**
- **o-series (o1, o3, o3-mini)**: Reasoning-optimized; excel at math, logic, research. Use simple prompts; avoid chain-of-thought instructions.
- **GPT-5 Series (GPT-5, GPT-5-mini, GPT-5-nano)**: Latest frontier models; strong reasoning + multimodal.
- **GPT-4o Series**: Fast, multimodal, good tool calling. Best all-rounder for production.

### **Anthropic**
*Best-in-class coding, long-context adherence, and constitutional AI alignment.*

**Positioning:** Industry-leading on software engineering benchmarks (SWE-bench); most reliable for long-horizon tasks and 200K context.

**Key Families:**
- **Claude Opus 4**: Flagship; best for complex coding, extended tasks. Slower, more expensive.
- **Claude 3.5 Sonnet**: Balanced; excellent speed+quality tradeoff. Recommended default for production.
- **Claude 3.5 Haiku**: Budget option; still capable for simple tasks.

### **Google DeepMind**
*Multimodal powerhouse with extreme context windows (1M+ tokens).*

**Positioning:** Best video understanding; million-token context enables new RAG patterns without vector databases.

**Key Families:**
- **Gemini 2.5 Pro**: Frontier reasoning + multimodal; 1M token context.
- **Gemini 2.5 Flash**: Optimized for throughput; best price-performance for long context.

### **Meta**
*Open-weight models; community preference for self-hosting.*

**Positioning:** Best open-source option; near-frontier quality at budget pricing on OpenRouter.

**Key Families:**
- **Llama 3.1 405B Instruct**: Best open-source; comparable to GPT-4 on many benchmarks.
- **Llama 3.1 70B Instruct**: Extreme value; cheap, solid performance.

### **Mistral**
*Efficient, production-focused models with strong tool calling.*

**Positioning:** Fast inference; reliable French support; good engineering discipline.

**Key Families:**
- **Mistral Large**: Balanced; 128K context; strong tool support.
- **Codestral**: Specialized for code; 256K context.

### **DeepSeek**
*Cost-effective reasoning models; disruptive pricing.*

**Positioning:** Reasoning capability at ~1/10th the cost of OpenAI's o1; open-weight versions available.

**Key Families:**
- **DeepSeek-R1**: Reasoning-specialized; excellent math/logic at low cost.
- **DeepSeek-Chat**: Ultra-cheap general chat model.

### **Alibaba (Qwen)**
*Multilingual, reasoning-focused open models.*

**Positioning:** Supports 120+ languages; strong non-English reasoning.

**Key Families:**
- **Qwen 2.5 32B / 72B**: Dense models; 32B for efficiency, 72B for advanced tasks.

---

## Model Families & Prompting Playbooks

---

### **OpenAI o-Series: Reasoning Models (o1, o3, o3-mini)**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `openai/o1`, `openai/o1-mini`, `openai/o3`, `openai/o3-mini` |
| **Type** | Text-only, specialized reasoning |
| **Context** | 128K tokens |
| **Pricing** | Premium (charges per reasoning token + completion token) |
| **Best For** | Math competitions, scientific reasoning, research synthesis, logic puzzles, complex multi-hop analysis |
| **Not Ideal For** | Creative writing, brainstorming, fast real-time chat, very simple queries |

#### **Prompting Strategy for o-Series**

**Core Principle:** These models perform reasoning *internally*, so external instructions to "think step by step" are **counterproductive**. Keep prompts concise and specific.

**Style & Approach:**
- **Keep it simple.** Write short, direct instructions (1-3 sentences). The model will handle complexity internally.
- **No chain-of-thought (CoT).** Avoid prompts like "Let's think step by step" or "Explain your reasoning." The model already does this internally.
- **Use delimiters for structure.** Use `###`, XML tags, or Markdown to separate input sections (task description, data, examples).
- **Avoid excessive context.** In RAG scenarios, include only the most relevant documents. Too much context causes over-thinking.
- **No few-shot examples.** Examples often confuse reasoning models; instead, describe the task clearly.
- **Be explicit about constraints.** If the task has limits (budget, time, format), state them clearly.

**Parameters:**
- **temperature**: 0.5–0.7 (default 0.7). Lower = more focused reasoning.
- **max_completion_tokens**: Set appropriately; reasoning models use separate token budgets for thinking vs. completion.
- **top_p**: 0.95 (recommended default).

**Prompt Template:**

```
You are solving {{TASK_NAME}}.

### Input
{{INPUT_DATA}}

### Requirements
- Output format: {{FORMAT}} (e.g., JSON, plain text, Markdown table)
- Constraints: {{CONSTRAINTS}} (e.g., "Under 100 words", "Budget ≤ $500")

Solve this problem.
```

**Example:**
```
You are a physics problem solver.

### Input
A 2 kg block slides on a frictionless surface at 5 m/s and collides with a 3 kg block at rest. After collision, they stick together. What is the final velocity?

### Requirements
- Show your final answer in the format: "Final velocity: [value] m/s"
- Assume a closed system.

Solve this problem.
```

**Common Pitfalls:**
- ❌ Adding detailed step-by-step instructions (model ignores or confuses them).
- ❌ Providing multiple examples to "teach" the model (degrades performance).
- ❌ Asking "what is your reasoning?" after a response (adds latency; reasoning is hidden).

---

### **OpenAI GPT-4o Series (GPT-4.1, GPT-4o, GPT-4o-mini)**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `openai/gpt-4.1`, `openai/gpt-4.1-mini`, `openai/gpt-4o`, `openai/gpt-4o-mini` |
| **Type** | Text + Vision, multimodal |
| **Context** | 128K tokens |
| **Pricing** | Mid-range (GPT-4o ~2x cheaper than o1 reasoning; mini is cheaper still) |
| **Best For** | Fast chat, coding (non-research), content generation, image understanding, tool calling, general assistants |
| **Not Ideal For** | Math olympiad problems, scientific research requiring deep reasoning |

#### **Prompting Strategy for GPT-4o Series**

**Core Principle:** These are *fast, capable general-purpose models* trained with traditional methods. They benefit from structured prompts with clear role, task, and format instructions. **Do** encourage explicit reasoning here (unlike o-series).

**Style & Approach:**
- **Assign a clear role.** Start with "You are a [role], like a senior [profession]."
- **Structure with delimiters.** Use `###`, XML tags, or Markdown for clarity.
- **Encourage reasoning for complex tasks.** "First, outline your approach. Then solve step-by-step."
- **Be explicit about output format.** JSON, tables, bullet lists—specify exactly what you want.
- **Use few-shot examples for non-obvious tasks.** 1–2 good examples help, especially for classification or code patterns.
- **System prompt is optional but useful.** Can set tone, guardrails, persona. Put task-specific instructions in the user message.

**Parameters:**
- **temperature**: 0.2–0.3 (code), 0.5–0.7 (balanced), 0.8–1.0 (creative).
- **top_p**: 0.9–0.95 (default works well).

**Prompt Template:**

```
{{SYSTEM_PROMPT}} (optional)

### Role
You are a {{ROLE}}.

### Task
{{TASK_DESCRIPTION}}

### Input Data
{{DATA}}

### Format
Respond in {{FORMAT}} with the following structure:
{{OUTPUT_STRUCTURE}}

### Constraints
{{CONSTRAINTS}}

Proceed.
```

**Example (Coding):**
```
### Role
You are a senior Python developer writing clean, well-documented code.

### Task
Write a function that fetches data from an API and caches results in memory for 1 hour.

### Format
Respond in Python code with docstrings.

### Constraints
- Use only standard library.
- Keep under 50 lines.

Proceed.
```

**Parameters for This Task:**
```python
temperature=0.2  # Low: prefer correctness
top_p=0.95
max_tokens=1024
```

---

### **Anthropic Claude 3.5 Sonnet & 3.5 Haiku**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `anthropic/claude-3.5-sonnet`, `anthropic/claude-3.5-haiku` |
| **Type** | Text + Vision |
| **Context** | 200K tokens |
| **Pricing** | Mid-range (Sonnet); Budget (Haiku) |
| **Best For** | Coding tasks, vision analysis, balanced reasoning, long-document understanding, tool calling |
| **Not Ideal For** | Real-time chat (slightly slower than GPT-4o), image generation |

#### **Prompting Strategy for Claude**

**Core Principle:** Claude responds exceptionally well to **XML-tagged structure** and **role prompting**. It tends to be verbose by default; guide the output explicitly.

**Style & Approach:**
- **Use XML tags for structure.** `<task>`, `<data>`, `<format>` tags. Claude is trained heavily on XML.
- **Assign a compelling role.** "You are the world's best [domain expert]" often outperforms neutral prompts.
- **Prefill the assistant response** if you want a specific format. Start with `Assistant: Here is...` to guide output style.
- **Direct language.** "Do this" instead of "Could you possibly consider doing this?"
- **Long context is Claude's strength.** Use 200K context for extensive documents or many examples.
- **System prompt for persona/guardrails.** Put everything else in the user message.
- **Avoid vague instructions.** Claude can hallucinate if asked ambiguously; be precise.

**Parameters:**
- **temperature**: 0.3–0.5 (analytical), 0.7–1.0 (creative).
- **top_p**: 0.95 (default).

**Prompt Template (System + User):**

```
SYSTEM:
You are {{ROLE}}. Your outputs are always well-structured and precise.

USER:
<task>
{{TASK}}
</task>

<data>
{{INPUT_DATA}}
</data>

<output_format>
Respond in {{FORMAT}} with the following structure:
{{STRUCTURE}}
</output_format>

<constraints>
{{CONSTRAINTS}}
</constraints>

Proceed.
```

**Example (Vision + Coding):**
```
SYSTEM:
You are an expert UI/UX engineer who translates design screenshots into clean, production-ready React code.

USER:
<task>
I'm attaching a screenshot of a login form. Convert it into a reusable React component.
</task>

<image>
[screenshot base64 or URL]
</image>

<output_format>
Respond with:
1. A description of the UI elements visible
2. Full React component code (TypeScript)
3. Tailwind CSS classes for styling
</output_format>

<constraints>
- No external UI libraries (Shadcn OK, Material-UI not OK)
- Form validation for email and password
- Include accessibility attributes (aria-label, etc.)
</constraints>
```

**Handling Verbosity:**
To get Claude to be concise, prefill the response:

```
User message ends with: "...Proceed."

Now add this to the Assistant content area (pre-fill):
"Here is the code:\n\n```typescript\n"
```

This forces Claude to start outputting code immediately.

---

### **Anthropic Claude Opus 4 & 4.1**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `anthropic/claude-opus-4-1`, `anthropic/claude-opus-4` |
| **Type** | Text + Vision |
| **Context** | 200K tokens |
| **Pricing** | Premium (best-in-class coding; slower) |
| **Best For** | Complex software engineering, multi-hour workflows, extended thinking, research tasks |
| **Not Ideal For** | Real-time chat, high-volume API calls (cost), simple queries |

#### **Prompting Strategy for Claude Opus**

**Core Principle:** Opus excels at **long-horizon, multi-step tasks** and can maintain context over hours. Same XML-based structure as Sonnet, but safe to ask for deeper analysis.

**Style & Approach:**
- **Leverage extended context.** Upload full codebases, long documents, or multiple rounds of conversation without degradation.
- **Explicit task decomposition.** "First analyze X, then refactor Y, then suggest Z." Opus will execute this reliably.
- **Request detailed explanations.** Opus can provide thorough walkthroughs without becoming verbose.
- **Few-shot prompting works well.** Provide 2–3 detailed examples for subtle tasks.

**Parameters:**
- **temperature**: 0.3–0.5 (for code/analysis).
- **extended_thinking** (if available): Enable for research-oriented tasks.

**Prompt Template:**

```
SYSTEM:
You are a senior software architect. You provide thorough, well-reasoned solutions with full code.

USER:
<task>
I need to refactor a large Python FastAPI application to use async/await and implement connection pooling.

Here is the codebase:
[Full codebase, 50KB+]
</task>

<requirements>
1. Identify performance bottlenecks
2. Refactor database layer to use asyncpg with pooling
3. Update endpoints to async/await
4. Provide before/after comparison
5. Suggest monitoring metrics
</requirements>

<output>
Provide a detailed refactoring plan and updated code sections.
</output>
```

---

### **Google Gemini 2.5 Pro & 2.5 Flash**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `google/gemini-2.5-pro`, `google/gemini-2.5-flash` |
| **Type** | Multimodal (text, vision, video, audio) |
| **Context** | 1,000,000 tokens (1M) |
| **Pricing** | Premium (Pro); Mid-range (Flash) |
| **Best For** | Long-document QA, video understanding, million-token RAG, multimodal research |
| **Not Ideal For** | Fine-grained coding (good but not best-in-class), extremely latency-critical tasks |

#### **Prompting Strategy for Gemini (Long Context)**

**Core Principle:** Gemini's superpower is handling **1M tokens**. Use it for document retrieval without vector databases, entire video analysis, and many-shot learning (100+ examples).

**Style & Approach:**
- **Treat context as a first-class resource.** Instead of RAG → vector search → top-K documents, just include everything.
- **Provide full documents inline.** PDFs, long articles, full video transcripts—Gemini handles it.
- **Many-shot prompting.** Use 50+ in-context examples for tasks where few-shot fails.
- **Query-first position.** Put your question early, data later. Gemini recalls better when query leads.
- **Use Markdown for structure.** Clear section headers help with recall.
- **Chain-of-thought is helpful.** Gemini benefits from "Explain your reasoning" prompts.

**Parameters:**
- **temperature**: 0.7 (balanced); 0.3–0.5 for deterministic tasks.
- **top_p**: 0.95.

**Prompt Template (Long-Context RAG):**

```
You are an expert analyst. Your task is to answer questions based on provided documents.

### Question
{{QUESTION}}

### Documents
[Inline full text of 10 PDFs, 2M+ tokens combined, or video transcript]

### Instructions
1. Search through all provided documents.
2. Cite the specific source (document name + section).
3. If the answer spans multiple sources, synthesize.
4. If data is insufficient, state clearly.

Answer the question.
```

**Example:**
```
### Question
What are the main findings from our Q3 earnings reports across all 3 divisions?

### Documents
[Full earnings reports: Finance Division, Operations Division, Sales Division]

### Instructions
1. Summarize key metrics (revenue, costs, profit) per division.
2. Highlight anomalies or changes vs. Q2.
3. Cite specific pages or sections.

Answer.
```

**Optimization Note:** Use **context caching** if you repeatedly query the same documents. OpenRouter supports this; include `cache_control` headers in multimodal requests.

---

### **Meta Llama 3.1 405B & 70B Instruct**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `meta-llama/llama-3.1-405b-instruct`, `meta-llama/llama-3.1-70b-instruct` |
| **Type** | Text only, open-weight |
| **Context** | 405B: 128K, 70B: 128K tokens |
| **Pricing** | Budget (especially on OpenRouter) |
| **Best For** | Open-source preference, high-volume / cost-sensitive, multilingual general chat |
| **Not Ideal For** | Vision, real-time latency (depends on provider), specialized reasoning |

#### **Prompting Strategy for Llama 3.1**

**Core Principle:** Llama is **open-weight** (weights publicly available) and benefits from **direct, instruction-following prompts**. No special tricks needed.

**Style & Approach:**
- **Keep prompts direct.** "Do X" works as well as elaborate structures.
- **System prompt optional.** Llama works with or without it, but it doesn't hurt to provide one.
- **Structured format helps.** Use Markdown or XML if task is complex, but not required.
- **Temperature sensitivity.** Lower temp (0.2–0.3) for code/facts; higher (0.8) for creative tasks.
- **Avoid excessive few-shot.** 1–2 examples usually sufficient; more can hurt.

**Parameters:**
- **temperature**: 0.2–0.3 (analytical), 0.6–0.8 (balanced/creative).
- **top_p**: 0.9.

**Prompt Template:**

```
You are {{ROLE}}.

Task: {{TASK}}

Input: {{DATA}}

Output format: {{FORMAT}}

Constraints: {{CONSTRAINTS}}

Respond now.
```

**Example (Simple Chat):**
```
You are a friendly customer support agent.

Task: Help the customer troubleshoot their WiFi connection.

Customer question: "My WiFi keeps disconnecting every 5 minutes."

Output format: Provide 3 quick steps to diagnose, then 2 solutions.

Respond now.
```

**Why Llama on OpenRouter?**
- **Cost:** 70B model ~1/10th the price of GPT-4o.
- **Speed:** Comparable to smaller closed models.
- **Open weights:** If you want to eventually self-host, Llama weights are public.
- **Multilingual:** Strong support for 8+ major languages.

---

### **DeepSeek-R1 (Reasoning Model)**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `deepseek/deepseek-r1`, `deepseek/deepseek-r1-distill-llama-70b` |
| **Type** | Text only, reasoning-focused, open-weight |
| **Context** | 64K–128K tokens |
| **Pricing** | Very budget-friendly (reasoning at ~1/10th o1 cost) |
| **Best For** | Math, logic, coding contests, research—especially cost-sensitive |
| **Not Ideal For** | Creative writing, real-time chat, tasks without clear logic |

#### **Prompting Strategy for DeepSeek-R1**

**Core Principle:** Similar to OpenAI o-series, but with **stricter requirements**: no system prompts, no few-shot examples, no chain-of-thought instructions. **Zero-shot only.**

**Style & Approach:**
- **No system prompt.** All instructions go in the user message.
- **No few-shot examples.** Providing examples degrades performance. Describe the task instead.
- **Clear, specific prompts.** Plain language; break complex tasks into sub-parts using Markdown or XML.
- **No chain-of-thought prompts.** The model reasons internally; you don't need to ask it to.
- **Simple and direct.** Shorter prompts often work better than elaborate ones.
- **Explicit constraints.** If the output has format requirements, state them clearly.

**Parameters:**
- **temperature**: 0.5–0.7 (recommended 0.6).
- **top_p**: 0.95.

**Prompt Template:**

```
{{TASK_DESCRIPTION}}

### Input
{{DATA}}

### Output Format
{{FORMAT}} (e.g., "Final answer in \\boxed{}")

### Constraints
{{CONSTRAINTS}}

Solve this.
```

**Example (Math):**
```
Solve this problem.

### Input
A train leaves Station A at 2 PM traveling at 60 mph. A second train leaves Station B (100 miles away) at 3 PM traveling at 80 mph toward Station A. When do the trains meet?

### Output Format
Provide the time and distance from Station A in the format:
Meeting time: [TIME]
Distance from A: [DISTANCE] miles

### Constraints
Show your work (DeepSeek will reason internally; don't ask for step-by-step in your prompt).
```

**Avoiding Common Pitfalls:**
- ❌ "Let's think step by step…" (model already reasons; wastes tokens).
- ❌ Providing examples to help (confuses model; use descriptions instead).
- ❌ Lengthy system prompts (ignored; put everything in user message).
- ❌ Asking "What is your reasoning?" (reasoning is hidden; you only see final answer).

**Forcing Thinking Mode:**
If DeepSeek doesn't show reasoning, prepend: "Start your response with `<think>`."

---

### **Mistral Large & Codestral**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `mistralai/mistral-large-latest`, `mistralai/codestral-2501` |
| **Type** | Text only |
| **Context** | 128K (Large); 256K (Codestral) |
| **Pricing** | Mid-range |
| **Best For** | Fast, reliable general chat; strong French; code generation (Codestral) |
| **Not Ideal For** | Vision, real-time ultra-low latency, math reasoning at frontier level |

#### **Prompting Strategy for Mistral**

**Core Principle:** Mistral is **fast and production-hardened**. Treat similar to GPT-4o: structured prompts with clear roles and formats work well. Excellent tool-calling support.

**Style & Approach:**
- **Role-based prompts.** "You are a [role]…" works well.
- **Structured with Markdown.** Use headers, bullet lists, code blocks.
- **Tool calling is a strength.** If your task requires function calls, Mistral handles this reliably.
- **Short prompts preferred.** Mistral is efficient; verbose instructions don't add value.
- **Temperature:** 0.3 (code/analytical), 0.7 (balanced).

**Prompt Template:**

```
You are a {{ROLE}}.

**Task:** {{TASK}}

**Input:** {{DATA}}

**Output Format:** {{FORMAT}}

Proceed.
```

**Example (Codestral for Code):**
```
You are an expert backend engineer.

**Task:** Write a TypeScript function that validates email addresses using regex and checks against a blocklist.

**Input:** 
- Email: string
- Blocklist: string[]

**Output Format:** Full TypeScript function with JSDoc comments, 50 lines max.

Proceed.
```

---

### **Qwen 2.5 (32B & 72B)**

| Aspect | Details |
|--------|---------|
| **OpenRouter Model IDs** | `qwen/qwen-2.5-32b-instruct`, `qwen/qwen-2.5-72b-instruct` |
| **Type** | Text only, multilingual (120+ languages) |
| **Context** | 32K–128K tokens |
| **Pricing** | Budget (32B); Mid-range (72B) |
| **Best For** | Multilingual tasks, non-English reasoning, balanced performance, open-source alternative |
| **Not Ideal For** | English-only workloads (other models faster), vision |

#### **Prompting Strategy for Qwen**

**Core Principle:** Qwen is **multilingual-first** and benefits from **clear role definition**. Reasoning capability is solid despite being open-weight.

**Style & Approach:**
- **Assign a role.** "You are a [profession/role]…"
- **Explicit output format.** State JSON, Markdown, or plain text.
- **Works in 120+ languages.** If user input is in a non-English language, respond in same language.
- **Temperature:** 0.3–0.5 (analytical), 0.7–0.9 (creative/brainstorming).
- **Many-shot examples work.** Qwen responds well to 5+ in-context examples if task is non-obvious.

**Prompt Template:**

```
You are a {{ROLE}}.

Task: {{TASK}}

Data: {{INPUT}}

Format: {{OUTPUT_FORMAT}}

Instructions: {{CONSTRAINTS}}

Generate the response now.
```

---

## Global Prompting Principles for OpenRouter

1. **Always Specify Role, Task, and Format.** Most models improve with explicit role ("You are a…"), clear task description, and output format specification.

2. **Use Delimiters for Clarity.** Separate instruction from data using `###`, `---`, XML tags (`<task>`, `<data>`), or Markdown headers. Prevents the model from confusing sections.

3. **Know Your Model's Reasoning Style:**
   - **Reasoning models (o1, o3, R1)**: Simple prompt + constraints. Avoid explicit CoT. Internally reason.
   - **Standard models (GPT-4o, Claude, Gemini)**: Explicit structure helps. Can encourage step-by-step reasoning.

4. **Adjust Temperature by Task:**
   - **0.0–0.3**: Deterministic outputs (code, math, facts).
   - **0.5–0.7**: Balanced (general chat, summarization).
   - **0.8–1.2**: Creative (brainstorming, writing, roleplay).

5. **For Long Context (Gemini 1M+), Put Query First.** Structure: Question → Then full documents. Models recall better with query-first ordering.

6. **Avoid Over-Specification in Few-Shot Prompts.** 1–3 examples usually sufficient. More can confuse or "teach" the wrong pattern. For reasoning models, use descriptions instead of examples.

7. **Explicit Constraints Outperform Implicit Ones.** Instead of hoping the model understands, say: "Keep under 100 tokens", "Use only standard library", "Respond in JSON with fields: [list]".

8. **Escape Special Characters in JSON/Code Blocks.** When outputting code or JSON, use triple backticks with language identifier:
   ```python
   # Code here
   ```
   Helps models format correctly.

9. **Use Markdown Tables for Structured Output.** Models reliably format tables with `| Header |` syntax. Better than asking for CSV or unstructured text.

10. **Monitor Provider Availability.** Some models rotate providers on OpenRouter (e.g., `:nitro` suffix for fastest, `:floor` for cheapest). Use routing shortcuts for automatic optimization.

11. **For APIs & Tool Calling, Prefer Claude Opus or Mistral Large.** Both have excellent tool-calling support. Provide JSON schema; models reliably invoke functions.

12. **Cache Your Prompts When Possible.** If you repeatedly send the same long prompt (e.g., codebase context), use OpenRouter's prompt caching (or equivalent on provider side) to save cost and latency.

---

## Parameter Reference

| Parameter | Range | Recommendation | Use Case |
|-----------|-------|-----------------|----------|
| **temperature** | 0.0–2.0 | 0.0–0.3 (analytical), 0.7 (balanced), 0.9–1.2 (creative) | Controls randomness; lower = predictable, higher = creative |
| **top_p** | 0.0–1.0 | 0.9–0.95 (default for most models) | Nucleus sampling; ignore tail of probability distribution |
| **top_k** | 1–500 | 20–50 (if tuning) | Limits to top-K tokens by probability (rarely used; top_p preferred) |
| **max_tokens** | 1–context_length | Task-dependent | Hard limit on output length; set to prevent runaway |
| **frequency_penalty** | -2.0–2.0 | 0.0–0.5 | Discourages repetition; increase if model repeats itself |
| **presence_penalty** | -2.0–2.0 | 0.0–0.5 | Encourages diversity; increase for varied outputs |
| **stop_sequences** | string[] | Model-specific | Stops generation at token(s); e.g., `["\n\n"]` to stop at double newline |

---

## Routing & Optimization on OpenRouter

**Auto Router:** `openrouter/auto`  
Automatically selects best model for your request using NotDiamond's evaluation system. Useful for exploratory phases; adds slight latency.

**Routing Shortcuts:**
- `:nitro` – Routes to fastest model available (lowest latency).
- `:floor` – Routes to cheapest model meeting your criteria.

**Fallback Chains:** Specify multiple models; OpenRouter tries them in order, failing over if primary is unavailable.

```python
extra_body={
    "models": [
        "anthropic/claude-opus-4-1",
        "openai/gpt-4o",
        "meta-llama/llama-3.1-70b-instruct"
    ]
}
```

---

## Notes on Updating This Bible

1. **Refresh Model List Monthly.** Visit `GET https://openrouter.ai/api/v1/models` to fetch current models. New models release frequently.

2. **Watch OpenRouter Blog.** Announcements on new model launches, provider integrations, and routing optimizations at `openrouter.ai/blog`.

3. **Provider Documentation.** Check official docs (Anthropic, OpenAI, Google, Meta) for model-specific guidance. OpenRouter follows upstream specs.

4. **Benchmark New Models.** When testing a new model family, run it on a small internal benchmark (5–10 test cases) to establish baseline quality vs. cost/latency.

5. **Community Feedback.** Monitor r/LocalLLaMA, OpenRouter Discord, and model provider forums for user discoveries (e.g., prompting tricks, quirks, breaking changes).

6. **Version Pinning.** Models can be updated in-place (e.g., `claude-opus-4-1` might become faster but slightly different). For deterministic production, consider pinning to specific dated versions when available (e.g., `claude-3-5-sonnet-20240620`).

---

## Appendix: Common Prompting Patterns

### **Pattern 1: Long-Document QA (1M Context)**
```
You are an expert analyst answering questions from provided documents.

**Question:** {{QUESTION}}

**Documents:** {{FULL_TEXT_OR_TRANSCRIPT}}

**Instructions:**
1. Find all relevant passages.
2. Synthesize into a coherent answer.
3. Cite sources (page number or timestamp).
4. If data is ambiguous, explain the ambiguity.

**Output Format:** Markdown with citations.

Answer now.
```

### **Pattern 2: Code Generation with Refactoring**
```
You are a senior [LANGUAGE] engineer.

**Task:** Refactor the following code for [GOAL: readability, performance, maintainability].

**Current Code:**
[CODE_BLOCK]

**Requirements:**
- [REQ1]
- [REQ2]

**Output:**
1. Explanation of changes (3–5 bullet points).
2. Refactored code (full function/module).
3. Performance impact (if applicable).

Proceed.
```

### **Pattern 3: Reasoning with Constraints (Math/Logic)**
```
Solve the following problem.

**Problem:** {{PROBLEM_STATEMENT}}

**Constraints:** {{CONSTRAINTS}}

**Output Format:** 
Final answer: \\boxed{ANSWER}

Solve this problem.
```

### **Pattern 4: Classification with Examples**
```
You are classifying {{CATEGORY}} for a database.

**Example 1:**
Input: {{EXAMPLE_INPUT_1}}
Output: {{EXAMPLE_OUTPUT_1}}

**Example 2:**
Input: {{EXAMPLE_INPUT_2}}
Output: {{EXAMPLE_OUTPUT_2}}

**New Input:**
{{NEW_INPUT}}

Classify this input.
```

### **Pattern 5: Many-Shot Few-Shot (50+ Examples)**
```
You are learning a task from examples.

**Examples:**

1. Input: {{INPUT_1}} → Output: {{OUTPUT_1}}
2. Input: {{INPUT_2}} → Output: {{OUTPUT_2}}
[... 50+ more examples ...]

**New Input:** {{NEW_INPUT}}

Based on the patterns, output the classification.
```

---

## Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| Model keeps repeating itself | Increase `frequency_penalty` to 0.5–1.0 |
| Output is too short | Increase `max_tokens` or ask: "Provide a detailed response" |
| Output is too verbose | Add: "Keep under 100 words" or use lower `temperature` (0.3) |
| Model refuses to respond | Check content policy; rephrase if needed; try different model |
| Reasoning model takes forever | Lower `reasoning_effort` (if available); reduce input context |
| Output format is inconsistent | Add strict format specification; use prefilled assistant response |
| Tool calling fails | Provide full JSON schema; ensure parameters are required/optional correctly |
| Long-context model misses information | Place query first; reduce document count or chunk size |

---

**Last Updated:** November 19, 2025  
**Scope:** 500+ models across 60+ providers via OpenRouter  
**Audience:** Intermediate–advanced developers building production AI applications

