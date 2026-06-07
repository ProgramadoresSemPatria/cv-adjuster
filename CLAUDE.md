# CV Adjuster - ATS-Optimized CV Generation

## Purpose

This project adjusts a LaTeX CV to match specific job descriptions while maximizing compatibility with modern ATS (Applicant Tracking Systems), recruiter workflows, AI-based resume screening, and human review.

The goal is to produce a CV that:

- Parses correctly in ATS systems
- Matches role-specific keywords naturally
- Ranks higher in recruiter searches
- Remains highly readable for humans
- Avoids obvious AI-generated writing patterns
- Preserves truthfulness and technical credibility

---

# Core Principles

## 1. Accuracy First

- Never fabricate experience, responsibilities, metrics, skills, certifications, or education
- Never imply production experience with technologies the user has only explored casually
- Never invent impact metrics
- Never change job titles to misleading titles
- Never add fake freelance/consulting/open-source experience

Allowed:
- Reframing existing experience
- Reordering bullet points
- Clarifying scope
- Improving wording
- Highlighting relevant technologies already used

---

## 2. ATS + Human Optimization

The CV must optimize simultaneously for:

1. ATS parsing
2. Recruiter keyword search
3. Human readability
4. Technical credibility
5. Concise communication

Do not optimize for ATS at the expense of readability.

A recruiter should be able to scan the CV in 10-15 seconds and immediately understand:
- Current role level
- Main technologies
- Domain expertise
- Business impact
- Seniority
- Remote/distributed experience

---

# Inputs

The workflow uses three inputs:

1. `cv_base.tex`
   - Master LaTeX CV containing ALL truthful experience, projects, skills, education, and achievements
   - ALWAYS use this as the source of truth

2. Job Description
   - Pasted or attached by the user

3. Additional Skills List
   - Extra keywords or technologies the user wants emphasized
   - Only use if they genuinely exist in the user's background

---

# Workflow

## Step 1 - Read Base CV

Analyze:
- Technical stack
- Domains
- Seniority
- Metrics
- Leadership
- Open-source work
- Infrastructure experience
- Remote/distributed collaboration
- Project scale
- Performance/security/reliability achievements
- Years of experience per technology (compute from role date ranges)

### Years-of-Experience Check

If the JD states "5+ years of X", verify the actual years of professional X usage in the base CV.

- If years match or exceed: include the technology prominently
- If short: do NOT claim the requirement. Reframe adjacent experience (e.g., "production Python services" instead of claiming a missing year count)
- Never fabricate dates or extend role ranges to reach a year threshold

---

## Step 2 - Analyze Job Description

Extract and classify:

### Hard Requirements

Split every hard requirement into two buckets. ATS and recruiters weight "required" much higher than "preferred":

**Must-Have** (explicit signals: "required", "must have", "X+ years", listed under Requirements):
- Languages
- Frameworks
- Infrastructure
- Cloud providers
- Databases
- DevOps tooling
- Security/compliance
- Methodologies

**Nice-to-Have** (explicit signals: "preferred", "bonus", "a plus", listed under Nice to Have):
- Same categories, but secondary stack

Treatment:
- Must-Have keywords MUST appear in Summary, Skills, and at least one Experience bullet
- Nice-to-Have keywords appear in Skills only, unless genuinely strong experience exists

### Soft Requirements
- Communication
- Ownership
- Mentorship
- Collaboration
- Cross-functional work

### Hiring Signals
- Seniority
- Startup vs enterprise
- Product vs platform
- Backend vs infrastructure
- IC vs leadership
- Remote-first culture
- Timezone requirements
- Location / work authorization / visa sponsorship (ATS often filters first on these)
- Country, region, or timezone overlap requirements

### Keywords
Extract:
- Exact phrases
- Acronyms
- Expanded forms
- Tool names
- Methodologies
- Certifications
- Platform names

---

## Step 3 - Prioritize Content

Reorder content based on relevance.

Priority order:
1. Most relevant experience
2. Matching technologies
3. Matching domain experience
4. Highest impact achievements
5. Leadership/ownership
6. Older or less relevant experience

Most relevant bullets should appear FIRST inside each role.

### Seniority Alignment

Compare candidate seniority to the JD level before generating bullets:

- JD junior, candidate senior: trim staff-level signals (architecture decisions, mentorship, team leadership). Emphasize hands-on implementation. Avoid sounding overqualified, which triggers auto-rejection.
- JD senior, candidate matches: keep ownership, scope, and impact bullets prominent.
- JD senior, candidate junior: do not apply. Do not inflate scope to bridge the gap.
- JD principal/staff, candidate senior: only apply if the base CV shows genuine staff-level scope (cross-team, multi-system, technical strategy). Otherwise skip.

---

## Step 4 - Order Sections by JD Signal

Section order is NOT fixed. Reorder based on what the JD weighs most.

Default base order:
1. Summary
2. Professional Experience
3. Skills
4. Open Source Contributions
5. Projects
6. Education
7. Languages / Certifications

Adjust based on JD signal:

### JD emphasizes proof of work (Senior/Staff, OSS-heavy, product-engineering)
Promote artifacts above Skills:
1. Summary
2. Professional Experience
3. Open Source Contributions
4. Projects
5. Skills
6. Education
7. Languages

Triggers:
- "Open source contributions" listed in requirements
- Mentions of specific OSS projects, ecosystems, communities
- Staff/Principal/Senior IC roles where artifacts > keyword match
- Product engineering, developer tooling, dev experience roles
- The candidate has substantial OSS work (e.g., merged PRs to notable projects)

### JD emphasizes specific tech stack match (ATS-driven, recruiter-heavy)
Keep Skills high for keyword density near top:
1. Summary
2. Professional Experience
3. Skills
4. Projects
5. Open Source Contributions
6. Education
7. Languages

Triggers:
- Heavy bullet list of required technologies
- Recruiter-style language ("must have X, Y, Z")
- Enterprise / regulated industries where ATS filtering is aggressive
- Roles where the company filters first on keywords

### JD emphasizes education or research
Promote Education:
1. Summary
2. Education
3. Professional Experience
4. Projects / Publications
5. Open Source Contributions
6. Skills

Triggers:
- "PhD required" / "Masters preferred"
- Research roles, ML/AI research, academic-adjacent positions
- New-grad roles where work history is short

### Rationale

For Staff/Senior candidates with strong artifacts, Skills is just a keyword surface for ATS. The artifacts are the proof a hiring manager actually evaluates on. Burying Projects + OSS under Skills wastes the strongest signal.

For roles where ATS filtering is the first gate, keyword proximity to the top of the document matters more than artifact depth, so Skills moves up.

Always justify the chosen order when explaining major optimization decisions in Step 6 of the workflow.

---

## Step 5 - Build Keyword Coverage Matrix

Before compiling, build a coverage matrix that maps every Must-Have and high-priority Nice-to-Have keyword from the JD to its location(s) in the generated CV.

Format (one row per keyword):

```text
| Keyword            | Required | Summary | Skills | Experience | Projects | Count |
|--------------------|----------|---------|--------|------------|----------|-------|
| Kubernetes         | yes      | yes     | yes    | yes (3x)   | no       | 5     |
| Terraform          | yes      | no      | yes    | no         | no       | 1     |  <-- gap
| OpenTelemetry      | nice     | no      | yes    | no         | no       | 1     |
```

Rules:
- Every Must-Have with Count = 0 is a blocker. Fix before delivery.
- Every Must-Have should appear at least once in Experience or Projects, not Skills alone.
- Save the matrix to the per-application archive (see Output Rules) as `coverage.md`.

---

# ATS Optimization Rules

## Keyword Matching

### Mirror Exact Keywords

If the job description says:
- "CI/CD pipelines"

Use:
- "CI/CD pipelines"

NOT:
- "continuous integration workflows"

ATS systems often rely on exact phrase matching.

---

## Keyword Coverage

Ensure important keywords appear naturally in:
- Summary
- Experience bullets
- Skills section
- Projects section

Critical keywords should appear at least once in context.

Do NOT dump keywords in isolated lists.

Bad:
- "Rust, Kubernetes, AWS, Docker, Kafka"

Good:
- "Built Rust microservices deployed on Kubernetes using AWS infrastructure and Docker containers."

---

## Acronym + Full Form Strategy

When space allows, use both forms once:

Examples:
- Amazon Web Services (AWS)
- Continuous Integration / Continuous Deployment (CI/CD)
- Representational State Transfer (REST)

After first mention, acronyms are acceptable.

---

## Remote Keywords

For remote roles, naturally include relevant phrases if truthful:
- Remote collaboration
- Distributed teams
- Async communication
- Cross-functional collaboration
- Global teams
- Remote-first environment

---

## AI Screening Optimization

Modern ATS and recruiter tooling increasingly use AI-assisted ranking.

The CV should:
- Sound natural
- Avoid exaggerated corporate language
- Avoid repetitive sentence structures
- Avoid generic AI-style phrasing
- Use concrete technical detail
- Include measurable outcomes

Avoid phrases like:
- "Results-driven professional"
- "Passionate engineer"
- "Dynamic team player"
- "Highly motivated self-starter"

Prefer:
- Specific technologies
- Specific impact
- Specific ownership
- Specific metrics

---

# Professional Summary Rules

The summary must:

- Be tailored to the role
- Be 2-4 lines maximum
- Mention:
  - Years of experience
  - Core technologies
  - Domain specialization
  - Relevant infrastructure/product experience
- Include top keywords from the JD naturally
- Avoid buzzword-heavy language

Good example:
- "Backend engineer with experience building distributed systems in Rust and Go, focusing on infrastructure reliability, API performance, and cloud-native services."

Bad example:
- "Passionate and results-driven software engineer with a proven track record of innovation."

---

# Bullet Point Rules

## Structure

Recommended structure:
- Action verb + technical implementation + measurable/business impact

Example:
- "Implemented Rust-based caching layer reducing API latency by 37% under peak traffic."

---

## Action Verbs

Prefer:
- Built
- Developed
- Implemented
- Optimized
- Designed
- Automated
- Migrated
- Reduced
- Improved
- Scaled
- Integrated
- Led

Avoid repetitive reuse.

---

## Metrics

Quantify whenever truthful:
- Latency reduction
- Cost savings
- Throughput
- Uptime
- Scale
- Team size
- User impact
- Revenue impact
- Deployment speed
- Build time reduction

Examples:
- "Reduced deployment time from 25 minutes to 8 minutes"
- "Handled 2M+ daily requests"
- "Improved query performance by 45%"

Never invent metrics.

---

## Bullet Length

Ideal:
- 1-2 lines
- Maximum ~30 words

Avoid:
- Long paragraphs
- Multi-sentence bullets
- Dense explanations

## Page Overflow Rule

Target length: 1-3 pages. When the generated CV exceeds 3 pages, cut in this order:

1. Bullets from the OLDEST roles (cut whole bullets, not partial sentences)
2. Bullets least relevant to the JD inside older roles
3. Secondary projects with no keyword overlap
4. Skills not referenced by the JD at all

Never cut from:
- The most recent role (always full set of bullets)
- The role most relevant to the JD
- The summary
- Contact information

If still over 3 pages after cuts: reduce vertical spacing in LaTeX before removing more content.

---

## Technical Depth

Strong bullets include:
- Technologies
- Architecture
- Scale
- Performance
- Infrastructure
- Ownership

Weak bullets are generic:
- "Worked with backend systems"

Strong:
- "Built gRPC services in Go handling high-throughput internal event processing."

---

# Job Title Alignment

The header job title must remain truthful. Never replace the actual title with the JD's title.

Allowed:
- Keep the actual title in the role header
- Mirror the JD's title language inside the Summary if scope genuinely matches (e.g., the role was titled "Software Engineer" but spanned ownership of distributed systems, the summary may use "backend engineer focused on distributed systems")
- Append `(equivalent to Senior Backend Engineer)` only when scope, responsibility, and seniority genuinely match the JD title

Never:
- Rewrite a "Software Engineer" title to "Senior Software Engineer" in the role header
- Inflate "Engineer" to "Tech Lead" or "Staff Engineer" without scope evidence

---

# Formatting Rules for ATS

## Layout

MUST use:
- Single-column layout
- Linear reading order
- Simple hierarchy

NEVER use:
- Tables
- Text boxes
- Sidebars
- Floating elements
- Multi-column layouts
- Absolute positioning

## Typography Rules for ATS

### Avoid Italic for Keywords

Never apply italic styling to any keyword, technology name, tool, framework, or skill term.

Italic is acceptable only for:
- Role titles in role headers
- Publication titles

Reason: older ATS parsers (Taleo, iCIMS in certain configurations) strip italic styling or misread italic glyphs depending on font embedding, which can drop or distort technology keywords during extraction. Bold and underline are safe.

### Minimum Text Contrast

All body text MUST be pure black (`#000000`) on white.

Accent color (section headers, name) may use dark navy or charcoal, but never lighter than `#444444`.

Banned: any text lighter than `#444444`.

Reason: some ATS pipelines re-rasterize PDFs and run OCR as a fallback when text extraction yields low confidence. Low-contrast text fails OCR, and the affected fields drop entirely.

### No Horizontal Rules as Section Dividers

Never use `\hrule`, `\rule`, `\hline`, or `\noalign{\hrule}` to separate sections.

Use whitespace plus a bold uppercase heading instead:

```latex
\vspace{8pt}
\textbf{\uppercase{Professional Experience}}
\vspace{4pt}
```

Reason: graphics-object rules can break parsers that detect section boundaries via font-delta heuristics, causing the bullets immediately after the rule to be attributed to the previous section.

### Page-Break Protection Inside Roles

A single role MUST never split across pages. Force the role header and its first bullets to stay together:

```latex
\needspace{3\baselineskip}
\textbf{Company Name} \hfill Jan 2024 - Present \\
\textit{Role Title}
\nopagebreak
\begin{itemize}
\item ...
\end{itemize}
```

Reason: some ATS (certain Workday and Greenhouse configurations) parse PDFs page-by-page. A role split across pages is parsed as two separate experience entries, with the second entry missing its title and company, polluting the parsed work history.

---

## Fonts

Use ATS-safe fonts or equivalents:
- Calibri
- Arial
- Helvetica
- Times New Roman
- Latin Modern
- Computer Modern

Avoid decorative fonts.

---

## Section Names

Use standard headings exactly:

- Professional Experience
- Skills
- Projects
- Education
- Certifications
- Open Source Contributions

Avoid creative headings like:
- "What I've Built"
- "Career Journey"

---

## Dates

Use consistent formatting:

Preferred:
- `Jan 2024 - Present`
- `Mar 2022 - Dec 2023`

Avoid inconsistent formats.

### Date Placement

Dates MUST appear on the SAME line as the role/company, right-aligned with `\hfill`:

```latex
\textbf{Company Name} \hfill Jan 2024 - Present \\
\textit{Role Title}
```

Never place dates:
- On a separate line above or below the role
- In a side column
- Centered or left-aligned on their own line

Reason: dates on a separate line are frequently mis-attributed to the adjacent role by ATS parsers. Same-line placement preserves the role↔date pairing in `pdftotext` extraction order.

## Role Header Order

Within each role block, the field order MUST be:

1. Company (bold)
2. Role title
3. Dates (right-aligned via `\hfill` on the same line as Company)
4. Location (optional, same line as Role or below)

Many ATS parsers use positional pattern matching tuned to `Company → Role → Dates`. Reordering (e.g., `Role → Company → Dates`) confuses parsers and can drop the company name from the parsed experience entry.

---

## Contact Information

Must appear in the body:
- Name
- Email
- GitHub
- LinkedIn
- Location
- Portfolio/website

Do NOT place critical information in:
- Headers
- Footers
- Icons-only sections

### Contact Block Layout

The contact block MUST be a single line (or at most two lines) directly below the name, with fields separated by a pipe `|` or middle dot `·`:

```text
Your Name
your.email@example.com | github.com/your-handle | linkedin.com/in/... | Location
```

Never:
- Split contact fields across left and right columns
- Use a sidebar for contact info
- Place fields in a multi-block grid

Reason: multi-block contact layouts cause ATS parsers to extract fields out of order, so the email can end up in the phone field or get dropped entirely.

### No Photos, No Icon-Fonts

Never include:
- Profile photo
- Company logos
- Icon-font glyphs (FontAwesome social icons, Material Icons, etc.)

Reason: icon-font glyphs use Private Use Area Unicode codepoints that extract as garbage characters next to the contact info, polluting the email and phone fields. Photos auto-fail many US/UK ATS for bias-compliance reasons (Workday, Greenhouse on EEO-strict configurations).

---

## Hyperlinks

Use plain clickable text.

Good:
- `github.com/username`
- `linkedin.com/in/username`

Avoid:
- Hyperlinked icons without visible URLs

ATS systems sometimes fail to extract hidden hyperlinks.

---

# LaTeX-Specific ATS Rules

## PDF Text Extraction

The generated PDF MUST:
- Be machine-readable
- Allow proper text selection
- Pass copy-paste extraction tests

Use:
```latex
\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}
```

Strongly recommended:
```latex
\input{glyphtounicode}
\pdfgentounicode=1
```

This improves ATS text extraction significantly.

---

## Avoid ATS-Breaking Packages

Avoid:
- `tikz` for layout
- `textpos`
- Heavy graphics systems
- Icon-only packages
- Complex table layouts
- Multi-column environments for core content

Avoid excessive:
- Custom spacing hacks
- Invisible formatting
- Overlay positioning

---

## Bullet Formatting

Use simple bullets:
```latex
\begin{itemize}
\item ...
\end{itemize}
```

### Bullet Character Whitelist

Only `•` (U+2022) or `-` (hyphen-minus) are permitted as bullet markers.

Never use:
- `▸ ★ ➤ → ◦ ●` (extended dingbats)
- Any emoji
- Custom Unicode glyphs

Reason: exotic bullet glyphs extract as `?`, `□`, or get dropped entirely by `pdftotext` and ATS parsers, which then mis-attribute or lose the bullet's text.

### Single-Level Bullets Only

Never nest `itemize` environments. One level only.

Bad:
```latex
\begin{itemize}
\item Built service X
  \begin{itemize}
  \item Sub-detail Y
  \end{itemize}
\end{itemize}
```

Good:
```latex
\begin{itemize}
\item Built service X with detail Y inlined into the same bullet.
\end{itemize}
```

Reason: nested bullets get flattened or duplicated by ATS parsers, and child bullets are frequently attributed to the wrong parent role.

Avoid:
- Custom bullet rendering systems
- Nested complex formatting

---

# Skills Section Rules

## Organization

Group logically:
- Languages
- Backend
- Infrastructure
- Cloud
- Databases
- DevOps
- Blockchain/Web3
- Observability
- Testing

---

## Ordering

Order by:
1. Relevance to JD
2. Strength of experience
3. Seniority of usage

Do NOT alphabetize blindly.

### Group Order

The FIRST skills group must reflect the JD's primary stack, and individual entries within that group should appear in the same order the JD lists them. Recruiters skim left to right, and ATS scoring weights early tokens more heavily.

Example, JD lists "Go, Kubernetes, PostgreSQL, AWS" as primary stack:

Good:
- Backend: Go, Kubernetes, PostgreSQL, AWS, ...

Bad (alphabetized):
- Backend: AWS, Go, Kubernetes, PostgreSQL, ...

Bad (unrelated group first):
- Frontend: React, TypeScript, ... (when the JD is a backend role)

---

## Truthfulness

Do not include:
- Beginner technologies
- Tutorial-level exposure
- Technologies never used professionally or seriously

---

# Humanizer Pass (Mandatory)

Every generated CV MUST go through a humanization pass before delivery.

Goals:
- Remove robotic phrasing
- Remove repetitive syntax
- Reduce AI-generated tone
- Improve natural flow
- Preserve technical precision

Specifically remove:
- ALL em dashes. Hard ban. Replace every `—` and `--` with a comma, colon, or parentheses depending on context. No exceptions in CV, cover letter, or outreach output.
- Overly polished corporate wording
- Buzzword stacking
- Formulaic sentence patterns
- The rhetorical pattern "not just X, but Y"
- Triplet constructions ("fast, reliable, and scalable")
- Vague intensifiers ("leveraged", "utilized", "spearheaded")

The final CV should sound like:
- A strong engineer wrote it
- Not an AI-generated template

This applies to:
- CVs
- Cover letters
- Outreach messages

---

# Cover Letter Rules

Cover letters must:
- Be concise
- Be role-specific
- Mention concrete overlap with the job
- Avoid generic enthusiasm language
- Avoid repeating the CV verbatim

Preferred structure:
1. Why this role/company
2. Relevant technical overlap
3. Relevant achievements
4. Closing

Maximum:
- ~300 words

---

# Validation & QA

Before delivery, ALWAYS validate:

## ATS Parsing Validation

Perform:
1. Copy-paste test from generated PDF
2. `pdftotext` extraction test (mandatory, not optional)
3. Verify bullets extract correctly
4. Verify links extract correctly
5. Verify no broken glyphs
6. Verify UTF-8 characters render correctly

Concrete validation commands:

```bash
pdftotext joao_soares_curriculum_vitae.pdf - > /tmp/cv_extracted.txt

# For every Must-Have keyword from the JD, confirm it appears:
grep -i "kubernetes" /tmp/cv_extracted.txt
grep -i "terraform" /tmp/cv_extracted.txt
# ... etc, one grep per Must-Have keyword

# Confirm contact info extracts:
grep -i "your.email@example.com\|github.com\|linkedin.com" /tmp/cv_extracted.txt
```

Any Must-Have keyword that fails its grep is a blocker. Do not deliver.

### Reading-Order Validation

Run `pdftotext` WITHOUT the `-layout` flag and confirm the extracted text reads top-to-bottom in the same logical order as the visual document:

```bash
pdftotext joao_soares_curriculum_vitae.pdf - > /tmp/cv_reading_order.txt
```

Verify in `/tmp/cv_reading_order.txt`:
1. Name appears first
2. Contact line appears immediately after name
3. Sections appear in the intended order (Summary → Experience → Skills → ...)
4. Within each role: Company, Role, Dates, then bullets, in that order
5. No bullets appear before their parent role
6. No fragments from page 2 interleave with page 1

Also run with `-layout` to cross-check:

```bash
pdftotext -layout joao_soares_curriculum_vitae.pdf - > /tmp/cv_layout.txt
diff /tmp/cv_reading_order.txt /tmp/cv_layout.txt
```

Significant content reordering between the two outputs indicates a parser-confusing layout. Any reading-order mismatch is a blocker.

### Scaffolding Guard

Before compiling, grep the .tex source for leftover scaffolding:

```bash
grep -niE 'TODO|FIXME|XXX|lorem|placeholder|\{\{[a-z_]+\}\}|<insert' cv_output.tex
```

Any hit is a blocker. Resolve before compile.

---

## Keyword Validation

Check:
- Critical JD keywords appear naturally
- Keywords exist in context
- No keyword stuffing
- No repeated unnatural phrasing

---

## Readability Validation

Verify:
- Easy to skim in 10 seconds
- Strongest bullets appear first
- No dense paragraphs
- Consistent formatting
- Good whitespace balance

---

## Final Quality Checklist

Before delivering the final CV:

- [ ] Every important JD requirement is addressed truthfully
- [ ] Keywords are naturally integrated
- [ ] Professional summary is tailored
- [ ] Strongest experience appears first
- [ ] Bullet points are measurable and technical
- [ ] ATS-safe formatting is preserved
- [ ] PDF is machine-readable
- [ ] No tables or columns exist
- [ ] Contact information is parsable
- [ ] Links are visible and readable
- [ ] No AI-style filler language remains
- [ ] No fabricated information exists
- [ ] CV length is appropriate (usually 1-3 pages)
- [ ] LaTeX compiles successfully
- [ ] Output filename is correct
- [ ] Zero em dashes in the final output
- [ ] Coverage matrix shows every Must-Have keyword with Count >= 1
- [ ] pdftotext extraction confirms every Must-Have keyword is parseable
- [ ] Scaffolding grep returns no hits
- [ ] Per-application archive folder is populated
- [ ] Application recorded in Applika via `applika applications new` (or skipped with a note if CLI not set up)
- [ ] Bullet markers are only `•` or `-`, no exotic glyphs
- [ ] No nested `itemize` environments
- [ ] Dates appear on the same line as Company via `\hfill`
- [ ] Role header order is Company → Role → Dates
- [ ] Contact info is a single line, pipe-separated
- [ ] No photos, no icon-font glyphs anywhere
- [ ] No italic styling on any keyword/technology name
- [ ] All body text is pure black, no text lighter than `#444444`
- [ ] No `\hrule`/`\rule`/`\hline` used as section dividers
- [ ] No role is split across pages (verified by viewing the PDF)
- [ ] `pdftotext` reading-order check passes

---

# File Structure

```text
cv_adjuster/
  CLAUDE.md
  cv_base.tex
  cv_output.tex
  joao_soares_curriculum_vitae.pdf
  cover_letter.tex
  cover_letter.pdf
  cover_letter.txt
  zed_contributions.md
  zed_prs.md
  job_descriptions/
    <company>_<role>/
      jd.md
      cv_output.tex
      joao_soares_curriculum_vitae.pdf
      cover_letter_<company>.tex
      cover_letter_<company>.pdf
      coverage.md
```

## Per-Application Archive

Every application generates a dedicated folder under `job_descriptions/<company>_<role>/`.

Rules:
- Folder name uses lowercase, underscores: `acme_backend_engineer`
- Save the raw JD as `jd.md`
- Save the tailored `.tex` and final PDF inside the folder
- Save the keyword coverage matrix as `coverage.md`
- The root `cv_output.tex` and `joao_soares_curriculum_vitae.pdf` reflect the most recent run, but the archive is the durable record

Reason: without archives, each new application overwrites the prior output and there is no audit trail of what was sent to which company.

---

# Applika Tracking Integration

Every generated application is also recorded in Applika (https://github.com/ProgramadoresSemPatria/applika), a job-application tracker, via its CLI.

Scope: Applika stores application METADATA only (company, role, platform, date, URLs, salary). It does NOT store or attach the CV/PDF. The tailored PDF remains in `job_descriptions/<company>_<role>/`; Applika holds the tracking entry that points at that application.

## One-Time Setup (per device)

```bash
uv tool install applika-cli        # or: pipx install applika-cli
applika login                      # GitHub OAuth, session saved to ~/.config/applika/session.json
applika whoami                     # confirm the session before relying on it
```

The session auto-refreshes, so `login` is needed only once per device.

## Recording Step (Step 13 of the workflow)

After the archive folder is populated, create the Applika entry using metadata already extracted from the JD in Step 2:

```bash
applika applications new \
  --company "<company from JD>" \
  --role "<role title from JD>" \
  --platform "<where the JD was found: LinkedIn, Email, company site, etc.>" \
  --mode active \
  --date <today, YYYY-MM-DD> \
  --job-url "<JD link if known>" \
  --work-mode <remote|hybrid|on_site, if stated> \
  --country "<country if stated>" \
  --experience-level <intern..principal, mapped from JD seniority>
```

Rules:
- `--mode active` when the user applied; `--mode passive` when a recruiter reached out first.
- `--company`, `--role`, `--platform`, `--mode`, `--date` are required. Omit optional flags whose value is not known from the JD rather than guessing.
- Salary rule: if any salary flag (`--expected-salary`, `--salary-min`, `--salary-max`) is passed, BOTH `--currency` and `--salary-period` become mandatory.
- Confirm the command with the user before running it (it writes to their remote Applika account, an outward-facing action).
- If `applika whoami` fails or the CLI is not installed, skip this step and tell the user to run the one-time setup, rather than failing the whole workflow.

---

# Output Rules

## Output Filename

The final compiled CV PDF MUST always be:

```bash
joao_soares_curriculum_vitae.pdf
```

Never deliver:
- `cv_output.pdf`
- `resume.pdf`
- Any other filename

## Cover Letter Filename

Cover letters MUST be named per company:

```bash
cover_letter_<company>.pdf
cover_letter_<company>.tex
```

Examples:
- `cover_letter_acme.pdf`
- `cover_letter_altius_labs.pdf`

A generic `cover_letter.pdf` may exist at the repo root as the most recent run, but the archived copy inside `job_descriptions/<company>_<role>/` must use the per-company name.

---

# Compilation

Compile using:

```bash
tectonic cv_output.tex && mv cv_output.pdf joao_soares_curriculum_vitae.pdf
```

---

# Interaction Pattern

When starting a new session:

1. Request the job description
2. Request additional target skills
3. Read `cv_base.tex`
4. Analyze requirements and keywords (Must-Have vs Nice-to-Have, location/visa, seniority)
5. Generate tailored `cv_output.tex`
6. Explain major optimization decisions
7. Build keyword coverage matrix, fix any Must-Have gaps
8. Run humanizer pass (zero em dashes)
9. Run scaffolding grep
10. Compile PDF
11. Run pdftotext extraction validation
12. Create `job_descriptions/<company>_<role>/` and save jd.md, .tex, .pdf, coverage.md
13. Record the application in Applika (see "Applika Tracking Integration")
14. Deliver final files

---

# What NEVER To Do

NEVER:
- Fabricate experience
- Invent metrics
- Fake certifications
- Add fake open-source contributions
- Use hidden keywords
- Use white-on-white text
- Keyword stuff unnaturally
- Sacrifice readability for ATS optimization
- Use generic corporate filler
- Over-design the resume
- Use graphics-heavy layouts
- Use multi-column ATS-breaking designs
- Claim expertise without evidence
- Deliver unvalidated PDFs
