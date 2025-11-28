# Handshake Log

**Purpose**: Internal audit trail of all Brainstem SAFE MODE operations. Each entry contains a machine-checkable verification block for deterministic verification.

**Last Updated**: 2024-11-26

**Verification Block Format**: v2 (as of BSM-VERIFY-003)

---

## Verification Block v2 Format

Each verification block must include the following fields for deterministic, verifier-independent confirmation:

```
=== VERIFICATION BLOCK ===
repo_url: <git-url>
repo_home_url: <github-repo-homepage>
owner_profile_url: <github-owner-profile>
branch: main
head_local: <commit-sha>
head_remote: <commit-sha>
commit_url: <github-commit-url>
tree_url: <github-tree-url>
worktree_clean: true|false
raw_urls:
  - Handshake_Log: <raw-url>
  - Public_Proof: <raw-url>
  - <other-changed-files>: <raw-url>
changed_files:
  - <status> <file-path>
diffstat:
  <file-path> | <insertions> <deletions>
=== END VERIFICATION BLOCK ===
```

**Required Fields**:
- `repo_home_url`: GitHub repo homepage (e.g., https://github.com/5DVai/Obsidian_Knowledge_Core)
- `owner_profile_url`: GitHub owner profile (e.g., https://github.com/5DVai)
- `commit_url`: Direct link to the commit on GitHub
- `tree_url`: Direct link to the tree at that commit
- `raw_urls`: List of all raw URLs for changed files (Handshake_Log, Public_Proof, plus any files changed in the task)
- `changed_files`: Name-status list of all changed files
- `diffstat`: Summary of insertions/deletions per file
- `worktree_clean`: Boolean indicating if worktree is clean

This format enables independent verification by opening raw files + commit page without requiring authenticated access.

---

## Task: BSM-SAFE-002

**Task ID**: BSM-SAFE-002  
**Date**: 2024-11-26  
**Description**: Brainstem SAFE MODE v2: retrieval strategy + diagnostics

**Note**: This entry uses verification block v1 format (pre-v2). See format documentation above for v2 requirements.

=== VERIFICATION BLOCK ===
repo_url: https://github.com/5DVai/Obsidian_Knowledge_Core.git
branch: main
head_local: 61ea744c526bbfd933ed172d5a06bcf33ab5cf23
head_remote: 61ea744c526bbfd933ed172d5a06bcf33ab5cf23
worktree_clean: true
caps_respected:
  max_sources: 5
  max_modules: 2
changed_files:
  - A Brainstem/RAG_Diagnostics_Eval.md
  - A Brainstem/Retrieval_Strategy_Core.md
  - M Research/Indexes/MOC_Brainstem.md
  - A Research/Scholarly/CRAG_Corrective_RAG.md
  - A Research/Scholarly/GraphRAG_Global_Summarization.md
  - A Research/Scholarly/RAGCHECKER_Diagnostics.md
  - A Research/Scholarly/RAPTOR_Hierarchical_Retrieval.md
  - A Research/Scholarly/Self-RAG_Adaptive_Retrieval.md
  - M Research/_inbox/queue.md
diffstat:
 Brainstem/RAG_Diagnostics_Eval.md                  | 152 +++++++++++++++++++++
 Brainstem/Retrieval_Strategy_Core.md               | 104 ++++++++++++++
 Research/Indexes/MOC_Brainstem.md                  |  17 ++-
 Research/Scholarly/CRAG_Corrective_RAG.md          |  77 +++++++++++
 Research/Scholarly/GraphRAG_Global_Summarization.md |  75 ++++++++++
 Research/Scholarly/RAGCHECKER_Diagnostics.md       |  77 +++++++++++
 Research/Scholarly/RAPTOR_Hierarchical_Retrieval.md |  77 +++++++++++
 Research/Scholarly/Self-RAG_Adaptive_Retrieval.md  |  77 +++++++++++
 Research/_inbox/queue.md                           |  17 +++
 9 files changed, 669 insertions(+), 4 deletions(-)
=== END VERIFICATION BLOCK ===

---

## Task: BSM-VERIFY-003

**Task ID**: BSM-VERIFY-003  
**Date**: 2024-11-26  
**Description**: Public proof pack - externally verifiable links and evidence

**Public Proof**: See [[Public_Proof]] for verifiable GitHub URLs.

=== VERIFICATION BLOCK ===
repo_url: https://github.com/5DVai/Obsidian_Knowledge_Core.git
repo_home_url: https://github.com/5DVai/Obsidian_Knowledge_Core
owner_profile_url: https://github.com/5DVai
branch: main
head_local: e067b432a70f98e524a08db72b5a8a491ef1578c
head_remote: e067b432a70f98e524a08db72b5a8a491ef1578c
commit_url: https://github.com/5DVai/Obsidian_Knowledge_Core/commit/e067b432a70f98e524a08db72b5a8a491ef1578c
tree_url: https://github.com/5DVai/Obsidian_Knowledge_Core/tree/e067b432a70f98e524a08db72b5a8a491ef1578c
worktree_clean: true
caps_respected:
  max_sources: 0
  max_modules: 0
raw_urls:
  - Handshake_Log: https://raw.githubusercontent.com/5DVai/Obsidian_Knowledge_Core/main/Research/Indexes/Handshake_Log.md
  - Public_Proof: https://raw.githubusercontent.com/5DVai/Obsidian_Knowledge_Core/main/Research/Indexes/Public_Proof.md
changed_files:
  - A Research/Indexes/Public_Proof.md
  - M Research/Indexes/Handshake_Log.md
diffstat:
 Research/Indexes/Handshake_Log.md | 10 ++++++----
 Research/Indexes/Public_Proof.md  |  6 +++---
 2 files changed, 9 insertions(+), 7 deletions(-)
=== END VERIFICATION BLOCK ===

---

**Note**: Future tasks will append their verification blocks below this entry.

