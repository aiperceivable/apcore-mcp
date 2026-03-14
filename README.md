<div align="center">
  <img src="./apcore-mcp-logo.svg" alt="apcore-mcp logo" width="200"/>
</div>

# apcore-mcp

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Python Version](https://img.shields.io/badge/python-3.11%2B-blue)](https://github.com/aipartnerup/apcore-mcp-python)
[![TypeScript Version](https://img.shields.io/badge/TypeScript-Node_18%2B-blue)](https://github.com/aipartnerup/apcore-mcp-typescript)

> **Build once, invoke by Code or AI.**

**apcore-mcp** turns any [apcore](https://github.com/aipartnerup/apcore)-based project into an [MCP Server](https://modelcontextprotocol.io/) and [OpenAI tool](https://platform.openai.com/docs/guides/function-calling) provider — with **zero code changes** to your existing project.

```
┌──────────────────┐
│  django-apcore   │  ← your existing apcore project (unchanged)
│  nestjs-apcore   │
│  tiptap-apcore   │
│  ...             │
└────────┬─────────┘
         │  extensions directory
         ▼
┌──────────────────┐
│    apcore-mcp    │  ← just install & point to extensions dir
└───┬──────────┬───┘
    │          │
    ▼          ▼
  MCP       OpenAI
 Server      Tools
```

---

## Installation

=== "🐍 Python"

    ```bash
    pip install apcore-mcp
    ```
    *Requires Python 3.11+ and apcore 0.13.0+.*

=== "📘 TypeScript"

    ```bash
    npm install apcore-mcp
    ```
    *Requires Node.js 18+ and apcore 0.13.0+.*

---

## Quick Start

### 1. Serve your modules via MCP

=== "🐍 Python"

    ```python
    from apcore_mcp import APCoreMCP

    mcp = APCoreMCP("./extensions")

    # Launch as MCP Server over stdio
    mcp.serve()

    # Or with HTTP + Explorer UI
    mcp.serve(transport="streamable-http", port=8000, explorer=True)
    ```

=== "📘 TypeScript"

    ```typescript
    import { serve } from "apcore-mcp";

    // Launch MCP server over stdio
    await serve("./extensions");

    // Launch over Streamable HTTP with Explorer UI
    await serve("./extensions", {
      transport: "streamable-http",
      port: 8000,
      explorer: true,
    });
    ```

=== "💻 CLI"

    ```bash
    # stdio (default)
    apcore-mcp --extensions-dir ./extensions

    # Streamable HTTP with Explorer UI
    apcore-mcp --extensions-dir ./extensions --transport streamable-http --port 8000 --explorer
    ```

### 2. Export as OpenAI Tools

=== "🐍 Python"

    ```python
    from apcore_mcp import APCoreMCP

    mcp = APCoreMCP("./extensions")
    tools = mcp.to_openai_tools()
    ```

=== "📘 TypeScript"

    ```typescript
    import { toOpenaiTools } from "apcore-mcp";

    const tools = toOpenaiTools("./extensions");
    ```

---

## Key Features

- **🚀 Zero Intrusion**: Your apcore project needs no code changes, no imports, and no extra dependencies.
- **🔍 Auto-discovery**: Point to an extensions directory, and everything is automatically discovered and exposed.
- **🌐 Triple Transport**: Supports `stdio` (for local LLMs), `Streamable HTTP`, and `SSE`.
- **🛠️ Tool Explorer**: Browser-based UI to browse schemas and test tools interactively (like Swagger UI for MCP).
- **🛡️ Security**: Built-in JWT authentication, PEM key support, and runtime approval elicitation.
- **🤖 AI Optimized**: Enriched metadata (`x-when-to-use`), error sanitization with AI guidance, and strict mode for OpenAI.
- **🔄 Dynamic**: Reflects module registrations/unregistrations at runtime without restarting.

---

## Architecture

apcore-mcp acts as a protocol-specific adapter on top of the apcore Registry, mapping its metadata to MCP and OpenAI standards:

| apcore Concept | MCP Mapping | OpenAI Mapping |
|----------------|-------------|----------------|
| `module_id` | Tool name | `name` (dash-normalized) |
| `description` | Tool description | `description` |
| `input_schema` | `inputSchema` | `parameters` |
| `annotations` | `ToolAnnotations` hints | Description suffixes (optional) |
| `metadata` | `_meta` fields | — |

---

## Documentation

- **[Full Documentation Site](https://aipartnerup.github.io/apcore-mcp/)**
- **[Getting Started Guide](docs/getting-started.md)** — Installation and basic setup
- [Feature Specs Overview](docs/features/overview.md) — All feature specifications
- [Specifications (PRD, TDD, SRS)](docs/spec/)

---

## Implementations

| Language | Repository | Package | Status |
|----------|-----------|---------|--------|
| Python | [apcore-mcp-python](https://github.com/aipartnerup/apcore-mcp-python) | `pip install apcore-mcp` | ✅ v0.10.x |
| TypeScript | [apcore-mcp-typescript](https://github.com/aipartnerup/apcore-mcp-typescript) | `npm install apcore-mcp` | ✅ v0.10.x |
| Go | apcore-mcp-go | — | Planned |

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for details.
