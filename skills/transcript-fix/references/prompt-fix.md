# Subagent prompt — pass 2: correct one chunk

Fill the placeholders and hand this to one subagent per chunk. The subagent
reads the chunk and context files itself; do not paste their text.

---

You are correcting one chunk of an automatic Whisper transcription of a
~3-hour software class in Brazilian Portuguese, taught by {{SPEAKER}}
(domain: {{DOMAIN}}). The goal is fidelity to what was said — not a better
text. There is no audio.

Files:
- Chunk to correct: `{{CHUNK_FILE}}`
- Context (end of the previous chunk, READ-ONLY — do not include it in your
  output): `{{CONTEXT_FILE}}` (may be absent for the first chunk)

Glossary — apply exactly this spelling:
{{GLOSSARY_TABLE}}

## What to fix
1. Terms in the glossary. Evidence A: apply. Evidence B: apply and append
   ` [?]` right after the term. Evidence C: keep the original and append ` [?]`.
2. English technical terms, acronyms, people, products and books not in the
   glossary that Whisper rendered phonetically, translated or Portuguese-ized.
   Apply only when the context leaves no reasonable alternative; otherwise
   keep the original and append ` [?]`. Never guess.
3. Wrong words from homophony or noise, when the sentence makes the intended
   word evident.
4. Punctuation and paragraph breaks that reflect pauses and topic changes.

## What not to do
- Do not summarize, cut, reorder or add content. Every sentence of the input
  appears in the output.
- Do not formalize spoken register: keep slang, repetitions and colloquial
  constructions. Only pure disfluencies ("é… é… então") may be dropped.
- Do not translate terms said in English. Do not swap words for synonyms.
- Do not touch `[HH:MM:SS]` timestamp markers: keep every one, in place, on
  its own line, same count as the input.
- Do not output the context, headers, notes or commentary.

## Output
1. Write the corrected text — only the text — to `{{OUTPUT_FILE}}`.
2. Write every substitution (ignore punctuation-only edits) to
   `{{CHANGES_FILE}}` as a JSON list:
   `[{"original": "tredófi", "corrected": "trade-off", "evidence": "A", "count": 3}]`
3. Reply in chat with one line: words in / words out / number of `[?]`.
