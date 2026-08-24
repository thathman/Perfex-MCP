# Native Ara — Admin Experience

## Product identity
Ara is Perfex's built-in intelligence operator. It should feel as if Perfex understands the record/workspace the user is already viewing, not like a third-party chat widget. The name **Ara** is canonical for this module.

## Entry points
- global launcher/search/command entry;
- Home operational briefing;
- contextual `Ask Ara`, `Explain`, `Draft`, `Act` on supported records;
- record-aware side panel/workspace;
- Attention/Watch recommendations;
- action/approval cards.

A dedicated administration area may exist for history/settings, but users should not need to navigate to an “AI module” to benefit from Ara.

## Context behavior
Browser sends explicit context identifiers; server Context Engine resolves authorised data. Ara never scrapes arbitrary DOM. UI shows current active context and allows user to remove/add permitted context deliberately.

## Modes
Ask, Draft, Act, Watch, Investigate, Recommend. Mode may be inferred but execution semantics remain explicit: drafts do not send; recommended actions do not run silently.

## Evidence UX
Material answers show compact source chips/deep links, record reference and freshness where useful. Inference/recommendation is visibly distinct from deterministic fact. “Cannot verify” is a valid outcome.

## Action UX
Ara renders registered action cards: target, normalized proposed effect, risk, preview/warnings, external sends, confirmation/Approval state and final verified result. Generic “Are you sure?” is insufficient for sensitive/high-impact actions.

## Interaction states
Streaming/thinking, tool in progress, waiting confirmation, awaiting approval, completed, denied, source stale, runtime unavailable, budget blocked, retry available. Never show “done” until authoritative action result succeeds.

## Conversation/retention
Conversation history is user/customer scoped and configurable. Sensitive content may be redacted/minimized. Archived conversations do not become a substitute for authoritative business records.

## Visual standard
Modern first-party Perfex integration: calm layout, progressive disclosure, restrained motion, excellent dark/light compatibility, keyboard access, mobile-safe panel/route. Avoid generic sparkle branding and avoid obscuring main work.

## MVP scenarios
- summarize a client relationship using authorised source records;
- explain project blockers/evidence;
- summarize overdue invoice status without inventing payment truth;
- draft follow-up/project update;
- create an authorised task through Action Registry;
- explain unavailable/denied data clearly.

## Tasks
1. Design launcher/panel/context state components.
2. Add explicit context payload and source-card renderer.
3. Implement conversation/run streaming states.
4. Add Ask/Draft flows.
5. Add action proposal/preview/confirm/approval cards.
6. Add history/archive/retention controls.
7. Add Home briefing integration once deterministic Attention exists.
8. Responsive/accessibility/security rendering review.

## Acceptance
- Ara works on multiple core records without per-page bespoke AI code.
- Active context is visible and permission filtered.
- Draft never sends automatically.
- Action cannot bypass registry/approval.
- Facts/sources/stale/inference states are understandable.
- UI is useful on mobile and does not cover critical Perfex controls.
