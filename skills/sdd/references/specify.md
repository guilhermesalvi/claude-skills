# Specify

**Goal:** capture *what* to build as testable and traceable requirements. The spec is the contract that Design refines, that Tasks decompose and that Verify audits. The spec lives in the solution space; the business rule lives in the PRD.

## Layout

```text
/docs/specs/<domain-slug>/<capability-slug>/
├── spec.md                      # living spec, edited in place
└── NNNN-<change-slug>/          # only when the change needs a design or tasks
    ├── design.md
    └── tasks.md
/docs/adr/NNNN-<slug>.md         # project-wide decision (adr.md)
```

The layout is a default of this skill: a specs or ADR folder fixed in the repository guide (CLAUDE.md) prevails by precedence (workflow.md, Tags and doubts).

### Slugs and numbering

- **Slugs** in English kebab-case, mirroring the name the code uses: `PartialReservation` becomes `partial-reservation`. A capability is a functional area of a bounded context, such as `reservation-book/reservation-lifecycle`.
- **`NNNN`** is a counter within the capability. Get the number by listing the capability folder and adding 1 to the highest `NNNN` (validation.md, Numbering). The change folder only exists when there is a `design.md` or `tasks.md`; never scaffold an empty artifact.

### Versioning

Git is the version control: the diff is the delta and the log is the history; approval is the commit (workflow.md, Approval and authorizations). No artifact carries a status, author, date or approval field.

### Machine comment

The first line of each artifact, before the `#`, is a machine comment in lowercase ASCII:

- spec: `<!-- sdd: spec | capability: offering/offer-lifecycle | prd: /docs/prd/0001-offering-offer-lifecycle.md | prd-rev: git:<hash> -->`
- design: `<!-- sdd: design | spec: ../spec.md -->`
- tasks: `<!-- sdd: tasks | spec: ../spec.md | design: ./design.md -->`

Conditional fields:

- `design:` enters the tasks comment only when `design.md` exists.
- `scope: RSV-07, RSV-10` enters the design and tasks comment when the change touches only part of the spec requirements; in the design, it limits the `IF ... THEN` coverage required in Error Handling to the listed IDs.
- `prd:` and `prd-rev:` enter the spec comment only when there is a PRD. `prd-rev` is the result of `git hash-object <prd.md>` on the file on disk, with the tree clean for that file (it is what the form check recomputes); a modified and uncommitted PRD asks for its commit before saving the spec. If the PRD changes later, the hash diverges; that finding is always fixed, never kept with a reason: re-derive the requirements that cite the touched IDs and update the hash.

### Prefix line

Right below the `#` comes the line ``Requirement prefix: `RSV`.`` and nothing else as a header.

## Origin

The signal in the request determines the origin of the spec, what counts as fact and the care the writing requires.

| Signal in the request | Origin | What is fact | Care |
|---|---|---|---|
| There is a PRD in `/docs/prd` | PRD | The untagged text of the PRD. What is tagged `[ASSUMPTION]` in the PRD stays `[ASSUMPTION]` in the spec, with the PRD origin noted, and does not count as a solution-space assumption | Follow the six rules of the From the PRD subsection |
| No PRD, but target user and problem are in the request | Idea | What the user stated | Record user, problem and result in the Context; missing observable behavior becomes `[GAP]` in the Context. If user or problem is missing, stop |
| Solution-first request, without user or problem ("CRUD for X", "screen for Y") | — | — | Answer "This needs a PRD first" and stop |
| Existing code without a spec ("document the spec of module X") | Code | What the code *does*: behavior, tests, docs | Inferred intent is `[ASSUMPTION]`; behavior without a business justification is `[GAP]`; divergences go in the Divergences section; reading the codebase follows design.md, Codebase |

### From the PRD

When the origin is the PRD, six rules apply:

1. **Cite the ID, do not rewrite the rule.** The EARS requirement cites the PRD ID at the end of the line (`[BOOK-04]`) and describes the observable behavior that realizes the rule (value, status, event, deadline), without rewriting it. A rule written in two places diverges. State, event and enumeration use the identifier the PRD fixes.
2. **A business rule is fixed in the PRD.** A gap or inconsistency in a business rule is fixed in the PRD and never becomes an assumption in the spec. A new assumption in the spec is only about the solution space: error format, technical deadline, processing order; an assumption inherited from the PRD keeps the tag with the origin noted (Origin).
3. **Distinct prefix.** The spec prefix is distinct from every PRD prefix and does not share the first two letters with any of them: `OFR` next to `OFF` invites error; prefer an acronym from another root. Check the collision by reading the prefixes declared in `/docs/prd` (validation.md, Form check).
4. **PRD 0000.** When it exists, PRD 0000 provides the context map, the event catalog and the decisions delegated to ADR. An event the capability produces or consumes becomes a requirement citing the ID that governs it; a decision delegated to ADR enters Open Questions with owner "Design/ADR".
5. **NFR.** An NFR with a result observable by test (deadline, atomicity, audit record) becomes an EARS requirement citing the `X-NFR-nn`. An NFR that is a quality attribute without a direct test becomes a design criterion (design.md, Criteria before approaches) and appears in the Traceability on a row whose second column starts with `Design criterion:` (fixed form).
6. **Acceptance scenarios.** The scenarios of the PRD's Acceptance Criteria are the minimum suite Execute reproduces (execute.md, Per-task cycle). The Traceability lists them by the case name when the PRD brings them in a table (first column; those citing an FR in scope are mandatory) and by the FR they exercise when the PRD brings them in bullets, with the EARS IDs that cover them. An FR or scenario from another capability enters a row whose second column starts with `Outside this capability:` and the name of the owning capability (fixed form). Accepting valid input and rejecting invalid input are distinct scenarios and distinct requirements.

### Prior reading

In any origin, read before writing: the living `spec.md` of the capability, if it exists, and the active ADRs in `/docs/adr`, which constrain what the spec can ask for.

## Clarify

- **Sweep the codebase before asking:** the module the capability touches, the patterns it uses and at least one sibling feature already implemented. Use what you find to anchor the questions, not to limit the spec to what already exists.
- **You are a technical peer, not an interviewer.** Challenge vagueness ("fast" is how much? "users" are who?) and make the abstract concrete ("walk me through one use of this").
- **Ask only when the answer changes** architecture, data model, decomposition, test design or acceptance (it is the exception of workflow.md, Tags and doubts; with written delegation for the solution space, do not ask). What the code or the PRD already answers is not asked; stylistic preference is not asked.
- **One question at a time:** a complete interrogative, one line of "why it matters" and two or three concrete options, with the recommended one first. Offer "you decide" when the choice is solution space and all options satisfy the requirements already written; the delegation becomes a recorded decision. Ceiling: five questions per spec; from the sixth on, the decision goes straight to Open Questions, with owner and what it blocks.
- **Encode each answer in the spec immediately,** as a requirement, assumption or out of scope. A material decision without an answer stays in Open Questions, blocks only what depends on it and never becomes a default.
- **The boundary of the change is fixed:** clarify clarifies *how* something behaves, never *whether* a new capability enters.

## Implicit dimensions

Go through the ten dimensions below, one by one, when closing the understanding. Only what generates a requirement is written, and it is written as a requirement; the rest leaves no trace.

- Input validation and limits
- Failure and partial failure: timeout, partial write, compensation
- Idempotency, retry and duplicates
- Authorization and rate limit
- Concurrency and ordering
- Data lifecycle: retention, deletion
- Observability
- External dependency failure
- State transition integrity
- Consistency between contexts: published event, consumed contract, eventual vs strong

## Requirements in EARS

| Pattern | Template |
|---|---|
| Ubiquitous | The [system] SHALL [response] |
| Event-driven | WHEN [trigger] THEN the [system] SHALL [response] |
| State-driven | WHILE [state] the [system] SHALL [response] |
| Optional-feature | WHERE [feature is present] the [system] SHALL [response] |
| Unwanted-behavior | IF [undesired condition] THEN the [system] SHALL [response] |
| Complex | WHILE [state], WHEN [trigger] the [system] SHALL [response] |

- **One pattern per requirement.** Every requirement contains `SHALL` and a concrete value (status code, message, limit, deadline). "Quickly" and "appropriate" are not requirements.
- **A requirement is one verifiable unit.** Obligations that fail separately go in separate requirements; a joint condition with an indivisible effect (write and publish in the same transaction) stays in a single requirement. The unit test: can you write one test that asserts exactly this result?
- **Keywords in English, body in the language of the spec.** In a spec written in Portuguese: `WHEN o investidor confirma a reserva THEN the system SHALL registrar a reserva com status Pendente em até 2s [BOOK-01]`.
- **A domain event relevant to another context is a requirement,** not an implementation detail. An edge case is an Unwanted-behavior requirement, with an ID like the others.
- **`<PREFIX>-nn` IDs from the draft on,** with two or more digits and never recycled. A removed ID dies.

### Writing form

Write the condition and the response in direct order, keeping the applicable EARS pattern. Name the result defined by the source. Preserve IDs, PRD references, keywords, values and error names. A requirement needs an observable result; a missing measure requires the resolution planned for gaps, not a number chosen to complete the sentence. A style edit does not authorize turning a violation record into an operation rejection, nor adding an HTTP status, persistence or an event.

## Living spec

The spec is edited in place; the change is the diff.

- **Remove a requirement:** delete the line and add the ID to the line `Retired: RSV-05, RSV-09` at the end of the spec; create the line at the first retirement. A reused ID and a skipped number outside the retired ones are form-check findings (validation.md, Form check).
- **Change meaning:** edit the text and keep the ID.
- **Replace a concept:** retire the ID and create a new one.
- **A refactor without a behavior change** does not touch the spec: the design or the tasks cite the IDs the refactor preserves, and the existing tests are the evidence.

## Sections

Each section exists when there is something to say; mandatory are only Context and Requirements and, when there is `prd:`, Traceability. The list is closed: the form check flags a `##` section outside it, tolerating only the section inherited from the PRD (validation.md, Form check). The heading is fixed in English whatever the language of the prose (workflow.md, Language); a name separated by a comma is an accepted alternative form.

| Section | Content |
|---|---|
| Context | 3–5 lines: the origin (the PRD and what was fixed in it; the idea; or the code and why it is being specified), the codebase read, and what the capability produces and consumes |
| Scope / Out of Scope, Scope | What enters; item / reason table for what stays out |
| Assumptions | Assumption / default / rationale table, each row tagged `[ASSUMPTION]` |
| Open Questions | Question, owner and what it blocks; the one blocking the most requirements first, in bold |
| Requirements | EARS list with IDs; `###` subheadings by theme from 8 requirements |
| Domain Events | Event, producer, consumers, semantic payload, trigger |
| Glossary | Only solution-space terms; a domain term points to the PRD glossary |
| Traceability | Present when there is a PRD: from each FR in scope (PRD ID cited by at least one EARS requirement) to the EARS IDs that cover it, and from each inherited scenario to the EARS IDs that cover it; the form check checks FRs and EARS IDs in both directions and the presence of each scenario of the PRD table |
| Divergences | Present in the code origin: what the code does and seemingly should not, what it should do and does not, dead code; each item with `file:line` |

Complete didactic format example; the data, contracts and decisions below do not claim adoption by the project.

## Template

```markdown
<!-- sdd: spec | capability: reservation-book/reservation-lifecycle | prd: /docs/prd/0002-reservation-book-reservation-lifecycle.md | prd-rev: git:3f9c2a1b7d0e4c5a9b8d7e6f0a1b2c3d4e5f6a7b -->
# Reservation Book

Requirement prefix: `RSV`.

## Context

Origin: [PRD 0002](../../../prd/0002-reservation-book-reservation-lifecycle.md).
The capability consumes `OfferPublished` and `OfferClosed` and provides the closed book to Allocation by query (BOOK-16).
Codebase read: `src/ReservationBook` has only the service composition.

## Scope / Out of Scope

**In scope:** registration, change and cancellation against an `Open` offering; freezing at closing.

| Out of scope | Reason |
|---|---|
| Pro-rata and conditioning | Capability `allocation/book-processing` |

## Assumptions

| Assumption | Default | Rationale |
|---|---|---|
| [ASSUMPTION] Form of the rejection | ProblemDetails 422 with `violations[]` | `AddApiDefaults()` already uses ProblemDetails; BOOK-09 requires all violations |

## Open Questions

- **Does the broker's practice allow adjusting the reservation until closing?** Owner: PRD author. Blocks the reservation change requirements, which only enter with the answer.

## Requirements

- **RSV-01** — WHEN the operator registers a reservation without a violation of RSV-02 and RSV-03 THEN the system SHALL register it as `Active` with instant and registration order [BOOK-01]
- **RSV-02** — IF the offering is not `Open` THEN the system SHALL record the violation `OFFER_NOT_ACCEPTING_RESERVATIONS` with rule BOOK-01 [BOOK-01]
- **RSV-03** — IF the investor's position with the new reservation exceeds the maximum investment THEN the system SHALL record the violation `POSITION_ABOVE_MAXIMUM` [BOOK-04]
- **RSV-04** — IF the same registration arrives twice with the same `idempotencyKey` THEN the system SHALL return the original reservation without duplicating
- **RSV-05** — WHEN the reservation is accepted THEN the system SHALL assign a strictly increasing registration order in the offering's book [BOOK-14]

## Traceability

| PRD ID | EARS IDs |
|---|---|
| BOOK-01 | RSV-01, RSV-02 |
| BOOK-04 | RSV-03 |
| BOOK-14 | RSV-05 |

| PRD scenario | EARS IDs |
|---|---|
| Second reservation within the maximum accepted | RSV-01 |
| Third reservation above the maximum rejected | RSV-03 |
```

After saving the spec, do the form check (validation.md, Form check) and the review of the entry point (validation.md, Review per entry point).

### Partial didactic rewrite example

A writing fragment; not a complete artifact nor evidence of a real execution.

```text
Before: IF the investor's position with the new reservation exceeds the
maximum investment THEN the system SHALL proceed with the recording of the
violation POSITION_ABOVE_MAXIMUM [BOOK-04]

After: IF the investor's position with the new reservation exceeds the
maximum investment THEN the system SHALL record the violation
POSITION_ABOVE_MAXIMUM [BOOK-04]

Preserved: condition, response, identifier and PRD reference.
```
