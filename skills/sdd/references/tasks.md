# Tasks

**Goal:** decompose the design into atomic tasks, with clear dependencies, co-located tests and traceability to the requirement. The result is a plan that an executor without context follows without guessing.

## Prerequisite and destination

- **Prerequisite:** the one in the table of workflow.md, Opening a change.
- **Destination:** save the artifact in `<capability>/NNNN-<change-slug>/tasks.md` (specify.md, Layout).
- **Without a design:** the structure of the change (files, components, what it reuses) goes in a paragraph at the top of `tasks.md`.

## How the repository tests

Before writing any task, find out how this repository tests; do not assume the ecosystem.

### Discovery

1. **Guides:** read `CLAUDE.md`, `CONTRIBUTING.md` and every file in `docs/` with `test` in the name, plus thresholds in runner or CI config. A guide found rules: follow it and cite the file.
2. **Sample:** read 5–10 existing test files and record layer, level (unit, integration, e2e), style, location and framework. The sample is a floor, never a ceiling: no task has fewer test types (unit, integration, e2e) than the sample in the same layer; the ceiling comes from the spec.
3. **Commands:** extract the commands from manifests, config and CI (`*.csproj`/`*.slnx` + `dotnet test`, `package.json`, `Makefile`, `pyproject.toml`, workflows), including lint, format and typecheck, because the Build gate runs all of that.

### No tests or no guide

- **Repository with no tests at all:** ask the user which test types and which commands to use.
- **Without a guide, the strong default applies:**
  - domain (aggregate, use case, domain service): every branch, 1:1 with the requirements;
  - inbound adapter: happy path, each edge case and the error paths;
  - repository: main queries and error;
  - config and schema: only the Build gate.
- **Build is not evidence of behavior:** Build proves it compiles and integrates; evidence of behavior is an assertion against the result the spec defines.

### Recording in `tasks.md`

The result of the discovery enters `tasks.md` in two forms:

- a "How this repository tests" paragraph, with guides, floor, layers and the total tests the Build gate runs before the change, the base count Verify compares against; the line `Base: <hash>` enters that paragraph only when Execute records the verification base (execute.md, Before the first task);
- the **Gate Commands** table, with the When column copied from this one and the Command column coming from item 3 of Discovery:

| Gate | When | Command |
|---|---|---|
| Quick | `Gate: quick` (Fields) | unit test command |
| Full | `Gate: full` (Fields) | command for the whole suite |
| Build | `Gate: build` (Fields) | build + lint + all tests |
| Mutation | mutation required or requested (verify.md, Mutation) | mutation tool command |

The table gives the command of each gate; which gate each task receives is the rule of the `Gate` field (Fields), and that is where it lives: the When column points to the field and does not repeat the rule. The gate name in the first column is one of `quick`, `full`, `build` and `Mutation`; any other row name is a form-check finding (validation.md, Form check).

The `Mutation` row is where the mutation command of the change is declared when there is a `tasks.md`; without it, the change does not run mutation (verify.md, Mutation). It is not a value of the `Gate` field of any task. It is optional, except when the Risks and Techniques table of the design has a row for one of the three risks that require mutation (verify.md, Mutation): then its absence is a form-check finding, which reads the design through the `design:` field of the machine comment.

## Atomic task

A task is a cohesive, verifiable and integrable deliverable: a component, a function, an endpoint, a handler, together with what it needs to be verified and integrated in the same task (implementation, tests and the indispensable registration, such as DI, route or migration). "Implement authentication" is not a task; "create `ReservationService.Place` with idempotency, tests and registration in the module" is. Two independent deliverables in the same task split into two: if the `What` field needs an "and" to join two deliverables, they are two tasks; tests and registration of the same deliverable do not count as a second deliverable.

### Writing form

The title identifies the deliverable. The Where field distinguishes creating from modifying files. Interfaces identifies consumed and produced contracts. Done when describes results that can be checked and puts the gate command in its own item. Avoid expressions that force the executor to guess the reference, such as "the above", "the previous one" or "similar to the other task". Preserve every field and the IDs. The existing behavior and interface specifications remain the source of the expected values.

### Co-located tests

- A task that creates or modifies a layer with a required test type includes writing those tests in the same task. "Tested in task N" is deferral.
- If the code is only testable after another task (a controller before the wiring, for example), move the tests to the task where they become executable (merge forward) or absorb the dependency into the current task (merge backward).
- No task produces unverified code.

### Fields

Every task has the fields below:

| Field | Content |
|---|---|
| **What** | One sentence: the exact deliverable |
| **Where** | Real paths of every file to create or modify, distinguishing each case in text |
| **Depends on** | Task IDs, or `none`. The dependency points only backwards in the order of the Execution Plan (previous phase or previous task of the same phase); the form check flags the opposite |
| **Requirement** | Spec IDs the task satisfies; a refactor task cites the IDs it preserves |
| **Interfaces** | *Consumes* and *Produces*: names, parameters, return types, errors and relevant external contracts defined in the spec/design (Interfaces) |
| **Done when** | Binary criteria tied to IDs: at least one of behavior, with setup, action and result from the spec when it requires concrete values; the gate command goes in a separate item, in backticks and copied character by character from the row the `Gate` field points to in the Gate Commands table; the form check flags the command of another gate, the item without any command and text in backticks that does not start with an executable declared in that table |
| **Tests** | `unit`, `integration`, `e2e` (one or more, in a list) or `none` alone; the tests described in the task are written and executed in it |
| **Gate** | `quick`, `full` or `build`, by the value of `Tests`, in order: the last task of each phase requires `build`, whatever the `Tests`; `none` requires `build`; a list containing `integration` or `e2e` requires `full`; `unit` alone requires `quick`. Every value used has a row in the Gate Commands table, which gives its command; the form check flags every combination of `Tests` and `Gate` outside this rule |

### Interfaces

The task must define the interfaces it consumes and produces without depending on reading other tasks. The executor also reads the requirements it cites and the pertinent passage of the design. Those documents remain the source of behavior and technical decisions. An interface missing from those documents must not be invented.

### No placeholders

None of these enters a task:

- "write tests for the above" without saying which tests;
- "similar to T3": repeat the content, because the executor may read the tasks out of order;
- a type or method that no task nor the design defines.

## Phases and dependencies

- **Phases by cohesion and dependency**, not by size: foundation, then domain, then adapters, then integration. The phases execute in sequence, and the tasks execute in order within the phase.

## Sections of `tasks.md`

The list is closed: the form check flags a `##` section outside it (validation.md, Form check). The heading is fixed in English whatever the language of the prose (workflow.md, Language).

- **Gate Commands** gives the command of each gate (Recording in `tasks.md`).
- **Execution Plan** lists the phases and the order of the tasks.
- **Tasks** has the body of each task.
- **Traceability** maps each requirement to the tasks that satisfy it; mandatory, and the form check checks consistency with the `Requirement` fields.
- **Deviations** is only created in Execute, when there is a deviation.
- **Correction Tasks** receives the tasks Verify generates: `TCn` IDs under `## Correction Tasks`, outside the Execution Plan.

Outside the list, in the machine comment: a change that touches only part of the spec requirements declares `scope:` in it (specify.md, Layout).

Complete didactic format example: the dependencies indicated are assumed only in this example. A real task replaces the illustrative contracts with those verified in the codebase and in the approved spec, and the gate commands with those discovered in the repository (Discovery); the commands below are those of a .NET repository and do not apply elsewhere. The illustrative base count is not evidence of execution.

## Template

```markdown
<!-- sdd: tasks | spec: ../spec.md | design: ./design.md -->
# Partial Reservation — Tasks

How this repository tests: `CLAUDE.md` mandates xUnit in `tests/UnitTests` and `tests/IntegrationTests`; the sample uses `[Fact]` + FluentAssertions, one file per class; domain 1:1 with requirements. The Build gate runs 212 tests before this change.

## Gate Commands

| Gate | When | Command |
|---|---|---|
| Quick | `Gate: quick` | `dotnet test tests/UnitTests` |
| Full | `Gate: full` | `dotnet test FundDistributionPlatform.slnx` |
| Build | `Gate: build` | `dotnet build FundDistributionPlatform.slnx && dotnet test FundDistributionPlatform.slnx` |

## Execution Plan

### Phase 1: Domain
T1 → T2

## Tasks

### T1: Create the `PartialReservation` value object
- **What:** value object with the reserved quantity, validation against the minimum investment (RSV-07) and its unit tests
- **Where:** `src/ReservationBook/Reservations/PartialReservation.cs` — create; `tests/UnitTests/Reservations/PartialReservationTests.cs` — create
- **Depends on:** none
- **Requirement:** RSV-07, RSV-08
- **Interfaces:**
  - Consumes: `Quantity`, `InvestmentLimits`
  - Produces: `PartialReservation.Create(Quantity amount, InvestmentLimits limits): Result<PartialReservation, ReservationError>`
- **Done when:**
  - [ ] `Create` rejects a quantity below the minimum investment with `ReservationError.MinInvestmentNotMet` (RSV-07)
  - [ ] `Create` accepts a quantity within the limits and preserves the value (RSV-08)
  - [ ] Gate passes: `dotnet test tests/UnitTests`
- **Tests:** unit
- **Gate:** quick

### T2: Integrate the reservation validation into the use case
- **What:** integrate the PartialReservation validation into the reservation use case and verify the results defined for the minimum investment
- **Where:** `src/ReservationBook/Reservations/ReservationService.cs` — modify; `tests/UnitTests/Reservations/ReservationServiceTests.cs` — create
- **Depends on:** T1
- **Requirement:** RSV-07, RSV-08
- **Interfaces:**
  - Consumes: `PartialReservation.Create(Quantity amount, InvestmentLimits limits): Result<PartialReservation, ReservationError>`
  - Produces: `ReservationService.Place(PlaceReservation cmd, CancellationToken ct): Task<Result<Reservation, ReservationError>>`
- **Done when:**
  - [ ] With investment limits set up in the test, a quantity below the minimum results in ReservationError.MinInvestmentNotMet (RSV-07)
  - [ ] With the same limits, a valid quantity is accepted and its value is preserved in the reservation (RSV-08)
  - [ ] Gate passes: `dotnet build FundDistributionPlatform.slnx && dotnet test FundDistributionPlatform.slnx`
- **Tests:** unit
- **Gate:** build

## Traceability

| Requirement | Tasks |
|---|---|
| RSV-07 | T1, T2 |
| RSV-08 | T1, T2 |
```

## Before presenting

Do the form check (validation.md, Form check) and the review of the entry point; then present and wait.

### Partial didactic rewrite example

A writing fragment; not a complete artifact nor evidence of a real execution.

```text
Before: Write tests to properly validate the rejection and make sure
everything works.

After: Set up investment limits in the test and call Create with a
quantity below the minimum. Check the result
ReservationError.MinInvestmentNotMet, as RSV-07 defines.

Origin of the result: the contract used in the didactic Tasks template.
In the real task, the value must come from the actually approved spec.
```
