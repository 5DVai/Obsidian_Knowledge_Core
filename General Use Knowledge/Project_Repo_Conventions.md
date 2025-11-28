# Project & Repo Conventions Bible

**Source:** 20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingested From:** .source_truth/20 Bibles Documents to Supercharge a Coding Assistant 1.txt
**Ingestion Date:** 2025-11-26

## Summary

This document establishes the canonical set of conventions for all software project repositories. It treats the repository structure as an API that must be stable, predictable, and secure. It covers directory layouts for various languages (Python, Go, JS/TS), naming conventions, configuration standards, and specific handling for AI/ML artifacts (prompts, embeddings, models).

## Key Points

- **Repo as API:** Structure is a contract for tools and developers.
- **Separate Concerns:** `src/` for code, `tests/` for tests, `infra/` for IaC.
- **Secure by Default:** `.gitignore` is the first line of defense; use Secret Managers, not `.env` files for production secrets.
- **AI Artifacts != Code:** Prompts go in `prompts/` or a registry; Embeddings go in Vector DBs; Models go in Model Registries (DVC).
- **Language Standards:**
  - **Python:** Mandatory `src/` layout.
  - **Go:** Standard `cmd/`, `internal/`, `pkg/` layout.
  - **JS/TS:** `src/` and `dist/` (gitignored).

## Detailed Content

### 1. Foundational Directory Structure

* **`src/`**: Source code root. Mandatory for Python to prevent import errors.
- **`tests/`**: Test code, mirroring `src/` structure.
- **`dist/` & `build/`**: Generated artifacts. **MUST be in `.gitignore`**.
- **`docs/`**: Documentation, following the Diátaxis framework (Tutorials, How-To, Reference, Explanation).
- **`infra/`**: Infrastructure as Code (Terraform, Ansible).
- **`scripts/`**: Utility automation scripts.

### 2. Language-Specific Layouts

* **Go (Golang):**
  - `cmd/`: Main entry points.
  - `internal/`: Private application code (compiler enforced).
  - `pkg/`: Public library code (use sparingly).
- **Python:**
  - `pyproject.toml`: Single config for metadata/deps.
  - `src/my_package/`: Actual package code.
- **JavaScript/TypeScript:**
  - `package.json` & `package-lock.json`.
  - `tsconfig.json`.
  - `src/`: Source files.

### 3. Naming Conventions

* **Directories:** `kebab-case` (e.g., `src/feature-auth`).
- **Files:**
  - Python/Go: `snake_case` (e.g., `user_service.py`).
  - JS/TS: `kebab-case` (e.g., `user-service.ts`).
- **Classes:** `PascalCase` (e.g., `UserService`).
- **Variables/Functions:**
  - Python/Go: `snake_case`.
  - JS/TS: `camelCase`.

### 4. Configuration & Security

* **`.gitignore`**: Strict exclusion of secrets, logs, and build artifacts.
- **`.editorconfig`**: Enforce consistent coding styles (indentation, line endings).
- **Dependency Management**: **Commit lock files** (`package-lock.json`, `poetry.lock`) for applications to ensure reproducibility.
- **Secrets Management**:
  - **Forbidden**: Hard-coded secrets, committed `.env` files.
  - **Prescribed**: Hybrid model. Local `.env.local` (gitignored) contains a *single* token for a Secret Manager (e.g., Vault, Infisical), which injects actual secrets at runtime.

### 5. AI/ML & RAG Conventions

* **Prompts**: Treat as content, not code. Store in `prompts/` directory (YAML/MD) or use an external Prompt Registry.
- **Embeddings**: **NEVER commit to Git**. Store in a Vector Database (Milvus, Pinecone).
- **Data & Models**: Use **DVC** (Data Version Control). Store pointers in Git, actual files in S3/GCS.
- **Notebooks**: `notebooks/` for exploration only. Production logic must be moved to `src/`.

### 6. Operational Playbooks

* **Greenfield**: Start with a template (e.g., Cookiecutter), init Git, create `.gitignore` immediately, set up CI/CD.
- **Brownfield**: Audit for secrets, implement `.gitignore`, migrate secrets to Manager, refactor to `src/` layout.
