# Notification Matrix

Notifications use native Perfex email templates/merge fields and Notifications Inbox where installed. No hard-coded email body. Sensitive content is minimized; deep links require authorization.

| Event | Staff in-app | Email | Client | Webhook/event | Chatwoot | Future push |
|---|---|---|---|---|---|---|
| MCP client created/rotated/revoked | security/admin | optional security email | no | yes | no | optional |
| repeated MCP denied/high-risk calls | security Attention + admin | threshold only | no | yes | no | optional |
| provider/OpenClaw unhealthy | admin/ops Attention | threshold/escalation | no | yes | no | optional |
| AI budget threshold/exceeded | admin + affected staff banner | configurable | client-safe only if service unavailable | yes | no | optional |
| sensitive action awaiting approval | approver | yes per Approval policy | only if client is approver | yes | no | optional |
| action executed/failed | actor; owners if configured | only meaningful external/business events | if action affects client and owner module policy says so | yes | maybe if support context | optional |
| handoff requested/queued | support team/contextual | configurable | portal status | yes | conversation created/updated | optional |
| human joined/replied | staff via Chatwoot/native bridge as configured | not duplicate spam | client portal + configured delivery | yes | source event | optional |
| handoff resolved | contextual | optional | client status | yes | source event | optional |
| Chatwoot connector failed/stale | admin/ops Attention | threshold | client sees honest unavailable/queued state, not technical detail | yes | n/a | optional |
| Attention created/escalated | role/owner | policy | client-safe variants only | yes | only if support-owned rule | optional |
| Watch triggered/failed | owner/role | policy | client-safe if client-owned | yes | optional action | optional |
| update available/failed | admin | optional | no | no/public webhook optional | no | no |

## Merge fields

Safe examples: actor name, action label, resource human reference, result/status, timestamp, deep link, correlation/reference code, provider/integration name. Never include model/provider tokens, MCP secrets, Vault values, OpenBao values, session tokens or protected prompt excerpts.
