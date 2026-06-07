# CV Adjuster

ATS-optimized CV generation. Tailors a master LaTeX CV to a specific job description, maximizing compatibility with Applicant Tracking Systems, recruiter search, AI screening, and human review, without fabricating experience.

The full ruleset lives in [`CLAUDE.md`](CLAUDE.md). This README is the operational quick reference.

## What this repo does

Given a job description, it produces:

- A tailored `cv_output.tex`, compiled to `your_name_curriculum_vitae.pdf`
- A per-company cover letter (`cover_letter_<company>.tex` / `.pdf`)
- A keyword coverage matrix (`coverage.md`)
- A durable archive under `job_descriptions/<company>_<role>/`
- An application tracking entry in [Applika](https://github.com/ProgramadoresSemPatria/applika)

## Layout

```text
cv-adjuster/
  CLAUDE.md            # full generation ruleset (source of truth for behavior)
  README.md            # this file
  cv_base.tex          # master CV with ALL truthful experience (source of truth for content)
  cv_output.tex        # most recent tailored CV
  your_name_curriculum_vitae.pdf  # most recent compiled output (canonical filename)
  cover_letter_<company>.tex/.pdf   # per-company cover letters
  job_descriptions/
    <company>_<role>/  # durable per-application archive
      jd.md
      cv_output.tex
      your_name_curriculum_vitae.pdf
      cover_letter_<company>.tex/.pdf
      coverage.md
```

## Prerequisites

- [`tectonic`](https://tectonic-typst.dev/) for LaTeX compilation
- `pdftotext` (poppler) for extraction validation
- `applika-cli` for application tracking (see below)

## Generation workflow

Driven by Claude Code per `CLAUDE.md`. High level:

1. Provide the job description and any extra target skills.
2. The base CV (`cv_base.tex`) is read as the content source of truth.
3. Requirements and keywords are analyzed (Must-Have vs Nice-to-Have, location/visa, seniority).
4. A tailored `cv_output.tex` is generated, sections ordered by JD signal.
5. A keyword coverage matrix is built; Must-Have gaps are fixed.
6. Humanizer pass runs (zero em dashes, no AI-style filler).
7. Compile and validate:

   ```bash
   tectonic cv_output.tex && mv cv_output.pdf your_name_curriculum_vitae.pdf
   pdftotext your_name_curriculum_vitae.pdf - > /tmp/cv_extracted.txt
   ```

8. Archive under `job_descriptions/<company>_<role>/`.
9. Record the application in Applika (see next section).

The hard rules (accuracy, ATS formatting, truthfulness) are non-negotiable and documented in `CLAUDE.md`.

## Applika tracking integration

[Applika](https://github.com/ProgramadoresSemPatria/applika) is a job-application tracker. Every generated application is also logged there via its CLI.

**Scope:** Applika stores application *metadata only* (company, role, platform, date, URLs, salary). It does **not** store or attach the CV/PDF. The tailored PDF stays in the local archive; Applika holds the tracking entry.

### One-time setup (per device)

Already installed in this environment via `pipx` (binary at `~/.local/bin/applika.exe`, on PATH). To reproduce on a new machine:

```bash
pipx install applika-cli      # or: uv tool install applika-cli
applika login                 # GitHub OAuth, session saved to ~/.config/applika/session.json
applika whoami                # confirm the session
```

`login` is interactive (opens a browser) and auto-refreshes, so it is needed only once per device. In a Claude Code session, run it yourself with `! applika login`.

### AI skill

The `applika-cli` skill is installed under `~/.claude/skills/applika-cli/`. It triggers whenever Applika is mentioned and provides the full command reference. Reinstall with:

```bash
applika skill --dir "C:\Users\<user>\.claude\skills"
```

> Note: on Windows the CLI throws a harmless `UnicodeEncodeError` when printing its success arrow (`â†’`) under the cp1252 console. The copy completes before the crash; verify the directory exists rather than trusting the exit code.

### Recording an application

Run after the archive folder is populated, using metadata extracted from the JD:

```bash
applika applications new \
  --company "<company>" \
  --role "<role title>" \
  --platform "<LinkedIn | Email | company site | ...>" \
  --mode active \
  --date <YYYY-MM-DD> \
  --job-url "<JD link if known>" \
  --work-mode <remote | hybrid | on_site> \
  --country "<country if stated>" \
  --experience-level <intern..principal>
```

Rules:

- Required: `--company`, `--role`, `--platform`, `--mode`, `--date`.
- `--mode active` when you applied; `--mode passive` when a recruiter reached out first.
- Salary rule: if any salary flag (`--expected-salary`, `--salary-min`, `--salary-max`) is passed, both `--currency` and `--salary-period` become mandatory.
- Omit optional flags whose value is unknown rather than guessing.
- This writes to your remote Applika account, an outward-facing action: confirm before running.
- If `applika whoami` fails or the CLI is missing, skip tracking and run the one-time setup, rather than failing the CV workflow.

### Other Applika commands

```bash
applika applications list
applika applications edit [ID]
applika applications steps list [ID]
applika applications steps add [ID] --step <step> --date <YYYY-MM-DD>
applika applications finalize [ID] --step <step> --feedback <text> --date <YYYY-MM-DD>
```

## Output rules (non-negotiable)

- Final CV PDF is always `your_name_curriculum_vitae.pdf`.
- Cover letters are per-company: `cover_letter_<company>.pdf` / `.tex`.
- Every Must-Have keyword must appear in the CV and pass `pdftotext` extraction.
- Zero em dashes anywhere (CV, cover letter, outreach).
- No fabricated experience, metrics, or certifications.
