# Architectural Decision Log

## ADR-001 — Perfex remains primary business system
**Decision:** Perfex core/modules own business truth; Intelligence is an orchestration/context/action layer.
**Rejected:** parallel AI-owned CRM records or direct DB agent.
**Consequence:** adapters/services wrap authoritative Perfex operations.

## ADR-002 — Ara is native intelligence, not a chatbot module
**Decision:** user-facing assistant is **Ara**, embedded globally/contextually in admin and client portal with Ask/Draft/Act/Watch/Investigate/Recommend.
**Rejected:** dedicated generic chat page as primary UX; stale Ariya name for Perfex.

## ADR-003 — OpenClaw default, not hard dependency
**Decision:** OpenClaw is preferred agent/orchestration runtime behind AI Gateway; business domains stay runtime/provider-neutral.
**Rejected:** direct OpenAI calls throughout modules; Perfex→OpenClaw as the only possible architecture.

## ADR-004 — Native Ara and external MCP share internal services
**Decision:** MCP is external-agent protocol; native Ara is first-party product experience. Both reuse registry/services.
**Rejected:** forcing every native action through Perfex→OpenClaw→public MCP→Perfex loop.

## ADR-005 — Target MCP 2026-07-28
**Decision:** greenfield server targets current stateless MCP revision, including `server/discover`; no legacy HTTP+SSE/session/initialize implementation in v1.
**Reason:** no legacy clients/module code to preserve.

## ADR-006 — One Action/Capability Registry
**Decision:** meaningful actions/resources are defined once with schema, permissions, scope, risk, idempotency, approval and surfaces.
**Rejected:** separate UI/API/MCP/automation implementations of the same mutation.

## ADR-007 — Caller authority is never expanded
**Decision:** every tool/action reauthorizes current Principal + record/customer scope at execution.
**Rejected:** “AI service account can see everything” model.

## ADR-008 — Chatwoot owns new support truth
**Decision:** Chatwoot owns new support conversations/messages/attachments/assignment/routing/status/CSAT. Perfex owns business context and safe mappings/references; historical Perfex tickets may remain read-only.
**Supersedes:** earlier brainstorming where a handoff might create a new Perfex support ticket as the authoritative issue.

## ADR-009 — Human handoff remains one client experience
**Decision:** client uses native Ara surface; handoff creates/links Chatwoot behind it, sends safe context/private note, human takes responder role, Ara may remain agent copilot, and Ara resumes after resolution.

## ADR-010 — Secret boundary
**Decision:** client-facing credentials → Perfex Vault; machine/service/infrastructure secrets → OpenBao. Generic AI/MCP secret reveal is unavailable.
**Rejected:** plaintext module options, merge-tag secrets, bulk credential tools.

## ADR-011 — Forms and Onboarding keep ownership
**Decision:** Forms owns structured collection; Onboarding owns journeys/access gating. Intelligence exposes/consumes actions/events/checks only.

## ADR-012 — Attention is not Notification
**Decision:** Attention represents a still-active condition; Notifications deliver awareness. Reading a notification cannot resolve underlying Attention.

## ADR-013 — Shared integration runtime
**Decision:** Chatwoot/OpenClaw/provider webhooks and external calls use shared verification/idempotency/retry/backoff/DLQ/replay/observability contracts.
**Rejected:** bespoke webhook controllers with different semantics in every module.

## ADR-014 — Durable external identity mappings
**Decision:** customer/contact/property/project/provider identities use canonical mapping IDs. Email may assist resolution but is never sole durable key.

## ADR-015 — API by design
**Decision:** internal services first; REST/MCP are adapters with explicit exposure. No “bolt API on later”.

## ADR-016 — Greenfield compatibility
**Decision:** current repo has no module code/data/migrations; no invented backward-compatibility migration is required. Once v1 ships, every published version receives contiguous migration target including no-op when needed.

## ADR-017 — Hardened updates
**Decision:** follow Magic Login release architecture: semantic version, immutable GitHub release/tag, ZIP+SHA256, archive/version/manifest/migration checks, backup before replacement, safe rollback boundary, update history, no retargeting published tags.

## ADR-018 — No direct cross-module table coupling
**Decision:** modules register services/actions/resources/events. Direct table reads across modules are exceptional and documented, not normal integration.
