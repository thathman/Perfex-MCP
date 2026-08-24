# API and MCP Contract

## Principles

Transport adapters reuse internal services; they are not the business-logic layer. All requests have a Principal, canonical permissions/scopes, record/customer restrictions, correlation id, validation, rate/usage policy and auditable outcome. Unknown fields are rejected or safely ignored according to documented schema version; sensitive writes are idempotent.

## REST design contract

Proposed versioned namespace: `/api/v1/intelligence` (exact routing follows the shared Perfex API layer).

Representative endpoints:

- `GET /capabilities` — permission-filtered catalogue metadata.
- `GET /actions` — actions available to caller/surface.
- `POST /actions/{action_id}/preview` — no-side-effect validation/impact.
- `POST /actions/{action_id}/execute` — idempotent registered action.
- `GET /attention` / `GET /attention/{id}` — filtered Attention.
- `POST /attention/{id}/acknowledge|snooze` — state only; does not falsely resolve source condition.
- `GET|POST /watches` — explicit Watch definitions when enabled.
- `GET /ai/runs/{id}` — safe run state/evidence.
- `GET /handoffs/{id}` — safe Chatwoot handoff/reference state.
- `POST /handoffs` — explicit client/staff handoff request under policy.
- `GET /mcp-clients` / credential rotation/revocation admin endpoints — administrators only.

Public API exposure is explicit per action/resource. Internal-only services are not automatically routable.

## Authentication / authorization

- Browser/admin/client calls use Perfex session/CSRF and native permission checks.
- Shared REST API clients use the ecosystem API Principal/auth layer where available.
- MCP/service clients use dedicated machine Principals; raw credentials are shown only at creation/rotation and secret material is OpenBao-managed. DB stores IDs/fingerprints/hashes/references and policy metadata, not plaintext.
- Remote external MCP release requires current MCP authorization conformance (issuer binding/metadata/PKCE where applicable). Trusted internal OpenClaw service-token mode may be enabled only by explicit deployment policy.

## MCP endpoint

Default conceptual endpoint: `POST /mcp` (or module route mapped to it).

Target protocol: `2026-07-28`.

MVP methods/capabilities:
- `server/discover`
- `tools/list`
- `tools/call`
- `resources/list`
- `resources/read`

The server is stateless at the protocol layer. It does not implement the retired `initialize/initialized` handshake, `Mcp-Session-Id`, or legacy HTTP+SSE transport in v1. Response identity/capability metadata follows the current spec. Tool/resource list cache metadata is stable and invalidated when module registration/policy changes.

Future: `io.modelcontextprotocol/tasks` for suitable long-running operations after lifecycle/idempotency/approval behavior is proven.

## MCP tool contract

Each tool declares:
- stable namespaced name and version;
- owner module;
- description designed for model selection without leaking hidden data;
- JSON Schema input/output;
- canonical required permission;
- record/customer scope resolver;
- data classification/output minimization;
- risk class;
- confirmation/Approval/step-up requirements;
- idempotency behavior;
- rate/usage class;
- provenance/freshness envelope;
- supported principal/surfaces.

Examples:
- `perfex.customers.search`
- `perfex.customers.get`
- `perfex.projects.get`
- `perfex.tasks.search`
- `perfex.tasks.create`
- `perfex.invoices.get`
- `perfex.invoices.list_overdue`
- `perfex.support.get_summary` (provider-neutral, not transcript proxy)
- `perfex.vault.search_metadata`
- `perfex.magic_login.create` when module installed and exposed.

## Pagination/filtering

List resources use bounded `limit` with cursor/page adapter, typed filters, sort and date ranges. Default/minimum fields are purpose-specific; agents do not receive full rows by default.

## Idempotency

`Idempotency-Key` (REST) / tool-call idempotency metadata is required for create/send/external side-effect actions. Keys are scoped to principal + action + target and retain outcome long enough to make retries safe. Duplicate requests return prior compatible result rather than duplicate side effects.

## Error envelope

Stable fields: `code`, `message`, `correlation_id`, optional `field_errors`, `retryable`, `required_permission`, `approval_required`, `stale_source`, and safe remediation hint. Never expose stack trace, SQL, provider token, Vault/OpenBao value or hidden-record metadata.

Representative codes: `AUTH_REQUIRED`, `FORBIDDEN`, `SCOPE_DENIED`, `NOT_FOUND`, `VALIDATION_FAILED`, `PRECONDITION_FAILED`, `CONFIRMATION_REQUIRED`, `APPROVAL_REQUIRED`, `IDEMPOTENCY_CONFLICT`, `RATE_LIMITED`, `SOURCE_STALE`, `PROVIDER_UNAVAILABLE`, `INTEGRATION_FAILED`.

## Webhooks / events

Outbound semantic events use shared signed webhook runtime with delivery id, event version, occurred time, safe payload, correlation id, retry/backoff and DLQ/replay. Inbound Chatwoot/OpenClaw/provider events enter through verified integration runtime, not module-specific ad-hoc controllers.

## Rate/usage limits

Per principal/client + tool/action class + global provider budget. Expensive AI/tool chains have tighter limits than simple resource reads. Rate-limit metadata is exposed where transport permits.
