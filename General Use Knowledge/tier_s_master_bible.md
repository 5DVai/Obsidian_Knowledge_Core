# The Tier-S / Tier-1 Knowledge Bible
## A Canonical Reference Atlas for AI Systems and High-Signal Research

---

## Introduction: The Epistemology of Reliable Knowledge

In an information landscape saturated with derivative content, hallucinations, and obsolete data, the operational efficacy of AI systems—particularly Large Language Models (LLMs) and autonomous agents—is fundamentally constrained by the quality of its retrieval corpus.

The concept of **Tier-S knowledge** is not merely a label of quality, but a designation of **provenance**. A Tier-S source is the **root of the directed acyclic graph** of information flow:
- The **primary specification** (RFCs, official standards)
- The **pre-print repository** (arXiv, ACL Anthology)
- The **official documentation kernel** (Python Docs, MDN Web Docs, PyTorch)
- The **legislative or governance standard** (NIST, IETF)
- The **frontier research** from organizations with unique computational capabilities (OpenAI, DeepMind)

**Tier-1 sources** are secondary but authoritative:
- Rigorously peer-reviewed journals (JMLR, Nature Machine Intelligence)
- Curated indexes and aggregators (Google Scholar, DOAJ)
- Well-maintained learning platforms and guides
- Community-driven resources with strong editorial standards

This Bible is designed **not for casual human browsing, but for high-velocity ingestion by AI agents**. It prioritizes:
- Deterministic entry points (stable API references, machine-readable schemas, canonical base URLs)
- RAG-friendly chunking and structure
- Clear decision rules for source selection
- Cross-source verification patterns

---

## Part 1: Understanding the Tier System

### Tier-S: Supreme / Root of Information Flow

**Definition:** Tier-S sources are the authoritative, non-derivative origins of knowledge in their domain. They are typically:
- Maintained by the original creators, organizations, or standards bodies
- Updated synchronously with their domain's state of the art
- Machine-accessible via APIs or structured exports where applicable
- Designed for professional/academic consumption, not general audiences

**Examples:**
- arXiv (preprints)
- Python Documentation (language spec)
- GitHub (working code)
- Stanford Encyclopedia of Philosophy (peer-reviewed reference)
- IETF RFCs (internet standards)

### Tier-1: Primary / Authoritative Secondary

**Definition:** Tier-1 sources are rigorously maintained, peer-reviewed, or curated by trusted institutions, but are secondary compilations or aggregators:
- High editorial standards
- Often paywalled or require institutional access
- Widely cited in their domain
- May have slower update cycles than Tier-S

**Examples:**
- JMLR (peer-reviewed ML journal)
- JSTOR (journal archive)
- IEEE Xplore (conference proceedings)
- DevDocs (aggregated documentation)

### Why Tier-2 and Below Are Excluded

Tier-2 and below (blogs, tutorials, StackOverflow answers, Medium posts) are **too voluminous and too mutable** for a reliable knowledge atlas. They serve important pedagogical roles but should not be primary sources for AI reasoning or citation.

---

## Part 2: Taxonomy of Domains and Use Cases

This atlas organizes Tier-S and Tier-1 sources into **primary domains**, each with distinct access patterns and use cases:

| Domain | When to Consult | Primary Use | Canonical Sources |
|--------|-----------------|-------------|-------------------|
| **Research & Preprints** | Need latest STEM research, theoretical breakthroughs | Literature review, understanding SOTA | arXiv, ACL Anthology, Papers With Code |
| **AI & Machine Learning** | Building or evaluating ML systems | Framework docs, research papers, code examples | PyTorch, Hugging Face, LangChain, LlamaIndex |
| **Software Engineering** | Language/framework APIs, coding patterns | Implementation, debugging, API reference | Python Docs, MDN Web Docs, GitHub |
| **Web Development** | Web standards, browser APIs, frontend frameworks | Web application development | MDN Web Docs, React Docs, Next.js, TypeScript |
| **Mathematics** | Definitions, theorems, sequences, formulas | Mathematical reference, proof verification | Wolfram MathWorld, OEIS, MIT OCW |
| **Philosophy & Ethics** | Ethics frameworks, philosophical analysis | AI ethics, alignment, interpretability | Stanford Encyclopedia of Philosophy, Stanford HAI |
| **Learning Platforms** | Structured learning from foundational to advanced | Education, self-paced skill building | Khan Academy, MIT OCW, Coursera, edX |
| **Standards & Governance** | Compliance, security, protocol specifications | Regulatory alignment, system design | IETF, W3C, NIST, OWASP |

---

## Part 3: Sourced Domains and Source Tables

### Domain 1: Research & Preprints
**Overview:** The cutting edge of academic knowledge, where ideas first appear before peer review or canonical publication. Essential for understanding state-of-the-art (SOTA) in STEM fields.

**Typical query patterns:**
- "Latest papers on [topic]"
- "How does [algorithm] compare to [baseline]?"
- "What do reviews say about [paper]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| arXiv | Tier-S | Preprints | https://arxiv.org | Primary preprint server for STEM; access via API | Unreviewed; adjust confidence accordingly |
| ACL Anthology | Tier-S | Proceedings | https://aclanthology.org/ | NLP and computational linguistics papers | 118k+ papers, open metadata |
| Google Scholar | Tier-S | Discovery | https://scholar.google.com | Cross-publisher search for scholarly literature | Good for citation tracking |
| JSTOR | Tier-S | Archive | https://www.jstor.org | Millions of journal articles across 75+ disciplines | Requires institutional access |
| Semantic Scholar | Tier-S | AI-powered Search | https://www.semanticscholar.org | Semantic search and citation analysis, especially CS/AI | Free; API available |
| DOAJ | Tier-S | Open Access | https://doaj.org | Find peer-reviewed open-access journals | Quality-controlled index |
| PubMed | Tier-S | Biomedical | https://pubmed.ncbi.nlm.nih.gov | Biomedical and life sciences literature | Free access to abstracts |
| NASA ADS | Tier-S | Astrophysics | https://ui.adsabs.harvard.edu | Astronomy and astrophysics research | Specialized index |
| Papers With Code | Tier-S | SOTA / Code Verification | https://paperswithcode.com | Link papers to implementations and benchmarks | JSON export API for SOTA queries |
| Distill | Tier-S | Interpretability | https://distill.pub | Mechanistic interpretability and interactive explanations | On hiatus but archive authoritative |
| OpenReview | Tier-S | Peer Review | https://openreview.net | Open peer review process for ICLR, NeurIPS | Metadata on rejections, reviews, confidence |
| ScienceDirect | Tier-1 | STM Journals | https://www.sciencedirect.com | Science, technical, medical journals | Often paywalled |
| SSRN | Tier-1 | Economics/Social | https://www.ssrn.com | Working papers in economics, law, finance | Free abstracts |

---

### Domain 2: AI & Machine Learning
**Overview:** Official documentation, research, and tooling for machine learning frameworks, algorithms, and systems.

**Typical query patterns:**
- "How do I use [library] to [task]?"
- "What's the latest in [subfield]?"
- "How do transformers work?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| PyTorch | Tier-S | Framework Docs | https://pytorch.org | Canonical reference for tensor operations and computational graphs | Split Stable/Beta channels |
| Hugging Face | Tier-S | ML Hub | https://huggingface.co | Transformers, datasets, inference; model hub and CLI | Model cards, upload/download specs |
| LangChain | Tier-S | Orchestration | https://python.langchain.com | LLM orchestration, chains, agents | Class hierarchy and integration points |
| LlamaIndex | Tier-S | RAG | https://docs.llamaindex.ai | RAG architecture, data ingestion, structured extraction | LlamaParse, LlamaExtract for unstructured data |
| OpenAI Research | Tier-S | Frontier Lab | https://openai.com/research/ | GPT family, DALL-E, RL algorithms | System cards, safety evaluations |
| DeepMind Research | Tier-S | Frontier Lab | https://deepmind.google/research/ | AI for Science, multimodal, robotics, RL | Research blog with technical depth |
| Stanford HAI | Tier-S | Ethics/Policy | https://hai.stanford.edu | AI ethics, policy, macro-level statistics (AI Index) | Annual reports on AI trends |
| Lil'Log | Tier-S | Literature Review | https://lilianweng.github.io/ | Comprehensive meta-reviews on LLM Agents, Hallucinations, Diffusion | Aggregates scattered research |
| BAIR Blog | Tier-1 | Academic Blog | https://bair.berkeley.edu/blog/ | Robotics, control theory, paper summaries | High-signal technical content |
| JMLR | Tier-1 | Journal | https://www.jmlr.org | Peer-reviewed ML research | Open access, high bar |
| Nature Machine Intelligence | Tier-1 | Journal | https://www.nature.com/natmachintell/ | Interdisciplinary AI research | Paywalled but high prestige |

---

### Domain 3: Software Engineering
**Overview:** Official language and framework documentation, APIs, design patterns.

**Typical query patterns:**
- "How do I use [function] in [language]?"
- "What's the API for [library]?"
- "How does [pattern] work?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| Python Documentation | Tier-S | Language Spec | https://docs.python.org | Authoritative Python language and standard library | Version-specific docs, official best practices |
| TypeScript Documentation | Tier-S | Language Spec | https://www.typescriptlang.org | TypeScript type system, compiler, configuration | Handbook, utility types, configuration options |
| GitHub | Tier-S | OSS Hub | https://github.com | Real-world code examples, issues, discussions | API for programmatic access, search |
| Stack Overflow | Tier-S | Q&A | https://stackoverflow.com | Practical debugging, error messages, edge cases | Community-driven, peer-curated answers |
| MDN Web Docs | Tier-S | Web Standards | https://developer.mozilla.org | HTML, CSS, JavaScript, browser APIs | Community-maintained, canonical for web |
| DevDocs | Tier-1 | Aggregated Docs | https://devdocs.io | Hundreds of languages/frameworks in one interface | Offline support, instant search |
| Sourcegraph | Tier-1 | Code Search | https://sourcegraph.com | Semantic code search, pattern discovery | Cross-repo navigation |

---

### Domain 4: Web Development
**Overview:** Frontend frameworks, standards, and tools.

**Typical query patterns:**
- "How do I build a [component] with [framework]?"
- "What's the CSS property for [effect]?"
- "How does [JS feature] work?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| MDN Web Docs | Tier-S | Web Standards | https://developer.mozilla.org | Canonical reference for web standards | Browser compatibility, APIs |
| React Documentation | Tier-S | Framework | https://react.dev | Official React API, hooks, patterns | Latest React version, best practices |
| Next.js Documentation | Tier-S | Meta-Framework | https://nextjs.org | Routing, data fetching, deployment | Vercel-maintained, SSR/SSG guidance |
| TypeScript Documentation | Tier-S | Type System | https://www.typescriptlang.org | TypeScript in web development | Type safety, compiler options |

---

### Domain 5: Mathematics
**Overview:** Mathematical definitions, theorems, sequences, and educational resources.

**Typical query patterns:**
- "What is [mathematical term]?"
- "What's the sequence [numbers]?"
- "How do I prove [theorem]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| Wolfram MathWorld | Tier-S | Encyclopedia | https://mathworld.wolfram.com | Definitions, theorems, formulas, visualizations | Wolfram Research, authoritative |
| OEIS | Tier-S | Sequence Database | https://oeis.org | Identify and explore integer sequences | Millions of sequences with references |
| MIT OCW – Mathematics | Tier-S | Learning | https://ocw.mit.edu/courses/mathematics/ | University-level math courses with full materials | Lectures, notes, problem sets |
| PlanetMath | Tier-1 | Collaborative Encylopedia | https://planetmath.org | Community-driven math explanations | Less polished but useful alternatives |

---

### Domain 6: Philosophy & Humanities
**Overview:** Philosophical analysis, ethics, and historical perspectives.

**Typical query patterns:**
- "What is [philosophical concept]?"
- "What do experts say about [ethics topic]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| Stanford Encyclopedia of Philosophy | Tier-S | Reference | https://plato.stanford.edu | Expert-written, citable philosophy articles | Nearly 1,800 entries, continuously updated |
| Internet History Sourcebooks | Tier-S | Primary Sources | https://sourcebooks.fordham.edu | Curated historical primary sources | Across civilizations and eras |
| Project Gutenberg | Tier-S | Digital Library | https://www.gutenberg.org | 75,000+ public-domain books | Classics in literature and philosophy |
| Perseus Digital Library | Tier-S | Classics | https://www.perseus.tufts.edu | Greek and Roman texts with translations | Lexical tools and historical context |
| Internet Archive (Texts) | Tier-1 | Document Archive | https://archive.org/details/texts | Scanned books and documents | Coverage unmatched, quality varies |

---

### Domain 7: Learning Platforms
**Overview:** Structured educational content for foundational and advanced learning.

**Typical query patterns:**
- "How do I learn [topic]?"
- "Are there free courses on [subject]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| Khan Academy | Tier-S | Learning | https://www.khanacademy.org | Structured K-12 and early college content | Videos, lessons, practice problems |
| MIT OpenCourseWare | Tier-S | University Courses | https://ocw.mit.edu | 2,500+ MIT courses with full materials | Lectures, notes, assignments, free |
| Coursera | Tier-S | MOOC Platform | https://www.coursera.org | University and industry courses, degrees | Mix of audit (free) and paid certificates |
| edX | Tier-S | MOOC Platform | https://www.edx.org | University courses from MIT, Harvard, others | MIT/Harvard founded, open-license content |
| freeCodeCamp | Tier-1 | Programming | https://www.freecodecamp.org | Project-based coding curriculum | Hands-on emphasis, certifications |
| Open Culture | Tier-1 | Aggregator | https://www.openculture.com | Curated directory of free courses and podcasts | Aggregates from major institutions |

---

### Domain 8: Data Portals & Specialized Archives
**Overview:** Domain-specific data repositories, chemical databases, and specialized collections.

**Typical query patterns:**
- "What are the properties of [chemical]?"
- "What spacecraft data is available for [mission]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| PubChem | Tier-S | Chemistry Database | https://pubchem.ncbi.nlm.nih.gov | Chemical properties, bioactivity data | NCBI, free, API-accessible |
| NASA Science | Tier-S | Space & Earth | https://science.nasa.gov | Mission data, Earth science datasets | Authoritative government source |
| PubMed | Tier-S | Biomedical | https://pubmed.ncbi.nlm.nih.gov | Biomedical abstracts and literature | Free access, comprehensive |

---

### Domain 9: Standards & Governance
**Overview:** Protocols, standards bodies, and compliance frameworks.

**Typical query patterns:**
- "What does [RFC/standard] say about [protocol]?"
- "How should we implement [security framework]?"

| Name | Tier | Category | Canonical URL | Suggested AI Use | Notes |
|------|------|----------|---------------|------------------|-------|
| IETF | Tier-S | Internet Standards | https://www.ietf.org | RFC specifications for protocols | Definitive source for HTTP, DNS, TLS, etc. |
| W3C | Tier-S | Web Standards | https://www.w3.org | HTML, CSS, WebAssembly specifications | International consensus body |
| NIST AI Risk Management | Tier-S | AI Governance | https://www.nist.gov/itl/ai-risk-management-framework | AI system safety and risk framework | US government guidance |
| OWASP | Tier-1 | Security Guidelines | https://owasp.org | Application security best practices | Community-driven, OWASP Top 10 |

---

## Part 4: How an AI Should Query This Bible

### Decision Tree for Source Selection

When a user query arrives, the AI should follow this decision logic:

1. **Identify the domain:** What field does this query belong to?
   - NLP → Research & Preprints (arXiv, ACL)
   - Python code → Python Docs
   - Web app → MDN + React/Next.js Docs
   - ML framework → Framework docs (PyTorch, Hugging Face)
   - Ethics question → Stanford Encyclopedia of Philosophy, Stanford HAI

2. **Select the primary source tier:**
   - **Prefer Tier-S first:** Official docs, canonical references, root sources
   - **Fall back to Tier-1:** If Tier-S doesn't directly address the query (e.g., "SOTA benchmarks" → Papers With Code; "comparison of approaches" → JMLR/arXiv reviews)

3. **Cross-check with a secondary source:**
   - Never rely on a single source for critical claims
   - Example: Verify algorithm implementation across PyTorch docs + GitHub examples + Stack Overflow discussions
   - Example: For research claims, check arXiv + OpenReview + JMLR

4. **Adjust confidence based on freshness:**
   - Tier-S sources are kept current; use as primary truth
   - Tier-1 sources may have lag; suitable for reference and verification
   - For "latest research," prioritize arXiv + Papers With Code over journals

### Example Query Resolution Paths

#### Query: "How do I implement attention in PyTorch?"
- Primary: PyTorch Docs (torch.nn.MultiheadAttention)
- Secondary: MDN Web Docs (browser contexts, if applicable)
- Verification: GitHub examples, Stack Overflow

#### Query: "What's the latest approach in multimodal AI?"
- Primary: arXiv (cs.CV, cs.CL, cs.AI categories)
- Secondary: Papers With Code (SOTA leaderboards)
- Verification: DeepMind/OpenAI research blogs

#### Query: "Is JSON the best format for APIs?"
- Primary: IETF RFCs (HTTP, REST principles)
- Secondary: W3C (web standards)
- Verification: MDN Web Docs

#### Query: "How should I handle AI system security?"
- Primary: NIST AI Risk Management Framework
- Secondary: OWASP Top 10
- Verification: Stanford HAI, NIST papers

---

## Part 5: Schema and Machine-Readable Format

### Source Record Schema

Each source in `tier_s_atlas.json` follows this structure:

```json
{
  "id": "arxiv",
  "name": "arXiv",
  "tier": "Tier-S",
  "primary_domain": "Research & Preprints",
  "category": "Research Papers",
  "canonical_url": "https://arxiv.org",
  "key_links": {
    "homepage": "...",
    "search": "...",
    "api": "...",
    "other": [{"label": "...", "url": "..."}]
  },
  "content_styles": ["Preprints", "Papers"],
  "suggested_use_for_ai": "Primary preprint server...",
  "notes": "Not peer-reviewed by arXiv itself...",
  "provenance": ["AI-Knowledge-Atlas-Generation.pdf", ...]
}
```

**Field descriptions:**
- **id**: Stable slug for unique identification
- **name**: Human-readable source name
- **tier**: Tier-S or Tier-1 (conservative classification)
- **primary_domain**: High-level domain of knowledge
- **category**: Functional category (Official Docs, Research Papers, etc.)
- **canonical_url**: Homepage or root docs URL
- **key_links**: Structured access points (search, API, specific subsections)
- **content_styles**: Types of content available
- **suggested_use_for_ai**: 1–3 sentence guidance for when to use this source
- **notes**: Caveats, licensing, access model, or special considerations
- **provenance**: Which input documents mentioned this source

---

## Part 6: Recommended Update Cycle and Governance

### When to Update This Atlas

- **New sources appear:** When a new official docs portal or tier-S research venue launches
- **Tier reassignments:** If a source changes its access model or maintenance status
- **Broken links or deprecated URLs:** When canonical URLs change
- **Coverage gaps:** When analyzing user queries reveals an unserved domain

### When NOT to Update

- Do not add Tier-2 or lower sources (blogs, Medium, StackOverflow threads)
- Do not remove sources unless they become defunct
- Do not modify provenance or historical record

### Versioning

This Bible uses **semantic versioning**:
- **Major**: Fundamental changes to tier system or domain taxonomy
- **Minor**: New sources added or domains reorganized
- **Patch**: Fixes to URLs, notes, or metadata

---

## Appendix A: Complete Source List (Quick Reference)

| ID | Name | Tier | Domain | Category |
|----|------|------|--------|----------|
| arxiv | arXiv | Tier-S | Research & Preprints | Research Papers |
| acl_anthology | ACL Anthology | Tier-S | Research & Preprints | Research Papers |
| google_scholar | Google Scholar | Tier-S | Research & Preprints | Research Papers |
| jstor | JSTOR | Tier-S | Research & Preprints | Research Papers |
| semantic_scholar | Semantic Scholar | Tier-S | Research & Preprints | Research Papers |
| doaj | DOAJ | Tier-S | Research & Preprints | Research Papers |
| pubmed | PubMed | Tier-S | Biomedical & Life Sciences | Research Papers |
| nasa_ads | NASA ADS | Tier-S | Space & Astrophysics | Research Papers |
| papers_with_code | Papers With Code | Tier-S | AI & Machine Learning | Research Papers |
| distill | Distill | Tier-S | AI & Machine Learning | Research Papers |
| openreview | OpenReview | Tier-S | AI & Machine Learning | Research Papers |
| sciencedirect | ScienceDirect | Tier-1 | Research & Preprints | Research Papers |
| ssrn | SSRN | Tier-1 | Research & Preprints | Research Papers |
| acm_digital_library | ACM Digital Library | Tier-1 | AI & Computer Science | Research Papers |
| ieee_xplore | IEEE Xplore | Tier-1 | AI & Computer Science | Research Papers |
| jmlr | JMLR | Tier-1 | AI & Machine Learning | Research Papers |
| nature_machine_intelligence | Nature Machine Intelligence | Tier-1 | AI & Machine Learning | Research Papers |
| openai_research | OpenAI Research | Tier-S | AI & Machine Learning | Frontier Lab |
| deepmind_research | DeepMind Research | Tier-S | AI & Machine Learning | Frontier Lab |
| stanford_hai | Stanford HAI | Tier-S | AI & Ethics | Frontier Lab |
| bair_blog | BAIR Blog | Tier-1 | AI & Machine Learning | Frontier Lab |
| lillog | Lil'Log | Tier-S | AI & Machine Learning | Frontier Lab |
| pytorch | PyTorch | Tier-S | AI & Machine Learning | Official Docs |
| hugging_face | Hugging Face | Tier-S | AI & Machine Learning | Official Docs |
| langchain | LangChain | Tier-S | AI & Machine Learning | Official Docs |
| llamaindex | LlamaIndex | Tier-S | AI & Machine Learning | Official Docs |
| python_docs | Python Docs | Tier-S | Software Engineering | Official Docs |
| mdn_web_docs | MDN Web Docs | Tier-S | Web Development | Official Docs |
| react_docs | React Docs | Tier-S | Web Development | Official Docs |
| nextjs_docs | Next.js Docs | Tier-S | Web Development | Official Docs |
| typescript_docs | TypeScript Docs | Tier-S | Software Engineering | Official Docs |
| devdocs | DevDocs | Tier-1 | Software Engineering | Official Docs |
| wolfram_mathworld | Wolfram MathWorld | Tier-S | Mathematics | Encyclopedia |
| oeis | OEIS | Tier-S | Mathematics | Encyclopedia |
| planetmath | PlanetMath | Tier-1 | Mathematics | Encyclopedia |
| stanford_encyclopedia_of_philosophy | Stanford Encyclopedia of Philosophy | Tier-S | Philosophy & Humanities | Encyclopedia |
| project_gutenberg | Project Gutenberg | Tier-S | Literature & Humanities | Data Portal |
| internet_archive | Internet Archive | Tier-1 | Multidisciplinary | Data Portal |
| perseus_digital_library | Perseus Digital Library | Tier-S | Classics & Humanities | Data Portal |
| internet_history_sourcebooks | Internet History Sourcebooks | Tier-S | History | Data Portal |
| pubchem | PubChem | Tier-S | Chemistry & Biology | Data Portal |
| nasa_science | NASA Science | Tier-S | Space & Earth Science | Data Portal |
| khan_academy | Khan Academy | Tier-S | General Education | Learning Platform |
| mit_ocw | MIT OpenCourseWare | Tier-S | General Education | Learning Platform |
| coursera | Coursera | Tier-S | General Education | Learning Platform |
| edx | edX | Tier-S | General Education | Learning Platform |
| freecodecamp | freeCodeCamp | Tier-1 | Software Engineering | Learning Platform |
| open_culture | Open Culture | Tier-1 | General Education | Learning Platform |
| github | GitHub | Tier-S | Software Engineering | OSS Hub |
| sourcegraph | Sourcegraph | Tier-1 | Software Engineering | OSS Hub |
| stack_overflow | Stack Overflow | Tier-S | Software Engineering | Q&A |
| ietf | IETF | Tier-S | Standards & Governance | Standards |
| w3c | W3C | Tier-S | Standards & Governance | Standards |
| nist | NIST AI Risk Framework | Tier-S | Security & Risk | Governance |
| owasp | OWASP | Tier-1 | Security & Risk | Governance |

---

## Appendix B: Domain Coverage Analysis

| Domain | Tier-S | Tier-1 | Total | Primary Use |
|--------|--------|--------|-------|-------------|
| Research & Preprints | 8 | 3 | 11 | Academic discovery, SOTA tracking |
| AI & Machine Learning | 13 | 4 | 17 | Framework docs, research, ethics |
| Software Engineering | 6 | 1 | 7 | Language specs, APIs, OSS |
| Web Development | 3 | 0 | 3 | Standards, frameworks |
| Mathematics | 3 | 1 | 4 | Reference, learning |
| General Education | 5 | 1 | 6 | Structured learning |
| Philosophy & Humanities | 4 | 1 | 5 | Ethics, history, literature |
| Data Portals | 6 | 1 | 7 | Domain-specific data |
| Standards & Governance | 3 | 1 | 4 | Compliance, protocols |
| **TOTAL** | **41** | **14** | **55** | — |

---

## Conclusion: Using This Bible Effectively

This atlas is a **living reference**, designed to be:

1. **Machine-readable**: Queries can be resolved programmatically via `tier_s_atlas.json`
2. **RAG-optimized**: Sections are chunked for efficient retrieval and summarization
3. **Conservative**: Sources are vetted to high standards; false positives avoided
4. **Actionable**: Each source record includes domain-specific guidance for AI agents

**For AI systems:** Use this Bible as the authoritative map when grounding reasoning in verifiable sources. Consult multiple tiers for cross-checking and confidence calibration.

**For humans:** Use this Bible as a quick-reference directory when you need to find the canonical source for any knowledge domain, from protocol specifications to cutting-edge AI research.

**For maintainers:** Update this Bible quarterly to reflect new canonical sources and tier reassignments. Archive old versions for historical reference.

---

*Version 1.0 — Generated November 19, 2025*
*Schema: https://github.com/perplexity/tier-s-knowledge-atlas*
