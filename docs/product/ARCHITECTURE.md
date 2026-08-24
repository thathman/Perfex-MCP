# Architecture

## System context

```text
Admin / Client Portal / API / MCP / Automations
                  │
          Perfex Intelligence
  ┌───────────────┼────────────────┐
  │ Context       │ Action/Capability│
  │ Engine        │ Registry         │
  └───────┬───────┴────────┬─────────┘
          │                │
      AI Gateway       Domain Services
          │                │
  OpenClaw adapter     Perfex core + module adapters
          │
  models/runtimes/tools

External: Chatwoot, OpenBao, connectors
via shared integration runtime
```

## Dependency direction

UI/MCP/API adapters depend on application services. Application services depend on stable Perfex/domain/module contracts. Provider adapters depend on application interfaces. Business modules never depend on OpenClaw/Chatwoot SDKs directly and never call Perfex-MCP database tables to invoke capability.

## Planned module structure

```text
perfex_mcp/
  perfex_mcp.php
  install.php
  config/
  controllers/
  models/
  services/
    Principal/
    Context/
    Capabilities/
    Actions/
    Intelligence/
    MCP/
    Attention/
    Handoff/
    Audit/
    Integrations/
    Updates/
  libraries/
  helpers/
  views/admin/
  views/client/
  assets/
  migrations/
  language/english/
  update_manifest.json
  release_handler.php
```

Exact class names follow the Perfex/CodeIgniter conventions in the supported runtime while keeping business logic out of controllers/views.

## Components

### Principal Resolver
Maps current staff/contact session, service account or MCP credential to a canonical Principal plus permissions and record/customer scope. All tools/actions re-resolve current authority at execution.

### Context Engine
Accepts explicit surface context (`area`, route, entity type/id, customer/contact/project/property where applicable). Providers return minimal typed slices with provenance/freshness. No DOM scraping and no unrestricted “load everything about client” query.

### Capability Registry
Code/runtime registry of read resources and context providers. Registration includes schemas, permission requirements, data classification, supported surfaces, output minimization and owner module.

### Action Registry
Registry of mutations/consequential operations. Definition includes id, version, target types, required capability, risk, schemas, preconditions, confirmation/approval/step-up, idempotency, audit, preview/dry-run and allowed surfaces. Policy overrides live in DB; definitions live in code/module registration.

### AI Gateway
Single entry for AI runs. Responsibilities: principal, context, runtime/model selection, redaction/classification, budgets, timeouts, tool catalogue, validation, usage, audit/correlation and degraded states. OpenClaw is adapter `openclaw`; direct model adapters are optional later.

### Native Ara
First-party UI/client of the AI Gateway and registries. It does not use the public MCP endpoint as its mandatory internal path. OpenClaw may call the MCP endpoint when orchestrating externally, while internal actions can be dispatched directly under the same Principal/correlation.

### MCP Server
Stateless HTTP adapter for MCP `2026-07-28`, with current protocol headers, `server/discover`, tools/resources and scoped auth. No legacy SSE/session implementation in initial release.

### Handoff Controller
Creates/links Chatwoot conversations using safe mappings/context; tracks reference and freshness only. Webhooks arrive through shared integration runtime. It cannot become a shadow Chatwoot inbox.

### Attention / Watch
Attention holds current actionable conditions from deterministic providers. Watch stores explicit monitored condition + response policy; execution uses event/schedule runtime and registered Actions. Notification delivery is delegated.

### Audit / Usage
Append-only/high-integrity audit evidence for security/consequential events; separate operational run logs and usage summaries. Prompt retention is minimized/configurable.

## Data ownership boundaries

- Perfex core/modules: business records.
- Perfex-MCP: AI conversations/runs, action policy/runs, MCP principals/config, Attention/Watch state, safe support references, audit/usage metadata.
- Chatwoot: new support conversations/messages/attachments/assignment/status/CSAT.
- Perfex Vault: client-facing secrets/credentials/access packs.
- OpenBao: machine/service/API secrets.
- Forms: structured submissions.
- Onboarding: journeys/gates/completion orchestration.
- Approval Workflow: approval state/policy where installed.
- Shared integration runtime: external webhook delivery/ingestion/retry/DLQ/replay and canonical external mapping infrastructure.

## Jobs / cron

- provider health checks;
- budget/usage aggregation;
- expired/revoked client cleanup metadata;
- stale support-reference refresh where needed;
- Watch schedules;
- Attention provider refresh where event coverage is insufficient;
- updater daily check according to policy.

Jobs are bounded, idempotent, observable and use central retry/locking semantics. No long request performs uncontrolled work synchronously.

## Security boundaries

Browser session auth and CSRF apply to UI writes. MCP/service auth is separate machine Principal auth. Model/runtime receives only eligible context after classification/redaction. OpenBao/Vault secret values are not generic context. External URLs are allowlisted/validated against SSRF. Action execution reauthorizes and validates state immediately before mutation.

## Failure model

Provider failures return explicit degraded states and never mutate business truth. External integration failures produce retry/DLQ/Attention as policy dictates. A successful model response does not equal successful action; action success requires verified domain result. Stale connector data is marked stale/unknown, never fabricated as healthy/zero.
