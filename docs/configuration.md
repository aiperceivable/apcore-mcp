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

## Tool Explorer

The Tool Explorer is a built-in UI that allows you to interactively test your MCP tools. It is only available when using `streamable-http` or `sse` transports.

- **URL:** `http://<host>:<port>/explorer/`
- **Execution:** Set `allow_execute=True` (Python) or `allowExecute: true` (TypeScript) to enable the "Call Tool" button.
- **Auth:** If JWT is enabled, the UI provides a field to enter your Bearer token.
