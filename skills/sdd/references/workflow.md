# SDD workflow

## Opening a change

### How much artifact the change needs

Two questions decide which artifacts the change needs, beyond the spec:

1. Is there a decision about deployment, architectural style or shared library (design.md, Deployment unit and reuse); a new file in a layer with fewer than three tracked files of the same kind, counted before deciding with `git ls-files '<layer glob>'` — without three specimens there is no convention to copy (design.md, Codebase); an interaction between three or more components to plan; or a risk of integration, money, regulation, public contract, migration or a concern found in the codebase (the sources of design.md, From risk to technique, except the implicit dimensions, which already became requirements)? If yes, the change needs `design.md`.
2. Are there more than five steps, or does some step depend on one that is not the immediately previous one? If yes, the change needs `tasks.md`.

**Ratchet.** If the change reveals a design or tasks trigger, interrupt only the work that depends on the missing artifact, present the discovery and produce that artifact respecting the prerequisites. The level already required is not reduced.

In an authorized implementation, Specify and Execute are never skipped: *what* is always known before doing. When there is no `tasks.md`, Execute starts with an inline plan (execute.md, Inline plan).

### Entry points and prerequisites

| Entry point | Prerequisite | Reference |
|---|---|---|
| Specify | PRD, a request with target user and problem (specify.md, Origin) or code to document | [specify.md](specify.md) |
| Design | committed spec | [design.md](design.md) |
| Tasks | committed design, or spec when Design was waived | [tasks.md](tasks.md) |
| Execute | committed tasks, or approved inline plan | [execute.md](execute.md) |
| Verify | last task closed | [verify.md](verify.md) |
| ADR | decision that fixes a convention for future features | [adr.md](adr.md) |

For a new entry point, read the whole reference and its normative dependencies. For a localized correction, read the affected passage, its prerequisites, exceptions and citing passages; widen the reading when a dependency appears. The file layout and the machine comments are defined in the Specify reference (specify.md, Layout).

## Approval and authorizations

**Approval is the commit.** A dirty tree is work in progress; the next entry point uses the approved and committed version. Present each artifact and wait for approval of its content before starting the next. A commit made by the user of the presented artifact also satisfies that approval.

Authorization to edit allows producing the requested result. Authorization to commit allows the local Git operation during the change: "commit it", said once, authorizes its local commits, one per artifact and one per task. That authorization does not remove the presentation and approval required for the content of each artifact. Push, deploy and other external effects still require their own authorization.

| Observed situation | May do | Must stop where |
|---|---|---|
| Request for spec/design/tasks only | Produce the requested artifact, respecting prerequisites | Before implementing or starting an unauthorized entry point |
| Content approved in chat and commit already authorized for the change | Local commit of the approved artifact and permitted advance | At the next required approval |
| Content approved in chat, without commit authorization | Ask once for the user to commit or authorize the commit | Before the entry point that requires the committed artifact |
| "Commit it" given before the next artifact exists | Keep the authorization of the local operation | Still present and obtain approval of the new content |
| Inline plan approved in chat | Start Execute as the plan says (execute.md, Inline plan) | At the blocks and scope limits already defined |
| Silence | Continue only independent work already authorized | Not treated as approval |

The inline plan is the exception to approval by commit: it lives in the chat and is approved in the chat. Explicit delegation of technical choices still applies; it does not delegate business decisions or external effects. Do not record approval fields in the artifacts.

Within the authorized scope, complete the edits, the required verifications and the corrections allowed by the ceilings of the entry point. Do not ask for a new approval for reading, inspection or local testing already authorized. If a decision blocks part of the work, first complete what is independent of it. When reporting the block, identify the blocked action and the local rule that requires the decision. Presenting a draft does not end an authorized implementation that still requires verification.

That authorization for local testing requires a command and isolation proven in the project configuration. Do not claim that tests do not reach production without checking. Installation, external access, commit, push and deploy remain subject to the authorizations already defined. Local precedence operates within the security controls and permissions of the environment.

## Tags and doubts

- **Tags.** `[ASSUMPTION]` marks an inference with a default and rationale; `[GAP]` marks insufficient information to decide. Untagged text is fact, with origin in the PRD, the user, the code or the documentation. Approval does not turn an assumption into a fact.
- **Facts you look up; decisions you ask.** The research chain for a fact, in this order: codebase, project docs, official documentation, web; if nothing answers, flag the uncertainty. Never fabricate an API, pattern or behavior; "I did not find it" is a valid answer.

Every doubt falls into one of three categories:

| Condition | Action | Block |
|---|---|---|
| 1. Decision within the autonomy delegated in writing in the session request or in CLAUDE.md | Decide, record and move on | Only the presentation of each artifact waits |
| 2. Solution-space inference without written delegation | Tag `[ASSUMPTION]` with default and rationale and move on; the assumption stays reviewable | Ask in the Clarify and Design exceptions below |
| 3. Material user decision: scope, business rule, trade-off or external effect | Ask; without an answer, do not adopt a default | Block only what depends on the answer |

Without written delegation, a business choice is category 3 and a solution-space choice is category 2. In the latter, ask in Clarify when the answer changes architecture, data model, decomposition, test design or acceptance (specify.md, Clarify), and in Design about criteria and approach (design.md, Criteria before approaches). With written delegation for the solution space, those two exceptions fall and only the presentation of each artifact waits.

- **Refine the context, not the error.** A wrong downstream artifact (a test that should not pass, code that contradicts the design, an impossible task) is not patched: fix the upstream artifact that carried the cause and re-derive the downstream one. A wrong business rule goes back to the PRD.
- **Precedence**, from what prevails to what yields:
  1. Session request.
  2. Repository convention.
  3. Defaults of this skill.

  The closed list of sections of each artifact is a default of this skill, step 3: a form finding that follows from one of the first two steps is kept, not fixed (validation.md, Form check).

## Language and writing

### Language

- **Structure in English, prose in the language of the artifact.** The structure is what the form check reads, and it has a single form, in English: the `##` headings of the section list of each artifact (specify.md, Sections; design.md, Sections; tasks.md, Sections of `tasks.md`; adr.md, Template), the machine comments, the prefix line, the tags `[ASSUMPTION]` and `[GAP]`, the task fields (tasks.md, Fields) and the fixed-form lines (`Retired:`, `Design criterion:`, `Outside this capability:`, `No real alternative:`, `Participants:`, `Supersedes:`, `Superseded by:`, `Positive:`, `Negative:`). A translated heading or label is a form-check finding; a section outside the list is a finding in design, tasks and ADR, and in the spec only the section inherited from the PRD is tolerated.
- **The language of the prose:** that of the PRD; without a PRD, that of the received material (with material in more than one language, that of the document the request cites first or, without a citation, that of the first attachment); without both, that of the request; if the request mixes languages, English. Any language serves for the prose, because the form check does not read it.
- A term without a translation of the same strength stays in English: domain event, outbox, idempotency key, retry, circuit breaker, aggregate, value object, port/adapter, trade-off, gate.
- EARS keywords, IDs, code, paths, slugs and identifiers are not translated.
- A term the team uses in its own language (CLAUDE.md, the PRD glossary or the code) stays in that language even if it is in the list above: that is the precedence of Tags and doubts.

### Writing

Apply the [Writing conventions](prose.md#writing-conventions). The writing heuristics and the checked tags are in (validation.md, Form check).
