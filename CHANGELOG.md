# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.13.0] - 2026-04-06

### Added

- **6 new features (F-036 through F-041)** fully leveraging apcore 0.17.1:
  - **Pipeline Strategy Selection** (F-036, P1) — `serve(strategy="standard"|"internal"|"testing"|"performance"|"minimal")` parameter, CLI `--strategy` flag, and Config Bus `mcp.pipeline.strategy`.
  - **Pipeline Observability** (F-037, P2) — `serve(trace=True)` enables `call_async_with_trace()`; `PipelineTrace` data in `_meta.trace` response, Explorer, and MetricsCollector.
  - **Tool Output Redaction** (F-038, P1) — `redact_sensitive(output, output_schema)` applied by default before serialization. Fields with `x-sensitive` or `_secret_*` keys replaced with `"***REDACTED***"`.
  - **Tool Preflight Validation** (F-039, P2) — `ExecutionRouter.validate_tool()` and Explorer `POST /validate` endpoint for dry-run via `Executor.validate()`.
  - **YAML Pipeline Configuration** (F-040, P2) — Config Bus `mcp.pipeline` section for declarative pipeline customization via `build_strategy_from_config()`.
  - **Annotation Metadata Passthrough** (F-041, P2) — `ModuleAnnotations.extra` keys prefixed with `mcp_` flow to tool descriptions and Explorer.
- **4 new error mappings** — `ConfigEnvMapConflictError`, `PipelineAbortError`, `StepNotFoundError`, `VersionIncompatibleError`.
- **RegistryListener wired to `serve(dynamic=True)`** — dynamic tool registration now operational.

### Changed

- **Dependency bump**: requires `apcore >= 0.17.1` (was `>= 0.15.1`) for Pipeline v2 delegation, step metadata, YAML pipeline configuration, `build_minimal_strategy()`, `requires`/`provides` on BaseStep, and sensitive field redaction.
- **Pipeline v2 alignment** — 11-step pipeline with `call_chain_guard` (renamed from `safety_check`), middleware before input validation.
- Updated PRD to v1.7 (41 features), SRS to v1.8 (127 requirements), Tech Design to v1.7, Test Plan to v1.6.
- Feature count: P0=9, P1=11, P2=21, Total=41.

---

## [0.12.0] - 2026-03-31

### Added

- **Config Bus namespace registration** (F-033) — apcore-mcp registers an `mcp` namespace with the apcore Config Bus (`Config.register_namespace("mcp", ...)`) using `APCORE_MCP` as the env prefix. MCP-specific configuration (transport, host, port, auth, explorer) can now be managed in a unified `apcore.yaml` file. The adapter reads logging defaults from the `observability` namespace.
- **Error Formatter Registry integration** (F-034) — apcore-mcp registers an MCP-specific `ErrorFormatter` with apcore's Error Formatter Registry (§8.8), formalizing camelCase wire keys and MCP error code sanitization into the shared protocol.
- **Dot-namespaced event type constants** (F-035) — Added `APCORE_EVENTS` constants (TypeScript) with canonical event type names from apcore 0.15.0 (`apcore.module.toggled`, `apcore.config.updated`, `apcore.module.reloaded`, `apcore.health.recovered`). No existing event subscriptions were changed — the `RegistryListener` uses callback-based `registry.on("register")` which is unaffected.
- **6 new error code mappings** — `CONFIG_NAMESPACE_DUPLICATE`, `CONFIG_NAMESPACE_RESERVED`, `CONFIG_ENV_PREFIX_CONFLICT`, `CONFIG_MOUNT_ERROR`, `CONFIG_BIND_ERROR`, `ERROR_FORMATTER_DUPLICATE` added to ErrorMapper.

### Changed

- Dependency bump: requires `apcore >= 0.15.1` (was `>= 0.13.0`) for Config Bus (§9.4), Error Formatter Registry (§8.8), simplified env prefix convention, and dot-namespaced event types (§9.16).
- Updated PRD to v1.5 (35 features, was 32), SRS to v1.6, Tech Design to v1.5.
- Feature count: P2 increased from 15 to 18 (added F-033, F-034, F-035).

---

## [0.11.0] - 2026-03-23

### Added

- **Display overlay in `build_tool()`** (§5.13) — MCP tool name, description, and guidance now sourced from `metadata["display"]["mcp"]` when present.
  - Tool name: `metadata["display"]["mcp"]["alias"]` (pre-sanitized by `DisplayResolver`, already `[a-zA-Z_][a-zA-Z0-9_-]*` and ≤ 64 chars).
  - Tool description: `metadata["display"]["mcp"]["description"]`, with `guidance` appended as `\n\nGuidance: <text>` when set.
  - Falls back to raw `descriptor.module_id` / `descriptor.description` when no display overlay is present.
- Updated `tech-design-apcore-mcp.md` — `build_tool()` mapping updated to reflect display overlay source for tool name and description.

### Changed

- Dependency bump: requires `apcore-toolkit >= 0.4.0` for `DisplayResolver`.

### Tests

- `TestBuildToolDisplayOverlay` (6 tests): MCP alias used as tool name, MCP description used, guidance appended to description, surface-specific override wins over default, fallback to scanner values when no overlay.

---

## [0.10.1] - 2026-03-22

### Changed
- Rebrand: aipartnerup → aiperceivable

## [0.10.0] - 2026-03-14

### Added
- `async_serve()` / `asyncServe()` function specification for embedding MCP in larger ASGI/HTTP applications
- `ExecutionCancelledError` mapping with `retryable=True` AI guidance
- `output_formatter` parameter for customizable tool output (e.g., Markdown via apcore-toolkit)
- Deep merge streaming accumulation with depth limit (32) for chunked responses
- New annotation fields in ModuleAnnotations: `streaming`, `cacheable`, `paginated`, `cache_ttl`, `cache_key_fields`, `pagination_style`
- FR-SERVER-012: async_serve() embeddable ASGI application
- FR-ERROR-012: ExecutionCancelledError mapping

### Changed
- Updated Python and TypeScript implementation versions to v0.10.0
- Default `output_formatter` changed to `None` (raw JSON) in both implementations
- Dependency bump to `apcore>=0.13.0` / `apcore-js>=0.13.0` for new annotation fields (`cacheable`, `paginated`)
- Annotation description suffix now includes `cacheable` and `paginated` when set
- Updated SRS to v1.5 with full `serve()` signature including all parameters added since v0.5.0 (explorer, auth, approval, metrics, output_formatter)
- Bumped minimum Python version from >= 3.10 to >= 3.11 (aligns with apcore-python pyproject.toml)
- Bumped minimum apcore dependency from >= 0.2.0 to >= 0.13.0 (required for new annotation fields and ExecutionCancelledError)
- Requirement count increased from 104 to 106 (86 FRs + 20 NFRs)

### Removed
- `apcore-toolkit` is no longer a required dependency in the Python implementation

## [0.9.0] - 2026-03-06

### Changed
- Replaced the anemone logo in `apcore-mcp-logo.svg` with a new jellyfish mascot; moved the original anemone design to `apcore-mcp-image.svg`

## [0.8.0] - 2026-03-02

### Added
- Approval system feature specification (F-028): runtime approval via `ElicitationApprovalHandler`, `--approval` CLI flag with modes `elicit`, `auto-approve`, `always-deny`, `off`
- Approval error codes: `APPROVAL_DENIED`, `APPROVAL_TIMEOUT`, `APPROVAL_PENDING`
- Enhanced error responses with AI guidance fields (`retryable`, `ai_guidance`/`aiGuidance`, `user_fixable`/`userFixable`, `suggestion`)
- AI intent metadata in tool descriptions (`x-when-to-use`, `x-when-not-to-use`, `x-common-mistakes`, `x-workflow-hints`)
- Streaming annotation in description suffixes
- Comprehensive developer documentation site using MkDocs
- Unified "Getting Started" guide and "Configuration Reference" for Python and TypeScript

### Changed
- Cross-language naming convention documented: Python uses snake_case (`ai_guidance`), TypeScript uses camelCase (`aiGuidance`) for AI guidance fields

## [0.7.0] - 2026-02-28

### Added
- JWT Authentication feature specification (F-027) — key file support, configurable strictness, path exemptions, audit logging
- `Authenticator` protocol for pluggable authentication backends
- `JWTAuthenticator` with `ClaimMapping` for JWT Bearer token validation
- `AuthMiddleware` ASGI middleware with `ContextVar` bridge for Identity injection
- CLI flags for JWT: `--jwt-secret`, `--jwt-algorithm`, `--jwt-audience`, `--jwt-issuer`

### Changed
- Updated security threat model with JWT-specific entries
- Updated `serve()` API with `authenticator` parameter

## [0.6.0] - 2026-02-25

### Added
- Streaming bridge specification — progress notifications, chunk accumulation, fallback to non-streaming
- Elicitation support specification — user input requests during module execution
- Dynamic tool registration specification

## [0.5.1] - 2026-02-25

### Changed
- Renamed "Inspector" to "Explorer" across the entire specification and documentation (PRD, SRS, design, and test plans).

## [0.5.0] - 2026-02-24

### Added
- MCP Tool Explorer specification (F-026) — browser-based UI for inspecting and testing tools
- Examples specification — cross-language standard for demo modules

## [0.4.0] - 2026-02-23

### Added
- Resource handlers specification for serving documentation via MCP
- Prometheus metrics specification (`/metrics` endpoint)
- CI/CD workflow specification for GitHub Actions

## [0.3.0] - 2026-02-22

### Added
- Input validation specification (F-010) — pre-execution validation via `Executor.validate()`
- `Context` and trace ID passback specification for request tracing
- Updated response key from `output` to `result` in SRS and test plan

## [0.2.0] - 2026-02-20

### Added
- MCPServer framework integration specification — non-blocking server wrapper and lifecycle hooks
- Health endpoint specification for HTTP-based transports
- Project logo (`apcore-mcp-logo.svg`) and MkDocs configuration (`mkdocs.yml`)
- Expanded PRD to cover 25 initial features with detailed metrics

## [0.1.0] - 2026-02-15

### Added
- Initial project setup for apcore-mcp
- Core concept: automatic bridging of apcore modules to MCP Server and OpenAI Tools
- Initial Product Requirements Document (PRD), SRS, Technical Design, and Test Plan
