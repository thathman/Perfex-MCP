# Attention & Watch

## Purpose
Give Ara proactive usefulness without asking an LLM to repeatedly scan the database and invent urgency. Deterministic domain providers state what still needs attention; Ara explains/ranks/recommends around those facts.

## Attention semantics
Notification = something happened/delivery. Attention = a condition is still active and deserves awareness/action. Reading/dismissing a notification does not resolve the source condition.

## Attention provider contract
Provider declares id, owner module, source condition evaluator/event mapping, dedupe key, audience/scope, priority rule, title/reason, source evidence/freshness, client-safe variant, primary registered action/deep link and resolution rule.

States: `open`, `acknowledged`, `snoozed`, `resolved`, `dismissed` only where policy allows, `stale_unknown` when evidence cannot refresh.

Examples: overdue invoice, renewal window, degraded property, blocked project, unanswered client communication, pending approval, failed automation/provider, Vault rotation request.

## Watch semantics
A Watch is explicit: condition + source + trigger mode + intended action/notification + owner principal + permissions. It is not mysterious autonomous monitoring.

Examples:
- watch a domain renewal and create a task at 60 days;
- watch invoice status and notify owner if still overdue Friday;
- watch Property Monitor health and escalate confirmed outage.

## Execution
Prefer domain events. Use bounded schedule refresh when event coverage cannot prove the condition. Trigger invokes only registered actions/notification services and inherits their risk/Approval/idempotency. Creating/editing a Watch never grants future authority beyond its run-as/service policy.

## AI boundary
Ara may summarize/rank existing Attention and suggest a Watch. AI-derived attention is clearly labeled/proposed unless an explicit policy accepts it. Deterministic source rule/evidence remains visible.

## Data
`tblperfex_mcp_attention_items` holds current condition references/snapshots/dedupe/freshness. `tblperfex_mcp_watches` holds definitions/schedule/event policy. Full generic automation workflow graphs are deferred.

## Tasks
1. Define provider/condition contracts.
2. Implement dedupe/state/resolution rules.
3. Add core fixture providers.
4. Integrate Notifications Inbox/native notifications.
5. Implement Watch event/schedule evaluator.
6. Route Watch actions through Action Registry/Approval.
7. Add stale-source and retry/DLQ handling.
8. Add Ara/Home briefing consumer.

## Acceptance
- Attention resolves only when source condition resolves or domain policy explicitly permits dismissal.
- One repeated condition deduplicates predictably.
- Client sees only client-safe Attention.
- Watch cannot weaken action permissions/Approval.
- Provider failure yields stale/unknown, never false healthy/resolved.
