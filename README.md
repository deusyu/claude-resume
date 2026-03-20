# claude-resume

English | [中文](README.zh-CN.md)

Claude Code skill that generates tailored LaTeX résumés from structured experience data — one source, many resumes.

> Not just another skill — a reference project showing how to build a complete workflow with Claude Code Skills.

---

## How It Works

```
experiences/  (Markdown with YAML frontmatter)
  │
  ▼
/generate-resume "Backend Engineer"
  │  reads all experience files
  │  analyzes job requirements
  │  selects & reframes relevant experiences
  │  adjusts emphasis, ordering, skills
  ▼
resume.tex  (tailored LaTeX source)
  │
  ▼
make en → output/Name-Title.pdf
```

The same work experience gets different framing depending on the target role. A cloud platform project emphasizes architecture design for infra roles, but highlights workflow orchestration for AI Agent roles.

## Features

- **Experience-driven** — work history, skills, and honors stored as Markdown; presentation is separate from data
- **AI-powered tailoring** — auto-selects experiences, adjusts bullet points, reorders priorities based on JD
- **Multi-role adaptation** — same experience, different angles (backend / cloud-native / AI / fullstack)
- **LaTeX output** — professional typesetting via XeLaTeX, based on [billryan/resume](https://github.com/billryan/resume)
- **Version tracking** — `.history/` auto-backup before each generation

## Prerequisites

- **Claude Code CLI** — installed and authenticated
- **XeLaTeX** — `brew install --cask mactex` on macOS ([download](https://tug.org/mactex/))

## Quick Start

### 1. Install

```bash
git clone https://github.com/deusyu/claude-resume.git
cd claude-resume
```

### 2. Initialize your experiences

**Option A: Guided setup (recommended)**

```
/init
```

The skill walks you through a Q&A — name, education, work history, projects, skills — and generates all `experiences/` files for you.

**Option B: Import from existing resume**

Paste your existing resume or LinkedIn export:

```
/init <paste your resume text here>
```

The skill parses and structures it into the right format automatically.

**Option C: Edit manually**

Edit the files under `experiences/` directly:

| File | Content |
|------|---------|
| `profile.md` | Name, contact info, education |
| `work/*.md` | Work history — one file per company |
| `projects.md` | Personal / open-source projects |
| `skills.md` | Full skill inventory with categories |
| `honors.md` | Awards + quantified metrics quick-reference |

### 3. Generate a résumé

In Claude Code:

```
/generate-resume Backend Engineer
```

Or point to a JD file:

```
/generate-resume jobs/bytedance-backend.md
```

The skill will show a tailoring plan (what to include, what to cut, how to reframe) and ask for confirmation before generating.

### 4. Build PDF

```bash
make en    # English PDF (resume.pdf)
make zh    # Chinese PDF (resume-zh.pdf)
make all   # Both
```

### 5. Find your output

Generated PDFs are copied to `output/`.

## Project Structure

| File | Purpose |
|------|---------|
| `.claude/commands/init.md` | Onboarding skill — guided Q&A or paste-to-structure import |
| `.claude/commands/generate-resume.md` | Core skill — reads experiences, analyzes JD, generates tailored `.tex` |
| `.claude/commands/add-experience.md` | Helper skill — add/update experiences via natural language |
| `experiences/` | Structured experience data (source of truth) |
| `jobs/_template.md` | Template for job description input |
| `resume.tex` | Current active LaTeX source |
| `resume.cls` | LaTeX class (layout, fonts, commands) |
| `Makefile` | Build commands (`make en`, `make zh`, `make all`, `make clean`) |
| `.history/` | Auto-backup of previous `.tex` versions |
| `output/` | Generated PDF output directory |

## Skill Details

### `/init [optional: paste resume text]`

Onboarding skill. Two modes:

- **No arguments** → guided Q&A, one question at a time (name → education → work → projects → skills → honors)
- **With text** → parses your existing resume or LinkedIn export into structured `experiences/` files

### `/generate-resume [job title or JD file path]`

The core skill. Full pipeline:

1. **Read** all experience files from `experiences/`
2. **Analyze** job requirements — extract key skills, priorities
3. **Plan** — select experiences, decide ordering and emphasis, choose which sections to include/omit
4. **Confirm** — show the plan to user, wait for approval
5. **Generate** — write tailored `resume.tex` using `resume.cls` commands
6. **Build** — `make en` → copy PDF to `output/`

### `/add-experience [description]`

Natural language interface to update experience files:

```
/add-experience Built an open-source RAG project with LlamaIndex, got 200+ stars
/add-experience Got AWS Solutions Architect certification
```

## Customization

### Experience file format

Work history files use YAML frontmatter + Markdown:

```markdown
---
company: Company Name
period: 2024/01 -- Present
department: Engineering
role: Senior Backend Engineer
tags: [go, kubernetes, distributed-system]
---

# Project Name
> Timeline | Role

## Background
...

## Key Work
...

## Metrics
...

## Tech Stack
...
```

The `tags` field helps the AI match experiences to job requirements during generation.

### LaTeX style

Modify `resume.cls` to customize layout, fonts, and spacing. Currently based on [billryan/resume](https://github.com/billryan/resume).

## Credits

- LaTeX template based on [billryan/resume](https://github.com/billryan/resume) and [imtsuki/resume](https://github.com/imtsuki/resume)
- Résumé generation powered by [Claude Code](https://claude.ai/code) Skills

## License

[MIT](LICENSE)
