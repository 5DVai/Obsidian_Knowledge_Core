# Prompt Builder Utilities for Hawx Recon
**Source:** Hawx-Recon-Agent\agent\llm\prompt_builder.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `prompt_builder.py` file in the Hawx Recon Agent project provides a set of utility functions designed to construct prompts for various tasks related to cybersecurity reconnaissance. These tasks include summarization, command recommendation, JSON repair, deduplication, and executive summary generation. The primary purpose of these utilities is to facilitate the interaction with large language models (LLMs) to automate and enhance the analysis of reconnaissance data. By structuring prompts effectively, the system can generate meaningful insights and actionable recommendations from raw command outputs.

The file encapsulates the logic for building prompts that guide LLMs in processing and interpreting data from cybersecurity assessments. This includes generating detailed executive summaries, repairing malformed JSON outputs, and deduplicating commands to optimize reconnaissance workflows. The utilities are tailored to ensure that the outputs are actionable, informative, and aligned with the constraints and requirements of cybersecurity operations.

## Key Concepts & Principles
- **Prompt Construction:** Techniques for creating structured prompts to guide LLMs in specific tasks.
- **Reconnaissance Workflow:** A systematic approach to cybersecurity assessment, including infrastructure analysis, web analysis, network services evaluation, vulnerability assessment, exploitation strategy, and post-exploitation activities.
- **Deduplication Strategy:** Methods to filter out redundant commands and focus on unique, informative actions.
- **Output Quality and Visibility:** Ensuring that command outputs are complete, visible, and actionable.
- **JSON Repair:** Techniques to correct malformed JSON outputs from LLMs.

## Detailed Technical Analysis

### Prompt Construction for LLMs
The file provides several functions to build prompts for different stages of cybersecurity analysis. Each function is designed to format the input data and constraints in a way that maximizes the effectiveness of LLMs in generating useful outputs. For example, `_build_prompt_post_step` constructs prompts that include detailed workflows and constraints to guide the LLM in analyzing command outputs.

### Reconnaissance Workflow
The prompts are structured around a comprehensive reconnaissance workflow that covers various aspects of cybersecurity assessment. This includes infrastructure scanning, web analysis, network services evaluation, vulnerability assessment, exploitation strategy, and post-exploitation activities. Each stage is detailed with specific tasks and focus areas, ensuring a thorough and methodical approach to reconnaissance.

### Deduplication and Optimization
The `_build_prompt_deduplication` function implements a strategy to optimize reconnaissance workflows by removing redundant commands. It classifies commands into categories and applies rules to retain only the most informative and distinct actions. This ensures that the reconnaissance process is efficient and focused on yielding meaningful results.

### JSON Repair and Output Quality
The file includes utilities for repairing malformed JSON outputs from LLMs, ensuring that the data is correctly formatted and parsable. Additionally, the prompts emphasize the importance of output quality and visibility, requiring that all command outputs are complete, visible, and actionable.

## Enterprise Q&A Bank

1. **What is the purpose of the prompt builder utilities in Hawx Recon?**
   - The utilities are designed to construct prompts for LLMs to automate and enhance the analysis of cybersecurity reconnaissance data.

2. **How does the deduplication strategy improve reconnaissance workflows?**
   - It filters out redundant commands, focusing on unique and informative actions to optimize the efficiency and effectiveness of the reconnaissance process.

3. **What are the key stages of the reconnaissance workflow outlined in the prompts?**
   - The stages include infrastructure analysis, web analysis, network services evaluation, vulnerability assessment, exploitation strategy, and post-exploitation activities.

4. **How does the file ensure output quality and visibility?**
   - By enforcing constraints that require complete, visible, and actionable command outputs, ensuring that the results are meaningful and useful.

5. **What techniques are used for JSON repair in the prompts?**
   - The prompts include specific instructions to correct malformed JSON outputs, ensuring they are correctly formatted and parsable.

## Actionable Takeaways
- Construct prompts that clearly define tasks and constraints for LLMs to generate meaningful outputs.
- Implement a comprehensive reconnaissance workflow to ensure thorough cybersecurity assessments.
- Apply deduplication strategies to optimize reconnaissance workflows by focusing on unique and informative actions.
- Ensure output quality and visibility by requiring complete and actionable command outputs.
- Use JSON repair techniques to correct malformed outputs and maintain data integrity.

---
**Raw Content:**
```python
# (The raw content of the file is included here for reference)
```