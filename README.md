<div align="center">
  <img src="./apcore-mcp-logo.svg" alt="apcore-mcp logo" width="200"/>
</div>

# apcore-mcp

> **Build once, invoke by Code or AI.**

Automatic MCP Server & OpenAI Tools Bridge for apcore.

**apcore-mcp** turns any [apcore](https://github.com/aipartnerup/apcore)-based project into an MCP Server and OpenAI tool provider — with **zero code changes** to your existing project.

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

## Design Philosophy

- **Zero intrusion** — your apcore project needs no code changes, no imports, no dependencies on apcore-mcp
- **Zero configuration** — point to an extensions directory, everything is auto-discovered
- **Pure adapter** — apcore-mcp reads from the apcore Registry; it never modifies your modules
- **Works with any `xxx-apcore` project** — if it uses the apcore Module Registry, apcore-mcp can serve it

## Features

- **Auto-discovery** — all modules in the extensions directory are found and exposed automatically
- **Three transports** — stdio (default, for desktop clients), Streamable HTTP, and SSE
- **Embeddable server** — `async_serve()` / `asyncServe()` returns an ASGI/HTTP handler for mounting in larger applications
- **JWT authentication** — optional Bearer token auth for HTTP transports with permissive mode and path exemptions
- **Approval mechanism** — runtime approval via MCP elicitation, auto-approve, or always-deny handlers
- **AI guidance** — error responses include `retryable`, `ai_guidance`, `suggestion` fields for agent consumption
- **AI intent metadata** — tool descriptions enriched with `x-when-to-use`, `x-when-not-to-use`, `x-common-mistakes` from module metadata
- **Streaming bridge** — progress notifications and deep merge chunk accumulation for streaming tool execution
- **Annotation mapping** — apcore annotations (readonly, destructive, idempotent, cacheable, paginated, streaming) map to MCP ToolAnnotations
- **Schema conversion** — JSON Schema `$ref`/`$defs` inlining, strict mode for OpenAI Structured Outputs
- **Error sanitization** — ACL errors and internal errors are sanitized; stack traces are never leaked
- **Dynamic registration** — modules registered/unregistered at runtime are reflected immediately
- **Dual output** — same registry powers both MCP Server and OpenAI tool definitions
- **Output formatting** — customizable tool output (JSON default, Markdown via apcore-toolkit, or custom formatter)
- **Extension helpers** — modules can call `report_progress()` and `elicit()` during execution
- **Tool Explorer** — browser-based UI for browsing schemas and testing tools interactively (like Swagger UI for MCP)

## How It Works

### Mapping: apcore to MCP

| apcore | MCP |
|--------|-----|
| `module_id` | Tool name |
| `description` | Tool description |
| `input_schema` | `inputSchema` |
| `annotations.readonly` | `ToolAnnotations.readOnlyHint` |
| `annotations.destructive` | `ToolAnnotations.destructiveHint` |
| `annotations.idempotent` | `ToolAnnotations.idempotentHint` |
| `annotations.open_world` | `ToolAnnotations.openWorldHint` |
| `annotations.cacheable` | `ToolAnnotations._meta.cacheable` |
| `annotations.cache_ttl` | `ToolAnnotations._meta.cacheTtl` |
| `annotations.paginated` | `ToolAnnotations._meta.paginated` |
| `metadata.x-preconditions` | `ToolAnnotations._meta.preconditions` |
| `metadata.x-cost-per-call` | `ToolAnnotations._meta.costPerCall` |

### Mapping: apcore to OpenAI Tools

| apcore | OpenAI |
|--------|--------|
| `module_id` (`image.resize`) | `name` (`image-resize`) |
| `description` | `description` |
| `input_schema` | `parameters` |

Module IDs with dots are normalized to dashes for OpenAI compatibility (bijective mapping).

### Architecture

```
Your apcore project (unchanged)
    │
    │  extensions directory
    ▼
apcore-mcp (separate process / library call)
    │
    ├── MCP Server path
    │     SchemaConverter + AnnotationMapper
    │       → MCPServerFactory → ExecutionRouter → TransportManager
    │
    └── OpenAI Tools path
          SchemaConverter + AnnotationMapper + IDNormalizer
            → OpenAIConverter → tool definitions
```

## Implementations

| Language | Repository | Package | Status |
|----------|-----------|---------|--------|
| Python | [apcore-mcp-python](https://github.com/aipartnerup/apcore-mcp-python) | `pip install apcore-mcp` |  ✅  v0.9.0 |
| TypeScript | [apcore-mcp-typescript](https://github.com/aipartnerup/apcore-mcp-typescript) | `npm install apcore-mcp` |  ✅  v0.9.0 |
| Go | apcore-mcp-go | — | Planned |

## Documentation

For full documentation, including Quick Start guides for Python and TypeScript, visit:
**[https://aipartnerup.github.io/apcore-mcp/](https://aipartnerup.github.io/apcore-mcp/)**

## Specification Documents

- [Product Requirements (PRD)](docs/prd-apcore-mcp.md)
- [Software Requirements (SRS)](docs/srs-apcore-mcp.md)
- [Technical Design](docs/tech-design-apcore-mcp.md)
- [Test Plan](docs/test-plan-apcore-mcp.md)

## License

Apache-2.0
