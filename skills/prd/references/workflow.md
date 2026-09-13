# Writing and validation process

## Workflow

1. **Understand.** Assess the scope, the context richness, the discovery material and the need for research as intake.md says; that is where the question ceiling lives, and what to do when the user declines discovery.
2. **Write.** Apply what is in writing.md: capability test, DDD lens, one rule, one place, IDs, PRD 0000, diagrams, sections and writing. Save the file as (conventions.md, Saving) says.
3. **Check.** Do the form check below before presenting.

### Check

Run `python <this skill's folder>/scripts/check_prd.py <PRD folder>` from the repository root (without an argument, the folder is `docs/prd`): it covers items 1 to 9 of the list below and prints one finding per line. Then read the saved PRD for item 10 and for the rendering of the diagrams, which the script does not see. Fix every finding and reread the whole file; there are at most two correction rounds. If the second still ends with a finding, present the PRD and list in the chat each remaining finding with the reason, without declaring complete validation. A form finding required by the request or proven by convention stays as it is, is reported in the chat with the source, using "kept by request" or "kept by convention", and does not count as a round. After the check, do the five-item review (workflow.md, Review before presenting).

1. **Numbering.** The file number is unique in the folder: list the `NNNN-*.md` files under `/docs/prd` and check that no other uses the same number (conventions.md, Path and numbering).
2. **Header.** `#` title, one-field table with one of the accepted labels, prefix line in the fixed form and, when PRD 0000 exists, the sentence that points to it (conventions.md, Header).
3. **Sections.** The mandatory ones present; no `##` outside the Sections table; the table's order; no section empty or reduced to "None.", "N/A" or equivalent (writing.md, Sections).
4. **IDs.** Every FR with a MoSCoW priority and the declared prefix; NFRs without MoSCoW; no ID defined twice; every ID citation resolves to a definition in some PRD in the folder; no `FR-nn` or `NFR-nn` without a prefix (writing.md, IDs).
5. **Forms per section.** Declared Trade-offs with *Cost* and *Reason* in each bullet, up to two lines; Success Metrics with a guardrail; the "if false" assumption only as the first, bold bullet of Open Questions; Weakest Point followed only by References; Given/When/Then scenario citing an ID; Regulatory Considerations bullet pointing to an ID, with a note of up to 20 words; a context listed in `; affects` with a row in Dependencies and Risks (writing.md, Sections).
6. **Links.** Every Markdown link to a local file resolves, relative to the PRD folder or from the repository root.
7. **PRD 0000.** `<!-- prd: overview -->` on the first line, number 0000, no requirement defined; with two or more prefixes in the folder it exists; every regular PRD references it by link on the prefix line (writing.md, PRD 0000).
8. **Diagrams.** Each Mermaid block read line by line: closed fence, diagram type declared, syntax that renders, no reserved word as an alias, label citing an ID and, in `stateDiagram-v2`, the table with the Identifier column next to it (writing.md, Diagrams).
9. **Tags and placeholders.** Only `[ASSUMPTION]` and `[GAP]` in brackets; no TBD, TODO or `[name]`; no prose paragraph repeated from another PRD in the folder.
10. **Synthesis of the material.** With discovery material in text, the three longest sentences of the PRD do not appear literally in it (intake.md, Discovery material).

A valid request to keep a finding is the one in the session that literally names the section, the field or the form that causes it, such as "include a Rollout Plan section". Precedence is defined at the root (SKILL.md, Limits). Convention is proven by the following rule.

### Convention in HEAD

To recognize a convention by examples, use the version of the files present in HEAD. List the files with `git ls-tree -r --name-only HEAD -- docs/prd` and filter the Markdown files of the PRD type under analysis. Read each example with `git show "HEAD:<path>"`. The convention must appear in at least three of those examples. A file that is only staged or untracked does not count. A local change to an already committed file also does not change the HEAD convention. If HEAD does not exist, there is no convention proven by examples; the convention written in the repository guide (CLAUDE.md) is still a valid source.

### Revalidation after review

If the content review changed the PRD, repeat the form check on the changed passages. Fix what you find once; if it persists, list it in the chat with the reasons. This revalidation does not reopen the review. Presenting an artifact with remaining findings does not prove approval.

## Review before presenting

After the form check, evaluate each of the five items below across every section of the PRD. The score per item is `max(0, 100 - 20 × occurrences)`.

| Occurrences | Score | Action |
|---|---|---|
| 0 | 100 | Item passes |
| 1 | 80 | Fix the item |
| 2 | 60 | Fix the item |
| 3 | 40 | Fix the item |
| 4 | 20 | Fix the item |
| 5 or more | 0 | Fix the item |

An item below 90 is fixed; an item at 90 or above stays as it is. After the fix, rescore only that item: there are at most two passes per item. If the second still ends below 90, present the PRD and say in the chat the item, the score and what is missing. Apply the editorial checklist in the same review (prose.md, Editorial checklist), without creating a score or additional cycle.

1. **Capability test.** The Proposed Solution, the solution sentence of the Executive Summary and each FR describe observable behavior, not mechanism (writing.md, Capability test); in a reverse PRD the reach is different, and a single source fixes it (modes.md, Reverse PRD mode). Each mechanism name (technology, component, implementation pattern) in the Proposed Solution, in the solution sentence of the Executive Summary or in an FR is an occurrence; in a reverse PRD, in any section. A term that the request or the material fixes as part of the product, with one line of reason in the chat, is not an occurrence.
2. **One rule, one place.** Each rule exists in a single FR and the rest cites the ID. A paraphrase that diverges from the FR is not resolved here: it becomes a `[GAP]` in Open Questions (writing.md, One rule, one place).
3. **Tags.** Every inference is tagged `[ASSUMPTION]` or `[GAP]`; synthesized discovery has its origin marked (intake.md, Discovery material).
4. **Form.** No section exists only to satisfy form (writing.md, Sections). What is verifiable form was already checked in the form check (workflow.md, Check), including a `##` section outside the table; here remain the two that only content reading catches:
   - a bullet inserted to complete a count, that is, a bullet whose removal takes no information out of the PRD; a bullet that a writing.md rule requires, such as the Success Metrics guardrail tagged `[ASSUMPTION]` (writing.md, Sections), is never an occurrence;
   - a Weakest Point without a judgment decision over known facts (scope cut, threshold, prioritization or target user), that is, cosmetic.
5. **Language and writing.** The prose follows the language fixed by precedence (conventions.md, Language); there is one concept per paragraph (writing.md, Writing). Occurrence: a prose paragraph in a language other than the fixed one, a paragraph with two subjects, or two adjacent paragraphs about the same point. Headings and labels were already checked in the form check and do not count here.

## Present and iterate

When presenting the PRD:

1. Give the file path.
2. Point to the Weakest Point, when the section exists.
3. Present every question that actually entered Open Questions (writing.md, Sections).
4. Add, only when applicable: target user not yet identified and missing signals after declined discovery (intake.md, Edge cases); search not performed (intake.md, Research).
5. Report the form findings kept or not fixed (workflow.md, Check).

Do not invent a weakness, question or section to fill this presentation.

- A change requested by the user that touches two or more sections, or that changes Context and Problem or Proposed Solution, regenerates the whole PRD, because consistency between sections is what a localized adjustment loses. A localized change (one FR, one threshold, one sentence, one `[GAP]`) is a localized adjustment. A regeneration or adjustment requested by the user restarts the Check step.
- Before changing or removing an FR, list who cites the touched IDs (writing.md, IDs).
