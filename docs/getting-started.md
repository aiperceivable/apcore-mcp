# Getting Started

This guide covers how to set up and use **apcore-mcp** to expose your apcore-based modules to AI agents as MCP tools or OpenAI tools.

## 1. Installation

Install **apcore-mcp** alongside your existing apcore project.

=== "Python"

    ```bash
    pip install apcore-mcp
    ```

=== "TypeScript"

    ```bash
    npm install apcore-mcp
    ```

## 2. Zero-Code CLI Usage

If you have an `extensions/` directory containing your apcore modules, you can launch an MCP server immediately without writing any code.

=== "Python"

    ```bash
    # stdio (default for desktop clients like Claude)
    apcore-mcp --extensions-dir ./extensions

    # HTTP with Tool Explorer UI
    apcore-mcp --extensions-dir ./extensions 
               --transport streamable-http 
               --port 8000 
               --explorer 
               --allow-execute
    ```

=== "TypeScript"

    ```bash
    # stdio (default)
    npx apcore-mcp --extensions-dir ./extensions

    # HTTP with Tool Explorer UI
    npx apcore-mcp --extensions-dir ./extensions 
                   --transport streamable-http 
                   --port 8000 
                   --explorer 
                   --allow-execute
    ```

Once running in HTTP mode with `--explorer`, visit `http://127.0.0.1:8000/explorer/` to browse and test your tools.

## 3. Programmatic Usage

Integrate **apcore-mcp** directly into your application.

=== "Python"

    ```python
    from apcore import Registry
    from apcore_mcp import serve, to_openai_tools

    # 1. Setup apcore registry
    registry = Registry(extensions_dir="./extensions")
    registry.discover()

    # 2a. Launch as MCP Server
    await serve(registry, transport="stdio")

    # 2b. Or convert to OpenAI tool definitions
    tools = to_openai_tools(registry)
    # response = openai.chat.completions.create(tools=tools, ...)
    ```

=== "TypeScript"

    ```typescript
    import { Registry } from "apcore-js";
    import { serve, toOpenaiTools } from "apcore-mcp";

    // 1. Setup apcore registry
    const registry = new Registry({ extensionsDir: "./extensions" });
    await registry.discover();

    // 2a. Launch as MCP Server
    await serve(registry, { transport: "stdio" });

    // 2b. Or convert to OpenAI tool definitions
    const tools = toOpenaiTools(registry);
    ```

## 4. MCP Client Configuration

To use your tools in desktop AI applications, add **apcore-mcp** to their configuration files.

### Claude Desktop

Add this to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

=== "Python"

    ```json
    {
      "mcpServers": {
        "apcore": {
          "command": "apcore-mcp",
          "args": ["--extensions-dir", "/absolute/path/to/your/extensions"]
        }
      }
    }
    ```

=== "TypeScript"

    ```json
    {
      "mcpServers": {
        "apcore": {
          "command": "npx",
          "args": ["apcore-mcp", "--extensions-dir", "/absolute/path/to/your/extensions"]
        }
      }
    }
    ```

### Cursor / Claude Code / Windsurf

Add a `.mcp.json` or equivalent in your project root:

```json
{
  "mcpServers": {
    "apcore": {
      "command": "apcore-mcp",
      "args": ["--extensions-dir", "./extensions"]
    }
  }
}
```

## Next Steps

- Explore the [Technical Design](tech-design-apcore-mcp.md) for architecture details.
- See how [Approval Mechanisms](tech-design-apcore-mcp.md#approval-mechanism) work for sensitive tools.
- Learn about [JWT Authentication](tech-design-apcore-mcp.md#security-auth) for remote access.
