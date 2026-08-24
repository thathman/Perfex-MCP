# Codebase Gap Analysis

## Current repository snapshot

At planning start the repository contains only `README.md` with `# Perfex-MCP`, on `main`, with a single initial commit and no production module tree, migrations, hooks, controllers, models, views, APIs, permissions, jobs, email templates, client integration, updater, release files, issues or PRs.

There is therefore no legacy implementation to preserve or migrate.

## Classification

| Area | Current | Target | Classification | Why |
|---|---|---|---|---|
| README | title only | truthful status/architecture/docs | REPLACE | existing content has no usable documentation |
| Perfex module bootstrap | absent | installable `perfex_mcp` | NEW | foundational |
| Migrations/data model | absent | contiguous versioned schema | NEW | required architecture |
| Hooks/services | absent | registry/context/principal/runtime hooks | NEW | no code exists |
| Controllers/views | absent | admin/client native surfaces | NEW | no UI exists |
| Permissions | absent | staff/contact/MCP scopes | NEW | security blocker |
| AI provider logic | absent | AI Gateway + OpenClaw adapter | NEW | core product |
| Action/Capability Registry | absent | shared internal contract | NEW | prevents duplication |
| MCP server | absent | current stateless protocol | NEW | core product |
| API | absent | versioned transport over services | NEW | design requirement |
| Chatwoot handoff | absent | safe mapping/handoff/webhook state | NEW | client support requirement |
| Attention/Watch | absent | extension-first deterministic model | NEW | proactive architecture |
| Module SDK | absent | namespaced registrations | NEW | ecosystem requirement |
| Audit/usage | absent | append-only/consequential + run logs | NEW | security/ops requirement |
| Cron/jobs | absent | health, usage, Watch, updater jobs | NEW | operational requirement |
| Email templates/notifications | absent | native templates/events | NEW | no hard-coded mail |
| Updater/release | absent | hardened Magic Login pattern | NEW | production lifecycle |
| Legacy compatibility | none | none before v1 | KEEP (none) | explicitly greenfield; do not invent migration baggage |

## Reuse/refactor/replace conclusion

There is no production code to reuse/refactor/remove. The repository can adopt the target architecture without backward-compatibility shims. Reference architecture is reused conceptually from the established Re:Solve/Ara product model and the hardened Magic Login updater, but Perfex-specific implementation must use Perfex services/hooks/permissions rather than copying another product's code.

## External gap / dependency risks

- Shared integration runtime and canonical external-ID mapping must be available or implemented as ecosystem platform contracts before Chatwoot production cutover.
- OpenBao secret broker must be reachable before real provider credentials are configured.
- Final supported Perfex/PHP/MySQL baseline must be frozen in Phase 0 and CI matrixed.
- Approval Workflow and Notifications Inbox may not yet be installed; interfaces must degrade/feature-gate cleanly.
