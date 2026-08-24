# Action & Capability Registry

## Purpose
Create one canonical catalogue of what Perfex Intelligence may read and what it may do. UI, Ara, MCP, REST, Watch/Automation and optional modules reuse these definitions instead of implementing separate mutations.

## Scope

### Capability/resource definition
Required fields: stable namespaced id, version, owner module, label/description, input/query schema, output schema, required canonical permission, scope resolver, data classification, provenance/freshness behavior, supported surfaces, client-safe flag, rate/usage class and availability callback.

### Action definition
Required fields: stable id/version, owner module, target type(s), input/output schema, required permission, scope resolver, risk class, preconditions, confirmation rule, Approval Workflow/step-up hook, idempotency policy, audit policy, preview/dry-run handler, execution handler, event names and explicit surface exposure (`admin`, `client`, `ara`, `mcp`, `api`, `automation`).

Registration does not grant permission and does not automatically expose an action on every surface.

## Risk policy

- `read`: no mutation.
- `draft`: produces content only.
- `standard_write`: routine/reversible; confirmation configurable.
- `sensitive_write`: financial/access/security/commercial/provider write; confirmation mandatory, Approval/step-up configurable/usually required.
- `high_impact`: destructive, broad/bulk, irreversible or infrastructure-critical; impact preview mandatory and may be unavailable to Ara/MCP.

## Execution sequence

1. Resolve current Principal fresh.
2. Resolve target and customer/project/property scope.
3. Confirm action is enabled for current surface/principal type.
4. Check canonical permission and record visibility.
5. Validate schema and preconditions.
6. Evaluate risk/policy and preview/impact.
7. Obtain confirmation/approval/step-up where required.
8. Claim idempotency key.
9. Call authoritative domain service.
10. Verify result; emit domain/activity/audit events.
11. Persist safe action run/result and return typed result.

No model output can skip this sequence.

## Data
Definitions live in code/runtime registration. `tblperfex_mcp_action_policies` stores deployment overrides/exposure. `tblperfex_mcp_action_runs` stores execution evidence/idempotency. Do not create DB rows for every definition just to make the registry work.

## Perfex core adapters
MVP resources/actions wrap existing Perfex model/service behavior for customers/contacts/leads/projects/tasks/commercial/billing. Use hooks/services that preserve normal Perfex side effects, notifications and activity logging; do not direct-update tables merely because the AI path can.

## Optional-module contract
Modules register namespaced definitions after their own initialization. Invalid/duplicate definitions are rejected with diagnostics and the module contribution stays unavailable. Disabling a module removes its runtime definitions without deleting historic action/audit evidence.

## Preview / dry-run
Preview is side-effect free and returns eligibility, normalized input, target identity, proposed mutation/external operation, dependency/warning list, expected notifications, confirmation/Approval/step-up requirement, and whether execution may partially succeed. Preview never returns secret values.

## Idempotency
Required for creates, sends, external calls and retry-prone writes. Key scope includes principal/action/target and normalized request fingerprint. Repeated compatible request returns prior result; conflicting input for the same key returns `IDEMPOTENCY_CONFLICT`.

## Tasks
1. Implement DTO/interfaces and registry validation.
2. Implement Principal/scope evaluator integration.
3. Implement action policy repository and effective-policy resolver.
4. Implement preview dispatcher.
5. Implement idempotent execution dispatcher and run store.
6. Implement audit/domain-event emission.
7. Add admin Action Catalogue/Policy diagnostics.
8. Add fixture module/resource/action and negative tests.
9. Add core read adapters then low-risk task/reminder actions.

## Acceptance
- Same fixture action executes through native test UI and MCP without duplicate business logic.
- Caller lacking target permission/scope is denied even when action is registered/enabled.
- Duplicate execution cannot duplicate side effects.
- Sensitive/high-impact policy cannot be weakened by model text or automation.
- Disabled module removes capability cleanly while historical audit remains readable.
