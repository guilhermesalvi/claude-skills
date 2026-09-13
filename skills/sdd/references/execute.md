# Execute

**Goal:** implement one task at a time. Each task is a cohesive deliverable, with tests derived from the spec, the gate run and an atomic commit when commit is authorized. Each task carries its own verification (tests, gate and post-gate review).

## Before the first task

1. **Prerequisite.** The one in the table of workflow.md, Opening a change. The request "implement it" authorizes editing the files of the change; commit is its own authorization, given once per change and valid for all its commits (workflow.md, Approval and authorizations).
2. **Context.** Read the task, the passage of the design it references and the spec requirements it satisfies. Do not load other changes into the context.
3. **Verification base.** If the change has neither its own branch nor an `NNNN-<change-slug>` folder (rules 2 and 3 of verify.md, Scope, return no hash), record the base before the first code edit: `git rev-parse HEAD`, saved as the line `Base: <hash>` of the "How this repository tests" paragraph of `tasks.md` or as the slot `; base: <hash>` of the `Gate` line of the inline plan. With its own branch or a change folder, record nothing: Verify resolves the base on its own, and an extra hash would be a second source of the same information.
4. **`[GAP]` in the way.** The rule of workflow.md, Tags and doubts applies; in a task, the three outcomes take this form:
   - decide and record the decision, when it fits within the granted autonomy;
   - tag `[ASSUMPTION]` in the spec, with default and rationale; the changed spec enters the task commit after approval of its content (workflow.md, Approval and authorizations); without a commit, follow the condition in After the last task;
   - ask the user ("The design has a gap: […]. Options: […]. I recommend […].") and execute only what does not depend on the answer.

### Inline plan

When the Tasks entry point was waived, Execute starts with an inline plan, presented in the chat before any code:

```markdown
## Plan
Requirements: [spec.md IDs the plan covers]
Structure: [one or two lines: where it goes, what it reuses]
Gate: [build and test command of the repository]; [total tests it runs before the change][; base: hash, when the change records the base][; mutation: command, when the change declares mutation]
1. [step] → files: […] → verifies: [how]
2. …
```

The `Gate` line is where the mutation command is declared when the change has no `tasks.md`: append `; mutation: <command>` to it when the user asks for mutation in this change or when the Risks and Techniques table of the design requires it (verify.md, Mutation). Without the declaration, the line ends at the test total and the change does not run mutation. The slot `; base: <hash>` of the same line is where the base goes when the change has no `tasks.md`, and it enters only under the conditions of Verification base, above.

Each step of the plan is a cohesive deliverable, by the same criterion as a task (tasks.md, Atomic task). If the list triggers a design or tasks trigger, follow the ratchet and its prerequisites (workflow.md, How much artifact the change needs).

## Per-task cycle

1. **Choose.** Execute the task the user indicated ("do T3") or, without indication, the next available one in the order of the plan. If the task depends on another not yet completed, ask before starting.
2. **Declare the task plan** in three lines: the files it will touch (only those of the task), the approach and how it will verify. If you see a simpler approach than the design's, or disagree with it, say so before implementing, not after.
3. **Write the tests derived from the spec.**
   - Each requirement of the task has at least one assertion whose expected value is the result the spec defines (status, message, state, event).
   - If the spec does not define a precise result, that is a precision gap: go back to the spec and fix it there; do not write a vague assertion.
   - The inherited scenarios listed in the Traceability of the spec (specify.md, Sections) are the minimum suite: each one becomes at least one test.
   - A test of new behavior fails before the code exists. A characterization test (behavior the change preserves) passes before and after.
   - If a test looks wrong when confronted with the spec, stop and confirm which is right before moving on: the tests are the executable spec.
4. **Implement** the minimum that satisfies the task.
   - Nothing beyond the request: no single-use abstraction, no unrequested flexibility, no handling of impossible scenarios.
   - Do not "improve" adjacent code or formatting; follow the existing style even when disagreeing with it.
   - A neighboring problem (bug, debt, dead code) is reported to the user, not fixed in the task.
   - An indispensable file discovered during implementation (registration, config) enters the `Where` field of the task, with a note saying why it entered. The note is the complete record and the only route: the file does not become a `SPEC_DEVIATION` nor a line in `## Deviations`, and in axis 2 of Verify it counts as conformance to the design, not as a gap or a deviation (verify.md, Axis 2: design adherence). A file that changes behavior does not fit here: it is a deviation, through the path of Refine the context, not the error.
5. **Run the gate.** Run the command of the task's level, read from the Gate Commands table of the change's `tasks.md` (described in tasks.md, Gate Commands) or from the `Gate` line of the inline plan. Non-zero exit: fix and run again, at most twice; if the third run still does not exit 0, stop and report the failure as it is, without weakening the test. A gate that cannot run (missing SDK, unavailable dependency) is not a green gate: the task is blocked, with the reason recorded.
6. **Review after the gate.** With the gate green, check:
   - every item of `Done when` is satisfied, including the behavior criteria;
   - no `SPEC_DEVIATION` was left unrecorded in `## Deviations`;
   - none of the three complexity signals is present: an abstraction used only once, a parameter or option with no caller, a layer the design does not ask for. If one is, simplify once and run the gate again, a single time: if it turns red, undo the simplification, because the green gate of step 5 is what the task delivers and insisting on the simplification would open a correction cycle without a ceiling; say in the chat which simplification was undone and what the gate flagged;
   - the evidence table: for each criterion, the `file:line` and the assertion that prove it; and, in the reverse direction, every new test maps to a criterion, requirement or edge case. The table goes in the chat when closing the task and is not persisted in a file.
7. **Close.** Mark the task as completed: every `- [ ]` item of `Done when` becomes `- [x]` in `tasks.md` (or in the inline plan). With commit authorized and with the approval required for the content of a changed artifact (workflow.md, Approval and authorizations), make a commit containing only the files of the task, the `tasks.md` and the spec when the task changed it (step 3), with a message in the format the repository establishes; if the repository has commit message validation, run it before committing. Without an authorized commit, the task closes with the gate green and the files in the working tree.

### Communication form

When starting the task, identify files, approach and verification in the form already required. When closing it, present result and evidence. In a block, state the pending action, what prevents it and which decision or resource resolves it. Use the task name and the pertinent file; do not replace the report with "everything is fine". Keep the required evidence table and do not report as executed a command that was only planned.

## Refine the context, not the error

The general principle is in workflow.md, Tags and doubts. During a task, it applies like this:

- **A wrong spec or design** (impossible rule, inconsistent contract, unforeseen codebase constraint) stops the task. Report in the chat: "I found an unforeseen constraint: […]. It invalidates [spec/design] at […]. I recommend fixing it there and re-deriving [affected tasks]."
- **A wrong business rule** goes back to the PRD first; the spec is only fixed after it.
- **A local deviation** that does not invalidate the artifact gets a marker in the code and a line in `## Deviations` of `tasks.md` (or of the inline plan, in the chat, when there is no `tasks.md`); the section is created at the first deviation. The marker:

```text
// SPEC_DEVIATION: [what diverged]
// Reason: [why]
```

## Security

- **A new package** is a suggestion until its provenance is validated in the official registry: "I suggest `[name]`; before installing, confirm at […]". A package with an invented name may exist in the registry, published by someone malicious.
- **A secret**, connection string or production data found in the codebase never enters the output; flag the occurrence as a risk.

## After the last task

Before Verify, confirm that the spec used by the tests corresponds to the committed version. If a task changed the spec and the change is not yet committed, the implementation may remain in the working tree, but Verify is blocked on that dependency. Ask once for the commit or its authorization, following (workflow.md, Approval and authorizations). Do not verify the implementation against the old spec to work around the block. A tree dirty with code changes does not, by itself, block Verify; the verification still includes the local changes planned in the scope.

Define `SPEC` as the real path of the spec relative to the root and run:

```bash
git cat-file -e "HEAD:$SPEC"
git diff --quiet -- "$SPEC"
git diff --cached --quiet -- "$SPEC"
```

The three commands must indicate that the file exists in HEAD and does not differ in the index or in the working tree. Do not replace that check with the global state of `git status`.

- **Verify.** With that condition met and the last task closed, move to Verify (verify.md, Fresh eyes).
- **Handoff.** An open branch and `git log` are the handoff of the change.

## Resume

First identify the next valid entry point by the prerequisites (workflow.md, Entry points and prerequisites), instead of assuming Execute from the name of the request.

**With tasks.md:** read the artifact, the pertinent requirements, the prerequisites and `git status`. A task only has its completion recorded when every item of `Done when` is marked `[x]`, which requires a green gate. The checkboxes do not authorize using evidence from a previous version if the files were changed afterwards; `git status` is a sign of progress, not proof.

**Without tasks.md:** recover from the conversation the approved inline plan, including requirements, steps, files, gate command, base count and the conditional base and mutation fields. If that plan is not available, do not guess its values from the code. Reconstruct a concrete plan with what is provable and ask for its approval by the existing rule before continuing Execute (workflow.md, Approval and authorizations). If the verification base cannot be recovered through the routes of (verify.md, Scope), keep the block for lack of a base. Do not create a state file or a persisted report.
