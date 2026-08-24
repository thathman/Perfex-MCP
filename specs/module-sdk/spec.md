# Perfex Intelligence Module SDK Contract

## Purpose
Allow every current/future Perfex module to become Ara/MCP/API/automation-aware without modifying Perfex-MCP core or creating circular install dependencies.

## Registration model
Perfex-MCP exposes stable hooks/interfaces. Optional module detects the contract at runtime and registers contributions; if Intelligence is absent, the module continues its own business function.

Conceptual hooks/interfaces (final naming follows Perfex conventions):

```php
hooks()->add_action('perfex_mcp/register_capabilities', ...);
hooks()->add_action('perfex_mcp/register_actions', ...);
hooks()->add_action('perfex_mcp/register_context_providers', ...);
hooks()->add_action('perfex_mcp/register_attention_providers', ...);
hooks()->add_action('perfex_mcp/register_events', ...);
```

Registration objects are typed/validated rather than loose arrays where practical.

## Required contribution metadata
Owner module/id/version; stable namespaced IDs; label/description; input/output schemas; permission/scope resolver; client-safe/surface exposure; classification; provenance/freshness; rate/risk; health/availability callback. Actions additionally require preconditions, idempotency and audit policy.

## Ownership rule
The registering module owns its business data and execution implementation. Perfex-MCP owns registry/exposure/policy/audit orchestration. It may store action-run metadata, not duplicate the module's authoritative record.

## Optional modules / no circular dependency
A module may register when Perfex-MCP is present, but should not require Perfex-MCP merely to install unless the module's entire purpose is Intelligence. Perfex-MCP never hard-depends on optional module tables/classes. Disabled/uninstalled module contributions disappear cleanly.

## Event contract
Modules emit namespaced domain events with stable version, occurred time, resource references, safe payload and correlation id. Intelligence consumes through shared event/integration runtime where async. Events are facts, not commands.

## Client-safe variants
A resource/action available to staff is not automatically client-safe. Module must explicitly define client surface behavior and the underlying contact/customer permission rule.

## Secret rule
SDK registration cannot expose raw Vault/OpenBao/provider secret callbacks as generic resources. Secret-use operations require dedicated owner-module capability handle/action and normal high-risk policy.

## Developer diagnostics
Admin diagnostics list registered contributions, source module/version, schemas, permissions, surfaces, health and validation errors. Invalid contribution is disabled with an actionable error; it must not crash global registry.

## Contract testing
Perfex-MCP provides fixture/test helpers for registration validation, Principal/scope checks, schema validation, idempotency and client isolation. Ecosystem module Definition of Done includes at least one positive and negative Intelligence contract test when it contributes capabilities.

## Tasks
1. Define registration interfaces/DTOs/version contract.
2. Implement hook discovery and validation.
3. Implement source-module lifecycle cleanup/cache invalidation.
4. Add diagnostics UI.
5. Build test fixture module.
6. Publish developer examples for read resource, standard action, Attention provider and client-safe capability.
7. Integrate first real module (recommended: Property Monitor or Forms) as proof.

## Acceptance
- A second module adds resource/action without Perfex-MCP code edit.
- Disabling module removes tools immediately and does not break historic audit.
- Invalid registration fails locally/diagnostically.
- No permission/surface is implicitly granted.
- No circular install dependency is required for ordinary optional modules.
