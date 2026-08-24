# Perfex-MCP

**Status:** planning / pre-implementation

Perfex-MCP is the repository for the **Perfex Intelligence Platform**: the native Ara assistant, permission-aware action/capability registry, AI gateway, MCP server, OpenClaw integration, client-portal AI, Chatwoot human handoff, attention/watch primitives, audit/provenance, and the extension contract that allows Perfex core and future modules to expose safe AI/API/MCP capabilities.

Perfex remains the business system and source of truth. Ara is a native Perfex experience, not a detached chatbot. OpenClaw is the preferred orchestration runtime behind a provider-neutral AI Gateway, not a hard architectural dependency. MCP is the external-agent protocol surface; native Ara and MCP reuse the same internal services/actions rather than proxying through each other.

## Current repository state

The repository began as a greenfield shell with only this README. No production module code, migrations, releases, legacy data model, or compatibility obligations exist yet. The current branch work is specification-only.

## Core decisions

- User-facing assistant identity: **Ara**.
- Operating modes: **Ask, Draft, Act, Watch, Investigate, Recommend**.
- Current MCP target: **2026-07-28** stateless protocol.
- OpenClaw is the default orchestration adapter; direct/future runtimes remain possible.
- All writes go through a registered, permission-aware Action/Capability Registry.
- Caller permissions and record scope are rechecked server-side at execution time.
- Chatwoot owns new support conversations/messages/routing/status; Perfex supplies business context and safe references.
- Client-facing credentials belong in Perfex Vault; machine/service secrets belong in OpenBao.
- Perfex Forms owns structured collection; Perfex Onboarding owns onboarding orchestration.
- Notifications and Attention are separate concepts.
- External integrations use the shared integration runtime for verification, idempotency, retry/backoff, DLQ/replay and observability.
- No direct arbitrary SQL, unrestricted filesystem access, generic Vault secret retrieval, or direct model-provider calls from business modules.

## Planned module identity

- Repository: `Perfex-MCP`
- Perfex module slug: `perfex_mcp`
- Product/UI label: `Perfex Intelligence` / `Ara`
- Release asset: `perfex_mcp.zip` + `perfex_mcp.zip.sha256`

## Documentation

Start with:

- [`docs/product/PRODUCT_SPEC.md`](docs/product/PRODUCT_SPEC.md)
- [`docs/product/MVP.md`](docs/product/MVP.md)
- [`docs/product/BUILD_PHASES.md`](docs/product/BUILD_PHASES.md)
- [`docs/product/ARCHITECTURE.md`](docs/product/ARCHITECTURE.md)
- [`docs/product/INTEGRATIONS.md`](docs/product/INTEGRATIONS.md)
- [`docs/product/API.md`](docs/product/API.md)
- [`docs/product/DATA_MODEL.md`](docs/product/DATA_MODEL.md)
- [`docs/product/PERMISSIONS.md`](docs/product/PERMISSIONS.md)
- [`docs/product/TEST_PLAN.md`](docs/product/TEST_PLAN.md)
- [`docs/product/DECISIONS.md`](docs/product/DECISIONS.md)
- [`docs/product/GAP_ANALYSIS.md`](docs/product/GAP_ANALYSIS.md)
- [`docs/product/IMPLEMENTATION_TASKS.md`](docs/product/IMPLEMENTATION_TASKS.md)

Feature-level implementation specs live under `specs/`.

## Requirements and dependencies

The module will target the supported Perfex CRM/PHP/MySQL baseline agreed at implementation start and must not patch Perfex core. Required ecosystem contracts are the shared integration runtime, canonical identity/external-ID mapping, permissions/audit conventions, OpenBao secret broker, and native Perfex hooks/services. OpenClaw and Chatwoot are required for the full MVP experience; Vault, Forms, Onboarding, Property Monitor and other Perfex modules integrate through optional stable contracts when installed.

## Installation

Not yet available. No production package has been published from this repository. Installation/update instructions will be added with the first implementation release.

## Development status

**Implemented:** planning repository only.

**Committed direction:** architecture, MVP, data model, API/MCP contracts, permission model, Chatwoot handoff, OpenClaw adapter, updater/release design and module extension contract documented in `docs/product/` and `specs/`.

**Future:** advanced watch/automation authoring, richer provider adapters, long-running MCP Tasks, expanded cross-module tools, deeper proactive intelligence and advanced analytics.
