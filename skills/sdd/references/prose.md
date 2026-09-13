# Writing the SDD artifacts

## Writing conventions

Start with the behavior, decision, deliverable or result the reader needs to understand. Add the pertinent justification and evidence. Avoid introductions that only announce the subject and conclusions that repeat the previous paragraph.

Develop one idea per paragraph. Group the sentences that explain the same decision and separate independent decisions. Preserve the context needed to understand a constraint, a cost or a risk.

Identify the action and its responsible party when known. Use project terms and results defined in the sources. An adjective like "robust" does not replace a mechanism, a failure condition or a test criterion. Missing information follows the rules for assumptions and gaps; editing cannot invent behavior.

Keep names of APIs, types, errors, events, paths and IDs exactly as in the sources. Preserve EARS keywords, modality, negations, comparators, units, deadlines and exceptions. Simplifying the sentence does not authorize changing the contract.

Use prose for context and justifications, lists for requirements and steps, and tables for comparable decisions, interfaces and evidence. Keep the fields and headings the artifact requires. Additional formatting is only useful when it helps distinguish different information.

Avoid stock phrases, invented labels and counterpoints without a real alternative. Authorization, security and scope limits remain explicit. Remove rhetorical excess without erasing the constraint.

## Instruction text

State the condition, the action and the output when the procedure depends on a decision. Put the exception next to the rule or cite its file and heading. Use steps for sequences and tables for execution alternatives. Do not impose that format on every explanation.

Vague references must be replaced by the concrete object. The task defines the interfaces it needs; the spec and the design remain the sources of behavior and decisions. Reference the needed section, without sending the reader to documents unrelated to the entry point.

## Artifact text

Apply the form of each entry point: `specify.md`, `design.md`, `tasks.md` and `adr.md`. For execution and evidence communications, use execute.md and verify.md. Language, approvals and prerequisites follow workflow.md. Validation follows validation.md.

The editorial conventions do not replace fields, EARS requirements, traceability or evidence. Examples illustrate writing; their data and results do not become facts of a real change.

## Editorial checklist

In the review already planned, check: main point identifiable; paragraph unity; action and responsible party clear; verifiable result when required; vocabulary and modality preserved; uncertainty visible; reference resolvable; functional formatting; no repetition without value.

Do not create a score, section or round exclusive to this list. If the review reveals a pending behavior decision, follow the return to the source artifact defined in the workflow. The text cannot resolve that decision on its own.
