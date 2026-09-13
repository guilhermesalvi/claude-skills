# PRD conventions

## Saving

### Path and numbering

- The path is `/docs/prd/NNNN-<domain-slug>-<feature-slug>.md`, with the slug in kebab-case English and without a `prd-` prefix. The folder is a default of this skill: a PRD folder fixed in the repository guide (CLAUDE.md) prevails by precedence (SKILL.md, Limits), and the form check receives that folder as an argument (workflow.md, Check).
- `NNNN` is a 4-digit counter, global in the folder, because it gives a short reference ("PRD 0007") and records the order of arrival. Get the number by listing the `NNNN-*.md` files in the folder and adding 1 to the highest; a folder without PRDs starts at 0001. The exception is PRD 0000, whose number is fixed and does not go through the counter.
- `0000-<slug>-overview.md` is PRD 0000 (writing.md, PRD 0000); an overview saved under any other number is a form-check finding (workflow.md, Check).
- The counter collides when two parallel PRs allocate the same number; the form check flags the duplicate (workflow.md, Check). Renumber the PRD of the branch whose merge happens later: move the file to the next free number in the folder, update whoever cites it and check the numbering again.
- Without a repository, use the same layout under the current working directory and say in the chat which path was written.

### In-place editing

- The PRD is edited in place: the diff is the change, and `git log` is author, date and history.
- The PRD has no status, author, date, confidence or approval field, and is not replaced by a file with a new number.

### Header

The header has these elements, in this order:

1. The first line is `# Title`.
2. Below it, a two-column table with a single field, `Originating Context`. The value is the primary context, followed by `; affects <contexts>` when there are any, with the contexts separated by commas; the impact on each affected context goes to Dependencies and Risks. If DDD does not apply, the label is `Module` or `Area`; the list is closed at those three, and in PRD 0000 only `Scope`.
3. Then the requirement prefix line (writing.md, IDs), with the fixed label `Requirement prefix:`.
4. On the same line as the prefix, the sentence that points to PRD 0000, when it exists.

PRD 0000 differs in three points: it uses `Scope` instead of `Originating Context`, has no prefix line and carries `<!-- prd: overview -->` on its first line, being the only PRD with its own form. No other machine comment enters any PRD.

The field label is fixed in English (Language); the context name preserves the domain term.

```markdown
# Asynchronous Document Verification

| | |
|---|---|
| **Originating Context** | Customer Onboarding; affects Account Activation |

Requirement prefix: `ONB`. Platform purpose, context map, event catalog and flows: [PRD 0000](0000-platform-overview.md).
```

## Language

- **Structure in English, prose in the language of the PRD.** The structure is what the form check reads, and it has a single form, in English: the `##` headings (writing.md, Sections and PRD 0000), the header labels and the prefix line (Header), the tags `[ASSUMPTION]` and `[GAP]` (writing.md, Tags), the form labels `*Cost:*` and `*Reason:*`, `Guardrail`, `**Given**`/`**when**`/`**then**`, `if false`, `; affects` and the `Identifier` column (writing.md, Sections). A translated heading or label is a form-check finding (workflow.md, Check), whatever the language of the prose.
- **The language of the prose** follows precedence (SKILL.md, Limits). When nobody fixed it, it is the language of the received material (with material in more than one language, that of the document the request cites first or, without a citation, that of the first attachment); without material, the language of the request. Once fixed, an explicit language request in the session is precedence and changes it; a message in another language without that request does not. Any language serves for the prose, because the form check does not read it.
- A canonical English term without a translation of the same strength stays in English in the prose: Factory Pattern, Entity Service Antipattern, Bounded Context, Domain Event, Ubiquitous Language, JTBD, MoSCoW, guardrail, leading/lagging, trade-off. Outside that list, translate a term only when the translation already appears in the received material or in PRD 0000; otherwise keep the English original.
- Domain identifiers (`Offering`, `ReservationBook`), IDs and tags are not translated. The domain term stays in the original (writing.md, Ubiquitous Language).
