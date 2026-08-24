# Test Plan

## Test strategy

Testing proves both business usefulness and inability to exceed authority. Each build phase must add automated tests before widening tool/action exposure.

## Unit

- Principal resolution and permission composition.
- Context provider filtering/minimization/provenance.
- Registry validation, duplicate IDs/version handling.
- Action risk/policy/confirmation selection.
- Idempotency key generation/replay outcome.
- Redaction/data classification.
- MCP schema serialization/current protocol headers.
- Chatwoot handoff packet minimization.
- updater version/checksum/archive/migration validation.

## Integration

- Perfex core model/service adapters using test DB fixtures.
- OpenClaw adapter success/timeout/auth failure.
- OpenBao secret-reference retrieval failure/rotation.
- Chatwoot create contact/conversation/private note/assignment + webhooks.
- shared integration runtime retry/backoff/DLQ/replay.
- optional module registration/unregistration.

## Migration

- fresh install from no tables.
- contiguous migration progression for every published version, including deliberate no-op targets.
- repeated migration safety.
- failed migration recovery instructions and updater state.
- uninstall/disable does not destroy business records or external truth.

## API/MCP

- MCP `2026-07-28` `server/discover`, tools/resources list/read/call.
- no retired session/initialize assumptions.
- auth scope and record restrictions.
- revoked/expired client.
- malformed JSON-RPC/schema/headers.
- pagination/filter bounds.
- rate limiting.
- idempotent standard writes.
- error envelope and correlation ids.
- cache invalidation when registry/policy changes.

## Permissions/security

Mandatory negative tests:
- staff without invoices permission asks Ara/MCP for invoice.
- client A asks for client B.
- contact with project but no billing permission requests invoice.
- hidden record existence/count cannot leak through search/attention.
- MCP client scope without record grant.
- model attempts unregistered tool/action.
- prompt-injected file/record asks to reveal Vault/OpenBao secret.
- CSRF, XSS payload rendering, SQL/filter injection, SSRF endpoint config, webhook replay, forged signature.

## Admin UI

Responsive settings/health/action catalogue/MCP client/audit views; keyboard/focus; empty/error/loading; destructive confirmation; policy validation; dark/light compatibility with Perfex theme conventions.

## Client Portal

Mobile-first Ara; active customer scope; contact permissions; logout/membership revocation mid-session; human handoff; queued/offline support; no staff-only source cards; notifications/deep links.

## Chatwoot handoff E2E

1. authenticated client requests human;
2. durable mapping resolved/created;
3. Chatwoot conversation created in expected inbox/team;
4. private handoff note includes only approved context;
5. human-active state reflected without transcript mirroring;
6. duplicate/out-of-order webhooks do not regress state;
7. resolved conversation returns Ara to responder mode;
8. Chatwoot outage queues/retries and tells client truthfully.

## Failure/retry

Kill OpenClaw, OpenBao and Chatwoot separately. Simulate 429/500/timeouts, duplicate webhook, delayed webhook, action partial failure, DB deadlock, cron overlap. Verify no duplicate business side effects, safe retry classification and visible DLQ/Attention.

## Updater/release

- semantic version matches module header/manifest/tag.
- package `perfex_mcp.zip` contains top-level `perfex_mcp/`.
- checksum exact.
- reject non-HTTPS/unapproved release URL.
- reject traversal/null-path archive entries.
- reject missing bootstrap/manifest/migration target.
- create backup before replacement.
- restore files only before DB migration begins; after migration starts, fail closed and retain backup/history for operator review.
- never rewrite existing published tag/release.

## Regression E2E scenarios

- Staff Ask→Draft→Act on project task.
- MCP OpenClaw discover→read→standard write.
- Client invoice explanation→human handoff.
- Property-monitor evidence→Ara investigation (when module installed).
- Hosting renewal recommendation→approval-gated action (later phase).
- Module disabled removes its tools without corrupting conversations/audit.

## Release exit

Zero critical/high security findings; all permission-negative suites green; migrations rehearsed; staging integrations proven; updater package verified; accessibility/responsive checklist passed; no credentials in repository/artifacts/log fixtures.
