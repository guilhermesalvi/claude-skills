# claude-skills

Claude Code skills packaged as a plugin, usable in any project.

| Skill | What it does |
|---|---|
| `prd` | Writes and revises product requirement documents: problem, target user, business behavior, requirements with IDs, metrics and trade-offs. Ships a deterministic form check (`scripts/check_prd.py`). |
| `sdd` | Spec-driven development: Specify, Design, Tasks, Execute and Verify, with traceable technical requirements (EARS) and ADRs. |
| `transcript-fix` | Corrects long Whisper transcripts of Portuguese talks where English technical terms were transcribed phonetically. Splits, fans out to subagents, keeps a persistent glossary and validates the merge. |

## Install

Inside Claude Code:

```
/plugin marketplace add guilhermesalvi/claude-skills
/plugin install claude-skills@claude-skills
```

The skills are then available as `/claude-skills:prd`, `/claude-skills:sdd` and `/claude-skills:transcript-fix`, and Claude also loads them on its own when a task matches their descriptions.

To try without installing, run Claude Code with `--plugin-dir /path/to/claude-skills`.

## Update

The plugin tracks the latest commit on `main` (no pinned version). A push does not reach installed copies by itself: run `/plugin marketplace update claude-skills` in Claude Code, or turn on auto-update for this marketplace in `/plugin` (Marketplaces tab) to refresh at session start.

## Conventions

- **Structure in English, prose in the input language.** The artifacts `prd` and `sdd` produce (PRDs, specs, designs, task plans, ADRs) have fixed English headings, labels, tags (`[ASSUMPTION]`, `[GAP]`) and field names, because the form checks compare them literally. The prose follows the language of the request, the repository convention or the source material, in that order of precedence.
- **Repository conventions win.** Default paths (`docs/prd`, `docs/specs`, `docs/adr`), test gates and section lists are skill defaults; a convention written in the repository's `CLAUDE.md` overrides them.
- **Scripts need Python 3.10+**, standard library only.

All three skills are written in English and work regardless of the language of the conversation.

## Layout

```
.claude-plugin/
  plugin.json          plugin manifest
  marketplace.json     lets this repo be added as a marketplace
skills/
  prd/                 SKILL.md, references/, scripts/check_prd.py
  sdd/                 SKILL.md, references/
  transcript-fix/      SKILL.md, references/, scripts/
```
