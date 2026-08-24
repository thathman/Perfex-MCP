# Perfex Intelligence / MCP — Product Specification

## 1. Product vision

Perfex Intelligence makes Perfex feel like a business system that understands and can safely operate itself. Ara is embedded across the admin area and client portal, understands the caller's authorised Perfex context, can explain and draft work, can invoke controlled actions, can monitor explicit conditions, can investigate cross-domain evidence, and can recommend useful next actions.

The same underlying capability layer is exposed to approved external agents through MCP. Perfex remains the source of business truth; OpenClaw is the preferred reasoning/orchestration runtime; Chatwoot is the source of truth for new support conversations; Vault/OpenBao preserve secret boundaries; Forms and Onboarding remain their own platform owners.

## 2. Problem statement

Perfex and its modules expose business capability through separate screens, models, hooks and integrations. Without a common intelligence/action layer, AI would become duplicated, over-privileged and inconsistent: one implementation for UI, another for API, another for MCP, another for automation, and module-specific model calls. This product creates one safe reusable contract.

## 3. Users / principals

- Owner/administrator: configures providers, MCP clients, action policies, integrations, budgets and audit.
- Staff user: asks/drafts/acts within existing permissions and record scope.
- Client contact: uses a narrower client-safe Ara experience bound to active customer/contact scope.
- Service account: runs approved integrations/jobs.
- MCP client: scoped machine principal with explicit tools/resources and record boundaries.
- Perfex module: registers namespaced resources, contexts, events, attention providers and actions.

## 4. Goals

1. Native Ara across Perfex, never a detached generic chatbot.
2. One Action/Capability Registry reused by UI, Ara, MCP, API and automation.
3. Provider-neutral AI Gateway with OpenClaw as the default runtime adapter.
4. First-class MCP server targeting protocol `2026-07-28`.
5. Context-aware answers/actions with provenance, freshness and fact-vs-inference distinction.
6. Server-side permission/scope enforcement on every read/write.
7. Human handoff from client Ara to Chatwoot without creating a competing support engine.
8. Safe extension contract for every current/future Perfex module.
9. Strong audit, observability, budgets, failure states and updater/release discipline.

## 5. Non-goals

- Replacing Perfex as system of record.
- Rebuilding Chatwoot inboxes, message storage, routing, CSAT or Captain.
- Replacing Forms, Onboarding, Vault, Approval Workflow or Notifications Inbox.
- Giving an LLM arbitrary SQL/filesystem access.
- Generic plaintext credential retrieval.
- Hard-coding OpenAI/Anthropic/OpenClaw calls inside business modules.
- Autonomous irreversible financial/legal/security actions without registered policy.
- A second CRM/support/ticket database.
- Legacy MCP transport compatibility in the first greenfield release unless deliberately added later.

## 6. Design principles

- **Native, contextual, quiet:** Ara appears where work happens and does not obscure primary UI.
- **Authority follows the caller:** AI never expands permissions.
- **Business facts before model inference:** deterministic records/events/attention drive truth.
- **Actions are registered:** no invented mutations.
- **Minimum necessary context:** retrieve only what a task needs.
- **Evidence is visible:** material answers expose records, timestamps/source and uncertainty.
- **Provider-neutral:** business domains do not know the model/runtime provider.
- **API/MCP by design:** services exist before transport adapters.
- **No duplicate ownership:** each ecosystem module keeps authoritative data.
- **Fail closed for sensitive actions; fail visibly for integrations.**

## 7. Terminology

- **Ara:** user-facing native AI operator.
- **AI Gateway:** routes authorised AI runs to OpenClaw/direct/future runtimes.
- **Context Provider:** produces minimum structured authorised context for a Perfex surface/record.
- **Capability:** readable/business ability a principal may access.
- **Action:** registered mutation or consequential operation with schema, risk and policy.
- **Resource:** curated read representation for MCP/AI/API.
- **Attention:** a condition that still requires awareness/action; different from a notification.
- **Watch:** explicit monitored condition plus policy-driven response.
- **Support Reference/Handoff:** safe link/snapshot to Chatwoot; not a duplicate conversation.
- **Principal:** staff, client contact, service account, API/MCP client or module identity.

## 8. Operating modes

### Ask
Search/explain authorised Perfex truth. Example: “What is happening with this client?”

### Draft
Produce proposed content such as email, proposal narrative, project update or reminder. Drafts have no external side effect until applied/sent by a registered action.

### Act
Invoke a registered authorised action. Example: create a task, update a lead, request a Vault access pack, create a Magic Login link.

### Watch
Observe an explicit deterministic condition via event/schedule/monitoring primitives and react through policy.

### Investigate
Correlate evidence across permitted records and distinguish deterministic fact from inference.

### Recommend
Propose useful next actions from real evidence/Attention without silently executing consequential work.

## 9. Major capabilities

### 9.1 Native assistant
Global launcher/command entry, Home briefing, contextual record panel/actions, active-context controls, sources, action cards, confirmation/approval UI, streaming/retry/degraded states and conversation history under retention policy.

### 9.2 Context engine
Explicit route/entity context from Perfex, never DOM scraping. Core providers cover customers/contacts, leads, projects, tasks, contracts/proposals/estimates, invoices/payments/subscriptions, staff, custom fields and safe historical support context. Modules register additional providers.

### 9.3 Action & Capability Registry
Each action declares stable id, schemas, required permissions/scope, risk class, confirmation/approval/step-up, idempotency, audit, supported surfaces and preview/dry-run support. Registration never automatically exposes an action to MCP/API/client portal.

### 9.4 AI Gateway / OpenClaw
All provider/runtime calls go through one gateway handling caller identity, context budget, redaction/data class, runtime/model routing, timeouts/retry policy, tool catalogue, validation, usage and audit metadata. OpenClaw is the default orchestration adapter and may consume the Perfex MCP server externally; native Ara may also use direct internal action/service adapters to avoid needless loopback.

### 9.5 MCP server
Remote stateless HTTP endpoint targeting MCP `2026-07-28`: `server/discover`, tool/resource catalogues, permission-filtered calls, cache metadata and future Tasks extension. No arbitrary SQL/provider-secret tool.

### 9.6 Client Portal Ara
Client-safe identity/scope, own projects/invoices/properties/requests/knowledge/actions, clear active organisation/customer context, mobile-first assistant and immediate human handoff option.

### 9.7 Chatwoot handoff
Client remains in one coherent Perfex/Ara surface. On escalation Ara creates/links a Chatwoot conversation, sends a minimal handover packet/private note, applies safe custom context and team routing, then switches client-responder mode off while a human is active. Chatwoot remains authoritative for messages, assignment, routing and status. Perfex stores only mapping/snapshot/freshness/audit references.

### 9.8 Attention / Watch
Deterministic Attention providers from core/modules; Ara explains/ranks. Watch definitions observe explicit events/schedules/conditions and invoke registered actions/notifications. Full general workflow authoring is deferred; extension contracts exist from MVP.

### 9.9 Module SDK
Modules register resources, context providers, actions, events, attention providers and automation hooks through stable hooks/interfaces. Future modules gain Ara/MCP capability without editing core Perfex-MCP code.

## 10. Core workflows

### Staff asks about current record
1. UI sends explicit route/entity context and caller principal.
2. Context Engine resolves authorised providers.
3. AI Gateway retrieves only required resource slices.
4. Ara answers with source references/freshness and marks inference.
5. Read/tool usage is audited at safe metadata level.

### Staff asks Ara to act
1. Intent resolves to a registered action.
2. Registry checks surface availability, permission, record scope and preconditions.
3. Preview/dry-run shown when required.
4. Confirmation/Approval/step-up occurs according to risk policy.
5. Action executes through domain service with idempotency key.
6. Domain event/activity/audit is emitted; Ara reports verified result only.

### Client human handoff
1. Client requests human or policy/insufficient evidence requires escalation.
2. Handoff Controller validates active portal identity/customer scope.
3. It resolves durable Chatwoot contact/inbox mappings through shared identity mapping.
4. It creates/links Chatwoot conversation and safe custom attributes.
5. Ara adds an internal handover summary/private note and routes to team/agent.
6. Perfex records only support reference/status snapshot and correlation id.
7. Webhooks update safe status/freshness; Chatwoot remains message truth.
8. On resolution Ara may resume client responder mode.

## 11. Admin experience

- **Perfex Intelligence** settings: Overview, Ara, Runtime/Providers, MCP Clients, Actions/Policies, Integrations, Attention/Watches, Usage, Audit, Updates.
- Contextual `Ask Ara`, `Explain`, `Draft`, `Act` actions on supported records.
- Home briefing based on permission-filtered Attention.
- Search/command integration rather than a giant separate module-only workflow.
- Setup wizard for OpenClaw/OpenBao/Chatwoot/MCP readiness with connection tests and least-privilege guidance.
- Saved views for MCP clients, denied calls, failed runs, pending approvals and integration failures where useful.

## 12. Client experience

- Native portal assistant; no admin table reuse.
- Contact-level permissions and customer boundaries enforced server-side.
- Context-aware actions: explain invoice/project/property, find client-visible knowledge, submit/request structured work through Forms/Requests, book Appointment where enabled, renew/request service where enabled, ask for human.
- Human handoff uses Chatwoot behind the same coherent experience; no repeated identity questionnaire when already authenticated.
- Internal notes, staff-only reasoning, margins, hidden properties, other clients, Vault values and broad audit detail never appear.

## 13. Integrations

Detailed ownership/contracts are in `INTEGRATIONS.md`. Core relationships include Perfex core, OpenClaw, Chatwoot, OpenBao, Vault, Forms, Onboarding, Property Monitor, Hosting Manager, Magic Login, Appointment, Handoff, Notifications Inbox, Approval Workflow, Organisation Self-Service, Verified Receipts, Documenso, Uptime Kuma, Bachs/payment, OJS and WordPress/WooCommerce integrations.

## 14. Notifications

Notification delivery belongs to native Perfex templates/Notifications Inbox. Intelligence raises semantic events/notification requests; it does not hard-code email bodies. See `NOTIFICATIONS.md`.

## 15. Automation/events

Examples:
- `perfex_mcp.ai_run.completed|failed`
- `perfex_mcp.action.proposed|approved|executed|failed|denied`
- `perfex_mcp.mcp_client.created|rotated|revoked`
- `perfex_mcp.mcp_call.denied|failed`
- `perfex_mcp.attention.created|resolved|stale`
- `perfex_mcp.watch.triggered|failed`
- `perfex_mcp.handoff.requested|queued|human_active|resolved|failed`
- `perfex_mcp.provider.health_changed`

External webhook/event processing uses the shared integration runtime with verification, idempotency, retry/backoff, DLQ/replay, duplicate detection and correlation ids.

## 16. Permissions/security

See `PERMISSIONS.md`. Mandatory properties: server-side authorisation, CSRF for browser writes, output encoding/XSS protection, parameterized DB access, strict file validation, SSRF controls for configurable endpoints, protected secrets via OpenBao/Vault, signed/expiring tokens where used, replay protection/idempotency, rate/usage limits, step-up/Approval for high-risk actions, and negative cross-customer tests.

## 17. Auditability

Consequential operations record actor/principal, interface, action/tool, resource/target, before/after summary where safe, timestamp, correlation/idempotency ids, approval/confirmation reference, provider/runtime, result, duration and optional IP/device metadata. Secret values and unnecessary prompt content are excluded/redacted.

## 18. Non-functional requirements

- No Perfex core patching.
- Graceful provider/runtime outage with no data corruption.
- p95 local read-tool overhead target under 500ms excluding provider latency; integration calls expose separate latency.
- MCP/tool payload minimization and pagination.
- Queueable long-running work; no blocking cron request abuse.
- Mobile responsive admin/client assistant.
- WCAG-conscious keyboard/focus/contrast behavior.
- Configurable retention and budgets.
- Structured logs, health checks, metrics and correlation ids.
- Installation/update must be recoverable and migration-aware.

## 19. Reporting

MVP: usage by principal/feature/runtime, action outcomes, denied calls, handoff counts/status, provider health, top tools and cost/token metrics where available. No employee productivity scoring or hidden client profiling.

## 20. Edge/failure cases

Provider unavailable; OpenClaw unavailable; tool denied mid-run; record deleted between preview/execution; stale connector; webhook replay/out-of-order delivery; duplicate action retry; context exceeds budget; ambiguous identity; client loses membership mid-conversation; Chatwoot offline; OpenBao unavailable; Approval expires; module registering an invalid action; MCP client revoked during request; updater migration failure. All fail safely and visibly.

## 21. Product acceptance criteria

- Ara is native and context-aware across supported Perfex surfaces.
- Read/write authority never exceeds caller principal + record scope.
- Actions are registered and auditable; high-risk actions cannot bypass policy.
- OpenClaw can orchestrate Ara without becoming a hard-coded business dependency.
- MCP `2026-07-28` exposes curated permission-filtered tools/resources.
- Chatwoot owns new support conversation truth and human handoff works end-to-end.
- Vault/OpenBao boundaries prevent generic secret exposure.
- Module SDK enables a second module to register a resource/action without modifying Perfex-MCP core.
- Provider/integration failure cannot fabricate success or corrupt Perfex records.
