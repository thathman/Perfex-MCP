# AI Gateway & OpenClaw Adapter

## Purpose
Provide one provider/runtime-neutral entry point for Ara. OpenClaw is the preferred orchestration runtime, but business modules and UI never depend directly on OpenClaw APIs or a specific model vendor.

## AI Gateway request
A run request contains: Principal, surface, Ara mode, explicit active context, permitted capability/action catalogue, data classification constraints, budget/usage policy, desired runtime profile, correlation id, timeout/cancellation metadata and optional conversation/session reference.

## AI Gateway responsibilities
- fresh Principal/context validation;
- context retrieval/minimization;
- data classification/redaction;
- runtime/model routing;
- bounded tool catalogue;
- timeout/retry/cancel semantics;
- response/tool schema validation;
- source/provenance mapping;
- usage/cost metadata where provider exposes it;
- audit/run state;
- degraded/error mapping;
- never treating a model claim as successful action evidence.

## OpenClaw adapter
The adapter translates AI Gateway runs into approved OpenClaw sessions/agent invocations and correlates runtime references. OpenClaw may consume the Perfex MCP server as an outbound MCP server when that deployment pattern is useful. Native Ara can also dispatch internal Perfex actions directly under the same Principal/correlation, avoiding a forced Perfex→OpenClaw→public MCP→Perfex loop for every operation.

OpenClaw runtime/session state is not authority. Every Perfex tool/action call reauthorizes at execution.

## Secret/network policy
OpenClaw endpoint credentials/tokens are OpenBao-managed references. Prefer file/secret-handle injection over inline tokens. HTTPS/WSS and configured host validation required; local/private endpoint allowances are explicit deployment policy, not arbitrary user URLs.

## Model/provider abstraction
Runtime profile may select agent/model/policy, but business code sees only Gateway contracts. Future direct model/fallback adapters implement the same interface. OpenClaw failure can degrade Ara; it must not force business-record fallback mutations.

## Tool strategy
Ara/OpenClaw receives only tools relevant to current caller/context/mode. Broad “all tools always” catalogues are discouraged. Tool results include source/freshness and stable target references. Untrusted record/file content is treated as data and cannot redefine system/action policy.

## Run states
`queued`, `running`, `input_required` where supported, `awaiting_confirmation`, `awaiting_approval`, `succeeded`, `failed`, `cancelled`, `budget_blocked`, `provider_unavailable`.

## Failure handling
Timeout/429/5xx/auth/provider-down map to safe statuses and retryability. Automated retry is limited to non-side-effecting provider calls; tool/action retry is controlled by its idempotency contract. A failed runtime never causes silent fallback to a more privileged provider.

## Tasks
1. Define runtime adapter interface and DTOs.
2. Implement data-policy/redaction/context-budget service.
3. Add AI profile metadata and OpenBao secret refs.
4. Implement OpenClaw adapter with timeout/cancel/error mapping.
5. Add tool bridge to Registry/Action Dispatcher.
6. Add run/usage/cost/health persistence.
7. Add staging health/probe diagnostics.
8. Add provider-outage and prompt/tool-injection tests.

## Acceptance
- Controller/UI code contains no direct OpenClaw/model SDK logic.
- Swapping fake adapter for OpenClaw changes no business-service code.
- Provider failure cannot fabricate or partially apply a business action.
- Runtime receives only authorised/minimized context and no generic secrets.
- Each run/action is traceable by correlation id without retaining unnecessary sensitive prompts.
