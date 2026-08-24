# Dependency Map

## Required for MVP

```text
Perfex CRM
  └─ Perfex-MCP / Intelligence
      ├─ shared integration runtime contract
      ├─ canonical identity/external-ID mapping contract
      ├─ OpenBao secret broker
      ├─ OpenClaw runtime
      └─ Chatwoot human-support transport
```

These are operational dependencies for the full MVP. Module activation should still fail gracefully/readiness-gate external features when an integration is temporarily unavailable.

## Optional integrations

```text
Perfex-MCP
  ├─ Vault ─ metadata/access/secret-use handles
  ├─ Forms ─ submissions/form requests
  ├─ Onboarding ─ journeys/checks/deep links
  ├─ Property Monitor ─ health/evidence/Attention
  ├─ Domain & Hosting ─ renewals/services
  ├─ Magic Login ─ login-link actions
  ├─ Handoff ─ handover generation/status
  ├─ Appointment ─ availability/booking
  ├─ Notifications Inbox ─ durable notification presentation
  ├─ Approval Workflow ─ approval gates
  ├─ Organisation Self-Service ─ client-safe organisation actions
  ├─ Verified Receipts/Bachs ─ verified financial evidence/actions
  ├─ Documenso ─ signing status/actions
  ├─ Uptime Kuma ─ optional monitoring signal source
  ├─ OJS integrations
  └─ WordPress/WooCommerce integrations
```

Absence of an optional module removes its registered capabilities; no fatal install dependency or circular module requirement.

## Provider / consumer relationship

Perfex-MCP **consumes** authoritative domain services/resources/events and **provides** AI/MCP transport, context composition, action policy, audit, attention/watch and assistant UX. It does not take ownership of the other module's records.

## Event relationships

- Providers emit domain events; Intelligence may create/update Attention or trigger explicit Watch policy.
- Intelligence emits action/run/client/handoff/health events; Notifications/Automation/Onboarding may consume them.
- Chatwoot/provider callbacks enter shared integration runtime and are normalized before Perfex-MCP consumes them.

## API relationships

- Module-to-module: PHP service/hook contracts + events first.
- External deterministic clients: shared REST API surface.
- External agents: MCP.
- AI runtime: AI Gateway adapter and/or MCP depending deployment path.

## Circular dependency prevention

Perfex-MCP never requires an optional module to install. Optional modules should not require Perfex-MCP to store their business data; they register capabilities only when Intelligence is present. Common contracts should live in a small shared interface layer or be discovered through hooks to avoid mutually required activation.
