# Configuration Reference

This page provides a detailed reference for all configuration options available in **apcore-mcp**, whether you are using the CLI or the programmatic API.

## CLI Arguments

The CLI allows you to launch an MCP server by pointing to an extensions directory.

| Argument | Default | Description |
|---|---|---|
| `--extensions-dir` | *(required)* | Path to apcore extensions directory |
| `--transport` | `stdio` | Transport protocol: `stdio`, `streamable-http`, or `sse` |
| `--host` | `127.0.0.1` | Host for HTTP-based transports |
| `--port` | `8000` | Port for HTTP-based transports (1-65535) |
| `--name` | `apcore-mcp` | MCP server name (appears in client UI) |
| `--version` | package version | MCP server version string |
| `--log-level` | `INFO` | Logging level: `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| `--explorer` | off | Enable the browser-based Tool Explorer UI (HTTP only) |
| `--allow-execute` | off | Allow tool execution from the explorer UI |
| `--jwt-secret` | — | JWT secret key for Bearer token authentication |
| `--jwt-require-auth` | `true` | Require authentication (use `--no-jwt-require-auth` for permissive mode) |

## Programmatic API

### `serve()` Options

The `serve` function launches the MCP server. It accepts a Registry or Executor as the first argument.

=== "Python"

    ```python
    from apcore_mcp import serve

    await serve(
        registry_or_executor,
        transport="stdio",           # "stdio" | "streamable-http" | "sse"
        host="127.0.0.1",
        port=8000,
        name="apcore-mcp",
        explorer=False,              # Enable Explorer UI
        allow_execute=False,         # Enable "Try it" in UI
        validate_inputs=False,       # Validate inputs against JSON Schema
        log_level="INFO",
        authenticator=None,          # JWTAuthenticator instance
    )
    ```

=== "TypeScript"

    ```typescript
    import { serve } from "apcore-mcp";

    await serve(registryOrExecutor, {
      transport: "stdio",            // "stdio" | "streamable-http" | "sse"
      host: "127.0.0.1",
      port: 8000,
      name: "apcore-mcp",
      explorer: false,               // Enable Explorer UI
      allowExecute: false,           // Enable "Try it" in UI
      validateInputs: false,         // Validate inputs against JSON Schema
      logLevel: "INFO",
      authenticator: undefined,      // JWTAuthenticator instance
    });
    ```

### `async_serve()` Options

The `async_serve` function returns an embeddable ASGI application (Starlette) for mounting within a larger server. It accepts the same options as `serve()` except `transport`, `host`, and `port`.

=== "Python"

    ```python
    from apcore_mcp import async_serve

    async with async_serve(
        registry_or_executor,
        name="apcore-mcp",
        explorer=True,
        allow_execute=True,
        authenticator=None,
        approval_handler=None,
        output_formatter=None,       # Callable[[dict], str] or None (default: json.dumps)
    ) as app:
        # Mount `app` in your ASGI server (e.g., uvicorn)
        ...
    ```

=== "TypeScript"

    ```typescript
    import { asyncServe } from "apcore-mcp";

    const { handler, close } = await asyncServe(registryOrExecutor, {
      name: "apcore-mcp",
      explorer: true,
      allowExecute: true,
      authenticator: undefined,
      approvalHandler: undefined,
      outputFormatter: undefined,    // (result: object) => string, or undefined
    });
    // Use `handler` with http.createServer(), call `close()` when done
    ```

### `to_openai_tools()` Options

Converts apcore modules into OpenAI-compatible tool definitions.

=== "Python"

    ```python
    from apcore_mcp import to_openai_tools

    tools = to_openai_tools(
        registry_or_executor,
        embed_annotations=False,    # Append annotation hints to descriptions
        strict=False,               # Enable OpenAI Structured Outputs strict mode
        tags=None,                  # Filter modules by tags (list of strings)
        prefix=None,                # Filter modules by ID prefix
    )
    ```

=== "TypeScript"

    ```typescript
    import { toOpenaiTools } from "apcore-mcp";

    const tools = toOpenaiTools(registryOrExecutor, {
      embedAnnotations: false,      // Append annotation hints to descriptions
      strict: false,                // Enable OpenAI Structured Outputs strict mode
      tags: [],                     // Filter modules by tags
      prefix: "",                   // Filter modules by ID prefix
    });
    ```

## Authentication (JWT)

For HTTP-based transports, you can secure your endpoints using JWT Bearer tokens.

=== "Python"

    ```python
    from apcore_mcp.auth import JWTAuthenticator
    from apcore_mcp import serve

    auth = JWTAuthenticator(key="your-secret-key")
    await serve(registry, transport="streamable-http", authenticator=auth)
    ```

=== "TypeScript"

    ```typescript
    import { serve, JWTAuthenticator } from "apcore-mcp";

    const authenticator = new JWTAuthenticator({
      secret: "your-secret-key",
    });
    await serve(registry, {
      transport: "streamable-http",
      authenticator,
    });
    ```

## Approval Mechanism

You can gate destructive or sensitive tool calls behind user approval using the `--approval` CLI flag or the `approval_handler` parameter.

**CLI modes:**

| Mode | Behavior |
|------|----------|
| `elicit` | Prompts user via MCP elicitation (default for interactive clients) |
| `auto-approve` | Approves all requests automatically |
| `always-deny` | Denies all approval requests |
| `off` | Disables approval checks entirely |

=== "Python"

    ```python
    from apcore_mcp import serve
    from apcore_mcp.adapters import ElicitationApprovalHandler

    handler = ElicitationApprovalHandler()
    await serve(registry, approval_handler=handler)
    ```

=== "TypeScript"

    ```typescript
    import { serve, ElicitationApprovalHandler } from "apcore-mcp";

    const handler = new ElicitationApprovalHandler();
    await serve(registry, { approvalHandler: handler });
    ```

## Output Formatting

By default, tool results are serialized as JSON. You can pass a custom `output_formatter` to format results differently (e.g., Markdown).

=== "Python"

    ```python
    from apcore_mcp import serve

    # Custom formatter example
    await serve(registry, output_formatter=lambda result: json.dumps(result, indent=2))

    # Or use apcore-toolkit for Markdown (optional dependency)
    from apcore_toolkit import to_markdown
    await serve(registry, output_formatter=to_markdown)
    ```

=== "TypeScript"

    ```typescript
    import { serve } from "apcore-mcp";

    await serve(registry, {
      outputFormatter: (result) => JSON.stringify(result, null, 2),
    });
    ```

## Tool Explorer

The Tool Explorer is a built-in UI that allows you to interactively test your MCP tools. It is only available when using `streamable-http` or `sse` transports.

- **URL:** `http://<host>:<port>/explorer/`
- **Execution:** Set `allow_execute=True` (Python) or `allowExecute: true` (TypeScript) to enable the "Call Tool" button.
- **Auth:** If JWT is enabled, the UI provides a field to enter your Bearer token.
