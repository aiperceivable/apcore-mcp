# Feature Overview

> Auto-generated index of feature specs for apcore-mcp.
> Updated: 2026-04-06

## Features

| Feature | Description | Dependencies | Status |
|---------|-------------|--------------|--------|
| [Schema Converter](./schema-converter.md) | Resolves JSON Schema references for MCP/OpenAI. | none | draft |
| [Annotation Mapper](./annotation-mapper.md) | Maps apcore metadata to protocol behavioral hints. | none | draft |
| [Execution Router](./execution-router.md) | Dispatches tool calls to the apcore Executor pipeline. | error-mapper | draft |
| [Error Mapper](./error-mapper.md) | Translates exceptions to protocol-compliant errors. | none | draft |
| [MCP Server Factory](./mcp-server-factory.md) | Builds the low-level MCP server instance. | schema-converter, annotation-mapper, execution-router | draft |
| [OpenAI Converter](./openai-converter.md) | Exports modules as OpenAI-compatible tool definitions. | schema-converter, annotation-mapper | draft |
| [Transport Manager](./transport-manager.md) | Manages stdio and network communication layers. | mcp-server-factory | draft |
| [Registry Listener](./registry-listener.md) | Enables hot-reloading of tools on registry changes. | mcp-server-factory | draft |
| [JWT Authenticator](./jwt-authenticator.md) | Secures HTTP transports using bearer tokens. | none | draft |
| [Approval Handler](./approval-handler.md) | Implements human-in-the-loop confirmation via MCP. | execution-router | draft |
| [Explorer UI](./explorer-ui.md) | Web dashboard for inspecting and testing tools. | mcp-server-factory, execution-router | draft |

## Execution Order

The implementation should follow this sequence to ensure core logic is stable before layering on transports and UI.

1. **Schema Converter** — Foundational for all protocol-compliant tool definitions.
2. **Annotation Mapper** — Required for both MCP and OpenAI adapters.
3. **Error Mapper** — Essential for clean feedback in both interfaces.
4. **Execution Router** — The bridge to the apcore Executor; depends on error-mapper.
5. **OpenAI Converter** — High-value, zero-dependency feature for immediate utility.
6. **MCP Server Factory** — Combines foundational modules into the core MCP server logic.
7. **Transport Manager** — Enables connectivity (stdio, HTTP) for the server.
8. **Registry Listener** — Adds dynamic capabilities once the base server is stable.
9. **Approval Handler** — Enhances safety for the operational server.
10. **JWT Authenticator** — Secures the server for network deployments.
11. **Explorer UI** — Provides the final development and debugging interface.
