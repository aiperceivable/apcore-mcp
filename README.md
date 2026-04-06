<div align="center">
  <img src="./apcore-mcp-logo.svg" alt="apcore-mcp logo" width="200"/>
</div>

# apcore-mcp

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Python Version](https://img.shields.io/badge/python-3.11%2B-blue)](https://github.com/aiperceivable/apcore-mcp-python)
[![TypeScript Version](https://img.shields.io/badge/TypeScript-Node_18%2B-blue)](https://github.com/aiperceivable/apcore-mcp-typescript)
[![Rust Version](https://img.shields.io/badge/Rust-1.75%2B-blue)](https://github.com/aiperceivable/apcore-mcp-rust)

> **Build once, invoke by Code or AI.**

**apcore-mcp** turns any [apcore](https://github.com/aiperceivable/apcore)-based project into an [MCP Server](https://modelcontextprotocol.io/) and [OpenAI tool](https://platform.openai.com/docs/guides/function-calling) provider — with **zero code changes** to your existing project.

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
    *Requires Python 3.11+ and apcore 0.17.1+.*

=== "📘 TypeScript"

    ```bash
    npm install apcore-mcp
    ```
    *Requires Node.js 18+ and apcore 0.17.1+.*

=== "🦀 Rust"

    ```bash
    cargo add apcore-mcp
    ```
    *Requires Rust 1.75+ and apcore 0.17.1+.*

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

=== "🦀 Rust"

    ```rust
    use apcore_mcp::APCoreMCP;

    // Launch MCP server over stdio
    let mcp = APCoreMCP::builder()
        .backend("./extensions")
        .build()?;
    mcp.serve()?;
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

=== "🦀 Rust"

    ```rust
    use apcore_mcp::APCoreMCP;

    let mcp = APCoreMCP::builder()
        .backend("./extensions")
        .build()?;
    let tools = mcp.to_openai_tools(false, true)?;
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

## Features & Specifications

The project is architected as a set of modular features, each with its own specification to ensure consistency across languages:

- **[Feature Overview](docs/features/overview.md)** — Implementation roadmap & dependencies
- [Schema Converter](docs/features/schema-converter.md) — Reference resolution for AI protocols
- [Annotation Mapper](docs/features/annotation-mapper.md) — Behavioral hint translation
- [Execution Router](docs/features/execution-router.md) — Dispatcher for the 11-step pipeline
- [Error Mapper](docs/features/error-mapper.md) — Protocol-compliant error feedback
- [MCP Server Factory](docs/features/mcp-server-factory.md) — Core server builder
- [OpenAI Converter](docs/features/openai-converter.md) — Tool exporter for OpenAI
- [Transport Manager](docs/features/transport-manager.md) — Stdio/HTTP connectivity
- [Registry Listener](docs/features/registry-listener.md) — Hot-reloading capabilities
- [JWT Authenticator](docs/features/jwt-authenticator.md) — Bearer token security
- [Approval Handler](docs/features/approval-handler.md) — Human-in-the-loop elicitation
- [Explorer UI](docs/features/explorer-ui.md) — Interactive dev dashboard

---

## Documentation

- **[Full Documentation Site](https://aiperceivable.github.io/apcore-mcp/)**
- **[Getting Started Guide](docs/getting-started.md)** — Installation and basic setup
- [Specifications (PRD, TDD, SRS)](docs/spec/)

---

## Implementations

| Language | Repository | Package | Status |
|----------|-----------|---------|--------|
| Python | [apcore-mcp-python](https://github.com/aiperceivable/apcore-mcp-python) | `pip install apcore-mcp` | ✅ v0.10.x |
| TypeScript | [apcore-mcp-typescript](https://github.com/aiperceivable/apcore-mcp-typescript) | `npm install apcore-mcp` | ✅ v0.10.x |
| Rust | [apcore-mcp-rust](https://github.com/aiperceivable/apcore-mcp-rust) | `cargo add apcore-mcp` | ✅ v0.10.x |
| Go | apcore-mcp-go | — | Planned |

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for details.
