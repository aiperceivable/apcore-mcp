# Annotation Mapper

> Feature spec for code-forge implementation planning.
> Source: extracted from apcore-mcp/docs/tech-design-apcore-mcp.md
> Created: 2026-04-06

## Purpose

The Annotation Mapper bridges apcore's behavioral annotations (e.g., `destructive`, `readonly`, `requires_approval`) into the specific metadata formats expected by the Model Context Protocol (MCP) and OpenAI's tool-use API. It provides critical safety hints to AI agents about how to handle tools.

## Scope

**Included:**
- Mapping `ModuleAnnotations` to MCP `ToolAnnotations` hints (`destructiveHint`, `readOnlyHint`, etc.).
- Generation of a parseable description suffix for OpenAI tool descriptions (since OpenAI tools lack native annotation support).
- Identification of high-consequence operations (e.g., those requiring human-in-the-loop approval).
- Inclusion of `streaming` status in annotations for agent awareness.

**Excluded:**
- Enforcement of these behavioral rules (the `Executor` or `ApprovalHandler` handles enforcement).
- Translation of non-standard, user-defined annotations.

## Core Responsibilities

1. **MCP Mapping** — Maps apcore's boolean annotation fields to their MCP `ToolAnnotations` equivalents.
2. **OpenAI Description Embedding** — Appends structured, machine-readable suffixes to tool descriptions when using the OpenAI adapter.
3. **Safety Warning Generation** — Identifies non-default, high-risk annotations and provides formatted warnings for display in agent interfaces.

## Interfaces

### Inputs
- **ModuleAnnotations** (apcore SDK) — The source metadata from the apcore `ModuleDescriptor`.

### Outputs
- **ToolAnnotations** (MCP SDK) — The protocol-specific hint object for the tool.
- **Annotation Suffix String** (OpenAI Converter) — A formatted string added to the tool description.

### Dependencies
- **apcore-python SDK** — Provides the `ModuleAnnotations` dataclass.
- **MCP Python SDK** — Provides the `ToolAnnotations` type definition.

## Data Flow

```mermaid
graph LR
    A[ModuleAnnotations] --> B[Map to MCP Hints]
    A --> C[Build Description Suffix]
    B --> D[MCP Tool Output]
    C --> E[OpenAI Tool Description]
```

## Key Behaviors

### Description Suffix Generation
For OpenAI tool descriptions, a suffix is generated only for non-default values:
1. Safety warnings for `destructive` or `requires_approval` are prioritized.
2. A structured block `[Annotations: readonly=true, idempotent=true]` is appended with non-default fields.
3. Default values (`readonly=false`, `destructive=false`, `idempotent=false`, `requires_approval=false`, `open_world=true`) are omitted to save tokens.

### Approval Check
The mapper identifies modules that require a human-in-the-loop approval step based on the `requires_approval` flag, which can then be used to trigger MCP elicitation or other confirmation flows.

## Constraints

- **Case Consistency**: Output labels in suffixes must follow a consistent, parseable naming convention.
- **Protocol Defaults**: When apcore annotations are `None`, the mapper must revert to the safe defaults specified by the protocol (e.g., `read_only_hint=False`, `open_world_hint=True`).

## Error Handling

- **Missing Annotations**: Handles `None` input gracefully by returning protocol-standard default objects.
- **Schema Drift**: Ignores unknown annotation fields from legacy versions without crashing.

## Notes

- This component is vital for safe AI-agent interactions with real-world tools. Without these hints, an agent might inadvertently perform destructive operations without human oversight.
