# MVP

## MVP definition

The MVP is the smallest production release that establishes the permanent intelligence architecture and is useful for both staff and clients. It is **not** basic CRUD and it does not ship a chatbot that will later be rewritten.

## Included

1. `perfex_mcp` module foundation, migrations, install/uninstall-safe lifecycle and hardened updater hooks.
2. Principal adapter for Perfex staff/client contacts plus service/MCP principals.
3. Capability/Action Registry with schemas, risk classes, permission/scope checks, idempotency, audit and surface exposure flags.
4. Context Engine with explicit route/entity context and core read providers for customers/contacts, leads, projects, tasks, contracts/proposals/estimates, invoices/payments/subscriptions, staff-safe lookups, custom fields and historical Perfex ticket summaries where permitted.
5. Provider-neutral AI Gateway and OpenClaw runtime adapter.
6. Native admin Ara experience supporting Ask, Draft, limited Act and source/evidence display.
7. MCP `2026-07-28` stateless endpoint with discover, permission-filtered tools/resources, rate limits, audit and client revocation.
8. Safe standard-write actions in MVP: task creation/update, notes/activity-safe actions where supported, reminders/requests through owning services, and module-defined low-risk writes. Financial/security/destructive actions remain unavailable or approval-gated.
9. Module SDK baseline for namespaced resource/context/action registration.
10. Client Portal Ara limited to client-visible context/actions.
11. Chatwoot human handoff with durable contact/conversation mapping, safe context packet/private note, team routing, webhook-updated status/freshness and return-to-Ara behavior.
12. OpenBao integration for machine/service secrets; Vault metadata/capability integration without generic secret reveal.
13. Audit, usage, provider/integration health and failure states.
14. Native Perfex notifications/email-template hooks for security/integration/action events.
15. Extension interfaces for Attention/Watch/Automation even where advanced authoring is deferred.

## Deferred after MVP

- Full general-purpose automation builder.
- Natural-language “save as automation” authoring.
- Advanced multi-agent delegation profiles.
- Long-running MCP Tasks extension beyond architectural support.
- Direct model-provider adapters beyond the default OpenClaw route unless needed as an operational fallback.
- Rich voice/image/file assistant workflows beyond safe basic attachment handling.
- Predictive scoring and advanced analytics.
- Broad high-impact action catalogue (payments/refunds/DNS/Vault reveal/legal issuance).
- Autonomous bulk actions.
- Cross-channel omnichannel Ara beyond the client portal/Chatwoot handoff contract.

## MVP dependencies

### Required
- Supported Perfex CRM/PHP/MySQL runtime.
- Shared integration runtime contract (verified webhook ingestion, retries, idempotency, DLQ/replay, observability).
- Canonical external-ID/identity mapping contract.
- OpenBao or the approved Airix secret broker for OpenClaw/Chatwoot/service credentials.
- OpenClaw instance for the default AI orchestration path.
- Chatwoot instance for client human handoff.

### Optional/feature-gated
- Perfex Vault, Forms, Onboarding, Property Monitor, Domain & Hosting Manager, Magic Login, Handoff, Appointment, Notifications Inbox, Approval Workflow, Organisation Self-Service, Verified Receipts and other modules. Their absence must not crash core MVP; their capabilities simply do not register.

## MVP launch blockers

- Any cross-customer/contact permission leak.
- Any generic Vault/OpenBao secret exposure to model context/logs.
- Ability for MCP/Ara to bypass Perfex permission or Action policy.
- Duplicate write execution on retry.
- Chatwoot handoff that mirrors/claims ownership of conversation messages in Perfex.
- No audit trail for consequential actions/MCP client administration.
- Missing integration retry/DLQ visibility.
- No safe degraded mode when OpenClaw/Chatwoot/OpenBao is unavailable.
- Update package without checksum/archive/version/migration validation.
- Missing negative permission and portal isolation tests.

## MVP acceptance criteria

A staff user can ask Ara about an authorised client/project/invoice, see source evidence, ask it to create an allowed task, preview/confirm where policy requires, and see verified outcome/audit. An approved OpenClaw/MCP client can discover and call the same authorised capabilities through the external MCP surface. A client contact can ask about own permitted records and hand off to a human in Chatwoot without leaving the coherent portal experience or exposing staff-only data. A second Perfex module can register at least one read resource and one standard action via the SDK without editing Perfex-MCP core.
