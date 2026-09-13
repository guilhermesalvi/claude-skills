# Writing the PRD

Rules of the Write step of the workflow (workflow.md, Workflow): what enters the PRD, in which form and why. Where the file is saved and what the header looks like are in conventions.md, Saving. What changes in reverse PRD mode and in platform, infra, SDK or API as a product mode is in modes.md.

## Tags

Untagged text is fact: it was confirmed by the user or comes from an authoritative source (official regulation, formalized policy, recorded decision). Two tags mark what is not fact:

- `[ASSUMPTION]`: an inference made by the skill; needs validation.
- `[GAP]`: insufficient information to fill the passage with substance.

Usage rules:

- Never fill a gap with untagged speculation. The gap is information: expose it.
- An assumption that, if false, brings down the approach of the PRD, and not just one requirement, is the first line of Open Questions, in bold, with the clause "if false, …" and how to validate it. It is declared once, there, and cited where it matters.

## Capability test

Describe the capability by the behavior observable in the business. The categories below delimit what belongs to the implementation.

Apply the test to the Proposed Solution, to the solution sentence of the Executive Summary and to each FR. The text passes when it does not name:

- UI: screens, wizards, modals, dashboards;
- services and modules;
- mechanisms: database, queue, cache, event bus, webhook;
- UX pattern, stack or vendor.

If it named one, rewrite it as a change in observable behavior.

Exception: when the change in UX or in a public interface is the capability itself, that category may be named. Component, protocol and schema remain downstream.

| Mechanism | Capability |
|---|---|
| Web portal with a 3-step upload wizard and email notification | Asynchronous document submission with status visibility for all parties |
| Kafka topic publishing `CustomerVerified` to downstream services | Verification status observable by other contexts without synchronous coupling |
| System shows a confirmation modal before deletion | Deleting an active record requires explicit user confirmation |
| System publishes to the audit queue on every state change | Every state change is auditable, with who changed it and when |

## DDD lens

Apply this lens when the team works in DDD or has an explicit domain vocabulary: glossary, event catalog or context names in the received material. Otherwise, Bounded Context, Ubiquitous Language and Domain Events use the team's label (module, area, integration).

### Ubiquitous Language

- Use the terms of the domain experts, not generic jargon. The meaning matters more than the word, and the language does not matter.
- Competing names for the same concept within a context become `[GAP]`: align before writing.
- Between experts of different areas, the same conflict is a sign of a boundary between contexts, not of a gap.

### Subdomain

| Subdomain | What changes in the PRD |
|---|---|
| Core (the received material or PRD 0000 names it as a competitive differentiator) | Maximum rigor: precise criteria; question whether the solution preserves the differentiator; Non-goals enters by the rule of the Sections table |
| Supporting (neither Core nor Generic) | Standard PRD |
| Generic (a market product or package covers the capability) | Question buying, contracting or reusing; may become a non-goal |

### Bounded Context as scope anchor

- The boundary reveals itself through vocabulary: two experts using different terms for the same thing, or the same term for different things, are a sign of distinct contexts, not proof. The boundary is confirmed when rules and reasons to change also diverge.
- The feature has one originating context, named in the header (conventions.md, Saving).
- When several contexts are touched, the originating one owns the decision; the others enter Dependencies and Risks, with the impact on their autonomy declared.
- Scope that crosses contexts without a clear origin is a risk: ask which context is the originating one before writing; if the user does not know or declines to answer, the Originating Context field of the header receives `[GAP]` and the PRD goes out like that.
- More than one context with its own PRD requires PRD 0000 (see PRD 0000, below).

### Domain Events

- An event is the recognition of an operation that changed state and matters to another context. It is a domain contract, not an implementation detail; poorly documented, it becomes implicit coupling.
- Event, state machine transition and transition FR come from the same question, asked attribute by attribute: in which scenario it changes, who triggers it, who needs to know.
- The event catalog lives in PRD 0000; each PRD declares only what it produces and what it consumes.

## One rule, one place

- **The FR is the single source of the rule.** Proposed Solution, Glossary, Regulatory Considerations, Acceptance Criteria, Dependencies, Open Questions and PRD 0000 cite the ID (`OFF-28`) and do not repeat the condition, because paraphrase diverges over time; the exception is the Glossary, which defines the term in one line and cites the ID (see Sections). If paraphrase and FR diverge, do not delete either or pick a side: the divergence is a business decision and enters as a `[GAP]` in Open Questions, citing the ID.
- **One FR, one verifiable behavioral unit.** Independent obligations, which can fail separately, go in separate FRs, because citation and test point to a single one. A joint condition with an indivisible effect stays in the same FR, because fragmenting them invents intermediate states the business does not have.
- **A conditional attribute has an FR for outside the condition.** An attribute or option that only applies under a condition gets an FR saying what happens when it is supplied outside it (rejected or ignored), because that is a business decision, not an implementation one.
- **A section that only restates FRs does not enter.** A metric that repeats an FR, an acceptance criterion without new value and a dependency that describes the same coupling from both sides are cost without information.
- **A shared fact lives in 0000.** Project purpose, context map, event catalog and couplings between contexts stay in PRD 0000, and each PRD references them, because what is repeated on both sides diverges.

## IDs

- **Format.** `<PREFIX>-nn` for FRs and `<PREFIX>-NFR-nn` for NFRs, with a prefix per context (`OFF-12`, `BOOK-18`, `OFF-NFR-03`). The prefix exists because `FR-14`, numbered locally in each PRD, meant different things in documents that cite each other. The prefix is declared on the line right after the header table (conventions.md, Saving) and in the contexts table of PRD 0000.
- **Definition.** `- **OFF-01 (Must)** condition.` The MoSCoW priority (Must, Should, Could, Won't) goes inside the parentheses; NFRs take no MoSCoW. Every ID citation resolves to a definition in some PRD in the folder.
- **Removal.** A removed ID dies and is not recycled, because a citation to a reused ID changes meaning silently. Before changing or removing an FR, look for who cites the ID outside this PRD with `git grep -n <ID>` at the repository root and list those citing places when presenting: the citation still points to the ID, but the text behind it changed.
- **Enumerations.** State, result reason, category and every enumeration the code will carry have an Identifier column in the table that defines them (next to the diagram, with the FR or in the Glossary), because the code carries that name, and a name invented outside the PRD is a language decision taken outside it.

## PRD 0000

When there is more than one context with its own PRD, PRD 0000 concentrates what is shared between them. The file is `0000-<slug>-overview.md`, with `<!-- prd: overview -->` on the first line (conventions.md, Saving). It contains no business rule: every rule lives in the owning PRD and is cited by ID.

The first column gives the heading of each section, fixed in English (conventions.md, Language).

| Section | Content |
|---|---|
| Purpose | The project in one paragraph |
| Contexts | Table: context, responsibility, PRD, ID prefix, position (upstream, consumes, returns) and the integration rules (persistence, direction of contract change) |
| Event Catalog | Table: event, producer, consumers, trigger, IDs that govern it. An event only enters with a consumer that the consuming PRD declares; an event without a consumer is a candidate, listed as such, because an event without a consumer is invented coupling |
| Flows Between Contexts | `sequenceDiagram` per flow (happy path, revocation, failure); the labels cite IDs |
| Terms per Context | Only when the same concept has different names across contexts: the concept and the term in each context. Each PRD keeps the glossary of its context; 0000 keeps the correspondence |
| Decisions Delegated to ADR | Table: decision, requirement the ADR must satisfy (cites the NFR). The ADR format is not the PRD's business |

Each PRD references 0000 on the prefix line of the header, instead of repeating purpose, context map or event catalog.

## Diagrams

Mermaid replaces prose when the structure is a graph of the size indicated below; below the threshold, prose:

- `stateDiagram-v2` for a state machine, from 3 states;
- `flowchart` for a decision pipeline, from 2 decision points, with short inequalities in the decision nodes;
- `sequenceDiagram` for a flow between contexts, from 3 contexts exchanging messages, in PRD 0000.

Rules:

- A transition, edge or message label cites the requirement ID and does not rewrite the condition, because the diagram is an index, not a second source.
- Next to the `stateDiagram-v2` goes the table state, identifier, meaning (see IDs).
- A Mermaid reserved word does not serve as a participant or node alias: `off` and `end` fail to parse even in uppercase (`participant OFF as Offering` breaks; `on` passes). Use the full name.
- Every block is read line by line in the form check before presenting (workflow.md, Check).

## Sections

Use `#` for the title, `##` for sections and `###` for subsections.

Five sections are mandatory, and the form check flags their absence (workflow.md, Check): Executive Summary, Context and Problem, Target User / JTBD, Proposed Solution and, when the PRD defines IDs, Functional Requirements. PRD 0000 has the sections of the table in PRD 0000, above.

Every other section enters when the criterion in the "Enters when" column is met, and never for form: an empty section, "None." or a bullet invented to complete a count is a defect, not conformance. The order of the sections in the PRD is the order of the table. Three sections have form rules that do not fit in the cell; they are in the subsections after the table.

The table is closed: a `##` section outside it has no entry criterion or position, and the form check flags it (workflow.md, Check). In PRD 0000 the closed list is the table of the PRD 0000 section, above. Content that fits no section of the table goes to the section that covers it; `###` is a subsection and is free. A section missing from the table that comes neither from the session request nor from the repository convention (SKILL.md, Limits) changes the table first; coming from one of the two, the finding is kept by the rule of the Check step (workflow.md, Check) and the table does not change.

The first column gives the heading of the section, fixed in English whatever the language of the prose (conventions.md, Language); the heading is copied as is.

| Section | Enters when | Form |
|---|---|---|
| Executive Summary | Always | One paragraph of 3–5 sentences: problem, solution, primary metric |
| Strategic Alignment | The received material or the conversation cites the business objective, OKR or goal the feature answers to | One paragraph of 3–5 sentences connecting to the business objective |
| Context and Problem | Always | The problem; facts and assumptions (see Tags); no business rule |
| Target User / JTBD | Always | One bullet per actor with the job |
| Opportunity / Hypothesis | Problem still under validation | Hypothesis and how it will be validated |
| Proposed Solution | Always | Capability, not mechanism; state machine or pipeline in Mermaid when it passes the Diagrams threshold, otherwise in prose; rule cited by ID; closes by saying what is downstream |
| Domain Glossary | There is a domain term used in two or more requirements without a definition in PRD 0000 or in the received material, or with competing synonyms | Term and one-line definition; a term whose definition is a rule cites the ID; a term from another context points to the owning PRD |
| Functional Requirements | There are requirements | List under thematic subheadings, each line one ID and one condition (see IDs and One rule, one place) |
| Domain Events | The context produces or consumes events | One paragraph: produces X (ID), consumes Y (ID); catalog and sequences stay in 0000 |
| Non-functional Requirements | There is a quality attribute or constraint by which the design will be evaluated | `<PREFIX>-NFR-nn`; quality attribute and constraint, never mechanism; a requirement an ADR must satisfy says which ADR |
| Regulatory Considerations | Regulation identified and read | Source and reading date at the top; one line per article, with `→ ID that models it` after what the article says (the regulation and the article open the line; after the ID fits a note of up to 20 words, and what does not fit in it is a rule and lives in the FR); an article not checked in the text is `[ASSUMPTION]`; an unidentified regulation is a `[GAP]` bullet, without an ID |
| Non-goals | The received material or the conversation cites adjacent functionality the PRD does not cover | One bullet per exclusion: what we will not do |
| Declared Trade-offs | There is a decision taken in the conversation or in the received material whose rejected alternative has a nameable cost | `**Decision.** *Cost:* … *Reason:* …`, up to two lines; Cost and Reason are mandatory because they prevent relitigation. Different from Non-goals (we will not do) and from Open Questions (not decided) |
| Success Metrics | The received material or the conversation names a metric or a target number | One line per type that exists: leading (proxy, now), lagging (outcome); and always a guardrail (what cannot degrade; without it the metric becomes a target; the form check requires it), tagged `[ASSUMPTION]` when the material does not name it. Platform and infra: see modes.md, Platform, infra, SDK or API as a product mode |
| Acceptance Criteria | There is an FR whose result depends on more than one numeric value or on branching | Numeric scenario in a table (case, input, intermediate values, branch, result); Given/When/Then only for what the table does not express. Rules in Acceptance Criteria, below |
| Dependencies and Risks | There is a dependency or risk outside the control of the context, or the header field declares an affected context in `; affects` (conventions.md, Header) | Table: item, type, impact; one row per affected context from the header, even when the impact is only to observe; coupling between contexts cites 0000, and only the owning side describes it |
| Open Questions | There is an assumption that, if false, brings down the approach of the PRD (Tags), an `[ASSUMPTION]` or `[GAP]` cited by a Must FR or by the Proposed Solution, a divergence between FR and paraphrase (One rule, one place) or competing intents in a reverse PRD | One line per question: the question, the impact, the owner and the criterion that resolves it, when known. Rules in Open Questions, below |
| Weakest Point | There is a judgment decision over known facts (scope cut, threshold, prioritization or target user) that, if wrong, invalidates the Proposed Solution or the primary metric | Last content section, only References after it: the decision, the concrete attack vector and the invitation to the author to challenge it before approving. Rules in Weakest Point, below |
| References | A source was used | Link, articles read, reading date, PRDs cited |

### Acceptance Criteria

- The first column of the table names the case, because the test cites the scenario by name.
- Each Given/When/Then cites the FR it exercises.
- "User can X" is a tautology, not a criterion.

### Open Questions

- The content is what the "Enters when" column of the Sections table lists, and nothing more: the criterion is written there, once. The assumption that brings down the PRD is the first item of that column, and the section enters because of it; when it exists, it is the first line, in the form fixed in Tags. A bullet with "if false" in another section is the same assumption declared out of place, and the form check flags it (workflow.md, Check).
- A decision already taken does not enter: with a cost, it lives in Declared Trade-offs; without a cost, it lives in the FR that applies it. A delegated architectural decision is an ADR candidate (see PRD 0000, Decisions Delegated to ADR).

### Weakest Point

- It is distinct from the tags. `[ASSUMPTION]` may be false (factual risk); `[GAP]` is missing information (coverage risk). Here the decision is about facts, without a gap, and still contestable: scope cut, threshold, prioritization, target user.
- It is exposure, not self-correction: the one with the context to resolve it is the author.
- Calibrate to the cost of the error: the decision enters only if, being wrong, it invalidates the Proposed Solution or the primary metric. A minor weakness named to look rigorous is cosmetic self-criticism.
- A material `[GAP]` is one that enters Open Questions through the "Enters when" column of the Sections table (see Sections); a gap outside that criterion is not material here. With one of those, point to the decision that depends on the gap and what validation would change, without fabricating an attack vector.

## Writing

Apply (prose.md, Writing conventions) and the editorial checklist in the existing review. The rules on content, tags, IDs and behavioral unit remain under their own headings; independent obligations go in separate FRs and a joint condition with an indivisible effect stays in the same FR (writing.md, One rule, one place).

### Forms per section

This table guides writing within the existing sections; it adds no sections to the PRD.

| PRD section | How to write | How to check |
|---|---|---|
| Executive Summary | Within the form already required, present problem, proposed capability and known metric. Use the existing origin for numbers and claims. | The reader identifies the three elements or the absence of information, without presupposing an invented goal. |
| Context and Problem | Relate observed situation, impact and evidence. Separate facts from hypotheses with the existing tags. | The text does not anticipate components and does not turn discovery into certainty. |
| Target User / JTBD | Keep the bullet per actor and state the job or outcome they need to achieve. | Each item has an identified actor or an explicit gap; it is not just the name of a screen. |
| Proposed Solution | Explain the capability and its relationships; point to the FRs that define conditions. | The prose lets the reader understand the goal without creating a second normative definition. |
| Functional Requirements | Identify actor or system, condition and observable behavior as the source states. Preserve ID, priority and behavioral unit. | Two independent obligations are not merged; indivisible joint conditions were not split artificially. |
| Non-functional Requirements | Present the known attribute or constraint, limit and unit. | Qualifiers do not replace measures; a missing limit follows the existing tags. |
| Declared Trade-offs | Keep Decision, Cost and Reason. Name the accepted consequence, without generic praise. | The review does not change the choice or omit its cost. |
| Metrics and acceptance | Preserve names, numbers, units, scenarios and required fields. | Clarity was not obtained by inventing a baseline, goal or result. |
| Open Questions | State the pending decision and its impact; keep owner and resolution criterion when known. | An already answered question was not reopened and an assumption was not treated as fact. |
| Weakest Point | Expose the contestable decision and the concrete condition that can invalidate it, when the section is required. | The text does not fabricate weakness to fill form nor uses only generic self-criticism. |
