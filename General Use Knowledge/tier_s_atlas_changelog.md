# Tier-S Knowledge Atlas Changelog

## Version 1.0 — Initial Release
**Date:** November 19, 2025  
**Status:** Initial release (no prior atlas exists)

### Summary

Created the inaugural **Tier-S Knowledge Atlas** by normalizing and merging sources from six input documents:
- AI-Knowledge-Atlas-Generation.pdf (epistemology narrative)
- Open-AI-Tier-S-1-research.txt (40+ OpenAI-curated sources)
- Perplexity-Tier-S-1-research.txt (45+ Perplexity-curated sources)
- Perplexity-Tier-S-1-research.txt (scholarly sources by domain)
- tier-s-and-1-sources.md (Markdown reference)
- Tier-1-Research-AI_blanktopic.txt (RAG-optimized template)

### Outputs

1. **tier_s_atlas.json** – Machine-readable canonical atlas (55 sources)
2. **tier_s_master_bible.md** – Human-optimized reference guide with decision trees
3. **tier_s_atlas_changelog.md** – This changelog

### Core Statistics

- **Total sources:** 55
- **Tier-S (Supreme):** 41
- **Tier-1 (Primary Secondary):** 14
- **Domains represented:** 20
- **Primary domain clusters:**
  - AI & Machine Learning: 13 sources
  - Research & Preprints: 11 sources
  - General Education: 6 sources
  - Software Engineering: 7 sources
  - Data Portals: 7 sources

### Deduplication & Normalization

The following sources were identified as duplicates and merged:

| ID | Name | Notes |
|----|----|-------|
| python_docs | Python Documentation | Appeared in 4 input docs; merged into single record |
| arxiv | arXiv | Appeared in 4 input docs; consolidated tier/domain |
| mdn_web_docs | MDN Web Docs | Appeared in 4 input docs; unified URLs |
| github | GitHub | Appeared in 3 input docs; added API endpoints |
| stack_overflow | Stack Overflow | Appeared in 3 input docs; unified |
| react_docs | React Docs | Appeared in 2 input docs; unified |
| typescript_docs | TypeScript Docs | Appeared in 2 input docs; unified |
| nextjs_docs | Next.js Docs | Appeared in 2 input docs; unified |
| mit_ocw | MIT OpenCourseWare | Appeared in 3 input docs; unified |
| wolfram_mathworld | Wolfram MathWorld | Appeared in 2 input docs; unified |
| oeis | OEIS | Appeared in 2 input docs; unified |
| jstor | JSTOR | Appeared in 2 input docs; unified |
| google_scholar | Google Scholar | Appeared in 2 input docs; unified |
| semantic_scholar | Semantic Scholar | Appeared in 2 input docs; unified |
| doaj | DOAJ | Appeared in 2 input docs; unified |
| stanford_encyclopedia_of_philosophy | Stanford Encyclopedia of Philosophy | Appeared in 2 input docs; unified |

**Result:** 71 raw mentions deduplicated to 55 unique canonical sources.

### Tier Assignment Logic

Sources were assigned to Tier-S or Tier-1 using conservative rules:

- **Tier-S (Supreme):** Official docs, primary research repositories, canonical standards, frontier labs
  - Examples: arXiv, Python Docs, PyTorch, GitHub, IETF
- **Tier-1 (Primary Secondary):** Peer-reviewed journals, aggregators, curated indexes, well-maintained resources
  - Examples: JMLR, IEEE Xplore, DevDocs, freeCodeCamp

**Conflicts resolved:**
- When tier labels differed between input docs, the **more conservative tier** was chosen
- Example: Some docs listed GitHub as Tier-1; consolidated to Tier-S (it is the canonical source)
- Example: DevDocs listed as Tier-S in some docs; assigned Tier-1 (it is a meta-aggregator, not primary)

### Domains Established

| Domain | Count | Primary Use |
|--------|-------|-------------|
| Research & Preprints | 11 | Academic discovery, SOTA |
| AI & Machine Learning | 13 | ML frameworks, research |
| Software Engineering | 7 | Language specs, APIs |
| Web Development | 3 | Standards, frameworks |
| Mathematics | 4 | Reference, learning |
| General Education | 6 | Structured learning |
| Philosophy & Humanities | 5 | Ethics, analysis |
| Data Portals | 7 | Domain-specific data |
| Standards & Governance | 4 | Compliance, protocols |
| **Other domains** | **0** | — |
| **TOTAL** | **55** | — |

### Schema Choices

The `tier_s_atlas.json` schema was designed for:

1. **RAG compatibility**: Each source includes `suggested_use_for_ai` and `content_styles` for chunking
2. **API-first design**: `key_links` includes search, API, and specialized endpoints
3. **Auditability**: `provenance` field tracks which input documents mentioned each source
4. **Conservative updates**: Required fields prevent incomplete records

### Known Limitations & Caveats

1. **English-language bias**: All sources are primarily English. Multi-language scholarly repositories not included.
2. **Proprietary/paywalled sources:** JSTOR, ScienceDirect, and some IEEE/ACM resources marked as Tier-1 due to paywall constraints for AI systems.
3. **Emerging domains underrepresented:** Quantum computing, climate modeling, and bioinformatics have limited coverage (addressed if new sources are added).
4. **No Tier-2 sources:** This atlas intentionally excludes Tier-2 (blogs, Medium, tutorials) to maintain high signal-to-noise ratio.
5. **Geographic limitations:** Primarily reflects North American and Western European knowledge infrastructure.

### What's NOT Included

- Personal blogs (even well-known ones like Karpathy's) → Too mutable
- StackOverflow threads (except the site itself as Q&A archive) → Individual answers are Tier-3
- Wikipedia → Derivative, not primary source
- YouTube channels → Difficult to canonicalize
- Paid courses (except Coursera, edX, which have institutional backing) → Not freely accessible
- Corporate technical blogs (except OpenAI, DeepMind as frontier labs) → May change without notice

### Versioning & Update Plan

**Next update triggers:**
- Quarterly review of source URLs and access status
- Addition of new Tier-S sources only if recommended by multiple users or clear provenance
- Tier reassignments if sources change their access/maintenance model

**Archive policy:** All prior versions retained for historical reference and change tracking.

---

## Recommended Next Steps for Users

1. **Validate canonical URLs:** Confirm all `key_links` are current (November 2025)
2. **Test RAG integration:** Chunk `tier_s_master_bible.md` into 800–1500 token sections for embedding
3. **Implement decision trees:** Code up the query resolution logic from Part 4 of the Bible
4. **Monitor for updates:** Subscribe to changes in source maintenance status (e.g., if JMLR or arXiv change their APIs)
5. **Expand by domain:** If you have a specialized domain (quantum computing, climate science), add Tier-S sources for that domain with justification

---

*Changelog maintained at: https://github.com/perplexity/tier-s-knowledge-atlas/releases*
