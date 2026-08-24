# Integrations

## Integration rules

1. One authoritative owner per datum/domain.
2. Prefer services/events/contracts over direct table access.
3. External calls use shared integration runtime for verification, idempotency, retry/backoff, DLQ/replay, duplicate detection, logs and correlation.
4. Durable external identity mapping is required; email alone is not canonical identity.
5. Machine secrets are OpenBao references; client credentials are Vault-owned.

## Dependency / ownership matrix

| Integration | Owner of truth | Perfex-MCP consumes/provides | Sync | Key permissions | Failure behaviour | Audit/UI |
|---|---|---|---|---|---|---|
| Perfex core | Perfex | customers/contacts/projects/tasks/commercial/billing/staff context and registered actions | sync | native staff/contact permissions | deny/stale-safe | contextual Ara, Action Catalogue |
| OpenClaw | OpenClaw owns agent runtime/session internals; Perfex owns business truth | AI orchestration, tools, MCP registration | sync/async | `intelligence.use`, runtime config | degraded Ara; no fake success | Runtime Health/AI Runs |
| Chatwoot | Chatwoot | human support conversation/ref/routing/status | async webhooks + sync API | handoff/support mapping scopes | queue/degraded; retry/DLQ | portal handoff/admin support ref |
| OpenBao | OpenBao | service-secret handles | sync | secret-policy/service identity | fail closed | integration settings health |
| Perfex Vault | Vault | metadata, access request, secret-use capability handles where explicitly supported | sync/events | Vault's own object permissions | deny; never fallback to plaintext | record/client contextual actions |
| Forms | Forms | submission summaries, mapping/convert actions | sync/events | Forms + target-domain perms | source unavailable/stale | submission/contextual Ara |
| Onboarding | Onboarding | completion checks/deep links/actions/events | sync/events | onboarding scope | no duplicate journey | onboarding surfaces/Ara |
| Property Monitor | Property Monitor | property health/evidence/events | sync/events | property/monitor scopes | stale/unknown not healthy | property Ara/Attention |
| Domain & Hosting | Hosting Manager | services/renewals/pricing state | sync/events | hosting/renewal scopes | safe retry/idempotency | customer/property/Ara |
| Magic Login | Magic Login | login-link lifecycle/actions | sync/events | magic-login create/revoke | action failure visible | contact/client actions |
| Handoff | Handoff module | project handover packs/state | sync/events | handoff read/generate/publish | draft remains safe | project/Ara |
| Appointment | Appointment | availability/booking | sync/events | appointment scopes | no duplicate booking | client/admin Ara |
| Notifications Inbox | Notifications module/native Perfex | delivery/presentation preferences | events/service | notification create/manage | queue/failure visible | inbox/email/push |
| Approval Workflow | Approval module | approval policy/state | sync/events | approval + target action | action waits/aborts safely | action confirmation/approval cards |
| Organisation Self-Service | owning module | client-authorised organisation changes/requests | sync/events | client scoped | no direct broad edit | portal Ara |
| Verified Receipts | receipt/payment owners | verified receipt/payment evidence | sync/events | billing read/action | text never creates payment truth | billing Ara |
| Documenso | document/signing integration | status/evidence actions only | async/sync | document scopes | stale/failed explicit | contracts/docs context |
| Uptime Kuma | monitoring integration | signal source only when configured | async/sync | monitoring read | provider outage != property outage | property evidence |
| Bachs/payment | payment module | verified transaction/payment action/evidence | async/sync | billing/payment scopes | idempotent/reconcile, no fabricated payment | billing context |
| OJS integrations | OJS modules/connectors | journal/property/submission operational capabilities | sync/events | module scopes | connector health/freshness | property/project Ara |
| WordPress/WooCommerce | connectors/modules | site/order/article context/actions | sync/events | property/ecommerce scopes | provider error explicit | property/client context |

## Chatwoot contract

Chatwoot owns new support inboxes, channels, contacts-as-support-provider records, messages, attachments, assignment, conversation status/routing, CSAT and Captain. Perfex owns customer/contact/project/property/commercial context and safe mapping/reference snapshots.

Handoff may create a Chatwoot contact/conversation, set safe custom attributes, assign team/agent and create a private handover note. It must not mirror the full transcript into Perfex. Webhooks update safe reference state/freshness through the integration runtime.

## OpenClaw contract

Perfex AI Gateway sends an authenticated principal/run envelope and tool catalogue to the OpenClaw adapter. OpenClaw may use Perfex MCP as an outbound MCP server. Native Ara may dispatch internal tools directly under the same registry to avoid a mandatory loopback. Runtime session IDs are stored as references, not business authority.

## Module registration contract

A module may register:
- resources/context providers;
- actions with risk/permissions/schemas/surfaces;
- events;
- Attention providers;
- Watch/automation triggers/actions;
- client-safe variants;
- API/MCP exposure metadata.

Registration failure disables that contribution and surfaces diagnostics; it never grants implicit access.

## Onboarding

Onboarding owns orchestration. Perfex-MCP exposes `intelligence.readiness`, `mcp.client_connected`, `ara.first_use_completed`, provider/handoff completion checks and deep links. It does not create a second onboarding engine.

## Forms

Forms remains structured collection. Ara may suggest/create a Form Request or summarize an authorised Submission, but sensitive credentials route to Vault requirements rather than ordinary form fields.

## Secrets

No integration stores plaintext API tokens in module options/tables. OpenBao stores Chatwoot/OpenClaw/service credentials and returns handles/short-lived use. Vault stores client-facing credentials. Merge tags expose safe metadata only.
