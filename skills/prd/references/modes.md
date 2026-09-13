# Conditional modes

This file is loaded only when the request triggers one of the two modes below. The general rules of intake.md and writing.md still apply in both modes; only what changes relative to them is here.

## Reverse PRD mode

Use this mode when the request is to document what has already been built: "PRD of module X", "document what we built". Because the material includes code and screens, apply the capability test to every section of the PRD (writing.md, Capability test). In this mode, and only in it, the review's count of mechanism terms covers every section of the PRD (workflow.md, Review before presenting).

- **What to ask for.** Ask for observable behavior, applied rules and decisions the system makes. Accept the material in any form: code, docs, bullets, free description.
- **Where to derive intent from.** Derive intent from outcomes, that is, from what the user or the business gains, not from the operations the system performs.
- **Inferred intent.** Every inferred intent is `[ASSUMPTION]`: intent that is not written in the received material (commented code, doc, ticket, commit) gets the tag, because reverse-engineered intent is fragile.
- **Behavior without justification.** Behavior without an identifiable business justification is `[GAP]`. The tag exposes the orphan feature, which may be dead weight or hidden value.
- **Competing intents.** When there are multiple plausible intents for the same behavior, they go to Open Questions (writing.md, Sections). Do not fabricate coherence that does not exist.

## Platform, infra, SDK or API as a product mode

Triggers when the product of the PRD is a platform, infra, SDK or API, that is, something consumed by another team or system.

- **Target user.** The user is the consuming team or system. JTBD still works for it; example: "integrate auth without managing session state".
- **Metrics.** The primary metrics are operational: latency percentiles, error rate, adoption by consumers, time-to-integration. Business outcome is second order, because it belongs to the consumers.
- **Acceptance Criteria.** Include the contract: stability of the API shape, SLA, backward compatibility window.
- **Deprecation.** When the PRD replaces an already published interface, deprecation goes into Declared Trade-offs when some old interface stops being covered (there is a cost), saying which interface and what will not be migrated; and into Non-goals when nothing published stops working, saying what will not be migrated (writing.md, Sections). In both cases, the entry says the schedule is not committed.
