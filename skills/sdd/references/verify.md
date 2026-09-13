# Verify

**Goal:** after the last task, prove with evidence that the implementation satisfies the spec (axis 1) and the design (axis 2). The report goes in the chat; no report is persisted in a file.

## Fresh eyes

Re-derive the coverage from the spec and the tests of the change. Use the mode of greatest independence the environment offers, according to the modes below. Do that re-derivation without starting from the Execute evidence table (in the author's own pass, the table is opened only at the end, to compare), and declare in the report what the degree of independence of the verification was:

- **Fresh subagent:** independent verification, done by someone who did not write the code. It is the mandatory mode when the subagent tool exists in the environment.
- **Author's own pass:** partial independence, only when there is no subagent tool; say so explicitly.

The degree of independence does not change the rest: each requirement has evidence or counts as zero, and both axes are verified.

## Scope

The object of the verification is the diff of the change, `git diff <base>`, plus the staged, unstaged and untracked files listed in the `Where` fields of the tasks (or in the inline plan).

`<base>` is the result of the first of these rules that returns a hash, in this order:

1. the base recorded before the first task: the hash of the line `Base: <hash>` of the "How this repository tests" paragraph of `tasks.md`, or of the slot `; base: <hash>` of the `Gate` line of the inline plan (execute.md, Before the first task);
2. with its own branch: `git merge-base HEAD <main branch>`;
3. `git log --diff-filter=A --format=%H --reverse -- <capability-dir>/NNNN-<change-slug> | head -n 1`, the first commit that added a file in the change folder, that is, the one that created the folder; the base is `<hash>^`. The `--reverse` with `head -n 1` is what returns the first: `-1` would return the most recent commit that touched the folder, and with `design.md` and `tasks.md` in separate commits (workflow.md, Approval and authorizations) the two diverge.

The commit that touched `spec.md` never serves as the base: the spec is living and its last commit may be from another change, or from the middle of this one. If none of the three returns a hash: the verification is blocked for lack of a base. Say which rule failed and ask which commit is the base; do not guess.

Under rule 3, a foreign commit made between the creation of the folder and HEAD enters the diff. A file that came from such a commit is not a gap: name it in the report as outside the change and leave it out of both axes; `git log --format=%h --follow -- <file>` says which commit it came from.

User changes outside the change stay outside the verification and untouched: Verify does no `add`, `stash`, `checkout` nor "restores" any file. Running the gate at `<base>` is no exception: it has its own route, in a separate tree, that does not touch this one (Build gate).

## Axis 1: spec conformance

Before computing coverage, confirm the committed version of the spec through the three commands and the blocking output defined in (execute.md, After the last task).

For each requirement in scope, fill one row of the evidence table. The Result column takes one of these values: `covered`, `gap` or `precision gap`.

| Requirement | Result defined by the spec | `file:line` + assertion | Result |
|---|---|---|---|
| RSV-07 | rejects with `MIN_INVESTMENT_NOT_MET`, book unchanged | `tests/…/PartialReservationTests.cs:41` — `result.Error.Should().Be(ReservationError.MinInvestmentNotMet)` | covered / gap / precision gap |

- The assertion must target exactly the result the spec defines; the existence of just any assertion is not enough.
- A requirement without `file:line` counts as not covered. Before declaring absence, search for the requirement ID and the identifier of the result the spec defines (error, event, status) in the tests of the diff and in the tests of the layer, and show in the report the terms searched.
- A requirement whose spec does not define a precise result is a precision gap: it enters the report as such and is never approved silently.
- A requirement retired in the spec (specify.md, Living spec) requires confirming that the behavior no longer exists: the test was removed with the reason recorded and no live code path still implements it.

## Build gate

Run the Build gate: the command of the Gate Commands table (tasks.md, Gate Commands) or, when the change uses an inline plan, the gate declared in the plan. Record in the report the command, the total tests, the passed, the failed, the skipped and the exit code.

Compare the test count with the base count recorded in the "How this repository tests" paragraph of `tasks.md` or in the `Gate` line of the inline plan (tasks.md, How the repository tests) and investigate any drop: only the retirement of a requirement with a recorded reason justifies fewer tests; a drop without that reason is a gap.

- A skipped test is not evidence.
- Zero tests executed is not a green gate.
- A gate that cannot run is a block with a declared reason, not a failure.
- **A red gate does not close the change.** Each failed test enters Gaps with the test name and becomes a correction task `TCn` (Gaps and correction tasks); the assertion is not loosened, skipped or mocked. A test that already failed at the base is reported as pre-existing, stays out of the gaps of this change and does not become a `TCn` — and only with the confirmation below.

Confirming a pre-existing failure has one route, and it is this:

1. `git worktree add <temp-dir> <base>`, with `<base>` being the hash from Scope and `<temp-dir>` a directory that does not yet exist, outside the working tree of the change.
2. Run inside `<temp-dir>` the same gate command that ran in the tree of the change.
3. `git worktree remove --force <temp-dir>` at the end, always, whether the test failed there or not. The `--force` is mandatory: the gate leaves untracked artifacts inside the worktree (`bin/`, `obj/`, `node_modules/`, `__pycache__`) and without it the remove exits 128 with `contains modified or untracked files`. There is nothing of the user's there — the directory only has what step 1 and step 2 put in it.

The worktree checks out `<base>` in a separate tree and does not touch the working tree of the change. Therefore the prohibition of `add`, `stash`, `checkout` and of "restoring" any file (Scope) still applies in full: this route uses none of the four, and no other path to `<base>` is allowed.

A `git worktree add` that exits with a code other than 0 ends the attempt: report the test as a failure not confirmed at the base and treat it like any failed test — it enters Gaps and becomes a `TCn`. Do not repeat the command, do not try another route and do not ask the user. If it is `git worktree remove --force` that exits other than 0, the gate result at the base still counts; name in the report the directory left behind and move on.

## Axis 2: design adherence

Compare with `design.md`, with the structure declared in `tasks.md` or with the inline plan, according to what the change has. Verify:

- **Structure:** files, components and location match the design. A file the design did not list and that a task recorded in the `Where` field with the discovery note counts as conformance, not as a gap: the rule is in execute.md (execute.md, Per-task cycle) and it is the one that applies here. A file in the diff that neither the design lists nor any note explains is a gap, except one that came from a foreign commit within the base window (Scope).
- **Responsibilities:** each component does what the design says, and only that.
- **Interfaces:** signatures equal to those of the design.
- **Dependencies:** none outside the plan (package, module, service).
- **Module boundaries:** no cycle between modules; no internal class of another module instantiated.
- **Domain events:** conforming to the design contract.
- **Risks:** mitigated as the design promised.
- **Deviations:** each `SPEC_DEVIATION` justified and listed.

In the report, only items with a caveat or not satisfied get a remark. An unsatisfied item becomes a gap.

## Code quality

For each file of the diff, verify:

- nothing beyond the request;
- no single-use abstraction;
- no unrequested flexibility;
- adjacent code not "improved";
- existing style followed;
- project test guides followed.

Every test in scope maps to a requirement, an edge case or a `Done when` criterion. An orphan test, without that mapping, is hidden scope.

## Chat report

The report follows this order:

1. **Coverage**, on the first line: N/N requirements with evidence.
2. **Gate:** command, counts and exit code.
3. **Design adherence:** only the caveats.
4. **Gaps**, ordered by severity: requirement without evidence, then precision gap, then design deviation.
5. **Next step.**

A verification blocked by the environment says what was missing and does not close the change.

### Communication form

Start with the coverage and keep the order defined for the report. Distinguish finding, hypothesis and verification not executed. Each caveat identifies requirement or file, expected result and available evidence. If a rule prevented the conclusion, cite the file and the section that establish it. The report must allow deciding the next step without consulting praise or generic conclusions.

## Gaps and correction tasks

Each gap becomes a correction task with ID `TCn`, recorded in `## Correction Tasks` of `tasks.md` (or in the inline plan, when there is no `tasks.md`). The `TCn` task has the same fields as a task (tasks.md, Fields) and enters the Traceability; repeat the form check of `tasks.md` (validation.md, Form check); with commit authorized and content approved, the changed `tasks.md` enters the commit of the first `TC` (workflow.md, Approval and authorizations). The correction task goes back to the Execute cycle and is followed by a new verification. After two correction rounds with a remaining gap, escalate to the user instead of spinning; the re-derivation due to a behavior deviation (Deviations, below) counts within those two rounds.

## Deviations

- **A deviation that changes behavior** does not survive verification. The path is to go back to the source artifact (PRD, spec or design), fix it, obtain its commit (workflow.md, Approval and authorizations), re-derive the implementation and verify again.
- **A deviation without a behavior change** (structure, internal name) is recorded in `## Deviations` of `tasks.md` (or of the inline plan) with a justification and is judged in axis 2. An indispensable file discovered during the task has its own route, and it is not this one (execute.md, Per-task cycle).

## Mutation

Mutation testing runs when the mutation command is declared in this change, and only then. The declaration has one place, and it depends only on whether the change has `tasks.md`: with `tasks.md`, it is the `Mutation` row of the Gate Commands table (tasks.md, Gate Commands); without it, it is the slot `; mutation: <command>` of the `Gate` line of the inline plan (execute.md, Inline plan). It applies equally in the four configurations: with design and without design, with `tasks.md` and without it.

Declaring the command is mandatory in two cases: when the Risks and Techniques table of the design has a row for one of these three risks, and only those — money and financial calculation; security and regulated data; concurrency, duplicates and retry (design.md, From risk to technique) —, and when the user asks for mutation in this change. A design with one of those three risks and no declared command is an axis 2 gap: the risk was not mitigated as the design promised. Outside those two obligations, declaring is the user's option.

The obligation coming from the design does not wait for Verify when there is `tasks.md`: the form check of `tasks.md` reads the Risks and Techniques table of the design and flags a Gate Commands table without the `Mutation` row (tasks.md, Recording in `tasks.md`). Without `tasks.md`, you are the one who checks, in the inline plan, before presenting it.

Use the mutation tool of the language (Stryker.NET, mutmut, cargo-mutants) on the new code and treat a surviving mutant as a gap; a missing tool is a block with a reason, like the gate. This skill does not describe its own mutation procedure.

### Partial didactic rewrite example

A writing fragment; not a complete artifact nor evidence of a real execution.

```text
Before: The tests passed, but there is a small pending coverage item.

After: Coverage: 2/3 requirements with evidence. RSV-03 has no located
assertion for POSITION_ABOVE_MAXIMUM. The gate ran 20 tests, with
20 passed, none skipped and exit 0. The RSV-03 gap prevents concluding
the verification of the change.

Illustrative numbers. In a real execution, use the gate output and
the searches actually performed. The complete report keeps every
item and the evidence the reference requires.
```
