# Input assessment and research

In the Understand step (workflow.md, Workflow), assess the scope, extract the signals from the received material and decide whether research is needed. Use the tags defined in (writing.md, Tags). To document an existing product or a platform, infra, SDK or API as a product, also apply the pertinent mode from modes.md.

## Collecting questions

1. Extract from the materials the answers already available; do not repeat answered questions.
2. Apply the conditions of scope, classification of an old PRD, originating context (writing.md, DDD lens) and context richness.
3. Gather the pre-generation questions still needed in a single message: there is one round before generating.
4. In the No signals case, use at most the three questions defined in Context richness. That ceiling is not global for the other framings.
5. After the answer, reclassify once and generate, without a second round.
6. If the user declines discovery, generate with the gaps planned in Edge cases, without a new round.

Questions asked when presenting the finished PRD (workflow.md, Present and iterate) stay out of the pre-generation round.

## Problematic scope

Before generating, check whether the scope of the request fits a PRD. Three symptoms call for intervention before any generation:

| Symptom | Action |
|---|---|
| Too broad: an entire domain or two or more initiatives without a cut (e.g. "credit platform") | Do not generate before the cut: propose a cut and ask for validation; if the user does not know or declines to choose, generate for the proposed cut, tagged `[ASSUMPTION]` |
| Framed as implementation (e.g. "PRD for microservice X", "PRD for screen Y") | Redirect: the PRD starts from the problem, not from the implementation |
| CRUD or entity-centric (e.g. "CRUD for X", "registration screen for Y") | Redirect with two questions: which capability or JTBD does this serve? Which business decision happens there? Problems are experienced by people, not by tables needing screens |

In any of the three cases, if the material still does not answer, include in the single round the base question: which user or business problem does this implementation solve?

## Discovery material

Documents received as input (PDF, docx, decks, minutes, briefs and old PRDs) are rich in signals but are not an authoritative source. Treat them like this:

- **Extract before asking.** Lift from the material the signals the PRD needs (problem, evidence, target user, direction, metrics, constraints) before asking the user anything.
- **Inference from discovery is `[ASSUMPTION]`.** What you deduce from the document enters as an `[ASSUMPTION]` derived from it, with the origin in parentheses at the end of the sentence: document name and page or section. Untagged text is fact, by the definition in writing.md, Tags.
- **Synthesize the material.** Preserve and reorganize the relevant information. Searching the material for the three longest sentences of the PRD identifies possible literal copying; its absence does not demonstrate good synthesis. With text material on disk, select the three longest prose sentences of the PRD (12 or more words, outside tables, headings and code blocks) and search for each in the material with `grep -i -F`; a sentence found is reformatting and gets rewritten (workflow.md, Check). For material without text search, read the corresponding passage. The content review verifies the synthesis.
- **An old PRD asks for classification.** Faced with an old PRD, determine which case it is: (a) it serves as a reverse PRD for an increment, (b) it is the document to update in place, or (c) it is only inspiration. The request decides: "increment" or a new feature on top of it is (a), "update" or "fix" is (b), "as a reference" or "similar to" is (c); a request with none of those signals asks the question before generating.
- **Conflicting sources become `[GAP]`.** When two sources contradict each other, record the `[GAP]` with a reconciliation request. Do not pick a side silently.

## Context richness

Count which of the six signals (problem, evidence, target user, direction, metrics, constraints) the received context brings, classify it into one of the three levels and follow the corresponding action:

| Level | Definition | Action |
|---|---|---|
| Enough to write | Target user identified + (problem or solution direction) | Generate the PRD and refine with the user |
| Partial | At least one signal present, without the pair that defines Enough to write | Generate with `[ASSUMPTION]` and `[GAP]`; do not force discovery |
| No signals | No signal: just a feature name, a single word or a generic idea | Ask at most 3 questions, all at once, in a single round: which real problem does it solve? who is the target user? how will we know it worked? With the answers, reclassify once and generate, without a second round |

"Enough to write" does not mean complete discovery: the missing signals are still treated as assumptions or gaps.

### Edge cases

- **A solution without a target user is partial context.** "Problem + solution" without an identified target user is unanchored solution-first. Treat it as Partial: generate with `[GAP]` on the target user and, when presenting, the first question is who the target user is.
- **Declining discovery does not block generation.** When the user declines the questions ("just write it"), generate tagging `[GAP]` on each of the six missing signals and move on. At the end, list what needs to be filled in before any next step. The refusal covers every question of this skill: an unnamed originating context follows the DDD lens (writing.md, DDD lens), and a too-broad scope follows the corresponding row of Problematic scope.

## Research

Do a web search when the PRD will cite a benchmark, competitor, user behavior, trend, technical pattern or regulation that is not in the received material. Cite each source on one line: link, relevant excerpt and reading date.

Without a search tool in the session, the datum is not written from memory: tag `[GAP]` on the passage that depended on it, say in the chat which search is pending and move on. The lack of search does not block generation, nor does it open a round of questions.

### Regulation

In a regulated domain (finance, health, personal data, payments, security, KYC/AML, telecom, energy, or another under a sector authority), the Regulatory Considerations section enters only when the regulation was identified and read; the form of the section, including the tag for an unchecked article, is in writing.md (writing.md, Sections). Verify by search which regulation is in force before including it; a regulatory hypothesis is never binding.
