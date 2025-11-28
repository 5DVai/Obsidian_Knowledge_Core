ACCEPT

# Smithery.ai SME Grimoire—Developer-First Reference Manual
**Source:** Smithery-ai-SME-Grimoire.md  
**Ingestion Date:** 2025-11-28

## Executive Summary
The Smithery.ai SME Grimoire serves as a comprehensive reference manual for developers working with Model Context Protocol (MCP) servers. It provides detailed insights into the architecture, deployment, and management of MCP servers, which are crucial for integrating AI models with external tools and services. The document outlines the capabilities of Smithery.ai as a centralized registry and hosting platform, emphasizing its role in enhancing developer velocity and ensuring cross-client compatibility. It also addresses critical security considerations and offers practical guidance for deploying and managing MCP servers at an enterprise scale.

Smithery.ai bridges the gap between AI models and external services by offering a robust ecosystem of over 2,880 indexed MCP servers. These servers facilitate a wide range of functionalities, including web scraping, image generation, database access, and API integrations. The Grimoire provides a detailed overview of the platform's strengths, such as its standards-based approach, local and remote development flexibility, and comprehensive observability features. Additionally, it highlights the importance of security and compliance, offering actionable insights into best practices for managing sensitive data and ensuring system integrity.

## Key Concepts & Principles
- **Model Context Protocol (MCP):** A standard for AI model-to-tool communication.
- **Smithery Registry:** A central index of MCP servers facilitating discovery and deployment.
- **Developer Velocity:** Enhanced by Smithery CLI for seamless development and deployment.
- **Cross-Client Compatibility:** Deploy once, use across multiple MCP-compliant clients.
- **Security Guardrails:** Emphasis on ephemeral configuration and data minimization.
- **Observability:** Comprehensive monitoring and analytics for hosted servers.

## Detailed Technical Analysis

### Architectural Patterns
Smithery.ai employs a layered architecture that integrates LLM clients, MCP servers, and external services. The architecture supports both local and remote development, with transport options including stdio and HTTP. The platform's registry layer facilitates server discovery and installation, while the hosting infrastructure provides managed deployment options with Docker container orchestration and health monitoring.

### Security and Compliance
The Grimoire outlines critical security measures, such as the prompt patching of vulnerabilities and the enforcement of ephemeral configuration policies. It emphasizes the importance of OAuth 2.1 for secure integrations and highlights the need for compliance with data protection standards, despite the absence of formal certifications like SOC 2 or GDPR.

### Deployment and Observability
Smithery.ai offers a streamlined deployment process through its CLI, supporting both local development and hosted deployment. The platform's observability features include usage tracking, tool call analytics, and server health monitoring, ensuring robust performance and reliability.

## Enterprise Q&A Bank

1. **How does Smithery.ai enhance developer velocity?**
   - By providing a CLI that handles tunneling, authentication, deployment, and testing without external setup.

2. **What security measures are in place for MCP servers?**
   - Smithery enforces ephemeral configuration, data minimization, and prompt vulnerability patching.

3. **How does Smithery.ai ensure cross-client compatibility?**
   - MCP servers are designed to work seamlessly across multiple clients without requiring client-specific rewrites.

4. **What are the key strengths of the Smithery ecosystem?**
   - A vast registry of MCP servers, standards-based development, and flexible deployment options.

5. **How does Smithery.ai handle observability?**
   - Through comprehensive monitoring and analytics, including usage tracking and server health checks.

## Actionable Takeaways
- Utilize the Smithery CLI for efficient development and deployment of MCP servers.
- Adhere to security best practices, including ephemeral configuration and OAuth 2.1 for integrations.
- Leverage Smithery's observability features to monitor server performance and ensure reliability.
- Ensure compliance with data protection standards by auditing server policies and configurations.
- Explore the extensive registry of MCP servers to enhance AI model capabilities with external services.

---
**Raw Content:**
---
title: "Smithery.ai SME Grimoire—Developer-First Reference Manual"
compiled_at_utc: "2025-11-23T12:06:00Z"
source_count: "143"
version_window: "2024-12-02 → 2025-11-22"
focus: "Developer-first MCP server design, registry operations, deployment, observability, and security at enterprise scale"
plan_policy: "Freemium SaaS; plans ignored unless upgrade/downgrade materially improves reliability, security, compliance, or performance"
---

# Executive Summary

**Smithery.ai** is a centralized registry, hosting platform, and management gateway for **Model Context Protocol (MCP)** servers. It bridges the critical gap between AI models (Claude, Cursor, and other LLM clients) and external tools, APIs, and data sources by indexing, deploying, and optimizing MCP-compliant services at scale.[13][17]

## What It Does

- **Registry**: 2,880+ indexed MCP servers discoverable and installable via Smithery CLI, playground, and web interface.[17]
- **Hosting**: Optional managed hosting for MCP servers on Smithery's infrastructure; eliminates self-hosting friction for many use cases.[18]
- **CLI & Tooling**: `@smithery/cli` for local development, testing, deployment, and ngrok-powered tunneling with hot-reload.[19]
- **Observability**: Usage tracking, tool call analytics, server health monitoring, and audit logs for hosted servers.[17][113]
- **Integration Gateway**: Transforms language models from isolated tools into agentic systems with unified, context-aware access to thousands of external services.[17][110]

## Key Strengths

1. **Ecosystem at Scale**: 2,880+ servers covering web scraping (Brave Search, Hyperbrowser), image generation (Stability AI, Flux), database access (Neon, Supabase), and API integrations (GitHub, Slack, Notion).[80]
2. **Developer Velocity**: Smithery CLI handles ngrok tunneling, authentication, deployment, and playground testing—no external setup required.[19]
3. **Standards-Based**: Native support for MCP specification; servers written in TypeScript (official SDK) or Python (FastMCP) work seamlessly.[14][59]
4. **Cross-Client Compatibility**: Deploy once, use across Claude, Cursor, and other MCP-compliant clients without client-specific rewrites.[17][113]
5. **Local & Remote Flexibility**: Supports both local stdio-based development and hosted remote access via HTTP streaming.[18][135]

## Critical Guardrails

- **Security Vulnerability History**: Path traversal vulnerability disclosed Oct 2025 affected 3,000+ servers; Smithery patched promptly.[58] OAuth metadata validation vulnerability found and fixed.[46]
- **No Long-Term Token Storage**: Configuration (including credentials) marked as ephemeral; not retained server-side per stated policy, but verify per-server.[18][43]
- **Data Minimization**: Anonymous tool call tracking; Smithery does not persist request payloads for hosted MCPs, but always audit sensitive integrations.[18]
- **Compliance Status**: No formal SOC 2 or GDPR certification published; enterprise deployments should confirm data residency and audit requirements.[111]

---

# Golden Paths (90-Minute Mastery)

## Path 1: Install & Use a Hosted MCP (5 min)

```bash
npm install -g @smithery/cli
smithery login --key YOUR_API_KEY
smithery install @smithery-ai/brave-search --client claude
# Restart Claude Desktop
# In Claude: tools now list Brave Search capabilities
```

**Result**: Installed, no code. Brave Search available in Claude immediately.[39]

## Path 2: Build & Test a Local MCP Server (45 min)

### TypeScript Quickstart

```bash
npx @smithery/cli create my-mcp-server
cd my-mcp-server
npm install
npm run dev
# Opens playground at http://localhost:3000
```

### Python Quickstart (FastMCP)

```bash
python3.12 -m venv venv
source venv/bin/activate
pip install fastmcp
cat > server.py << 'EOF'
from fastmcp import FastMCP

mcp = FastMCP("Hello Server")

@mcp.tool()
def greet(name: str) -> str:
    """Greet someone."""
    return f"Hello, {name}!"

if __name__ == "__main__":
    mcp.run(transport="stdio")
EOF

npx @smithery/cli run server.py --client claude
```

**Result**: Local server running, testable via CLI or playground.[56][59]

## Path 3: Deploy to Smithery (30 min)

1. **Prepare `smithery.yaml`**:

```yaml
runtime: "container"  # or "nodejs", "python"
build:
  dockerfile: "Dockerfile"
  dockerBuildPath: "."
startCommand:
  type: "http"
configSchema:
  type: "object"
  properties:
    apiKey:
      type: "string"
      description: "Your API key"
  required: ["apiKey"]
exampleConfig:
  apiKey: "sk-example123"
```

2. **Deploy**:

```bash
smithery deploy . --key YOUR_SMITHERY_API_KEY
# Server now hosted at https://smithery.ai/@namespace/my-mcp-server
```

3. **Verify**: Visit registry, inspect payload, test in playground.[56]

---

# System Overview

## Architecture & Lifecycle

```
┌─────────────────────────────────────────────────────────────────┐
│ LLM Client Layer (Claude, Cursor, etc.)                         │
└──────────────────────┬──────────────────────────────────────────┘
                       │ MCP Protocol (JSON-RPC 2.0)
┌──────────────────────▼──────────────────────────────────────────┐
│ Smithery MCP Client (or Native MCP Client)                       │
│ ├─ Discovery: Registry API queries for servers                  │
│ ├─ Installation: `smithery install` → local config              │
│ ├─ Transport: stdio (local) | HTTP (remote)                    │
└──────────────────────┬──────────────────────────────────────────┘
                       │ Transport (stdio/SSE/HTTP)
┌──────────────────────▼──────────────────────────────────────────┐
│ MCP Server (Local or Hosted)                                     │
│ ├─ Tools: callables (e.g., query_db, send_slack_msg)           │
│ ├─ Resources: readable context (e.g., docs, config)            │
│ ├─ Prompts: templated instructions for LLM                     │
│ ├─ Configuration: environment vars or config file              │
└──────────────────────┬──────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────────┐
│ External Services (APIs, Databases, etc.)                        │
│ ├─ GitHub, Slack, Google Drive, PostgreSQL, etc.               │
└──────────────────────────────────────────────────────────────────┘
```

### Smithery Registry Layer

```
Smithery.ai
├─ Web UI (https://smithery.ai)
│  ├─ Server Listing & Discovery
│  ├─ Interactive Playground (live tool/resource testing)
│  ├─ Analytics & Usage Dashboard
│  └─ API Key Management & Settings
├─ CLI (@smithery/cli)
│  ├─ install / uninstall / list servers
│  ├─ inspect server capabilities
│  ├─ dev (local hot-reload + tunnel)
│  ├─ build (produce .cjs bundle)
│  └─ deploy (publish to registry)
├─ Registry API
│  ├─ GET /servers (listing, filtering, search)
│  ├─ GET /server/{id} (metadata, config schema)
│  ├─ POST /servers (publish; requires API key)
│  └─ Analytics & Usage Events
└─ Hosting Infrastructure (optional)
   ├─ Docker container orchestration
   ├─ Reverse proxy (Nginx)
   ├─ Health monitoring & auto-restart
   └─ Egress tunnel (ngrok integration for dev)
```

## Component Interactions

**Server Registration Flow**:
1. Developer creates MCP server (TypeScript SDK / Python FastMCP).
2. `smithery.yaml` declares runtime, build config, and metadata.
3. `smithery deploy` submits to Smithery; GitHub repository becomes single source of truth.
4. Smithery builds Docker image, tests connectivity, publishes metadata to registry.
5. Users discover via CLI (`smithery search`) or web UI; install with `smithery install --client claude`.

**Tool Invocation Flow**:
1. User queries LLM with task requiring external action.
2. LLM client resolves MCP server connection (local stdio or remote HTTP).
3. LLM calls tool via MCP `call_tool` request (JSON-RPC).
4. MCP server routes to implementation, executes external API call.
5. Result returned to LLM as text/image/bytes; LLM reasons over result.
6. Analytics/tracing recorded by Smithery if hosted.

---

# Capabilities & Feature Matrix

| **Feature** | **Maturity** | **Availability** | **Notes & Dependencies** |
|---|---|---|---|
| **Registry (2,880+ servers)** | Stable | Public | Web UI + CLI; search, filter by category/language. [17] |
| **Local Development (stdio)** | Stable | Included | No external dependencies; fastest for CLI/local. [135] |
| **Hosted Deployment (Docker)** | Stable | Paid tiers | Automatic CI/CD from GitHub; observability included. [18] |
| **CLI Installation** | Stable | Included | Auto-detects LLM client (Claude Desktop, Cursor, etc.). [39] |
| **OAuth 2.1 Support** | Beta | SDK | Smithery SDK handles PKCE, token validation, scope enforcement. [40][43] |
| **Environment-Based Config** | Stable | Included | Ephemeral secrets; no long-term storage. [18] |
| **Hot-Reload Development** | Stable | `dev` command | Embedded ngrok; no external account needed. [19] |
| **Playground Testing** | Stable | Web UI | Real-time tool/resource inspection; parameter validation. [17] |
| **Analytics & Usage Tracking** | Stable | Hosted servers | RPM (requests per minute), tool call counts; opt-in. [17] |
| **Multi-Transport Support** | Stable | stdio, HTTP | SSE deprecated; moving to HTTP streaming. [138] |
| **Custom Domain (MCP)** | Paid | Pro+ | Per-server custom subdomain. [113] |
| **Official Registry Sync** | Beta | Pro+ | Publish to `.well-known/mcp.json` standards directory. [110] |
| **Observability (Logs, Traces)** | Beta | Pro+ | Advanced metrics, error categorization coming Q4 2025. [19] |
| **Resource Indicators (RFC 8707)** | Planned | — | Token scoping to specific resource servers (enterprise). [43] |

---

# Power Moves & Hidden Features

## 1. Ephemeral Configuration & Secret Handling

**What**: Smithery claims configuration data (including API keys) is **not retained** on their servers. Credentials are passed to MCP servers only when invoked.

**When to Use**: Sensitive integrations (GitHub PAT, Stripe API, Slack tokens).

**How to Maximize**:
- Store secrets in **environment variables** on your local machine or deployment platform.
- Use `--config` CLI flag with JSON, not plaintext files: `smithery install server --config '{"key":"$MY_SECRET"}'`
- For hosted MCPs, secrets are injected into the runtime environment; never logged or persisted.
- Audit each server's privacy policy if handling PII.

```bash
# ✓ Safe: env var injection
export GITHUB_PAT="ghp_xxx"
smithery install @smithery-ai/github --client claude --config "{\"token\":\"$GITHUB_PAT\"}"

# ✗ Avoid: hardcoding in config file
echo '{"token":"ghp_xxx"}' > config.json
smithery install @smithery-ai/github --config @config.json
```

**Citation**: [18][43]

---

## 2. Local-First Development with Automatic Tunneling

**What**: `smithery dev` spins up a hot-reloading development server on localhost:3000 and **automatically tunnels** via ngrok without requiring your own ngrok account.

**When to Use**: Rapid iteration, testing against real LLM clients (Claude, Cursor) without re-deployment.

**How to Maximize**:
```bash
smithery dev server.ts --port 3000 --key YOUR_API_KEY
# Output:
# ✓ Server running at http://localhost:3000
# ✓ Tunnel live at https://xxxxx-ngrok.smithery.io
# ✓ Playground: https://smithery.ai/playground?server=https://xxxxx-ngrok.smithery.io
```

- Edit code; changes auto-reload without restarting tunnel.
- Share tunnel URL with teammates for collaborative debugging.
- Tests run in Smithery playground or your LLM client's config.

**Citation**: [19][56]

---

## 3. Inline Configuration with `--config` Flag

**What**: Pass JSON configuration directly to override or provide defaults without interactive prompts.

**When to Use**: CI/CD pipelines, automated deployments, non-interactive environments.

**How to Maximize**:
```bash
# Skip all prompts
smithery install @smithery-ai/github \
  --client claude \
  --config '{"token":"ghp_xxx","org":"my-org","baseUrl":"https://github.com"}'

# Multi-line JSON from file
smithery run my-server --config @config.json

# Works with special characters
smithery install server --config '{"url":"https://example.com?a=1&b=2"}'
```

**Citation**: [39][42]

---

## 4. Beta: OAuth 2.1 Helper in Smithery SDK

**What**: New Smithery SDK abstracts OAuth 2.1 PKCE flow, token refresh, and scope validation.

**When to Use**: Integrations requiring user-delegated permissions (Salesforce, Microsoft Graph, etc.).

**How to Maximize**:

```typescript
import { OAuthHandler } from "@smithery/sdk";

const oauth = new OAuthHandler({
  clientId: "YOUR_CLIENT_ID",
  clientSecret: "YOUR_CLIENT_SECRET",
  redirectUri: "https://your-mcp.smithery.io/callback",
  scopes: ["user:email", "repo:status"],
  tokenEndpoint: "https://github.com/login/oauth/access_token",
  authEndpoint: "https://github.com/login/oauth/authorize"
});

// Handles PKCE, token caching, refresh
const token = await oauth.getToken(userId);
// Token automatically refreshed if expired
```

**Before SDK**: Manual PKCE validation, token refresh logic.
**After SDK**: One-liner; Smithery handles protocol compliance.

**Citation**: [40]

---

## 5. Lesser-Known CLI Flags & Undocumented Options

| Flag | Command | Effect | Notes |
|---|---|---|---|
| `--verbose` | Any | Detailed logs to stderr | Include in error reports. |
| `--debug` | Any | Debug-level tracing | Max verbosity; extremely noisy. |
| `--port <port>` | `dev` | Custom port (default 8081) | Avoid 3000, 8000 (reserved). |
| `--key <apikey>` | `install`, `deploy`, `run` | Explicit API key (vs. stored) | Override saved key without `login` step. |
| `--no-open` | `dev` | Don't auto-open playground | Useful in headless environments. |
| `--profile <name>` | `install`, `run`, `list` | Use non-default profile | Support multiple configs per client. |
| `--prompt <msg>` | `dev` | Initial prompt in playground | Preset scenario for testing. |
| `-c, --config <path>` | `dev` | Path to `smithery.config.js` | Auto-detect if absent; explicit override. |
| `--transport <type>` | `build` | `stdio`, `sse`, `http` | Choose transport for production build. |

**Example**: Multi-profile setup for staging & production MCPs.
```bash
smithery install @company/prod-mcp --client claude --profile prod --key prod_key
smithery install @company/staging-mcp --client claude --profile staging --key staging_key

# Later, switch profiles
smithery run @company/prod-mcp --profile prod
```

**Citation**: [42][45]

---

## 6. Smithery.yaml Path Traversal Hardening

**⚠️ Security Alert**: October 2025 disclosure revealed path traversal via `dockerBuildPath` in `smithery.yaml`. Exploit allowed arbitrary file read from builder machine.[58]

**Mitigations (Smithery applied)**:
- Validate `dockerBuildPath` is within cloned repository bounds.
- No parent directory (`../`) access allowed.
- Build context user must have explicit read permission.

**Developer Hardening**:
```yaml
# ✗ VULNERABLE (before fix)
build:
  dockerfile: "test/Dockerfile"
  dockerBuildPath: ".."  # Accesses parent, home directory exposed

# ✓ SAFE
build:
  dockerfile: "Dockerfile"
  dockerBuildPath: "."   # Stays within repo
```

**Citation**: [58][60]

---

## 7. Caching & Rate-Limit Strategies

**When to Use**: High-traffic MCPs or upstream API integrations with strict quotas (arXiv, Shopify, etc.).

**Caching Layers**:
1. **Server-side (Redis)**: Cache tool results; invalidate on events or TTL.
2. **LLM context caching**: Reuse resource blocks across tool calls.
3. **Local database**: Persistent cache for repeated queries.

**Example: arXiv MCP with Local Caching**:
```python
from fastmcp import FastMCP
import sqlite3

mcp = FastMCP("arxiv-cached")
db = sqlite3.connect(":memory:")

@mcp.tool()
async def search_arxiv(query: str, limit: int = 10) -> str:
    # Check cache
    cached = db.execute(
        "SELECT result FROM cache WHERE query = ?", (query,)
    ).fetchone()
    if cached:
        return cached[0]
    
    # Fetch from API (rate-limited by arXiv: 3s/request)
    result = await fetch_arxiv_api(query, limit)
    
    # Store in cache
    db.execute("INSERT INTO cache (query, result) VALUES (?, ?)", (query, result))
    db.commit()
    return result
```

**Result**: Repeated queries return instantly; respects upstream rate limits.[86][87]

**Citation**: [86][87][88]

---

## 8. Resource Indicators (RFC 8707) for Token Scoping

**Status**: Planned; not yet released.

**What**: Tokens issued by authorization server are scoped to **specific MCP server**, reducing blast radius if token is compromised.

**When to Use**: Enterprise multi-tenant deployments with centralized OAuth provider.

**Future Implementation** (anticipated):
```typescript
const token = await oauth.getToken(userId, {
  resourceIndicator: "https://smithery.ai/@company/database-mcp"
});
// Token is only valid for this specific MCP server
```

**Citation**: [43]

---

# Limits, Quotas, Timeouts

## Hard Limits

| **Parameter** | **Tier: Free** | **Tier: Pro** | **Tier: Enterprise** | **Notes** |
|---|---|---|---|---|
| **MCP Tool Call Volume** | 10K RPM (requests/month) | 1M RPM | Custom | Overages: $100/1M RPM. [113] |
| **Server Instance Count** | 1 (local only) | Unlimited | Unlimited | Free: no hosted instances. |
| **Concurrent Connections/Server** | 1 (stdio) | 100+ | Custom | Hosted HTTP: subject to ngrok limits. |
| **Request Timeout** | 30s (default) | 30s (configurable to 120s) | Custom | Hitting timeout returns `-32000` error. |
| **Configuration Size** | 64 KB | 256 KB | Unlimited | Large configs stored externally. |
| **Payload Size (Request/Response)** | 10 MB | 50 MB | Unlimited | Oversized payloads trigger 413 error. |
| **Concurrent Dev Tunnels** | 1 | 5 | Unlimited | Per-account; restart tunnel to free slot. |
| **API Key Rotation Cooldown** | None | None | None | New key active immediately; old key invalidated after 24h. |

---

## Soft Limits & Behavioral Throttling

| **Scenario** | **Behavior** | **Mitigation** |
|---|---|---|
| **Rate Limit (429)** | Retry-After header issued; honor exponential backoff. | Queue tool calls; use jitter (random 0-100ms). |
| **Quota Exceeded** | Free tier capped; Pro overages charged per 1M RPM. | Monitor dashboard; set up billing alerts. |
| **Tool Call Stuck (>30s)** | Timeout; MCP client receives JSON-RPC error `-32000 (Server error)`. | Add `timeout` param to tool definition; implement circuit breaker. |
| **Unhealthy Hosted Server** | Health check fails 3x in 60s; auto-restart triggered. | Log to stderr; restart auto-recovers within 30s. |
| **Malformed Config** | Install silently skips; no error msg to user. | Always test with `smithery inspect`. |

---

## Pagination & Iteration

**Registry API Pagination** (internal/undocumented):
```bash
# Web UI: auto-paginated, 50 results/page
# CLI: `smithery list` streams all; no explicit pagination needed

# To fetch all servers programmatically:
# Use REST endpoint (if exposed): https://api.smithery.ai/servers?page=1&limit=100
```

---

## Retry & Idempotency

**MCP Spec Requires Idempotency**:
- Tool calls are **not inherently idempotent** (e.g., "send email" sends twice if retried).
- Server must implement idempotency via deduplication key or side-effect tracking.

**Recommended Retry Strategy**:
```typescript
async function retryTool(toolName, args, maxAttempts = 3) {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await mcpClient.callTool(toolName, args);
    } catch (err) {
      if (err.code === -32000 && attempt < maxAttempts) {
        // Exponential backoff with jitter
        const delay = Math.pow(2, attempt - 1) * 1000 + Math.random() * 1000;
        await new Promise(resolve => setTimeout(resolve, delay));
      } else {
        throw err;
      }
    }
  }
}
```

**Example Idempotent Tool** (Slack):
```typescript
@mcp.tool()
async function sendSlackMessage(channel: string, text: string, idempotencyKey: string) {
  // Check if already sent with this key
  const sent = await redis.get(`slack:${idempotencyKey}`);
  if (sent) return JSON.parse(sent);
  
  // Send
  const result = await slack.chat.postMessage({ channel, text });
  
  // Cache result
  await redis.setex(`slack:${idempotencyKey}`, 3600, JSON.stringify(result));
  return result;
}
```

**Citation**: [87]

---

# APIs, SDKs, CLI—Practical Reference

## Authentication

### Method 1: API Key (Bearer Token)

```bash
# Login once
smithery login
# Stores key in ~/.smithery/config.json

# Or provide explicitly
smithery install server --key sk_YOUR_API_KEY_HERE

# Programmatically
curl -H "Authorization: Bearer sk_YOUR_API_KEY" \
  https://api.smithery.ai/servers
```

**Key Scope**: Full read/write on servers owned by your account; query public registry.

**Rotation**: Generate new key via web UI → Account Settings → API Keys; old key revoked within 24h.

---

### Method 2: OAuth 2.1 (For MCP Servers, Not Smithery API)

**Use Case**: Your MCP server integrates with GitHub, Slack, etc.; end-user delegates permissions.

```typescript
import { OAuthHandler } from "@smithery/sdk";

const handler = new OAuthHandler({
  clientId: process.env.GITHUB_CLIENT_ID,
  clientSecret: process.env.GITHUB_CLIENT_SECRET,
  authEndpoint: "https://github.com/login/oauth/authorize",
  tokenEndpoint: "https://github.com/login/oauth/access_token",
  scopes: ["user:email", "repo:status"],
  redirectUri: "https://my-mcp.smithery.io/oauth/callback"
});

// Start auth flow
const authUrl = handler.getAuthorizationUrl(state);

// Exchange code for token
const token = await handler.exchangeCode(code);
```

**Citation**: [40][43]

---

## Registry API (Implicit / Undocumented)

### Listing Servers

```bash
# CLI
smithery search "github"
# OR
smithery list  # installed servers

# Web UI
# https://smithery.ai → Browse all 2,880+ servers

# Programmatic (reverse-engineered)
curl https://api.smithery.ai/servers \
  -H "Accept: application/json" \
  -d '{"search":"github","limit":20}' 2>/dev/null | jq .
```

---

### Server Metadata

```bash
smithery inspect @smithery-ai/github

# Output:
# Name: GitHub
# Description: Access GitHub API, manage repos, files, PRs
# Language: TypeScript
# Latest Version: v1.0.0
# Config Schema: { token: { type: "string", ... } }
# Capabilities: Tools (list_repos, create_pr, ...), Resources (repo_files, ...)
```

---

## CLI Commands Reference

### `smithery install <server>`

```bash
smithery install @smithery-ai/github \
  --client claude \
  --config '{"token":"ghp_xxx"}' \
  --key sk_optional_explicit_key

# Flags:
#   --client: claude | cursor | windsurf (REQUIRED)
#   --config: JSON string or @file.json (optional; interactive if omitted)
#   --key: Smithery API key (optional; uses stored key if available)
```

**Side Effects**:
- Adds entry to LLM client's config file (e.g., `claude_desktop_config.json`).
- Restarts LLM client to load new server.

---

### `smithery uninstall <server>`

```bash
smithery uninstall @smithery-ai/github --client claude
```

---

### `smithery inspect <server>`

```bash
smithery inspect @smithery-ai/github

# Interactive: Lists all tools, resources, prompts
# Lets you test each tool with dummy data
```

---

### `smithery run <server>`

```bash
smithery run @smithery-ai/github \
  --config '{"token":"ghp_xxx"}'
```

**Use**: Standalone execution; server runs in foreground, logs to stderr.

---

### `smithery dev [entryFile]`

```bash
smithery dev src/index.ts \
  --port 3000 \
  --key sk_your_api_key \
  --no-open \
  --prompt "List my GitHub repos"

# Output:
# ✓ Hot-reload watching src/
# ✓ Tunnel ready: https://xxx-ngrok.smithery.io
# ✓ Playground: https://smithery.ai/playground?...
```

**Flags**:
- `--port`: Dev server port (default 8081).
- `--key`: Smithery API key for tunnel auth.
- `--no-open`: Skip auto-opening playground.
- `--prompt`: Pre-fill playground prompt for testing.
- `-c, --config`: Path to `smithery.config.js` (auto-detect default).

---

### `smithery build [entryFile]`

```bash
smithery build src/index.ts \
  --transport http \
  -o .smithery/index.cjs

# Outputs: bundle.cjs with all deps vendored
# Use in Docker: COPY .smithery/index.cjs /app && node /app/index.cjs
```

---

### `smithery deploy [directory]`

```bash
# Publish to Smithery registry
smithery deploy . \
  --key sk_your_api_key

# Reads smithery.yaml, builds Docker image, uploads to Smithery
# Server becomes available at https://smithery.ai/@namespace/name
```

---

### `smithery list [type]`

```bash
smithery list servers --client claude
# Lists installed MCP servers for Claude

smithery list clients
# Outputs: [ "claude", "cursor", "windsurf", ... ]
```

---

### `smithery search [term]`

```bash
smithery search "database"
# Searches Smithery registry for "database" in name/description

# Interactive: pick server to install or inspect
```

---

### `smithery login`

```bash
smithery login
# Prompts: "Get your API key from: https://smithery.ai/account/api-keys"
# Saves to ~/.smithery/config.json

# Verify
cat ~/.smithery/config.json | jq .apiKey
```

---

## Error Codes & Taxonomy

| **Code** | **HTTP Status** | **Cause** | **Action** |
|---|---|---|---|
| `-32000 (Server error)` | 500 | MCP tool execution failed; timeout or exception. | Check server logs; retry with exponential backoff. |
| `-32001 (Invalid params)` | 400 | Tool arguments don't match schema (type mismatch, missing required). | Validate args against `configSchema` from `inspect`. |
| `-32002 (Method not found)` | 404 | Tool name doesn't exist. | Verify tool name; run `smithery inspect`. |
| `401 (Unauthorized)` | 401 | API key invalid or expired. | Regenerate key; re-run `smithery login`. |
| `429 (Too Many Requests)` | 429 | Rate limit exceeded. | Implement exponential backoff; check quota. |
| `503 (Service Unavailable)` | 503 | Smithery or hosted server temporarily down. | Retry after delay; check status.smithery.ai. |
| `ECONNREFUSED` | Network | Can't connect to local server (stdio). | Check server is running; verify port. |
| `ETIMEDOUT` | Network | Request took >30s; server hung or crashed. | Restart server; increase timeout if legitimate. |

---

## Code Samples

### TypeScript MCP Server (Minimal)

```typescript
import {
  Server,
  Tool,
  TextContent,
  ErrorContent
} from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  {
    name: "my-tools",
    version: "1.0.0"
  },
  {
    capabilities: {
      tools: {}
    }
  }
);

// Define a tool
server.setRequestHandler(Tool.Request, async (request) => {
  if (request.params.name === "add") {
    const a = request.params.arguments?.a || 0;
    const b = request.params.arguments?.b || 0;
    return {
      content: [
        {
          type: "text",
          text: `${a} + ${b} = ${a + b}`
        }
      ]
    };
  }
  return {
    content: [
      {
        type: "text",
        text: "Tool not found"
      }
    ]
  };
});

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

---

### Python MCP Server (FastMCP)

```python
from fastmcp import FastMCP

mcp = FastMCP("my-tools")

@mcp.tool()
def add(a: int, b: int) -> str:
    """Add two numbers."""
    return f"{a} + {b} = {a + b}"

@mcp.resource("calc://history/{op}")
def history(op: str) -> str:
    """Get calculation history."""
    return f"History for {op}: [1+2=3, 2+3=5]"

if __name__ == "__main__":
    mcp.run(transport="stdio")
```

---

### Retry Logic (Client-Side)

```typescript
async function callToolWithRetry(
  client: MCP.Client,
  toolName: string,
  args: Record<string, any>,
  maxRetries: number = 3
): Promise<string> {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const result = await client.callTool(toolName, args);
      return result.content[0].text;
    } catch (error: any) {
      const isRetryable =
        error.code === -32000 || // Server error
        error.code === 503 || // Service unavailable
        error.message?.includes("timeout");

      if (isRetryable && attempt < maxRetries) {
        const delay = Math.pow(2, attempt - 1) * 1000 + Math.random() * 1000;
        console.log(`Attempt ${attempt} failed; retrying in ${delay}ms...`);
        await new Promise((resolve) => setTimeout(resolve, delay));
      } else {
        throw error;
      }
    }
  }
}
```

---

# Integration Patterns & Interop

## Pattern 1: Multi-MCP Workflows

**Scenario**: User asks Claude, "Summarize my GitHub issues and create a Slack reminder."

**Workflow**:
1. Claude resolves `github` MCP → lists issues.
2. Claude invokes `sequential-thinking` tool → reasons over issues.
3. Claude resolves `slack` MCP → creates reminder.

```yaml
# claude_desktop_config.json
{
  "mcpServers": {
    "github": {
      "command": "node",
      "args": ["path/to/github-mcp/index.cjs"],
      "env": { "GITHUB_TOKEN": "ghp_xxx" }
    },
    "slack": {
      "command": "node",
      "args": ["path/to/slack-mcp/index.cjs"],
      "env": { "SLACK_BOT_TOKEN": "xoxb_xxx" }
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["@smithery-ai/sequential-thinking"]
    }
  }
}
```

**Key Pattern**: Each MCP runs independently; Claude orchestrates via natural language.

---

## Pattern 2: Chaining Tools Across MCPs

**Scenario**: MCP A (Notion) reads a task; MCP B (GitHub) creates a PR; MCP C (Slack) posts update.

```typescript
// In Notion MCP
@mcp.tool()
async function getTask(taskId: string): Promise<Task> {
  // Fetch task
  return task;
}

// In GitHub MCP (consumed by LLM)
@mcp.tool()
async function createPr(owner: string, repo: string, branchName: string): Promise<PR> {
  // Create PR
  return pr;
}

// In Slack MCP
@mcp.tool()
async function postMessage(channel: string, text: string): Promise<Message> {
  // Post to Slack
  return message;
}

// LLM orchestrates:
// 1. "Read task from Notion"
// 2. "Create a PR on GitHub based on task"
// 3. "Post summary to Slack"
```

**Best Practice**: LLM maintains context across MCPs; no direct inter-MCP calls (simplifies debugging).

---

## Pattern 3: Gateway-Based Authorization

**Scenario**: Enterprise deploys centralized OAuth gateway; all MCPs validate tokens via gateway.

```
┌─────────┐       ┌──────────────────┐       ┌──────────────┐
│  LLM    │◄─────►│  OAuth Gateway   │◄─────►│  Auth Server │
│ Client  │       │  (Smithery Edge) │       │  (Okta/AD)   │
└─────────┘       └──────────────────┘       └──────────────┘
                         │
                         ├──► Validate bearer token
                         ├──► Enforce scopes & policies
                         ├──► Log audit events
                         ▼
                  ┌──────────────────┐
                  │   MCP Servers    │
                  │ (GitHub, Slack) │
                  └──────────────────┘
```

**Implementation** (gateway):
```typescript
router.post("/proxy/:mcp", async (req, res) => {
  const token = req.headers.authorization?.split(" ")[1];
  const validated = await validateToken(token);
  
  if (!validated) return res.status(401).send("Unauthorized");
  
  // Forward to MCP server
  const result = await mcpServers[req.params.mcp].callTool(
    req.body.method,
    req.body.params
  );
  res.json(result);
});
```

---

## Pattern 4: Resource-Based Context Injection

**Scenario**: MCP exposes read-only resources (docs, config) for LLM to reason over.

```typescript
@mcp.resource("docs://api/endpoints")
function getApiDocs(): string {
  return fs.readFileSync("docs/api.md", "utf8");
}

@mcp.resource("config://user/{userId}")
async function getUserConfig(userId: string): Promise<Config> {
  return await db.getUserConfig(userId);
}

// LLM can now ask:
// "Given the API docs (docs://api/endpoints), what endpoint should I call?"
// → LLM reads resource context, then calls tool with correct params
```

---

## Pattern 5: Fallback & Degradation

**Scenario**: Primary MCP server is down; fallback to cached or alternative service.

```typescript
async function callWithFallback(
  primaryTool: string,
  fallbackTool: string,
  args: Record<string, any>
) {
  try {
    return await mcpClient.callTool(primaryTool, args);
  } catch (err) {
    if (err.code === 503) {
      console.warn(`${primaryTool} unavailable; falling back to ${fallbackTool}`);
      return await mcpClient.callTool(fallbackTool, args);
    }
    throw err;
  }
}

// Usage:
const result = await callWithFallback(
  "github-list-repos",  // primary
  "local-cache-repos",   // fallback
  { org: "myorg" }
);
```

---

# Security, Privacy, Compliance

## Key Rotation & Token Management

### Smithery API Key Rotation

**Procedure**:
1. **Generate new key**: https://smithery.ai/account/api-keys → "New Key".
2. **Update clients**: Replace old key in `~/.smithery/config.json`, CI/CD secrets, etc.
3. **Invalidate old key**: Web UI → "Revoke" (takes effect within 24h).
4. **Verify**: Test with `smithery login --key sk_new_key`.

**Recommendation**: Rotate keys quarterly or if exposed.

---

### MCP Server Secrets (OAuth, API Keys)

**Secure Handling**:
1. **Never commit** secrets to Git; use `.gitignore`.
2. **Environment variables**: Inject at runtime via `smithery.yaml` or LLM client config.
3. **Secret managers** (for hosted): Use AWS Secrets Manager, HashiCorp Vault, or Smithery's ephemeral config.

```yaml
# ✓ Safe smithery.yaml
env:
  GITHUB_TOKEN: ${GITHUB_TOKEN}  # Resolved at build time
  SLACK_BOT_TOKEN: ${SLACK_BOT_TOKEN}

# ✗ Unsafe
env:
  GITHUB_TOKEN: "ghp_xxx123"  # Hardcoded
```

---

## Scopes & Permissions (OAuth 2.1)

**Example**: Smithery GitHub MCP requires `user:email` + `repo:status`.

**Best Practice**: Request **minimum scopes** necessary.

```typescript
const handler = new OAuthHandler({
  scopes: [
    "user:email",    // ← Minimum needed
    // "repo",        // ← Don't request if only reading
    "repo:status"    // ← Use repo:status, not full repo
  ]
});
```

**Citation**: [40][43]

---

## Data Flows & PII Handling

### Smithery's Data Handling

**Claimed (Per [18])**:
- **Tool call tracking**: Anonymous (aggregated counts only).
- **Configuration**: Ephemeral; not persisted server-side.
- **Request payloads**: Not logged or stored.
- **User data**: Not shared with third parties.

**Reality Check**: Audit per-server policies; Smithery hosts servers but doesn't guarantee upstream vendors' practices.

### If Handling PII/PHI

1. **Encrypt in transit**: TLS 1.2+ (enforced by HTTPS).
2. **Encrypt at rest** (if cached): AES-256 or better.
3. **Audit logging**: Log who accessed what; retain <90 days.
4. **Data residency**: Confirm Smithery infrastructure location (US-based as of 2025).

**Citation**: [18][43][111]

---

## Compliance Frameworks

| **Framework** | **Status** | **Smithery Notes** |
|---|---|---|
| **SOC 2 Type II** | Not published | Contact Smithery for enterprise audit evidence. |
| **GDPR** | Partial support | Ephemeral config; no PII retention claimed. Per-server audit required. |
| **HIPAA** | Not certified | Don't send PHI via Smithery-hosted servers; use self-hosted + BAA. |
| **ISO 27001** | Not published | — |
| **NIST 800-53** | Not applicable | — |

**Recommendation**: For regulated workloads (healthcare, finance), deploy MCPs **self-hosted** and ensure TLS + access controls.

**Citation**: [111][114]

---

## Threat Model & Hardening Checklist

### Attack Vectors & Mitigations

| **Vector** | **Risk** | **Mitigation** |
|---|---|---|
| **Malicious MCP Server** | Server steals API keys or data. | Audit server code; use only trusted registries. Least-privilege credentials. |
| **Token Leakage** | Token visible in logs/prompts. | Never log tokens; use env vars. Inject secrets only at runtime. |
| **Path Traversal (Hosting)** | Attacker accesses host files. | Smithery validates `dockerBuildPath`; keep services updated. |
| **Unsecured HTTP** | MITM intercepts credentials. | Enforce HTTPS everywhere; verify certs. |
| **Privilege Escalation** | MCP runs as root; attacker gains full system access. | Run MCPs as unprivileged user (Docker default). |
| **Injection Attacks** | LLM-generated tool args contain code. | Validate all inputs schema; whitelist allowed ops. |
| **DoS via Large Payloads** | 50MB request crashes server. | Enforce request size limits (10-50MB by tier). |
| **Rate-Limit Bypass** | Attacker exhausts quota faster than billed. | Monitor usage; set up alerts. Implement per-user rate limits. |

---

## Security Checklist for Production MCPs

- [ ] **Credentials**: Stored in environment variables, never committed.
- [ ] **HTTPS**: All endpoints use TLS 1.2+.
- [ ] **Authentication**: Bearer token or OAuth 2.1 validated.
- [ ] **Input Validation**: Tool arguments match schema; no arbitrary code execution.
- [ ] **Logging**: Sensitive data (tokens, PII) never logged.
- [ ] **Error Messages**: Don't leak internals (DB connection strings, API URLs).
- [ ] **Rate Limiting**: Implement per-IP, per-user, or per-tool limits.
- [ ] **Health Checks**: Respond to `/health` or equivalent.
- [ ] **Dependencies**: Audit for CVEs; pin versions.
- [ ] **Secrets Rotation**: Keys rotated <90 days; old keys invalidated immediately.

---

# Observability & Diagnostics

## Metrics You Should Emit

### Performance Metrics (via stderr or structured logging)

```typescript
// Log to stderr (MCP convention)
console.error(JSON.stringify({
  timestamp: new Date().toISOString(),
  level: "info",
  event: "tool_executed",
  toolName: "list_repos",
  durationMs: 234,
  statusCode: 200,
  callerId: "claude-user-123"  // Optional: for multi-tenancy
}));
```

### Key Metrics

| **Metric** | **Unit** | **Alert Threshold** |
|---|---|---|
| **Tool Execution Time** | ms | >5s (potential timeout) |
| **Error Rate** | % | >5% (indicates service degradation) |
| **Request Volume** | RPM | Approaching quota limit |
| **Uptime** | % | <99.9% (SLO breach) |
| **Latency (P50, P95, P99)** | ms | P95 >2s (degradation) |

---

## Logging Best Practices

### ✓ Safe Log Format (Stdout/Stderr)

```javascript
// Logs go to stderr
console.error(`[INFO] Tool called: ${toolName}`);
console.error(`[DEBUG] Request args: ${JSON.stringify(sanitized)}`); // Redact secrets
console.error(`[ERROR] Failed: ${error.message}`, error.stack);
```

### ✗ Unsafe Logging

```javascript
console.log(`API Key: ${apiKey}`);  // ← Never!
console.log(`User Password: ${password}`);  // ← Never!
console.log(request.headers);  // ← May contain Authorization header
```

---

## SLO & SLA Design

### Example SLO for Hosted MCP

```
Availability SLO: 99.9% (43.2 min downtime/month)
└─ Target: <5min mean-time-to-recovery (MTTR)

Latency SLO: P95 latency <1000ms
└─ Target: 95% of requests complete <1s

Error Rate SLO: <0.1% errors
└─ Target: Automatic retry + alerting at >0.05%
```

### Monitoring Dashboard (Recommended)

- **Request rate** (RPM over time).
- **Error rate** (by tool, status code).
- **Latency percentiles** (P50, P95, P99).
- **Resource usage** (memory, CPU, connections).
- **Rate-limit events** (429 responses).

---

## Runbook Triggers & Escalation

| **Signal** | **Check** | **Action** |
|---|---|---|
| **Latency spike** | P95 >2s for >2min | Check upstream API status; scale server. |
| **Error rate surge** | >5% for >1min | Check logs for exceptions; rollback if recent deploy. |
| **Quota exhausted** | RPC count ~limit | Alert team; pause non-critical requests. |
| **Server crash** | Health check fails 3x | Auto-restart; if persists, page on-call. |
| **Token expired** | 401 errors | Regenerate token; redeploy. |

---

# Tuning Recipes & Cost-Control

## Performance Tuning: Caching & Concurrency

### Recipe 1: Server-Side Result Caching

```typescript
import NodeCache from "node-cache";

const cache = new NodeCache({ stdTTL: 3600 }); // 1-hour TTL

@mcp.tool()
async function queryDatabase(query: string): Promise<string> {
  const cacheKey = `db:${query}`;
  
  // Check cache
  let result = cache.get(cacheKey);
  if (result) {
    console.error(`[INFO] Cache hit for: ${query}`);
    return result;
  }
  
  // Fetch & cache
  result = await db.query(query);
  cache.set(cacheKey, result);
  return result;
}
```

**Cost Impact**: Reduce API calls by ~70% → lower upstream costs.

---

### Recipe 2: Request Batching

**Problem**: 100 clients each call `getUser()` for the same user → 100 DB queries.

**Solution**: Deduplicate in-flight requests.

```typescript
const pendingRequests = new Map();

async function getUser(userId: string): Promise<User> {
  // Coalesce duplicate requests
  if (pendingRequests.has(userId)) {
    return pendingRequests.get(userId);
  }
  
  const promise = db.getUser(userId)
    .finally(() => pendingRequests.delete(userId));
  
  pendingRequests.set(userId, promise);
  return promise;
}
```

**Cost Impact**: Reduce redundant queries by ~90%.

---

### Recipe 3: Pagination for Large Datasets

**Problem**: Fetching 10,000 records at once consumes 500MB LLM context + $50 in tokens.

**Solution**: Paginate; LLM iterates.

```typescript
@mcp.tool()
async function listRepositories(
  org: string,
  page: number = 1,
  perPage: number = 50
): Promise<Repository[]> {
  return await github.repos.listForOrg({
    org,
    per_page: perPage,
    page
  });
}

// LLM calls multiple times:
// 1. "List repos for acme, page 1"
// 2. "List repos for acme, page 2"
// ... stop when fewer than 50 returned
```

---

### Recipe 4: Concurrency Control

**Problem**: 50 concurrent tool calls overwhelm upstream API (rate limit 10 RPS).

**Solution**: Queue with concurrency limit.

```typescript
import pLimit from "p-limit";

const limit = pLimit(5); // Max 5 concurrent

@mcp.tool()
async function fetchMultiple(urls: string[]): Promise<string[]> {
  return Promise.all(
    urls.map(url => limit(() => fetch(url)))
  );
}
```

---

## Cost Optimization

| **Lever** | **Savings** | **Trade-off** |
|---|---|---|
| **Caching** | 50-90% API calls | Stale data risk; TTL tuning. |
| **Pagination** | 80% token usage | More LLM steps. |
| **Compression** | 10-30% payload size | Negligible latency overhead. |
| **Batching** | 70% RPM | Slightly higher latency. |
| **Cheaper tier** | 60-80% cost | Lower SLA; shared resources. |

---

## Safe Defaults (Performance)

```yaml
# smithery.yaml
env:
  # Concurrency
  MAX_CONCURRENT_REQUESTS: "5"
  
  # Caching
  CACHE_TTL_SECONDS: "3600"
  MAX_CACHE_SIZE_MB: "100"
  
  # Timeouts
  REQUEST_TIMEOUT_MS: "30000"
  CONNECT_TIMEOUT_MS: "5000"
  
  # Rate limiting
  RATE_LIMIT_PER_MINUTE: "1000"
  RATE_LIMIT_BURST: "100"
  
  # Retries
  MAX_RETRIES: "3"
  RETRY_BASE_DELAY_MS: "100"
```

---

# Troubleshooting Playbooks

## Symptom: "Server Disconnected" or "Tool Not Found"

**Diagnosis**:
1. **Check server is running**:
   ```bash
   smithery inspect my-server
   # If no output: server crashed or not started
   ```

2. **Check client config**:
   ```bash
   cat ~/.config/Claude/claude_desktop_config.json | jq .mcpServers.my_server
   # Verify command, args, env vars
   ```

3. **Check logs**:
   ```bash
   # For local stdio servers, check LLM client's logs
   # For hosted: Smithery dashboard → Analytics → Error Logs
   ```

**Common Causes & Fixes**:

| **Cause** | **Fix** |
|---|---|
| Port conflict (dev server) | `lsof -i :3000`; kill process; retry. |
| Missing env var | Export: `export GITHUB_TOKEN=ghp_xxx`; restart. |
| Timeout (>30s) | Reduce data size; optimize query; increase timeout. |
| Out of memory | Reduce cache size; enable pagination. |
| Stale config | Restart LLM client after `smithery install`. |

---

## Symptom: "401 Unauthorized" or "Invalid Token"

**Diagnosis**:
1. **Check API key is valid**:
   ```bash
   smithery login
   # If fails: key is malformed or revoked
   ```

2. **Check token not expired**:
   ```bash
   curl -H "Authorization: Bearer sk_YOUR_KEY" https://api.smithery.ai/servers
   # If 401: key revoked
   ```

**Fixes**:
- Regenerate key: https://smithery.ai/account/api-keys → "New Key".
- Rerun: `smithery login`.
- Verify env var: `echo $SMITHERY_API_KEY`.

---

## Symptom: Rate Limited (429 "Too Many Requests")

**Diagnosis**:
1. **Check quota**:
   ```bash
   curl -H "Authorization: Bearer sk_YOUR_KEY" \
     https://api.smithery.ai/usage
   # See current RPM vs. limit
   ```

2. **Check for runaway loops**:
   ```bash
   # Smithery dashboard → Usage → RPM trend graph
   # Spike indicates runaway retries or repeated calls
   ```

**Fixes**:
- Upgrade tier (free → pro: 10K → 1M RPM).
- Implement caching to reduce API calls.
- Add exponential backoff to retries.
- Check for infinite retry loops in tools.

---

## Symptom: Timeout (Request Took >30s)

**Diagnosis**:
1. **Check tool latency**:
   ```bash
   time smithery run my-server --config '{"query":"test"}'
   # If >30s: tool too slow
   ```

2. **Check upstream service status**:
   ```bash
   curl https://api.github.com/user  # or other API
   # Slow response?
   ```

**Fixes**:
- Optimize query (add filters, pagination).
- Use caching for repeated queries.
- Upgrade to faster database instance.
- Split large operations into smaller steps.
- Contact upstream provider if their API is slow.

---

## Symptom: "Build Failed" or Docker Image Won't Start

**Diagnosis**:
1. **Check Dockerfile**:
   ```bash
   docker build -f Dockerfile .
   # Does it build locally?
   ```

2. **Check smithery.yaml**:
   ```bash
   npm install -g @smithery/cli
   smithery build .
   # Any errors?
   ```

3. **Check environment**:
   ```bash
   # Smithery build logs visible in web UI
   # https://smithery.ai/server/@namespace/name → Build Logs
   ```

**Common Causes**:
- Missing `package.json` or dependencies.
- Dockerfile references file outside build context.
- Port conflict (service already using configured port).

**Fixes**:
```bash
# Rebuild locally
docker build -t my-mcp:latest .
docker run -e API_KEY=test my-mcp:latest

# If works locally but fails on Smithery:
# - Check env vars in smithery.yaml
# - Verify Dockerfile uses COPY, not ADD external URLs
# - Ensure all deps in package.json or Dockerfile
```

---

# Anti-Patterns & Gotchas

## ❌ Anti-Pattern 1: Hardcoding Credentials

```typescript
// ✗ WRONG
const githubToken = "ghp_xxx123";
const mcpServer = new MCPServer({ token: githubToken });
export { mcpServer };  // Committed to Git!
```

**Why**: Credential leaked; anyone with repo access can impersonate your app.

```typescript
// ✓ RIGHT
const githubToken = process.env.GITHUB_TOKEN || throw new Error("GITHUB_TOKEN not set");
const mcpServer = new MCPServer({ token: githubToken });
```

---

## ❌ Anti-Pattern 2: Synchronous Tool Execution Without Timeout

```typescript
// ✗ WRONG
@mcp.tool()
function queryLargeDatabase(query: string): string {
  // Hangs if DB is slow; no timeout
  return db.query(query);  
}
```

```typescript
// ✓ RIGHT
@mcp.tool()
async function queryLargeDatabase(query: string): Promise<string> {
  return Promise.race([
    db.query(query),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Query timeout")), 30000)
    )
  ]);
}
```

---

## ❌ Anti-Pattern 3: Tool Names Leaking Internal Structure

```typescript
// ✗ WRONG
@mcp.tool()
async function _internal_list_users(): Promise<string> { }

@mcp.tool()
async function DEBUG_get_token(): Promise<string> { }

// LLM sees internal/debug tools; confusing and risky
```

```typescript
// ✓ RIGHT
@mcp.tool()
async function listUsers(): Promise<string> { }  // Polished name
```

---

## ❌ Anti-Pattern 4: Unvalidated Inputs

```typescript
// ✗ WRONG
@mcp.tool()
async function deleteRepository(owner: string, repo: string): Promise<string> {
  // No validation; LLM could pass owner="../../", repo="../.."
  return await github.repos.delete({ owner, repo });
}
```

```typescript
// ✓ RIGHT
@mcp.tool()
async function deleteRepository(owner: string, repo: string): Promise<string> {
  // Validate format
  if (!/^[a-zA-Z0-9_-]+$/.test(owner)) throw new Error("Invalid owner");
  if (!/^[a-zA-Z0-9_-]+$/.test(repo)) throw new Error("Invalid repo");
  
  return await github.repos.delete({ owner, repo });
}
```

---

## ❌ Anti-Pattern 5: No Idempotency for Destructive Operations

```typescript
// ✗ WRONG
@mcp.tool()
async function sendSlackMessage(channel: string, text: string): Promise<string> {
  // If retried, sends 2+ times
  return await slack.chat.postMessage({ channel, text });
}
```

```typescript
// ✓ RIGHT
@mcp.tool()
async function sendSlackMessage(
  channel: string,
  text: string,
  idempotencyKey: string
): Promise<string> {
  // Check if already sent
  const cached = await redis.get(`slack:${idempotencyKey}`);
  if (cached) return cached;
  
  const result = await slack.chat.postMessage({ channel, text });
  
  // Cache result
  await redis.setex(`slack:${idempotencyKey}`, 3600, JSON.stringify(result));
  return result;
}
```

---

## ❌ Anti-Pattern 6: Trusting LLM-Generated Queries

```typescript
// ✗ WRONG (SQL Injection)
@mcp.tool()
async function queryDatabase(sqlQuery: string): Promise<string> {
  // LLM could pass: "'; DROP TABLE users; --"
  return await db.query(sqlQuery);
}
```

```typescript
// ✓ RIGHT (Parameterized)
@mcp.tool()
async function queryDatabase(table: string, filters: Record<string, string>): Promise<string> {
  // Build query safely
  let query = `SELECT * FROM ${table} WHERE`;
  const params: any[] = [];
  
  for (const [key, value] of Object.entries(filters)) {
    if (!["name", "id", "email"].includes(key)) throw new Error(`Invalid field: ${key}`);
    query += ` ${key} = ? AND`;
    params.push(value);
  }
  
  return await db.query(query, params);
}
```

---

## ❌ Anti-Pattern 7: Returning Sensitive Data in Tool Output

```typescript
// ✗ WRONG
@mcp.tool()
async function listSecrets(): Promise<string> {
  const secrets = await secretManager.list();
  return JSON.stringify(secrets);  // Exposed to LLM & logs
}
```

```typescript
// ✓ RIGHT
@mcp.tool()
async function getSecretMetadata(secretId: string): Promise<string> {
  // Return only metadata, not the actual secret
  const secret = await secretManager.get(secretId);
  return JSON.stringify({
    id: secret.id,
    createdAt: secret.createdAt,
    lastRotated: secret.lastRotated
    // ← No actual secret value
  });
}
```

---

## ❌ Anti-Pattern 8: Exponential Backoff Without Jitter

```typescript
// ✗ WRONG (Thundering Herd)
for (let attempt = 1; attempt <= 3; attempt++) {
  try {
    return await retryableCall();
  } catch (e) {
    await sleep(Math.pow(2, attempt) * 1000);  // 2s, 4s, 8s
    // ← All clients retry at same time; overwhelming server
  }
}
```

```typescript
// ✓ RIGHT (With Jitter)
for (let attempt = 1; attempt <= 3; attempt++) {
  try {
    return await retryableCall();
  } catch (e) {
    const jitter = Math.random() * 1000;  // 0-1s random
    const delay = Math.pow(2, attempt) * 1000 + jitter;
    await sleep(delay);  // 2s+jitter, 4s+jitter, 8s+jitter
  }
}
```

---

# Roadmap & Changelogs (Curated)

## Recent High-Signal Changes (Last 12 Months)

### Q4 2025: Security Patches & Observability

- **Oct 2025**: Path traversal vulnerability patched; `dockerBuildPath` validation enforced.[58]
- **Sep 2025**: OAuth metadata validation fixed; CSP headers added.[46]
- **Aug 2025**: Beta: Smithery SDK with OAuth 2.1 helpers; one-line PKCE setup.[40]
- **Q4 2025 (Upcoming)**: Advanced observability (tracing, error categorization).[19]

### Q3 2025: MCP Ecosystem Standardization

- **Ongoing**: `.well-known/mcp.json` registry specification gaining adoption.[110]
- **Impact**: Future; decentralized discovery may reduce Smithery registry monopoly.

### Q2 2025: CLI & Deployment UX

- **Jun 2025**: Smithery CLI v0.4.4 released; multi-profile support, `--transport` flag.
- **May 2025**: Hot-reload development server stabilized; ngrok tunneling built-in.[19]

---

## Deprecations & Migration Notes

| **Feature** | **Status** | **Replacement** | **Deadline** |
|---|---|---|---|
| **SSE Transport** | Deprecated | HTTP streaming | Q1 2026 |
| **Node.js 16 Support** | Deprecated | Node.js 18+ | Q1 2026 |
| **Hardcoded Secrets** | Discouraged | Environment variables | Ongoing |
| **OAuth 1.0** | Deprecated | OAuth 2.1 | Q2 2026 |

---

## Watchlist Queries (For Operators)

Monitor these GitHub repos & Smithery announcements:

1. **smithery-ai/cli** → New CLI versions, bug fixes.
2. **smithery-ai/sdk** → SDK upgrades, new helper functions.
3. **smithery-ai/docs** → Breaking changes, new features.
4. **modelcontextprotocol/** → Upstream MCP spec changes.

---

# Competitor Flyover

| **Alternative** | **Strength** | **Trade-off** | **Use When** |
|---|---|---|---|
| **Composio** | Native MCP + 500+ integrations; managed auth. | Proprietary; less community-driven. | Enterprise with single-vendor preference. |
| **MCP.so** | Community directory; >4,500 servers indexed. | Discovery-only; no hosting. | Exploring; open-source philosophy. |
| **Glama** | Smaller, polished server set; commercial support. | Limited catalog; paid. | Enterprise wanting SLA. |
| **Pipedream** | No-code workflows; hybrid MCP support. | Not MCP-native; abstraction layer. | Teams without coding bandwidth. |
| **DIY (GitHub)** | Full control; no vendor lock-in. | Maintenance burden; reinvent wheels. | Building custom integrations. |

---

# References

| **[ID]** | **Title** | **Publisher** | **URL** | **Pub/Updated** | **Accessed** | **Tier** | **Notes** |
|---|---|---|---|---|---|---|---|
| 13 | Master Smithery.ai's Cutting-Edge Capabilities | XPack.AI | https://xpack.ai/techblog/… | 2025-07-09 | 2025-11-23 | 3 | High-level overview; marketing blend. |
| 14 | smithery-ai/sdk | GitHub | https://github.com/smithery-ai/sdk | 2024-12-02 | 2025-11-23 | 1 | Official SDK README; TypeScript + Python. |
| 17 | Unveiling Smithery: Ultimate Platform for Agentic AI | mcp.so | https://mcp.so/smithery-ai | 2025-03-25 | 2025-11-23 | 2 | Comprehensive MCP + Smithery overview. |
| 18 | Smithery.ai: Central Hub for MCP Servers | WorkOS | https://workos.com/blog/smithery-ai | 2025-04-07 | 2025-11-23 | 2 | Detailed local vs. hosted workflow; security. |
| 19 | Smithery: Shaping Agent-First Internet | ngrok | https://ngrok.com/blog/smithery-ai-shaping-agent-first-internet | 2025-08-18 | 2025-11-23 | 2 | Vision + CLI tooling deep-dive; ngrok integration. |
| 39 | cli | MCP Server Finder | https://www.mcpserverfinder.com/servers/smithery-ai/cli | 2024-12-21 | 2025-11-23 | 2 | CLI commands reference. |
| 40 | OAuth 2.1 for MCP Servers | LinkedIn (Calclavia) | https://linkedin.com/posts/calclavia/… | 2025-09-28 | 2025-11-23 | 2 | OAuth SDK + PKCE in MCP context. |
| 42 | smithery-ai/cli (GitHub) | GitHub | https://github.com/smithery-ai/cli | 2024-12-21 | 2025-11-23 | 1 | Official CLI repo; comprehensive commands. |
| 43 | OAuth for MCP: Enterprise Patterns | GitGuardian | https://blog.gitguardian.com/oauth-for-mcp-emerging-enterprise-patterns-for-agent-authorization/ | 2025-10-21 | 2025-11-23 | 2 | Enterprise OAuth + gateway patterns; RFC 8707. |
| 45 | Page Speed Insights MCP Server | Remote MCP Servers | https://www.mcpnow.io/en/server/pagespeed-insights-phialsbasement-pagespeed-mcp-server | 2025-05-27 | 2025-11-23 | 3 | Example error handling in MCP servers. |
| 46 | Obsidian Security: Well-Known to Well-Pwned | Obsidian Security | https://www.obsidiansecurity.com/blog/from-well-known-to-well-pwned-common-vulnerabilities-in-ai-agents | 2025-11-04 | 2025-11-23 | 1 | OAuth metadata XSS vulnerability; Smithery fixed. |
| 56 | smithery-ai-cookbook-python-quickstart | MCPKit | https://www.mcpkit.com/tools/ai.smithery%2Fsmithery-ai-cookbook-python-quickstart | 2012-12-31 | 2025-11-23 | 2 | FastMCP quickstart; deployment recipe. |
| 58 | From Path Traversal to Supply Chain Compromise | GitGuardian | https://blog.gitguardian.com/breaking-mcp-server-hosting/ | 2025-10-26 | 2025-11-23 | 1 | Critical: Path traversal in Smithery hosting. |
| 59 | Has Anyone Developing MCP Servers in Python? | Reddit | https://www.reddit.com/r/AI_Agents/comments/1k784co/… | 2025-04-24 | 2025-11-23 | 3 | Community tips; FastMCP adoption. |
| 60 | Smithery.ai Servers & Keys Exposed | LinkedIn | https://www.linkedin.com/posts/thedailytechfeed_… | 2025-10-22 | 2025-11-23 | 2 | Path traversal impact; 3,000+ servers. |
| 80 | Smithery: App Purchase Models & Untapped Niches | AI Coding Helper | https://www.aicoding.help/ai/smithery-ai | 2025-11-22 | 2025-11-23 | 2 | Pricing tiers; feature comparison. |
| 86 | Definitive Guide to arXiv MCP Servers | Skywork | https://skywork.ai/skypage/en/unlocking-terminal-ai-engineer-guide/1980904294935072768 | 2025-10-26 | 2025-11-23 | 2 | Caching strategies; rate-limit handling. |
| 87 | Mastering Webhook Retry Logic | SparkCo | https://sparkco.ai/blog/mastering-webhook-retry-logic-strategies-and-best-practices | 2025-11-21 | 2025-11-23 | 2 | Exponential backoff + jitter patterns. |
| 88 | Shopify MCP Server Deep-Dive | Skywork | https://skywork.ai/skypage/en/[shopify-mcp-server-main]… | 2025-09-28 | 2025-11-23 | 2 | Caching, rate limits, multi-core handling. |
| 110 | Why We're All-In on MCP | Mastra | https://mastra.ai/blog/mastra-mcp | 2025-03-04 | 2025-11-23 | 2 | MCP ecosystem + registry specification. |
| 111 | Best Smithery Alternatives 2025 | Composio | https://composio.dev/blog/smithery-alternative | 2025-11-19 | 2025-11-23 | 2 | Competitive analysis; MCP platform comparison. |
| 113 | Smithery AI MCP Review 2025 | Skillcraft AI | https://www.youtube.com/watch?v=7t8TJ6wH41U | 2025-11-05 | 2025-11-23 | 3 | Pricing tiers demo; feature walkthrough. |
| 114 | GDPR vs. SOC 2 Compliance | Scytale | https://scytale.ai/question/what-are-the-key-differences-between-gdpr-and-soc-2-compliance/ | 2025-04-10 | 2025-11-23 | 2 | Compliance mapping;