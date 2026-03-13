# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.10.0] - 2026-03-13

### Added
- `async_serve()` / `asyncServe()` function specification for embedding MCP in larger ASGI/HTTP applications
- `ExecutionCancelledError` mapping with `retryable=True` AI guidance
- `output_formatter` parameter for customizable tool output (e.g., Markdown via apcore-toolkit)
- Deep merge streaming accumulation with depth limit (32) for chunked responses
- New annotation fields in ModuleAnnotations: `streaming`, `cacheable`, `paginated`, `cache_ttl`, `cache_key_fields`, `pagination_style`
- FR-SERVER-012: async_serve() embeddable ASGI application
- FR-ERROR-012: ExecutionCancelledError mapping

### Changed
- Updated SRS to v1.5 with full `serve()` signature including all parameters added since v0.5.0 (explorer, auth, approval, metrics, output_formatter)
- Bumped minimum Python version from >= 3.10 to >= 3.11 (aligns with apcore-python pyproject.toml)
- Bumped minimum apcore dependency from >= 0.2.0 to >= 0.13.0 (required for new annotation fields and ExecutionCancelledError)
- Updated implementation versions in README: Python and TypeScript both at v0.9.0
- Requirement count increased from 104 to 106 (86 FRs + 20 NFRs)

## [0.9.0] - 2026-03-07

### Changed
- Replaced the anemone logo in `apcore-mcp-logo.svg` with a new jellyfish mascot; moved the original anemone design to `apcore-mcp-image.svg`

## [0.8.1] - 2026-03-03

### Added
- Comprehensive developer documentation site using MkDocs
- Unified "Getting Started" guide with Python and TypeScript examples for CLI and programmatic usage
- "Configuration Reference" detailing CLI arguments and programmatic API options for both languages
- Cross-links to the unified documentation site in root, Python, and TypeScript READMEs

## [0.8.0] - 2026-03-06

### Added
- Approval system feature specification (F-028): runtime approval via `ElicitationApprovalHandler`, `--approval` CLI flag with modes `elicit`, `auto-approve`, `always-deny`, `off`
- Approval error codes: `APPROVAL_DENIED`, `APPROVAL_TIMEOUT`, `APPROVAL_PENDING`
- Enhanced error responses with AI guidance fields (`retryable`, `ai_guidance`/`aiGuidance`, `user_fixable`/`userFixable`, `suggestion`)
- AI intent metadata in tool descriptions (`x-when-to-use`, `x-when-not-to-use`, `x-common-mistakes`, `x-workflow-hints`)
- Streaming annotation in description suffixes

### Changed
- Cross-language naming convention documented: Python uses snake_case (`ai_guidance`), TypeScript uses camelCase (`aiGuidance`) for AI guidance fields

## [0.7.0] - 2026-02-28

### Added
- JWT Authentication feature specification (F-027) — key file support, configurable strictness, path exemptions, audit logging

## [0.6.0] - 2026-02-25

### Added
- Streaming bridge specification — progress notifications, chunk accumulation, fallback to non-streaming
- Elicitation support specification — user input requests during module execution
- Dynamic tool registration specification

## [0.5.0] - 2026-02-25

### Added
- MCP Tool Explorer specification — browser-based UI for inspecting and testing tools
- Examples specification — cross-language standard for demo modules

## [0.4.0] - 2026-02-27

### Added
- JWT Authentication feature specification (F-027) across PRD, SRS, tech design, and test plan
- `Authenticator` protocol for pluggable authentication backends
- `JWTAuthenticator` with `ClaimMapping` for JWT Bearer token validation
- `AuthMiddleware` ASGI middleware with `ContextVar` bridge for Identity injection
- `auth/` package added to package structure (protocol.py, jwt.py, middleware.py)
- CLI flags: `--jwt-secret`, `--jwt-algorithm`, `--jwt-audience`, `--jwt-issuer`
- `PyJWT>=2.0` added as required dependency
- 47 test cases specified: 22 unit (JWT), 12 unit (middleware), 13 integration

### Changed
- Updated NG-06 non-goal: from "no auth" to "no OAuth/API key management" (JWT bridges to ACL)
- Updated C-05 constraint: authentication bridges to apcore ACL, not replaces it
- Updated security threat model with JWT-specific entries
- Updated `serve()` API with `authenticator` parameter

## [0.3.0] - 2026-02-25

### Added
- MCP Tool Explorer feature for browsing and testing tools (PRD, SRS, test plan)
- Examples specification for apcore-mcp cross-language standard (`docs/examples-spec.md`)
- Tool Explorer UI screenshot (`apcore-mcp-explorer-ui.png`)

### Changed
- Updated response key from `output` to `result` in SRS and test plan

## [0.2.0] - 2026-02-23

### Added
- Documentation deployment workflow via GitHub Actions
- Project logo (`apcore-mcp-logo.svg`)
- MkDocs configuration for documentation site (`mkdocs.yml`)
- Streaming bridge specification added to SRS and technical design documents
- Background context and Python package availability notes in README

### Changed
- Expanded PRD to cover 25 features with detailed metrics and validation parameters
- Updated SRS to reflect increased feature count and deferred feature notes
- Revised technical design documentation to align with updated feature set and naming conventions
- Updated test plan to validate new naming conventions (module IDs using hyphens instead of double underscores)
- Enhanced test cases to ensure compliance with updated API specifications
- Updated README with TypeScript repository link and current package status

## [0.1.0] - 2026-02-15

### Added
- Initial project setup for apcore-mcp
- Core concept: automatic bridging of apcore modules to MCP Server and OpenAI Tools
- Product Requirements Document (PRD)
- Software Requirements Specification (SRS)
- Technical Design Document
- Test Plan
- Initial README
