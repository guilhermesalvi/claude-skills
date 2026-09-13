# ADR

**Goal:** record a project-wide decision with the context, the alternatives considered and the consequences, so that the why survives the code, the diagram and turnover.

## When a decision is project-wide

A decision is **project-wide** when it fixes a convention, constraint or pattern that future features must follow. Examples:

- architectural style;
- event transport;
- form of shared persistence;
- versioning policy.

A decision local to the feature does not become an ADR; its destination is fixed by the design (design.md, Technical Decisions).

Explicit user triggers that ask for an ADR: "record this decision", "this is a project decision", "from now on always…".

## What the ADR keeps

- **The why.** The ADR keeps what code and diagrams do not: the reason for the decision.
- **Alternatives considered and consequences.** Without them, the AI re-proposes paths already discarded and the team relitigates what was already paid for.
- **Participants.** An architecture decision is rarely one person's; the names of who decided and who was consulted are what answers "why did we do it this way?" after turnover. It is content of the decision, not an authorship field of the file (specify.md, Versioning).

### Writing form

In Context, state the problem and the constraints. In Decision, state the choice in direct order. In the alternatives, explain why each option actually evaluated was discarded using the criteria of the decision. In the consequences, describe concrete benefits and costs. Preserve participants, supersession references and paths of the derived rules. Do not add a fictional alternative or a generic cost to complete the format.

## File

Save in `docs/adr/NNNN-<slug>.md`. Get `NNNN` by listing `docs/adr` and adding 1 to the highest number (validation.md, Numbering).

A project that already has an ADR format or directory keeps its own; this entry point does not create a parallel format. The project format is a convention when it appears in at least three committed ADRs or is written in the repository guide (validation.md, Form kept by request or convention). Form findings that follow from it are kept and reported.

To recognize a convention by examples, use the version of the files present in HEAD. List the files with `git ls-tree -r --name-only HEAD -- docs/adr` (or the directory actually established by convention) and filter the Markdown files that are ADRs. Read each example with `git show "HEAD:<path>"`. The convention must appear in at least three of those examples. A file that is only staged or untracked does not count. A local change to an already committed file also does not change the HEAD convention. If HEAD does not exist, there is no convention proven by examples; the convention written in the repository guide is still a valid source.

With one or two ADRs in their own format and no written convention, ask which format applies before saving; without an answer, save in the format of this entry point. A session request that literally names the section, the field or the form exempts in the same way, without counting (validation.md, Form kept by request or convention).

Complete template with replaceable fields: fill them with the real decision. Unknown participants, alternatives and costs must not be invented; an instructional field of this template is not a permitted placeholder in the delivered artifact.

### Template

```markdown
# ADR 0007: Domain events leave through a transactional outbox

Participants: [who decided]; [who was consulted].

## Context
[Situation and constraints that forced the decision; what was at stake.]

## Decision
[One sentence: what we will do.]

## Alternatives considered
| Alternative | Why rejected |
|---|---|
| [evaluated alternative] | [what brought it down, against the same criteria] |

## Consequences
- Positive: [what the decision buys]
- Negative: [the accepted cost; an ADR without a negative consequence is an unexamined decision]

## Derived rules
- [rule that exists because of this ADR] — `CLAUDE.md`
```

The ADR sections are those of the template, and the list is closed: the form check flags a `##` section outside it (validation.md, Form check). The headings are fixed in English whatever the language of the prose (workflow.md, Language).

## Before presenting

Do the form check (validation.md, Form check); then go through the closed list of the ADR entry point (validation.md, Review per entry point) and present.

## Conform and supersede

- **Read before designing.** Every Design reads the active ADRs before designing; an active decision is a constraint. When the best for the feature conflicts with an active ADR, the way out is to conform or supersede, never to ignore.
- **How to supersede.** Create a new ADR with the line `Supersedes: NNNN` below the title. In the old ADR, add `Superseded by: NNNN` in the same place (below the title) and change nothing else in it. Never delete an ADR.
- **A derived rule cites the ADR.** A project rule the change creates or alters (in CLAUDE.md or .claude/rules) cites the ADR or the principle that justifies it. A rule without a why is followed blindly or ignored.
- **A derived rule has a path.** Each rule in the `## Derived rules` section is a bullet and carries, in backticks, the path of the file where the rule lives — `CLAUDE.md`, `.claude/rules/tracing.md`. A rule without a path is not locatable and is not followed; the form check flags the section without a bullet and the bullet without a path. The section exists only when the decision creates or alters a rule.

### Partial didactic rewrite example

A writing fragment; not a complete artifact nor evidence of a real execution.

```text
Before: A decision was taken to the effect that the publication of domain
events will be carried out by means of a transactional outbox.

After: Domain events will be published through a transactional outbox.

Preserved: the choice shown in the title of the existing template.
The pair does not claim that this ADR was adopted by the project.
```
