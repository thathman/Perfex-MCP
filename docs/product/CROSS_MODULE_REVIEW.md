# Cross-Module Review

## Does another module already own this responsibility?

- Business records: Perfex core/domain module — Intelligence only reads/acts through contracts.
- Structured collection: Forms.
- Secrets/credential access: Vault for client-facing; OpenBao for machines.
- Onboarding journeys: Onboarding.
- New support conversations: Chatwoot.
- Approval state: Approval Workflow.
- Notification presentation/delivery: native Perfex/Notifications Inbox.
- Monitoring facts: Property Monitor/Uptime Kuma connector sources.
- Renewals: Domain & Hosting Manager/owning module.

## Could this become reusable platform capability?

Yes: Principal resolution, Action/Capability Registry, Context Engine, AI Gateway, audit/provenance, Attention/Watch contracts and module registration are reusable platform primitives and are intentionally centralized here rather than reimplemented per module.

## Does this duplicate Forms?
No. Ara may request/summarize/map Forms; Forms owns definitions/submissions.

## Does this duplicate Vault?
No. Intelligence gets safe metadata/capability handles only; raw secret access remains Vault-controlled and normally excluded.

## Does this duplicate Onboarding?
No. Intelligence exposes readiness checks/deep links/actions and can explain progress; Onboarding owns orchestration/gating.

## Does this duplicate Chatwoot?
No. No new inbox/message/assignment/status engine. Store only safe mappings/references/freshness; human handoff uses Chatwoot.

## Should integration use event rather than tight coupling?
Yes for lifecycle/state changes and asynchronous integrations. Synchronous service calls are reserved for immediate validated reads/actions. No ordinary cross-module table reads.

## Should AI/MCP consume this through service contracts?
Yes. Every module-facing capability must be registered through the shared contracts before it is exposed to Ara/MCP.

## Is API exposure needed?
Yes for action execution/admin/run state where external deterministic systems need it. Public exposure is explicit, versioned and separate from internal services.

## Client portal surfaces?
Yes: client-safe Ara, human handoff, contextual self-service. Admin tables/settings do not leak into portal.

## Where does it belong contextually?
Global launcher/Home plus record-level context on Customers/Contacts, Leads, Projects, Tasks, Contracts/Proposals/Estimates, Invoices/Payments/Subscriptions, Properties, Forms/Requests, Onboarding and supported module records.

## Merge fields?
Only safe notification/template metadata. Never prompts, API tokens, Vault/OpenBao secrets or unrestricted AI output as implicit trusted merge content.

## Notifications/email templates?
Yes, via native templates/Notifications Inbox; see `NOTIFICATIONS.md`.

## Cron jobs?
Yes for health, usage, schedules/Watch, stale refresh and updater checks; all bounded/idempotent/observable.

## Webhooks?
Yes, through shared integration runtime for Chatwoot and future providers; signed outbound semantic events.

## Audit trail?
Mandatory for MCP administration/calls, consequential actions, security/config changes, handoff mappings, provider changes and updates.
