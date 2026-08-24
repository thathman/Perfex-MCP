# Build Phases

## Phase 0 — Foundation / repository and release baseline
**Objective:** establish installable module skeleton and permanent platform boundaries.

Features: module bootstrap, namespacing, service container/factories, config, migrations, permissions registration, audit base, updater manifest/release handler design, test harness, shared integration-runtime adapter interfaces.

DB: initial settings, audit/run/update tables and schema-version baseline.

APIs/UI: no public AI yet; settings/readiness shell only.

Tests: install/uninstall safety, migration chain, lint/static checks, permission registration, updater unit tests.

Exit: clean install/activate/deactivate; no core patch; first migration target contiguous; CI can package a non-production artifact.

## Phase 1 — Capability, Action, Context and Principal core
**Objective:** create the internal contract all surfaces reuse.

Features: Principal resolver, capability/resource registry, Action Registry, risk classes, preview/confirmation hooks, idempotency, context providers, provenance/freshness envelope, module SDK hooks.

DB: action-policy overrides, action runs, context/conversation metadata as required.

APIs: internal service contracts only; transport-agnostic DTO/schema definitions.

UI: admin Action Catalogue/Policy view and diagnostics.

Tests: permission matrix, negative scope tests, action retry/idempotency, invalid module registration, provenance.

Exit: UI test harness and a fixture module both invoke the same read/action services.

## Phase 2 — AI Gateway, OpenClaw and native admin Ara
**Objective:** make Ara useful to staff without weakening controls.

Features: AI Gateway, OpenClaw adapter, model/runtime profiles, context budgeting/redaction, Ask/Draft, limited Act, sources, active context, streaming/failure states, usage/budgets.

DB: conversations, AI runs, tool runs, provider profile metadata (secrets are OpenBao references).

UI: global launcher, contextual panel/actions, Home briefing placeholder fed by deterministic sources.

Integrations: OpenClaw + OpenBao.

Tests: provider failure, tool denial mid-run, context minimization, prompt injection boundaries, action result verification.

Exit: staff E2E scenarios pass using real staging OpenClaw with test data.

## Phase 3 — MCP and API surface
**Objective:** expose the same capability layer to controlled external agents.

Features: MCP `2026-07-28` HTTP endpoint, `server/discover`, tools/resources, client principals/scopes, revocation/rotation, rate/usage limits, auth adapter, cache metadata, correlation/audit. Versioned REST endpoints for module administration/action execution where appropriate.

DB: MCP clients/scopes/usage references; secret material remains OpenBao-managed.

Tests: protocol conformance, auth/issuer/scope tests, revoked client, cross-customer denial, rate limit, malformed schema, replay/idempotency.

Exit: OpenClaw can register/probe the Perfex MCP server and invoke fixture/core tools in staging.

## Phase 4 — Client Portal Ara + Chatwoot human handoff
**Objective:** deliver safe client AI with first-class escalation.

Features: client-safe context/action catalogue, contact permissions, portal UI, handoff controller, Chatwoot contact/conversation mapping, safe handover packet/private note, team routing, webhook status/freshness, queued/offline states, return-to-Ara after resolution.

DB: support reference/handoff metadata only; no duplicate Chatwoot transcript.

Integrations: Chatwoot through shared runtime; Notifications Inbox/email templates for queued/replied/failure notices as configured.

Tests: client isolation, revoked membership, Chatwoot outage, duplicate webhook, out-of-order status, explicit-human-request path, no secret/internal-note leakage.

Exit: authenticated client completes AI→human→AI journey in staging.

## Phase 5 — Attention, Watch and ecosystem integrations
**Objective:** add proactive intelligence without making the LLM the workflow engine.

Features: Attention provider registry, deterministic attention state, Watch conditions, scheduled/event triggers, Approval Workflow handoff, module event/action registrations for Hosting/Property Monitor/Forms/Onboarding/etc.

DB: attention items and watch definitions/runs.

Tests: resolve because source condition changed, not because notification read; stale source handling; duplicate event suppression; approval expiry.

Exit: at least three ecosystem modules register attention/events/actions and one Watch flow runs end-to-end.

## Phase 6 — Advanced capabilities
**Objective:** deepen capability without changing ownership boundaries.

Candidates: long-running MCP Tasks extension, richer investigations, multi-agent/specialist delegation inside OpenClaw, save-as-automation UX, advanced provider routing/fallback, bulk action preview, richer files/knowledge tools, expanded client self-service.

Exit: each feature has separate acceptance/security review before release.

## Phase 7 — Hardening / production release
**Objective:** production evidence and safe lifecycle.

Work: full security review, negative auth suite, performance/load, accessibility/responsive QA, migration rehearsal, backup/recovery, integration chaos testing, release artifact/checksum validation, updater rollback-boundary tests, staging soak, documentation.

Release: semantic version, immutable GitHub tag/release, `perfex_mcp.zip`, checksum, matching `update_manifest.json` and migration target. Never retarget/rewrite a published version.

Exit: signed-off release checklist and production-ready package.
