# MCP Server

## Purpose
Expose curated Perfex Intelligence capabilities to approved external agents without UI scraping, arbitrary SQL or unrestricted provider access.

## Protocol baseline
Target MCP `2026-07-28` over stateless HTTP. The initial implementation supports `server/discover`, `tools/list`, `tools/call`, `resources/list`, and `resources/read`. It does not implement the retired `initialize/initialized`, protocol sessions/`Mcp-Session-Id`, or legacy HTTP+SSE in v1.

Use current protocol headers/response metadata and deterministic/cacheable list ordering. Do not make client-reported `clientInfo` a security authority.

## Endpoint
Conceptual route: `POST /mcp`. Deployment may place it behind a reverse proxy/WAF. Requests carry correlation/request IDs; safe `Mcp-Method`/`Mcp-Name` header routing can be used by gateway/rate controls.

## Authentication
MCP caller is a dedicated machine Principal, never an omnipotent staff impersonation.

MVP supports explicit trusted OpenClaw service-principal credentials, with raw secret material managed in OpenBao and only fingerprint/reference metadata in Perfex. External/public remote enablement is blocked until current MCP authorization conformance is implemented/tested: protected-resource/auth metadata, issuer binding/validation, PKCE/appropriate OAuth flow and current client metadata approach where applicable.

Credential create/rotate/revoke is privileged, audited and may require step-up.

## Authorization
Effective permission = MCP client scopes ∩ tool required capability ∩ customer/contact/project/property restrictions ∩ current record visibility/preconditions. `tools/list` is permission-filtered and must not leak hidden tool/resource availability that implies confidential installed capabilities where policy says it is hidden.

## Tool/resource catalogue
Generated from Action/Capability Registry with explicit `mcp=true`. Output is minimized and structured. Tool descriptions explain intended use, not hidden data. Full record payloads are not default.

Representative MVP reads: customer/contact search/get, project/task summary, invoice/status/balance read where permitted, safe support summary/reference, Attention read, knowledge/resource search where an owner module registers it.

Representative MVP writes: task create/update and other explicitly standard-write module actions. Financial, payment verification/refund, Vault reveal, production DNS/registrar, destructive/bulk and legal signature actions are unavailable or require stronger policy and may remain excluded.

## Errors
Return JSON-RPC-compatible errors plus stable safe application detail (`correlation_id`, retryability, permission/approval/stale state where appropriate). Never leak stack trace, SQL, secrets or hidden record names/counts.

## Rate/usage
Limits by client, tool class, global workload and provider budget. Repeated denied/high-risk abuse can create Security Attention. Revocation takes effect on next request; no protocol session may preserve old authority.

## Observability
Record safe client/tool/target/result/duration/correlation metadata. No raw Vault/OpenBao values and no automatic full prompt/transcript capture merely for debugging.

## Future Tasks extension
Long-running actions may later use `io.modelcontextprotocol/tasks` only after cancel/update/idempotency/Approval behavior is defined. Do not emulate long work with hidden blocking HTTP requests.

## Tasks
1. Build protocol adapter and header/version validation.
2. Implement discover/list/read/call against registries.
3. Implement MCP Principal credential administration.
4. Add auth/scope/rate middleware.
5. Add list cache/version invalidation on registry/policy changes.
6. Add audit/usage/correlation.
7. Add protocol conformance and malformed-request tests.
8. Connect OpenClaw staging and verify discovery/tool invocation.
9. Add external OAuth authorization path before public enablement.

## Acceptance
- OpenClaw can discover and invoke authorised tools.
- Revoked client is denied immediately on next request.
- Cross-customer scope denial is server-side.
- No direct SQL/filesystem/secret-dump tool exists.
- Retried write is idempotent.
- Current stateless protocol works without legacy session assumptions.
