---
name: sdd
description: 'Specifies, designs, plans, implements and verifies changes with traceable technical requirements (Specify, Design, Tasks, Execute, Verify). Use for "tech spec", a "spec" of system behavior, "solution design", "break it into tasks", "implement the spec", "verify the implementation", "resume" a change, "document the spec of module X" and when implementing a non-trivial feature even without mentioning a spec; not for PRDs, discovery, a standalone ADR, code review without a spec or mechanical refactors.'
---

# Spec-Driven Development

This skill turns technical requirements into a verifiable change: Specify defines what, Design defines how, Tasks defines the order, Execute implements one task at a time and Verify proves conformance to the spec and the design. The PRD in `/docs/prd`, with `<PREFIX>-nn` IDs, provides the business rules; this method starts where it ends.

Use it for a technical or system-behavior specification, solution design, decomposition into tasks, non-trivial implementation, verification and resumption of a change, including "document the spec of module X". PRD/discovery, a standalone ADR, code review without a spec and mechanical refactors stay outside this flow. A project-wide decision identified in Design uses the ADR entry point.

| Request | Entry point | Read | Result of this entry point |
|---|---|---|---|
| Define technical behavior; tech spec; document an existing module | Specify | [specify.md](references/specify.md) | Spec |
| Design a solution; solution design | Design | [design.md](references/design.md) | Design |
| Decompose work; break into tasks | Tasks | [tasks.md](references/tasks.md) | Tasks |
| Implement; implement the spec; non-trivial feature | Execute | [execute.md](references/execute.md) | Executed tasks and Verify at the end of the authorized implementation |
| Verify the implementation | Verify | [verify.md](references/verify.md) | Evidence report |
| Resume a change | Resumption routing | [execute.md — Resume](references/execute.md#resume) | Next valid entry point, determined by the prerequisites |
| Record a project-wide decision discovered in Design | ADR | [adr.md](references/adr.md) | ADR |

Execute only the requested entry point and the prerequisites it needs. A request for a spec, design or tasks does not authorize implementing code. In an authorized implementation, Specify precedes Execute; finishing the implementation requires Verify. The approval required between entry points still applies.

Before acting, read the pertinent prerequisites and authorizations in [workflow.md](references/workflow.md). Commit authorization is permission for the operation; each artifact still requires approval of its content (workflow.md, Approval and authorizations). Business rules are resolved in the PRD; doubts follow (workflow.md, Tags and doubts). A test or validation that was not executed never counts as approval.

For a new entry point, read its whole reference and the normative dependencies. For a localized correction, read the affected passage, its prerequisites, exceptions and citing passages; widen the reading when a dependency appears. Resumption also reads the plan and the requirements of the change. Layout and machine comments are in (specify.md, Layout); the execution contract is in (execute.md, Per-task cycle) and (execute.md, Security).

Writing: [Writing conventions](references/prose.md#writing-conventions), once per version of the file in the writing session. Read examples only for the artifact being produced or to clarify an observed editorial defect. Before presenting a complete or revised artifact, comply with [validation.md](references/validation.md): selective reading waives neither the form check nor the review. A clean form check proves form; the review checks content and does not record validation state in the artifact.
