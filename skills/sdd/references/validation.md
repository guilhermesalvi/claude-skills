# SDD validation

## Form check

Before presenting an artifact, read the saved file and check each item of the list for its type. Fix every finding and reread the whole file; there are at most two correction rounds. If the second still ends with a finding, present the artifact and list in the chat each remaining finding with the reason, without declaring complete validation. A form finding required by the request or proven by convention stays as it is, is reported in the chat with the origin, using "kept by request" or "kept by convention" (Form kept by request or convention), and does not count as a round. After the check, do the content review (Review per entry point).

An artifact presented with a listed remaining finding is not approved and does not open the next entry point without the prerequisites (workflow.md, Approval and authorizations). The Execute gate has its own protocol: an initial run and up to two correction attempts, three runs in total before stopping (execute.md, Per-task cycle).

### Numbering

Before creating the folder of a change or an ADR, list the direct children of the folder, take the highest `NNNN` and add 1; a folder without items starts at 0001. The slug is lowercase ASCII kebab-case. Before presenting any numbered artifact, check that no number repeats in the folder; the duplicate is born when two branches allocate the same number. Renumber the item whose branch lands later: move it to the next free number, update whoever cites it and check again.

### Spec

- Machine comment on the first non-empty line, in the form `<!-- sdd: spec | capability: <domain>/<capability> [| prd: <path> | prd-rev: git:<hash>] -->` (specify.md, Machine comment).
- Prefix line right below the title, in the form `Requirement prefix: \`RSV\`.`.
- `## Context` and `## Requirements` present, without a duplicated section; every `##` in the Sections list (specify.md, Sections), with the section inherited from the PRD as the only tolerance.
- Every requirement with one `SHALL` per line, in an EARS pattern (WHEN, WHILE, WHERE, IF or `The <system> SHALL`), in the form `- **PFX-NN** — text`, without vague terms.
- ID prefix equal to the declared one; unique IDs; a retired ID (`Retired:` line) not reused; a skipped number only when it is among the retired ones.
- Prefix distinct from every prefix declared in a PRD and not sharing the first two letters with any of them.
- With `prd:`: the PRD exists; `prd-rev` equal to `git hash-object <prd>`; every `X-nn` or `X-NFR-nn` citation resolves to a definition in the PRD; `## Traceability` present, with every FR in scope and every scenario of the Acceptance Criteria table that cites an FR in scope, and only EARS IDs that exist in the spec; a line with only PRD IDs that no requirement cites starts with `Design criterion:`.
- Context with 3 to 5 lines; Requirements with `###` subheadings from 8 requirements and without them below that.
- Tags only `[ASSUMPTION]` and `[GAP]`; no placeholder, hedging or meta-narration; every local Markdown link resolves.

### Design

- Machine comment `<!-- sdd: design | spec: ../spec.md [| scope: ...] -->` whose `spec:` resolves to a file; every ID in `scope:` exists in the spec.
- Every `##` in the Sections list, in its order, without a section empty or reduced to "None." or "N/A" (design.md, Sections).
- Without `## Approaches`, the last line of `## Evaluation Criteria` is `No real alternative: <reason>`; with the section, that line does not exist.
- Every `IF ... THEN` requirement of the spec (in scope) cited in `## Error Handling`.
- A section with more than ten body lines only when its subject appears in `## Risks and Techniques`; in `## Components`, each `- **Purpose:**` with one sentence and one purpose.
- Every Mermaid block read line by line: closed fence, syntax that renders, no reserved word as an alias.
- Tags only `[ASSUMPTION]` and `[GAP]`; no placeholder, hedging or meta-narration.

### Tasks

- Machine comment `<!-- sdd: tasks | spec: ../spec.md [| design: ./design.md] [| scope: ...] -->` whose targets resolve to files.
- "How this repository tests" paragraph before `## Gate Commands`, with the base test count of the Build gate.
- `## Gate Commands`, `## Execution Plan` and `## Traceability` present; every `##` in the Sections list (tasks.md, Sections of `tasks.md`).
- Gate Commands table with rows only among `quick`, `full`, `build` and `Mutation`, no empty command cell; the `Mutation` row present when the Risks and Techniques table of the design has a risk that requires mutation (verify.md, Mutation); every `Gate` value used has a row.
- Each task `### Tn:` or `### TCn:` with a unique ID and the fields What, Where, Depends on, Requirement, Interfaces, Done when, Tests and Gate, once each and filled in (tasks.md, Fields).
- `Done when` with the gate command of the task in backticks, copied from the table, and at least one behavior criterion; `Tests` and `Gate` in the combination the `Gate` field fixes; the last task of each phase with `build`.
- Every spec requirement (in scope) with a task and every task with at least one existing requirement; `## Traceability` consistent with the `Requirement` fields in both directions.
- Dependencies only backwards in the plan order, without a cycle and without a `T` depending on a `TC`; every task cited in the plan with a body and every `T` task with a body cited in the plan.
- `What` with one deliverable; `Where` with recognizable paths; `Tests` `none` only when every path in `Where` is config, schema or migration.
- Tags only `[ASSUMPTION]` and `[GAP]`; no placeholder, hedging or meta-narration.

### ADR

- Title `# ADR NNNN: title`; `Participants:` line with a name before the first `##`.
- `## Context`, `## Decision`, `## Alternatives considered` and `## Consequences` present, in that order, without an empty section; no `##` outside those and `## Derived rules`, which when it exists is the last (adr.md, Template).
- Alternatives considered with a filled table: each row with alternative and reason.
- Consequences with the line `- Negative: <text>`.
- `## Derived rules` with at least one bullet, each with the path of the file where the rule lives in backticks (adr.md, Conform and supersede).
- `Supersedes: NNNN` and `Superseded by: NNNN` pointing to an ADR existing in the folder, with the reciprocal line in the other ADR.
- Tags only `[ASSUMPTION]` and `[GAP]`; no placeholder, hedging or meta-narration.

### Form kept by request or convention

A valid request to keep a form finding literally names the section, the field or the form it comes from, for example "include a Rollout Plan section in the design". A valid convention is form proven in at least three committed artifacts of the same type, or written in the repository's CLAUDE.md. Say the number of specimens in the chat.

To recognize a convention by examples, use the version of the files present in HEAD. List the files with `git ls-tree -r --name-only HEAD -- docs/specs` and filter the Markdown files of the type under analysis: `spec.md`, `design.md` or `tasks.md`, separately. Read each example with `git show "HEAD:<path>"`. The convention must appear in at least three of those examples. A file that is only staged or untracked does not count. A local change to an already committed file also does not change the HEAD convention. If HEAD does not exist, there is no convention proven by examples; the convention written in the repository guide is still a valid source. For ADRs, apply the directory and selection of (adr.md, File).

### Writing heuristics

Write declaratively. Hedging, meta-narration, placeholders and tags other than `[ASSUMPTION]` and `[GAP]` are form-check findings in all four artifacts. The review applies (prose.md, Editorial checklist), without a new style cycle.

A clean form check proves the skeleton; the review checks the content.

## Review per entry point

Do this review before presenting each artifact, after the form check. It applies to every entry point: no section exists only to satisfy form.

The list of the entry point is closed. In Specify, Design, Tasks and ADR:

- go through the whole artifact for each item and give the item the score `max(0, 100 - 20 × occurrences)`; one occurrence already brings the item down, and the score exists to record how many;
- an item below 90 is rewritten; an item at 90 or above stays as it is;
- if you rewrote, score again: there are at most two passes per artifact;
- an item that remains below 90 in the second pass does not hold the artifact: present it and say in the chat which item it is, with the score and what is missing.

In Execute and Verify the items are binary, satisfied or not: an unsatisfied item is fixed within the ceilings of its own cycle (execute.md, Per-task cycle; verify.md, Gaps and correction tasks).

| Occurrences | Score | Action |
|---|---|---|
| 0 | 100 | Item passes |
| 1 | 80 | Fix the item |
| 2 | 60 | Fix the item |
| 3 | 40 | Fix the item |
| 4 | 20 | Fix the item |
| 5 or more | 0 | Fix the item |

### Revalidation after review

The review comes after the form check. If it changed the artifact, repeat the form check on the changed passages; fix what you find once and reread to confirm. If it persists, list it in the chat. That extra pass does not reopen the review indefinitely. The editorial checklist (prose.md, Editorial checklist) makes the existing review concrete, without a score, section or additional cycle.

### Specify

- `SHALL`, unique ID and EARS pattern are already in the form check; here: the pattern is correct, the value of each requirement is concrete and the test that asserts it can be written. If it cannot, rewrite the requirement.
- Each scenario of the PRD's Acceptance Criteria appears in the Traceability with the EARS IDs that cover it: presence is already in the form check when the PRD lists them in a table; here, the listed IDs actually cover the scenario.
- A requirement that comes from the PRD cites the ID and does not rewrite the rule.
- No business rule was decided by a new assumption in the spec; an assumption inherited from the PRD, with its origin noted, does not count.
- Every inference is tagged.
- If Design will need to decide behavior, the spec is incomplete.

### Design

- Depth proportional to risk: the form check marks the section above ten body lines; here you decide, for each marked section, whether its subject appears in Risks and Techniques — if it does not, it is inflation and the section shrinks. A risk without a technique or acceptance is a hole.
- Criteria fixed and criticized before the approaches; the fourth question (is there a cheaper or less risky way to do the same?) answered.
- No behavior decided here that should have been in the spec.
- Interfaces with types; the coverage of every `IF/THEN` of the spec in error handling is already in the form check.
- ADRs conformed to or superseded (adr.md, Conform and supersede).

### Tasks

- Coverage requirement to task and task to requirement is already in the form check; here: no task cites in `Requirement` an ID it does not exercise.
- `Consumes` and `Produces` consistent between the tasks and with the design, sufficient without reading another task; the executor also reads the cited requirements and the pertinent passage of the design (tasks.md, Interfaces).
- `Tests` consistent with the layer of the task, with co-located tests.
- `Done when` with a behavior criterion the spec defines; the form check already requires that the command in backticks be the task's gate and that a criterion beyond it exist; the content of the criterion is yours to check.

### ADR

Four items, those the form check does not reach because they are content, not form:

- The decision fixes a convention, constraint or pattern that future features follow; a decision local to the feature is an occurrence, and its destination is the design (adr.md, When a decision is project-wide).
- Each row of Alternatives considered is an alternative actually evaluated, brought down against the same criteria that support the decision; an alternative written to fill the table is an occurrence.
- The `Negative` line names the accepted cost of this decision; a generic risk, which any decision would have, is an occurrence.
- Every project rule the decision creates or alters is in Derived rules; the bullet and the file path are already in the form check (adr.md, Conform and supersede), here: each listed rule is really created or altered by this decision, and an ADR that creates no rule has no section.

### Execute

- Plan declared before the code.
- Spec tests failing before the implementation.
- Minimal implementation.
- Green gate.
- Evidence table filled in.
- No gap resolved silently.
- Only the files of the task touched.

### Verify

- Coverage re-derived with fresh eyes, with the degree of independence declared in the report.
- Every evidence row with `file:line` and assertion.
- Precision gap reported, never approved.
- Both axes covered: spec conformance and design adherence.
- Gaps ordered by severity (verify.md, Chat report) and converted into `TCn` correction tasks.
