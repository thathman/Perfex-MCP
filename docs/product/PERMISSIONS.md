# Permissions and Security Matrix

## Canonical permission approach

Use Perfex native staff permissions plus module capabilities. A permission never grants record access by itself; customer/contact/project/property ownership/scope is also evaluated. Client contacts use separate explicit feature permissions.

## Staff permissions

| Capability | View | Create/Use | Edit | Delete/Revoke | Approve | Configure |
|---|---|---|---|---|---|---|
| Ara | `perfex_mcp.ara.view` | `perfex_mcp.ara.use` | conversation manage as own | archive own where policy | n/a | `perfex_mcp.ara.configure` |
| AI runtime profiles | read | create | edit | disable | n/a | `perfex_mcp.providers.manage` |
| Action catalogue | `perfex_mcp.actions.view` | execute requires target action permission | policy edit | n/a | via Approval Workflow | `perfex_mcp.actions.manage` |
| MCP clients | `perfex_mcp.clients.view` | create/rotate | scope edit | revoke | optional approval | `perfex_mcp.clients.manage` |
| Attention | `perfex_mcp.attention.view` | acknowledge/snooze | n/a | dismiss if provider allows | n/a | provider config manage |
| Watches | view | create/run | edit | disable/archive | sensitive actions via approval | `perfex_mcp.watches.manage` |
| Handoff/support refs | `perfex_mcp.handoff.view` | request/handoff | safe mapping manage | unlink only with policy | n/a | `perfex_mcp.handoff.configure` |
| Audit | `perfex_mcp.audit.view` | n/a | never | never ordinary path | n/a | retention/security admin only |
| Updates | view | install | policy edit | n/a | n/a | `perfex_mcp.updates.manage` |

Target-domain permissions remain mandatory. Example: `perfex_mcp.actions.execute` cannot create an invoice unless caller also has the canonical invoice-create capability and target scope.

## Client contact permissions

Feature-gated per contact/customer:
- `client_ara_use`
- `client_ara_view_projects`
- `client_ara_view_billing`
- `client_ara_view_properties`
- `client_ara_view_knowledge`
- `client_ara_request_support_handoff`
- owning-module client actions such as booking/request/renewal.

No client permission exposes staff notes, internal margins, other clients, generic Audit, provider config, MCP administration or Vault secret values.

## MCP scopes

Examples:
- `customers.read`
- `contacts.read`
- `projects.read`
- `tasks.read`
- `tasks.create`
- `billing.read`
- `support.summary.read`
- `attention.read`
- `vault.metadata.read`
- namespaced module scopes.

MCP client also carries explicit customer/property/project restrictions where needed. A broad scope never overrides record restrictions.

## Risk classes

- **read:** automatic after permission/scope check.
- **draft:** no business side effect; application/send still separate action.
- **standard_write:** reversible/routine; confirmation configurable.
- **sensitive_write:** financial/access/connector/Vault/security/commercial; explicit confirmation and often Approval/step-up.
- **high_impact:** destructive/bulk/irreversible/external-critical; mandatory impact preview plus step-up/Approval or unavailable to AI/MCP.

## Security requirements

- Server-side auth on every UI/API/MCP/tool/action request; UI hiding is not security.
- CSRF tokens on browser state changes.
- Output encoding/sanitization for model/user content; strict CSP-compatible rendering where possible.
- Parameterized queries and allowlisted sort/filter fields.
- Upload type/size/content validation and destination authorization; no model-controlled arbitrary path.
- SSRF prevention for provider/integration endpoints: HTTPS policy, host validation/allowlist, private-network rules, redirect/DNS-rebinding controls where relevant.
- OpenBao for service secrets; Vault for client credentials; no secrets in options/logs/prompts/merge tags.
- Credential/token expiry, rotation, revocation and fingerprinting.
- Webhook signature verification, timestamp/replay checks and idempotency.
- Rate limiting by Principal/action/tool/provider.
- Step-up/Approval for privileged credential administration and high-risk actions.
- Negative authorization tests for cross-customer/contact/project/property and hidden-record count leakage.
- Prompt/tool injection protection: model output cannot invent tool name or bypass schema; untrusted record/file content is data, not policy.

## Ownership rules

Staff sees only what native Perfex permissions and record rules allow. Client principal is bound to authenticated contact + active customer membership/permissions. Service/MCP principals have explicit grants. Module registration contributes capability definitions but grants no access.

## Sensitive activity logging

Always audit MCP client create/rotate/revoke, provider/secret-reference changes, denied high-risk calls, Approval/step-up outcomes, Chatwoot mapping changes, bulk/sensitive actions, updater operations and permission/policy changes. Secret values are never logged.
