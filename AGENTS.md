# AGENTS.md

Instruction entry point for agents that read `AGENTS.md` (OpenAI Codex CLI, and other `AGENTS.md`-aware tools). Claude Code reads `CLAUDE.md` directly; this file makes the same project usable from Codex without duplicating the ruleset.

## How to operate

1. **`CLAUDE.md` is the complete ruleset.** Read it in full and follow it exactly. Everything about accuracy, ATS formatting, section ordering, the humanizer pass, validation, and output filenames lives there. This file does not restate it; it only adapts for agents that are not Claude Code.
2. **`cv_base.tex` is the content source of truth.** Never invent experience, metrics, or skills not present there.
3. Produce the same outputs CLAUDE.md specifies: tailored `cv_output.tex`, compiled `your_name_curriculum_vitae.pdf`, per-company cover letter, `coverage.md`, and the archive under `job_descriptions/<company>_<role>/`.

## Differences for Codex (no skill system)

Claude Code auto-loads "skills" from `.claude/skills/`. Codex has no equivalent auto-trigger, so treat those skill files as plain reference docs and read them when relevant:

- **Humanizer pass** (mandatory, CLAUDE.md step 8 / workflow step 6): read [`.claude/skills/humanizer/SKILL.md`](.claude/skills/humanizer/SKILL.md) and apply it to every CV, cover letter, and outreach message. The hard rule is zero em dashes plus removal of AI-writing tells. There is no `/humanizer` command in Codex, so run the pass yourself by following that file.
- **Applika tracking**: read [`.claude/skills/applika-cli/SKILL.md`](.claude/skills/applika-cli/SKILL.md) for the flow. The `applika` CLI itself is agent-agnostic and works identically under Codex. Verify the session with `applika whoami` before any write, then `applika applications new ...` (see README "Applika tracking integration" for flags and rules).

## One-time setup under Codex

```bash
pipx install applika-cli            # or: uv tool install applika-cli
applika login                       # GitHub OAuth, interactive, once per device
applika whoami                      # confirm
applika skill --dir "<codex-skills-dir>"   # optional: install the applika skill bundle; the picker also supports Codex directly
```

Prerequisites are the same as Claude Code: `tectonic` for LaTeX, `pdftotext` (poppler) for extraction validation.

## Source of truth

If anything here conflicts with `CLAUDE.md`, `CLAUDE.md` wins. Keep this file thin: a router, not a second copy of the rules.
