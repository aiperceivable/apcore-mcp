# Error Mapper

> Feature spec for code-forge implementation planning.
> Source: extracted from apcore-mcp/docs/tech-design-apcore-mcp.md
> Created: 2026-04-06

## Purpose

The Error Mapper is responsible for translating apcore's rich error hierarchy (including schema validation errors, ACL denials, and timeouts) into structured, protocol-compliant error responses for MCP clients and OpenAI's API. It ensures that AI agents receive actionable, machine-readable feedback without leaking sensitive system information.

## Scope

**Included:**
- Mapping all `ModuleError` subclasses to `CallToolResult` error responses.
- Formatting field-level schema validation details for display in the client.
- Redaction of sensitive details (e.g., specific `caller_id` or `trace_id` in ACL denials) for protocol-facing output.
- Attachment of AI guidance metadata (e.g., `retryable`, `aiGuidance`) to error responses.
- Sanitation of unexpected non-module exceptions into generic internal error messages.

**Excluded:**
- Implementation of the `Executor` (where errors originate).
- Protocol-level response wrapping (handled by the `ExecutionRouter`).

## Core Responsibilities

1. **Category Mapping** — Maps specific apcore exceptions (e.g., `ModuleNotFoundError`, `ModuleTimeoutError`) to corresponding protocol-level error codes and formatted messages.
2. **Detail Extraction** — Extracts structured information (e.g., specific fields that failed validation) and converts them into a human-readable, bulleted string.
3. **AI Guidance Injection** — Attaches metadata to the error response to help agents understand whether to retry, what the user should fix, or what the model should adjust.
4. **Information Security** — Replaces internal exceptions and stack traces with sanitized, safe messages (e.g., "Internal error occurred") for external consumption.

## Interfaces

### Inputs
- **Exception Instance** (Execution Router) — The error raised during the execution pipeline.

### Outputs
- **CallToolResult** (MCP SDK) — A protocol result object with `isError=true` and formatted text content.

### Dependencies
- **apcore-python SDK** — Provides the `ModuleError` base class and its specific subclasses.
- **MCP Python SDK** — Provides the `CallToolResult` and `TextContent` types.

## Data Flow

```mermaid
graph LR
    A[apcore Exception] --> B[Identify Error Type]
    B --> C[Extract Field Details]
    C --> D[Apply AI Guidance]
    D --> E[Sanitize Message]
    E --> F[CallToolResult Output]
```

## Key Behaviors

### Structured Validation Feedback
For `SchemaValidationError`, the mapper generates a multi-line, bulleted summary showing each failed field, its error code, and its human-readable message.

### AI Guidance Metadata
If an error object carries guidance attributes (`retryable`, `ai_guidance`, `user_fixable`, `suggestion`), the mapper extracts these and attaches them to the response, often as a JSON appendix in the error text content to ensure visibility for LLMs.

### Security Sanitization
Only errors derived from `ModuleError` (which are designed to be user-facing) are allowed to pass their messages to the client. All other exceptions result in a generic "Internal error occurred" message, with the original traceback only visible in server-side logs.

## Constraints

- **Consistency**: The same error code from apcore must always result in the same message format and wire key convention.
- **Protocol Requirements**: Every mapped error result must explicitly set `isError=True`.
- **Wire Case**: Use camelCase for keys in the JSON guidance appendix to align with MCP/OpenAI conventions.

## Error Handling

- **Unrecognized Error**: If the mapper receives a `ModuleError` it doesn't specifically know, it returns a generic "Module error: {code}" response using the original code.
- **Extreme Failure**: If the mapping logic itself fails, it reverts to a hardcoded "Internal error occurred" safety response.

## Notes

- This component is critical for the "auto-retry" and "intelligent fix" behaviors in AI agents.
- It leverages apcore's built-in error code registry to stay synchronized with the core framework.
