# Brainstem Knowledge Map

**Version:** 1.0  
**Last Updated:** 2024-11-26  
**Status:** Active

---

## Purpose

The **Brainstem** is the foundational knowledge layer of the Obsidian Knowledge Core—the essential, high-ROI sources that form the bedrock of understanding across domains. This map organizes Tier S and Tier 1 sources into a structured, RAG-ready knowledge base.

### What Is the Brainstem?

The Brainstem represents:
- **Tier S Sources**: Ground truth references (official docs, canonical papers, authoritative specs)
- **Tier 1 Sources**: High-quality, curated secondary sources (aggregators, learning platforms, expert communities)
- **Distilled Knowledge**: Each source is compressed into 1–2 page notes with:
  - Key concepts and patterns
  - When to use / failure modes
  - Links to related sources
  - YAML frontmatter for metadata

---

## Structure

### Top-Level Organization

```
Research/
├── Indexes/
│   └── MOC_Brainstem.md          # Master index of all brainstem notes
├── Coding/
│   ├── MDN_Web_Docs.md
│   ├── React_Docs.md
│   ├── TypeScript_Docs.md
│   └── ...
├── Scholarly/
│   ├── arXiv_Overview.md
│   ├── Google_Scholar.md
│   └── ...
├── Mathematics/
│   ├── Wolfram_MathWorld.md
│   ├── OEIS.md
│   └── ...
└── ...
```

### Note Format

Each brainstem note follows this structure:

```markdown
---
type: brainstem
tier: S | 1
domain: coding | scholarly | mathematics | science | humanities | learning
source_url: https://...
last_updated: YYYY-MM-DD
tags: [tag1, tag2]
---

# Source Name

## Overview
Brief 2-3 sentence summary of what this source is and why it's Tier S/1.

## Key Concepts
- Concept 1: Brief explanation
- Concept 2: Brief explanation
- ...

## When to Use
- Use case 1
- Use case 2
- ...

## Failure Modes
- What this source doesn't cover well
- Limitations or gaps
- When to look elsewhere

## Related Sources
- [[Related Note 1]]
- [[Related Note 2]]

## Update YYYY-MM-DD
(For idempotent updates)
```

---

## Domains

### 1. Coding & Software Engineering
**Tier S:**
- MDN Web Docs
- GitHub
- Stack Overflow
- Python Documentation
- React Docs
- TypeScript Docs
- Next.js Docs

**Tier 1:**
- DevDocs
- freeCodeCamp
- Sourcegraph

### 2. Multidisciplinary Scholarly Search
**Tier S:**
- arXiv
- Google Scholar
- JSTOR
- Semantic Scholar
- DOAJ

**Tier 1:**
- SSRN
- ScienceDirect

### 3. Mathematics
**Tier S:**
- Wolfram MathWorld
- OEIS
- MIT OCW Mathematics
- arXiv Math

**Tier 1:**
- PlanetMath

### 4. Science, Engineering & Medicine
**Tier S:**
- PubMed
- NASA ADS
- NASA Science
- ScienceDirect
- PubChem

**Tier 1:**
- Semantic Scholar (STEM focus)
- DOAJ (STEM journals)

### 5. History, Philosophy & Humanities
**Tier S:**
- Stanford Encyclopedia of Philosophy
- Internet History Sourcebooks
- Project Gutenberg
- Perseus Digital Library
- JSTOR (Humanities)

**Tier 1:**
- Internet Archive (Texts)

### 6. General Learning Platforms
**Tier S:**
- Khan Academy
- MIT OpenCourseWare
- Coursera
- edX

**Tier 1:**
- Open Culture

---

## Ingestion Workflow

1. **Source Selection**: From `config/sources.yaml`
2. **Fetch**: Use MCP tools (Paper_Search, Exa_Search, webpage-to-markdown)
3. **Distill**: Compress into 1–2 page note
4. **Extract Entities**: Optionally add to knowledge graph
5. **Update MOC**: Link in `Research/Indexes/MOC_Brainstem.md`
6. **Commit**: Git commit with clear message

---

## Maintenance

- **Idempotent Updates**: If note exists, add new section under `## Update YYYY-MM-DD`
- **Regular Refresh**: Re-fetch and update notes quarterly or when major changes occur
- **Quality Check**: Ensure each note is retrieval-friendly (headings, lists, clear structure)

---

## Related Documents

- [[Knowledge_Core_RAG_Standard]] - Standards for RAG ingestion
- [[MOC_Brainstem]] - Master index of brainstem notes
- `config/sources.yaml` - Source configuration

---

**The Brainstem is the foundation. Build it well, and everything else follows.**

