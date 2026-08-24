# Client Portal Ara

## Purpose
Give authenticated client contacts a first-class AI/self-service experience inside native Perfex while maintaining strict customer/contact permissions and seamless Chatwoot human handoff.

## Identity/scope
Principal derives from active Perfex contact session + customer relationship + contact feature permissions. Every request revalidates membership/access. Conversation context cannot preserve access after logout, permission change, suspension or customer reassignment.

## Client-safe capability set
Potential resources/actions only when owning module exposes them safely:
- own projects/tasks/deliverables/client actions;
- own invoices/payment/receipt status (never fabricate payment truth);
- own properties/monitoring status;
- client-visible knowledge/files;
- Forms requests/submissions that contact may access;
- Onboarding progress/actions;
- Appointment availability/booking;
- Hosting renewal/request flows;
- Organisation Self-Service requests;
- Support/Chatwoot handoff.

Excluded: staff notes, internal margins/risk reasoning, other customers, broad staff details, internal audit, MCP/provider settings, hidden properties, raw Vault/OpenBao values.

## UX
Ara is reachable from portal shell and contextual record pages. Mobile-first conversation workspace shows active customer/record context, concise sources, draft/action cards, human-support state and clear back-navigation. Client portal is not a reduced admin assistant.

## Human handoff
Explicit “speak to a human” acts immediately. Handoff follows `specs/chatwoot-handoff/spec.md`. During human-active state Ara does not pretend to be the human or race replies. Human responses are rendered from Chatwoot/provider source; transcript remains Chatwoot-owned.

## Requests versus support
Ara classifies by deterministic/controlled policy: actual support conversation routes to Chatwoot; structured new-scope/business request routes to Perfex Forms/Request/Opportunity/Proposal flow where owning modules define it. Do not turn every client ask into a support conversation.

## Sensitive actions
Client actions such as renew, approve, pay, sign, access credential or change account data route to owner module/action policy and may require confirmation, secure external flow, Approval or step-up. Ara cannot accept legal/commercial terms on the client's behalf without explicit action UI/evidence.

## Notifications
Client receives only meaningful configured updates: handoff human reply/queue resolution, requested action status, approval/request/renewal events. Sensitive technical/security details are minimized.

## Tasks
1. Implement client Principal/context resolver integration.
2. Build portal Ara shell/components.
3. Register initial safe project/billing resources.
4. Add action-card confirmation for allowed client actions.
5. Add Chatwoot handoff states/provider message rendering bridge.
6. Integrate optional Forms/Onboarding/Appointment/Property capabilities.
7. Add mobile/accessibility and cross-client negative suite.

## Acceptance
- Client A cannot infer/read Client B data through answer, source chip, tool error, search count or Attention.
- Revoked permission takes effect next request.
- Human handoff works without duplicate identity questionnaire/transcript store.
- Staff-only fields and secrets never appear in model context/client output.
- Client self-service actions use authoritative owner-module services and verified outcomes.
