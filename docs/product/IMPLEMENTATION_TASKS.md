# Implementation Tasks — First Build Phases

Tasks are dependency ordered and map to `BUILD_PHASES.md`. Each task is complete only when code, migration/docs and tests for its scope are green.

## Phase 0

### P0-001 Freeze runtime baseline
Record supported Perfex version(s), PHP, MySQL/MariaDB, server extensions and CI matrix. **Complete when:** CI/runtime document exists and tests can boot a clean fixture.

### P0-002 Create module skeleton
Add `perfex_mcp/` bootstrap, activation/deactivation hooks, language/config/assets/services directories without core patching. **Complete when:** module installs/activates/deactivates on clean staging.

### P0-003 Establish migration chain
Create initial contiguous migration target and schema helper conventions. **Complete when:** fresh install + re-run tests pass and installed version advances correctly.

### P0-004 Create settings/readiness service
Typed settings with no plaintext secrets; readiness statuses for OpenBao/OpenClaw/Chatwoot/shared runtime. **Complete when:** admin diagnostics show configured/unconfigured/degraded without exposing secrets.

### P0-005 Add audit/run primitives
Implement append-only audit writer, correlation IDs and safe metadata/redaction utility. **Complete when:** security-sensitive test event is queryable and secret fixture is absent from logs/DB.

### P0-006 Add shared integration-runtime adapter interface
Consume canonical webhook/outbound job/retry/DLQ/replay contract; no bespoke Chatwoot controller. **Complete when:** fake integration job can succeed/retry/dead-letter in tests.

### P0-007 Add hardened updater foundation
Implement manifest/release handler/checksum/archive/migration/backup/update-history services using Magic Login rules adapted to `perfex_mcp`. **Complete when:** malicious/mismatched fixtures are rejected and pre-migration restore works.

### P0-008 CI quality gates
PHP lint/static checks, unit/integration tests, package layout validator and secret scan. **Complete when:** branch CI passes on docs+module skeleton.

## Phase 1

### P1-001 Principal Resolver
Staff/contact/service/MCP principal DTO + current authority resolution. **Complete when:** positive/negative fixture matrix passes.

### P1-002 Capability Registry
Register typed resources/context providers with owner module, permission, classification and surfaces. **Complete when:** duplicate/invalid registration is rejected deterministically.

### P1-003 Action Registry
Stable action definition/version, schema validation, risk, target resolver, surface flags. **Complete when:** fixture action appears only to authorised principal.

### P1-004 Action policy/preview
DB policy overrides, confirmation/approval hooks, dry-run contract. **Complete when:** sensitive fixture cannot execute without configured gate.

### P1-005 Idempotent action dispatcher
Idempotency store/result replay and domain-event/audit emission. **Complete when:** duplicate create request produces one business mutation.

### P1-006 Context Engine
Explicit route/entity context, provider fan-out, minimization and provenance. **Complete when:** customer/project fixtures return bounded typed context with source timestamps.

### P1-007 Core read adapters
Customers/contacts/leads/projects/tasks/commercial/billing/custom fields and safe legacy-ticket summary. **Complete when:** each has permission-negative tests.

### P1-008 Core low-risk actions
Task/reminder/request-safe actions through native services; no direct DB mutation if normal service exists. **Complete when:** UI test harness executes and audit captures verified result.

### P1-009 Module SDK hooks
Namespaced registration hooks/interfaces and diagnostics. **Complete when:** fixture module can add one resource and one action without Perfex-MCP code change.

## Phase 2

### P2-001 AI Gateway contract
Run request/response, mode, tool catalogue, context budget, policy envelope. **Complete when:** fake runtime executes Ask/Draft with no provider-specific code in controllers.

### P2-002 OpenBao secret references
Provider credentials resolved only through approved broker. **Complete when:** secret never persists in module DB/logs and unavailable broker fails closed.

### P2-003 OpenClaw adapter
Authenticated request/session correlation, timeout/cancel/error mapping, tool/action bridge. **Complete when:** staging `doctor/probe` equivalent and controlled tool call succeed.

### P2-004 Native admin Ara shell
Global launcher + contextual panel with active-context indicator, sources, streaming, retry. **Complete when:** works on desktop/mobile and never obscures primary actions.

### P2-005 Ask/Draft flows
Core record questions and drafts with fact/inference/source distinction. **Complete when:** E2E fixtures cite exact source records and stale sources are marked.

### P2-006 Limited Act flow
Action proposal card, preview, confirmation/approval state and verified outcome. **Complete when:** task-create E2E succeeds once and denied action cannot execute.

### P2-007 Usage/budget/health
Run usage, latency, provider health, budget blocks and admin visibility. **Complete when:** budget/provider failure yields clear degraded state.

## Immediate next task

Start with **P0-001**, then P0-002. Do not implement UI or OpenClaw calls before the Principal/Registry foundations are in place.
