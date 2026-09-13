# Design

**Goal:** define *how* to build — structure, components, interfaces and what to reuse — with depth proportional to the **risk** of the change, not to its size. The design does not decide behavior: if a behavior decision is needed, go back to the spec and resolve it there.

Prerequisite: the one in the table of workflow.md, Opening a change. Save the design in `<capability>/NNNN-<change-slug>/design.md`; the folder layout and the machine comment are described in specify.md (specify.md, Layout).

## Loading context

Read, in this order, before designing:

1. **The `spec.md` of the capability.** It is the contract; the design does not reinterpret it.
2. **The active ADRs in `/docs/adr`.** Each one is a project constraint. When a previous decision conflicts with what would be best for this feature, the choice is explicit: conform to the ADR or supersede it, through the procedure described in adr.md. Silently ignoring the ADR creates invisible inconsistency between features.
3. **The PRD, when it exists.**
   - The Declared Trade-offs and Dependencies and Risks sections are design constraints and a source of risks.
   - The NFRs are the first origin of the evaluation criteria.
   - In PRD 0000: the context map fixes who is upstream and the direction of contract change; the event catalog fixes the producer and the consumers of each event; the decisions delegated to ADR are decisions of this design or of their own ADR.

## Codebase

Do not read the whole codebase; the spec is the focus guide.

1. Identify the modules and files tied to the scope. Read the directory structure before opening any file.
2. Read in this order: interfaces and contracts; domain entities; services and use cases; infrastructure. Open the complete implementation only when signature and name are not enough.
3. Declare what was read and what was ignored, in the form: "I analyzed X, Y, Z. A and B were left out and may contain constraints not considered."
4. Separate fact from inference: what the codebase imposes is fact; what you inferred from a pattern is `[ASSUMPTION]`. A pattern seen in fewer than three files of the same layer is not a project convention; count the tracked files of the layer with `git ls-files '<layer glob>'`, the same threshold that decides whether the change needs a design (workflow.md, How much artifact the change needs).
5. Every concern found in the codebase (coupling, debt, exposed secret, N+1, test gap in the path of the change) becomes a row in the Risks and Techniques section, with a mitigation or an acceptance. As long as you do not find the decision that explains the design (ADR, commit, PR), that concern stays tagged `[ASSUMPTION]`: a design that looks wrong today may have been the best under the constraints of the time.
6. Reuse: each new component references the existing component it follows; a component without reuse justifies why.

## From risk to technique

Before filling in the design sections, list what can fail expensively in this change. Go through the seven sources: the implicit dimensions of the spec, the concerns found in the codebase, integrations, money, regulation, public contract and migration. For each risk, choose the technique that reduces it and do only that work. A risk without a technique is a recorded acceptance, in the form "accepted because …".

| Typical risk | Proportional technique |
|---|---|
| Consistency between contexts, lost event | Explicit domain event contract; eventual vs strong decided; outbox or equivalent |
| Concurrency, duplicates, retry | Idempotency model; key; optimistic lock; transition table |
| Unstable external integration | Anti-corruption layer; timeout, retry and circuit breaker declared; fallback |
| Money, financial calculation | Value types; isolated and testable rules; declared decimal precision |
| Migration, compatibility | Migration strategy; dual write; rollback |
| Performance | Budget (p95, throughput) and where it is spent; index; pagination |
| Security, regulated data | Authorization boundary; retention; masking; audit |

Do not impose an architectural style: the design speaks the language of the codebase (ports and adapters, aggregates, or whatever is in it). Introducing a new style is a project-wide decision and follows adr.md. Ceremony without a risk that justifies it is dead weight.

## Criteria before approaches

Whoever proposes and whoever judges is the same agent; therefore, a criterion written after the proposal becomes rationalization. The order is fixed: criteria, critique of the criteria and only then approaches.

1. **Criteria.** Each criterion has a declared origin: a PRD NFR, a spec dimension, an ADR, cost or deadline. A criterion is a quality attribute or constraint, never a mechanism: "no single point of failure" is a criterion; "use a Bloom filter" is not.
2. **Critique of the criteria.** Ask: which criterion is missing for this type of problem (false positives in security, data freshness, operating cost)? Which trade-off decides the choice and is not yet fixed? A missing business criterion goes back to the PRD as a question; a solution-space criterion follows the branches below (workflow.md, Tags and doubts).
3. **Approaches.** A real alternative is an approach that satisfies all the criteria and trades places with the recommended one in at least one of them; the section exists only when there is one. In that case, present 2–3 materially viable approaches, with the same scope, evaluated against the criteria (which are the columns of the table) and against the four questions below. The recommended one comes first, with the rationale, and is confirmed by the user before you detail components, unless there is written delegation for the solution space (workflow.md, Tags and doubts). Without a real alternative, the section does not exist; the last line of Evaluation Criteria says "No real alternative: <one-sentence reason>".

| Situation | Criteria and critique | Approaches and waiting |
|---|---|---|
| Written delegation for the solution space | Fix the criteria and critique them | Decide the approach; the presentation/approval of the artifact remains |
| No delegation; all criteria come from PRD NFRs and the critique found no gap | Present criteria, critique and recommendation together, in a single wait | After the answer, evaluate a real alternative; if there is one, the confirmation of the approach from item 3 is the second and only additional wait |
| Other cases, without delegation | Present criteria and critique and wait for the answer before proposing an approach | An approach with a real alternative requires confirmation before detailing components, as item 3 says |

A conflicting convention must be exposed through precedence (workflow.md, Tags and doubts). Do not invent alternatives to fill a quota; criteria → critique → approaches remains the order.

### The four questions of an architectural decision

1. Does it meet the business objectives?
2. Does it respect the quality attributes?
3. Does it respect the constraints (ADRs, codebase, regulation, team)?
4. **Is there a cheaper or less risky way to do the same?**

The fourth is always answered: complexity (components × interconnections) is cost, and unjustified complexity is unnecessary cost.

## Components, contracts and data

- **Components.** For each component: purpose in one sentence (without "and"), real path, interfaces with types, dependencies and what it reuses. The interfaces come before the implementation: they are what the tasks consume.
- **Domain events.** For each event: producer, known consumers, semantic payload, partition or ordering key, delivery guarantee and versioning. At-least-once is the normal guarantee; the consumer's idempotency is what makes redelivery safe. A poorly documented event is implicit coupling between contexts.
- **Data Model.** Present when the feature touches persistence: entities, relationships, invariants and migration.
- **Error handling.** One row per scenario: scenario (with the requirement ID), handling and impact. Every `IF … THEN` of the spec appears here, with the mechanism chosen to handle it.

### Writing form

Describe each choice with the affected element, the decision and the constraint that justifies it. In tables, compare alternatives by the same criteria. In components, keep purpose, location, interfaces, dependencies and reuse. Preserve the signatures and limits already approved. A quality adjective must point to the risk, requirement or criterion that supports it. The explanation of a decision may take a paragraph; do not fragment it into independent bullets when the sentences form the same reasoning.

## Deployment unit and reuse

### Three concepts

They are distinct concepts; always say which one you are talking about:

- **Module:** a code boundary — assembly, package or namespace with a public interface.
- **Release package:** what is versioned and published.
- **Deployment unit:** what goes up and down together.

### Order of preference

1. A change in the existing deployable.
2. A new module in the existing deployable.
3. A new deployable.

What justifies a new deployable is a demand for **independent deployment**: a team with its own cadence, a different stack, strangling a legacy. Scalability, resilience and "separation of responsibilities" do not justify it on their own, because a replica and a module deliver the same. A new deployable carries an interface contract, versioning, backward compatibility and a named owner.

A deviation from the order of preference records the why in the Technical Decisions table, not in its own section.

### Shared library and module boundaries

- A shared library only enters with three conditions: an owner; stability, that is, the public interface did not change in the last three changes that touched it; and the absence of an equivalent public package.
- A business rule does not live in a platform library. `Utils` or `Shared` as a destination is the smell of that rule not being applied.
- No cycle between modules with their own boundary; a module is consumed only through its public interface.

## Technical Decisions

Record only the decisions in which another choice also met the evaluation criteria, in a table with four columns: decision, choice, rationale and type. The type distinguishes:

- **Public contract:** API, event, persisted format or one exposed to third parties. Changes with versioning and notice.
- **Internal decision:** changes without notice.

A decision that fixes a convention, constraint or pattern for future features becomes an ADR, in the adr.md format; a decision local to the feature stays only in the table.

## Sections

Each section exists when there is something to say; no empty section. The list is closed: the form check flags a `##` section outside it and a section out of this order (validation.md, Form check). The heading is fixed in English whatever the language of the prose (workflow.md, Language). In document order:

1. Design Context — constraints from the spec, the PRD and the ADRs; codebase read and codebase ignored.
2. Evaluation Criteria.
3. Risks and Techniques.
4. Approaches — only when there is a real alternative.
5. Architecture Overview — one paragraph and, when three or more components exchange messages, a Mermaid diagram.
6. Deployment Unit — one line when the change stays in the existing deployable.
7. Components.
8. Domain Events.
9. Data Model.
10. Error Handling.
11. Technical Decisions.
12. Files to Create or Modify — direct input to `tasks.md`.

Complete didactic format example; the data, contracts and decisions below do not claim adoption by the project.

## Template

```markdown
<!-- sdd: design | spec: ../spec.md | scope: RSV-07, RSV-08, RSV-09, RSV-10, RSV-11, RSV-12 -->
# Partial Reservation — Design

## Design Context

Spec: RSV-07 to RSV-12. ADR 0001 (outbox) constrains event publication. Codebase read: `src/ReservationBook/Reservations/*`; ignored: `src/ReservationBook/Reports/*`.

## Evaluation Criteria

| # | Criterion | Origin |
|---|---|---|
| C1 | The book read by Allocation is identical to the frozen one | BOOK-NFR-02 |
| C2 | Result visible within 5s after `BookProcessed` | user, in this session |

No real alternative: ADR 0001 already fixes the transport and the spec fixes the behavior.

## Risks and Techniques

| Risk | Source | Technique | Where |
|---|---|---|---|
| Duplicate due to channel retry | RSV-10 | Persisted idempotency key; uniqueness (investorId, offerId) | `ReservationService` |

## Architecture Overview

[Paragraph; Mermaid diagram by the criterion of the Sections section.]

## Deployment Unit

Stays in `src/ReservationBook`.

## Components

### ReservationService
- **Purpose:** keep the reservation book of a published offering.
- **Location:** `src/ReservationBook/Reservations/ReservationService.cs`
- **Interfaces:** `Place(PlaceReservation cmd, CancellationToken ct): Task<Result<Reservation, ReservationError>>`
- **Dependencies:** `IOfferReader`, `IReservationStore`
- **Reuses:** `src/ServiceDefaults/TraceAsync.cs`

## Error Handling

| Scenario (ID) | Handling | Impact |
|---|---|---|
| Position above the maximum (RSV-11) | `Result.Failure(POSITION_ABOVE_MAXIMUM)`; 422 at the endpoint | Operator sees the resulting position |

## Technical Decisions

| Decision | Choice | Rationale | Type |
|---|---|---|---|
| Registration order | Counter per offering, not instant | BOOK-14 requires a total order with equal instants | internal |

## Files to Create or Modify

- `src/ReservationBook/Reservations/ReservationService.cs` — new
- `tests/UnitTests/Reservations/ReservationServiceTests.cs` — new
```

After saving, do the form check, including the diagrams (validation.md, Form check), and the review of the entry point; then present the design and wait before starting the Tasks.

### Partial didactic rewrite example

A writing fragment; not a complete artifact nor evidence of a real execution.

```text
Before: The assignment of the registration order will be carried out by
means of a counter per offering, in view of the need to guarantee a total
order when the instants are equal, as established in BOOK-14.

After: A counter per offering defines the registration order. BOOK-14
requires a total order even when the instants are equal.

Preserved: mechanism of the decision and the requirement that justifies it.
```
