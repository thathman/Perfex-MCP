# Chatwoot Human Handoff

## Canonical ownership
Chatwoot is the source of truth for **new** support conversations, messages, attachments, team/agent assignment, routing/status, CSAT and Captain. Perfex owns customer/contact/project/property/commercial context and stores only safe support mappings/references/snapshots/freshness. Historical Perfex tickets may remain accessible read-only during transition; the handoff feature does not create a new competing Perfex ticket engine.

## Client goal
A logged-in client speaks to Ara in the native Perfex portal. If the client asks for a human, Ara cannot safely resolve, or policy requires escalation, the same coherent experience transitions to human support without asking the client to re-identify unnecessarily or opening a separate disconnected widget.

## Handoff triggers
- explicit “human/support/person” request — immediate;
- insufficient evidence/confidence for a consequential support answer;
- policy categories such as billing dispute, account compromise, cancellation/legal/security where configured;
- deterministic incident/SLA/escalation rules;
- repeated unresolved exchange only as a bounded signal, never opaque emotional manipulation.

## Handoff sequence
1. Validate authenticated client contact/customer and support eligibility.
2. Resolve durable Perfex↔Chatwoot contact/inbox mapping; email is fallback evidence, not canonical key.
3. Determine safe customer/project/property/request context and route/team policy.
4. Create or reuse the appropriate Chatwoot conversation.
5. Set safe custom attributes/references and correlation id.
6. Add a private internal handover note summarizing client request, Ara findings, source freshness and relevant Perfex deep links/references. Exclude secrets/internal financials/unrelated notes.
7. Assign team/agent where policy specifies.
8. Store/update `tblperfex_mcp_support_refs` mapping/status snapshot only.
9. Client sees queued/human-active/waiting/resolved truth.
10. Chatwoot webhooks update status/freshness through shared integration runtime.
11. When human is active, Ara client-responder mode is disabled; optional agent copilot may remain internal later.
12. On resolution, Ara may resume client responder mode.

## Chatwoot API usage
Current API capabilities expected: contact creation/mapping, conversation create, custom attributes, message/private note creation, team/agent assignment and webhook events. Provider implementation stays behind connector/service adapter.

## Inbox routing
Preserve established Perfex support model: one Support Space/inbox per top-level brand/client-family where configured rather than inbox-per-property by default; e.g. a parent organisation may use one inbox with signed/safe Property context. Route policy is configurable and durable mappings are authoritative.

## Offline/degraded behavior
If Chatwoot is unavailable, tell client the support team cannot be connected immediately, queue retry through integration runtime when policy permits, preserve correlation/handoff request state and notify staff/Attention. Do not claim a human has been contacted until provider acknowledgement exists.

## Webhooks
Verify signatures/auth according to Chatwoot connector capability, deduplicate provider event id/payload fingerprint, handle out-of-order updates monotonically, retry processing, DLQ irrecoverable errors and allow safe replay. Provider stale state is labeled stale, not treated as closed/zero.

## Privacy
Never send passwords, session tokens, Vault/OpenBao content, staff-only notes, margins, unrelated client records or unnecessary PII. Custom attributes are allowlisted. Handover private note content is minimal and auditable at safe-summary level.

## Tasks
1. Implement Chatwoot connector interface over shared integration runtime.
2. Implement identity/inbox mapping resolver.
3. Implement safe handoff packet builder + allowlist.
4. Implement create/reuse conversation, private note and assignment.
5. Persist support reference/snapshot/freshness.
6. Implement normalized webhook consumers and state machine.
7. Build portal queued/human-active/resolved states.
8. Add staff diagnostics/deep link.
9. Add negative privacy/duplicate/out-of-order/offline tests.

## Acceptance
- Explicit human request is never blocked by Ara trying to retain the conversation.
- Client does not re-enter known identity data.
- Human receives useful safe context.
- Chatwoot remains conversation truth; Perfex does not mirror transcript.
- Duplicate/out-of-order webhook cannot corrupt handoff state.
- Chatwoot outage is stated truthfully and recoverably.
- No cross-client/internal/secret data leaks.
