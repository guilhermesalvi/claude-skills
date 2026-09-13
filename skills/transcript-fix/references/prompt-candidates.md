# Subagent prompt — pass 1: term candidates

Fill the placeholders and hand this to one subagent per chunk. The subagent
reads the chunk file itself; do not paste chunk text into the prompt.

---

You are reviewing one chunk of an automatic Whisper transcription of a
~3-hour software class in Brazilian Portuguese, taught by {{SPEAKER}}
(domain: {{DOMAIN}}). There is no audio; everything must be inferred from text.

Read `{{CHUNK_FILE}}`. Do NOT correct anything. Your only job is to list terms
the transcriber likely got wrong.

Whisper renders English technical terms, acronyms, people, products and book
titles by Portuguese phonetics ("cuberneti" → Kubernetes, "tredófi" →
trade-off, "eventi sórcin" → event sourcing), sometimes translates them, and
sometimes maps a proper noun onto a common word. Look for: words that do not
exist in Portuguese, odd phonetic sequences, proper nouns spelled as common
words, technical terms that were translated or Portuguese-ized, and correct
terms that are candidates for error elsewhere.

Existing glossary (already known; still report new wrong forms of these terms):
{{GLOSSARY_TABLE}}

Write your findings to `{{OUTPUT_FILE}}` as a JSON list, nothing else:

```json
[
  {
    "variants": ["tredófi", "trade of"],
    "correct": "trade-off",
    "evidence": "A",
    "occurrences": 3,
    "example": "…é um tredófi entre latência e consistência…"
  }
]
```

Evidence scale — be honest, this drives what gets auto-applied:
- A: the context settles it; no reasonable alternative.
- B: probable, but a plausible alternative exists.
- C: something is wrong here but you cannot tell what was said. Leave
  `correct` as an empty string; do not guess.

Include everything, even obvious cases. Keep `example` under 20 words.
Reply in chat with only the number of candidates written.
