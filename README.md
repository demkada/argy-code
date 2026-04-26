# Argy Code

**Your governed AI development team in the terminal.**

50+ expert AI profiles in one agent — Clean Code, TDD, cloud architects, security, networks, COBOL, ABAP, every programming language. Interactive for daily work, autonomous for CI/CD. Governed by the [Argy LLM Gateway](https://argy.cloud/en/llm-gateway) (quotas, filters, audit).

[![npm](https://img.shields.io/npm/v/@argy-cloud/code)](https://www.npmjs.com/package/@argy-cloud/code)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux%20%7C%20Windows-blue)](https://github.com/demkada/argy-code/releases)
[![Docs](https://img.shields.io/badge/docs-argy.cloud-informational)](https://argy.cloud/en/docs/developer-guide)

---

## Installation

### macOS

```bash
# Homebrew — recommended
brew install demkada/tap/argy

# curl
curl -fsSL https://argy.cloud/install | sh
```

### Linux

```bash
curl -fsSL https://argy.cloud/install | sh
```

### Windows

```powershell
# winget — recommended
winget install demkada.ArgyCode

# PowerShell one-liner
irm https://argy.cloud/install.ps1 | iex

# Scoop
scoop bucket add argy https://github.com/demkada/scoop-argy
scoop install argy
```

### npm (all platforms)

```bash
npm install -g @argy-cloud/code
```

---

## Quick start

```bash
# Open the interactive TUI — login is triggered automatically on first launch
argy

# Or authenticate explicitly
argy auth login
```

[Get a free account](https://portal.argy.cloud) — no credit card required.

---

## Key commands

| Command             | Description                            |
| ------------------- | -------------------------------------- |
| `argy`              | Open the interactive TUI               |
| `argy run "prompt"` | Autonomous execution (CI/CD)           |
| `argy auth login`   | Authenticate via Device Flow or PAT    |
| `argy auth status`  | Show auth status and credits           |
| `argy models`       | List available models                  |
| `argy agent list`   | List available agents                  |
| `argy mcp list`     | List MCP servers                       |
| `argy session list` | Browse session history                 |
| `argy stats`        | Usage statistics                       |
| `argy acp`          | Start the ACP server (IDE integration) |
| `argy serve`        | Start local HTTP server (SDK access)   |
| `argy upgrade`      | Upgrade to the latest version          |

---

## Two execution modes

### Interactive TUI

```bash
argy
```

Full terminal UI with chat interface, tool confirmations, session management, slash commands, and sub-agent orchestration. Designed for daily development work with human-in-the-loop confirmation.

### Autonomous mode

```bash
argy run "Write unit tests for src/auth.ts"
argy run --agent code-reviewer --model argy-gateway/claude-sonnet-4-5 "Review PR #42"
argy run --max-turns 10 "Refactor the database layer"

# Stdin support
git diff | argy run "Summarize these changes for a PR description"
cat error.log | argy run "Diagnose this error and suggest a fix"
```

Non-interactive, deterministic, machine-readable. Designed for CI/CD pipelines, cron jobs, and orchestrators.

**Exit codes**: `0` success · `1` failed · `2` stopped (turn limit)

---

## IDE integration — ACP

Argy Code implements the [Agent Client Protocol](https://agentclientprotocol.com/) for editor integrations (VS Code, JetBrains, Zed, etc.).

```bash
argy acp
```

### Zed

Add to `~/.config/zed/settings.json`:

```json
{
  "agent_servers": {
    "Argy": {
      "command": "argy",
      "args": ["acp"]
    }
  }
}
```

---

## Authentication

```bash
argy auth login           # Device Flow (opens browser, no password stored)
argy auth login --pat     # Personal Access Token
argy auth status          # Show status, credits, tenant
argy auth logout          # Remove stored credentials
```

Or set the `ARGY_PAT` environment variable directly.

Credentials are stored in `~/.config/argy/auth.json`.

---

## Configuration

Create `argy.json` or `argy.jsonc` at your project root:

```jsonc
{
  "$schema": "https://argy.cloud/config.json",

  // Custom slash commands
  "command": {
    "security-review": {
      "description": "Full security audit",
      "template": "Run a comprehensive security audit...",
      "agent": "security-auditor",
    },
  },

  // MCP servers
  "mcp": {
    "filesystem": {
      "type": "local",
      "command": ["npx", "@modelcontextprotocol/server-filesystem", "."],
    },
  },

  // Model settings
  "provider": {
    "argy-gateway": {
      "models": {
        "claude-sonnet-4-5": { "temperature": 0.3 },
      },
    },
  },
}
```

### Key environment variables

| Variable               | Description                                       |
| ---------------------- | ------------------------------------------------- |
| `ARGY_PAT`             | Personal Access Token                             |
| `ARGY_ENV`             | Environment: `LOCAL` / `DEV` / `STAGING` / `PROD` |
| `ARGY_SERVER_PASSWORD` | Password for `argy serve`                         |
| `ARGY_AUTO_SHARE`      | Auto-share all sessions                           |

---

## CI/CD examples

```bash
# Multi-step pipeline
argy run "Analyze the codebase and list all API endpoints" && \
argy run -c "Generate OpenAPI documentation for each endpoint" && \
argy run -c "Write integration tests for the 3 most critical endpoints"

# Parallel agents
argy run --agent security-auditor "Audit authentication flows" &
argy run --agent performance-analyst "Profile the database queries" &
wait

# JSON output for programmatic consumption
argy run --format json "Run security scan" | jq '.events[]'

# Exit code usage
argy run "Deploy to staging" && echo "Deployed" || echo "Deployment failed"
```

---

## Built-in agents

| Agent     | Mode     | Description                                     |
| --------- | -------- | ----------------------------------------------- |
| `build`   | primary  | Default. Full tool access with write governance |
| `plan`    | primary  | Research-first, read-only by default            |
| `docgen`  | primary  | Generate PowerPoint and Excel documents         |
| `general` | subagent | General-purpose parallel sub-agent              |
| `explore` | subagent | Fast read-only codebase exploration             |

Custom agents can be defined in `.argy/agent/` as Markdown files with YAML frontmatter.

```bash
argy agent list
argy agent create --description "Expert TypeScript code reviewer"
```

---

## SDK integration

Run a local HTTP server and connect via the TypeScript SDK:

```bash
argy serve --port 4096
```

```typescript
import { ArgyClient } from "@argy-cloud/code-sdk/v2"

const client = new ArgyClient({ baseUrl: "http://localhost:4096" })
const session = await client.sessions.create({ title: "Automated task" })
const stream = await client.messages.stream(session.id, {
  content: "Analyze the codebase and list all TODO comments",
})
for await (const event of stream) {
  console.log(event)
}
```

---

## Links

- [Website](https://argy.cloud/en/argy-code)
- [Documentation](https://argy.cloud/en/docs/developer-guide)
- [Argy Portal](https://portal.argy.cloud) — free account
- [npm package](https://www.npmjs.com/package/@argy-cloud/code)
- [LLM Gateway](https://argy.cloud/en/llm-gateway)
- [Pricing](https://argy.cloud/en/pricing)

---

_Argy Code is a proprietary product. Binaries are distributed freely. Source code is not public._
