---
name: prd
description: 'Creates and refines product or feature PRDs: problem, user, business behavior, requirements with IDs, metrics and trade-offs. Use for "PRD", "product requirements", "product spec", "let''s document/specify this feature", including a PRD of what already exists and requests framed as a screen or CRUD; not for tech specs, design, tasks, ADRs, meeting notes, general documentation or an API spec without product context.'
---

# PRD Writer

Describe the problem, the affected user and the expected business behavior. Implementation decisions belong to the technical work that follows.

## When it is a PRD

A PRD covers a feature, a digital product or a technology initiative with functional impact on a user: after it, the user observes a new result, a new piece of data or a new deadline. A technical initiative that produces one of those three gets a PRD focused on the impact, not on the implementation.

| Not a PRD | Right artifact |
|---|---|
| Technical debt, refactor, modernization without new functional impact | ADR, technical debt doc, refactor plan |
| Architectural decision | ADR |
| Internal process without software delivery | Runbook, process doc |
| API contracts, modules, task plan, component design | Tech spec, design doc |

Two framings get their own treatment:

- A request that is a PRD but arrives framed as implementation, screen or CRUD: reframe it by the problem (intake.md, Problematic scope).
- "PRD of what already exists" (reverse PRD) and platform, infra, SDK or API as a product: follow modes.md; it says what changes in each of those modes.

## Choosing the entry point

| Request | Entry point | Read | Result of this entry point |
|---|---|---|---|
| Create a PRD; product requirements; specify a product or feature | Creation | [intake.md](references/intake.md); [writing.md](references/writing.md); [conventions.md](references/conventions.md); [workflow.md](references/workflow.md) | PRD and presentation of the pertinent open items |
| Update a PRD | Editing | Current PRD; [IDs and affected rules](references/writing.md#ids); [In-place editing](references/conventions.md#in-place-editing); [workflow.md](references/workflow.md) | The same file, revised and checked |
| Document the existing product | Reverse PRD | Creation references and [Reverse PRD mode](references/modes.md#reverse-prd-mode) | Reverse PRD with inferred intent tagged |
| Platform, infra, SDK or API as a product | Product consumed by another team or system | Creation references and the [corresponding mode](references/modes.md#platform-infra-sdk-or-api-as-a-product-mode) | PRD with consumer and metrics suited to the product |

Writing: [Writing conventions](references/prose.md#writing-conventions). Read the policy once per version of the file in the writing session. Read the example per section as the reference below describes.

## Limits

- **Approval is the commit.** A dirty tree is work in progress; the committed file is the valid version.
- **Precedence.** For layout, sections, language and form, the session request comes first, then the repository convention and last the defaults of this skill. A form finding that follows from the first two is kept and reported (workflow.md, Check).
- Tags, IDs and the single source of rules are defined in (writing.md, Tags), (writing.md, IDs) and (writing.md, One rule, one place).
- Check results, confidence notes and validation marks stay out of the PRD. Report verification that was not executed and remaining findings as (workflow.md, Check) says; do not declare approval without evidence.

## Reading per step

For a new entry point, read its references and normative dependencies. For a localized correction, read the affected passage, the prerequisites, the exceptions and the citing passages; widen the reading when a dependency appears. The distinction between regeneration and localized adjustment is defined in (workflow.md, Present and iterate). Selective reading does not waive the validation and review of the complete or revised artifact (workflow.md, Review before presenting).

## Example

A PRD in the target format is in [references/example.md](references/example.md). Read the corresponding section the first time, in this session, you write a section from the table (writing.md, Sections); it is not a template to copy. Additional examples are read only for the section being produced or to clarify an observed editorial defect.
