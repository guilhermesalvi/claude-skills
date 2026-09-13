---
name: transcript-fix
description: Fix long automatic transcriptions (Whisper .txt/.srt/.vtt) of Portuguese talks and classes where English technical terms, acronyms and proper nouns were transcribed phonetically or translated. Splits the transcript with deterministic scripts, fans chunks out to subagents, keeps a persistent glossary across sessions and validates the merge. Use this whenever the user mentions transcrição, transcript, legenda, Whisper, aula gravada, mentoria, "termos errados na transcrição", or wants transcribed text made faithful to the spoken class — even for a single file and even if they do not ask for chunking.
---

# transcript-fix

Correct a 2–3 h Whisper transcript without the orchestrator ever reading it.
Long transcripts blow output limits and degrade correction quality, and an
agent that cuts text in-context loses or duplicates paragraphs silently. So:
scripts split and merge, subagents read and write files, the orchestrator only
reads manifests, glossaries and reports.

## Orchestrator rules

- Never open `work/chunks/*.txt` or `work/fixed/*.txt` yourself. Only the
  manifest, the glossary, the candidates summary and the reports.
- Never paste chunk text into a subagent prompt; pass file paths.
- Run every subagent with the prompt templates in `references/`; do not
  improvise the correction rules per chunk — inconsistency between chunks is
  the main failure mode.
- Fan out subagents in parallel (one per chunk). If the platform has no
  subagents, run the same prompts yourself one chunk at a time, in separate
  fresh contexts where possible.
- `merge_chunks.py` exit code 1 means a chunk is missing, truncated, padded or
  leaked context: re-run that chunk's subagent; do not hand-fix.

## Layout (one project directory, reused across sessions)

```
<project>/
  glossary.json            persistent, grows every session — never delete
  glossary.md              rendered table, regenerated
  transcripts/<name>.srt   source files
  work/<name>/             chunks/, candidates/, fixed/, manifest.json
  output/<name>.fixed.txt  merged result + .changes.md, .uncertain.md, .report.json
```

Scripts: `scripts/` in this skill directory (`$SKILL`). Python 3.10+, stdlib only.

## Workflow

### 0. Setup
Ask only for what is missing: source file, speaker name, domain (e.g.
"software architecture, DDD, .NET"). Create `glossary.json` as `[]` if absent.
Prefer `.srt`/`.vtt` over `.txt` when both exist — timestamps survive the
whole pipeline and make uncertain passages checkable against the video.

### 1. Split
```bash
python $SKILL/scripts/split_transcript.py transcripts/<name>.srt --out work/<name> --words 2500
python $SKILL/scripts/glossary_tool.py render --glossary glossary.json --out glossary.md
```
Read `work/<name>/manifest.json` for the chunk list. ~2500 words ≈ 15 min of
speech; a 3 h class yields 10–14 chunks.

### 2. Pass 1 — candidates (one subagent per chunk)
Fill `references/prompt-candidates.md` with speaker, domain, chunk file,
glossary table (contents of `glossary.md`) and output path
`work/<name>/candidates/NN.json`. Then:
```bash
python $SKILL/scripts/glossary_tool.py merge --glossary glossary.json --candidates "work/<name>/candidates/*.json"
python $SKILL/scripts/glossary_tool.py render --glossary glossary.json --out glossary.md
```
Show the user the rows that are new or not yet confirmed (evidence A, then B,
then C), grouped, and ask them to confirm or reject by term. Apply:
```bash
python $SKILL/scripts/glossary_tool.py confirm --glossary glossary.json --term "trade-off" --term "Kubernetes"
python $SKILL/scripts/glossary_tool.py reject  --glossary glossary.json --term "foo"
python $SKILL/scripts/glossary_tool.py render  --glossary glossary.json --out glossary.md
```
This is the only human checkpoint that needs domain knowledge; it does not
require watching the video. Confirmed entries stay evidence A in every future
session.

### 3. Pass 2 — correction (one subagent per chunk)
Fill `references/prompt-fix.md` with speaker, domain, chunk file, context file
(from the manifest; omit for chunk 00), glossary table, output
`work/<name>/fixed/NN.txt` and changes `work/<name>/fixed/NN.changes.json`.

### 4. Merge and validate
```bash
python $SKILL/scripts/merge_chunks.py --work work/<name> --out output --name <name>
python $SKILL/scripts/check_consistency.py --glossary glossary.json --text output/<name>.fixed.txt
```
Re-run failed chunks until merge exits 0. For `check_consistency` findings,
spawn one subagent with the merged file and the exact list of leftovers /
spelling variants to normalize — nothing else — then re-run the check.

### 5. Uncertainties
Read `output/<name>.uncertain.md` (short). Group by likely term, propose the
most probable reading for each group, and let the user resolve in bulk. Apply
the resolutions with a scoped subagent (replace `X [?]` → `Y`), then add the
resolved terms to the glossary via a candidates file + `merge` + `confirm`, so
the next class benefits.

### 6. Report
Deliver `output/<name>.fixed.txt` and a five-line summary: chunks, words
in/out, substitutions applied, uncertainties resolved/remaining, new glossary
terms. Nothing more.

## Tuning
- `--words`: lower (1500) for noisy audio or dense jargon; higher (3500) for
  clean speech. Output-limit-safe up to ~4000.
- `--tolerance` in merge: 0.08 default. Whisper txt with heavy disfluency
  removal may legitimately shrink 10 %; raise to 0.12 only after inspecting the
  drift numbers in the report.
- `--context-words`: 150 default; raise if many `[?]` land in the first lines
  of chunks.
