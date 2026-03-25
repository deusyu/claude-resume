---
name: resume
description: "AI-powered resume system. Manages structured experience data and generates tailored LaTeX resumes for different job targets. Use when user says 'resume', 'generate resume', 'make resume', 'init resume', 'add experience', '简历', '生成简历', '初始化简历', or '添加经历'."
user_invocable: true
argument-hint: "<subcommand> [args] — subcommands: init, generate, add"
---

# Resume — One Source, Many Tailored Resumes

A complete resume management system: structured experience data → AI-tailored LaTeX → PDF.

```
experiences/  →  /resume generate  →  resume.tex  →  make en  →  output/Name-Title.pdf
   (素材)         (AI 定制化)          (LaTeX源)      (编译)        (输出)
```

## Subcommand Routing

Parse `$ARGUMENTS` to determine which workflow to run:

- `init [text]` → **Init Workflow**
- `generate [job title or JD file]` or `gen [...]` → **Generate Workflow**
- `add [description]` → **Add Experience Workflow**
- Empty or unrecognized → Show available subcommands and ask user what they want to do

---

## Prerequisites Check

Before any workflow, verify the environment:

1. Check if `resume.cls` exists in the current working directory. If not, copy from `${CLAUDE_SKILL_DIR}/assets/resume.cls`
2. Check if `Makefile` exists. If not, copy from `${CLAUDE_SKILL_DIR}/assets/Makefile`
3. Create directories if missing: `experiences/work/`, `jobs/`, `output/`, `.history/`
4. Check if `xelatex` is available: `which xelatex`. If not, warn the user: "XeLaTeX is required. Install with: brew install --cask mactex"

---

## Init Workflow (`/resume init [text]`)

Initialize the experience data library from scratch.

### Mode Detection

Based on `$ARGUMENTS` (after stripping "init"):

1. **User pasted large text** (resume, work history, LinkedIn export) → **Import Mode**
2. **Empty or short description** → **Q&A Mode**
3. **File path provided** (PDF/Markdown) → Read file, then **Import Mode**

### Import Mode

1. **Parse** the provided content, extracting: personal info, education, work history (company, period, department, role, projects, metrics), personal projects, skills, honors
2. **Confirm** with user — show structured result, ask about missing info or inaccurate metrics
3. **Generate files** into `experiences/`:
   - `profile.md` — personal info + education
   - `work/<company>.md` — one file per company
   - `projects.md` — personal projects
   - `skills.md` — skill inventory
   - `honors.md` — honors + quantified metrics

### Q&A Mode

Walk through one question at a time. **Never ask multiple questions at once.**

1. **Basic info** → name, email, phone, wechat (optional), blog/github (optional) → write `experiences/profile.md`
2. **Education** → school, degree, major, period, highlights → append to `experiences/profile.md`
3. **Work history** (loop) → company, department, role, period, projects, metrics, tech stack → write `experiences/work/<company>.md`. After each: "Any more work experience?" Continue or move on.
4. **Personal projects** → name, link, description, tech stack, highlights → write `experiences/projects.md`
5. **Skills** → auto-generate from collected tech stacks, show for confirmation → write `experiences/skills.md`
6. **Honors** → extract quantified metrics from work history + user additions → write `experiences/honors.md`

### File Format Reference

**profile.md:**
```markdown
---
name: Name / English Name
email: email@example.com
phone: "+86 xxx"
wechat: xxx
blog: https://...
github: https://...
---

## Education

### School · College | Degree (Period)
- Major, highlights
```

**work/\<company\>.md:**
```markdown
---
company: Company Name
company_en: English Name
url: https://...
location: City
period: YYYY/MM -- YYYY/MM
department: Department
role: Role
tags: [tag1, tag2, ...]
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

Key principles:
- Keep raw details in experiences — trimming happens at generation time
- Tags should cover core tech and business domain keywords
- Metrics must include specific numbers
- One file per company, filename in lowercase English (e.g., `bytedance.md`)

---

## Generate Workflow (`/resume generate [target]`)

Generate a tailored resume for a specific job target.

### Step 1: Read All Materials

Read all files:
- `experiences/profile.md`
- All `.md` files under `experiences/work/`
- `experiences/projects.md`
- `experiences/skills.md`
- `experiences/honors.md`

Read `${CLAUDE_SKILL_DIR}/assets/resume-example.tex` to understand the LaTeX structure and available commands.

If the argument is a file path, also read that JD file.

### Step 2: Analyze Job Requirements

Based on JD or job title, determine:
1. **Core technical requirements** — what skills matter most
2. **Experience priority** — which work experiences are most relevant
3. **Framing angle** — how to present the same experience differently for this role
4. **Content selection** — include/omit personal projects section, which experiences to trim

### Step 3: Present the Plan

Before generating .tex, show the user:
1. Target positioning (one sentence)
2. Content selection (which experiences to include/omit)
3. Key differentiation (how this resume differs from a generic one)

**Wait for user confirmation before proceeding.**

### Step 4: Generate LaTeX

Generate `resume.tex` following these rules:

1. **Use resume.cls commands strictly**:
   - `\name{}`, `\basicInfo{}`, `\email{}`, `\phone{}`, `\wechat{}`, `\homepage[]{}`
   - `\section{}`, `\datedsubsection{}{}`, `\role{}{}`, `\rolewithdate{}{}{}`
   - `itemize` with `[parsep=0.25ex]`

2. **Keep to 1 page** (critical for Chinese version):
   - 5-6 sections: positioning, education, work, (projects), skills, honors
   - 3-5 bullet points per project
   - Trim early internships to 1-2 lines each
   - If too long, cut early internships and low-relevance bullets first

3. **LaTeX header for Chinese**:
   ```latex
   \documentclass{resume}
   \usepackage{xeCJK}
   \setCJKmainfont{SourceHanSerifSC-Medium}
   \setCJKsansfont{SourceHanSerifSC-Medium}
   \setCJKmonofont{SourceHanSerifSC-Medium}
   ```
   For English version: remove the three xeCJK lines.

4. **Formatting**:
   - Use `\textbf{【keyword】}` to mark each bullet's keyword
   - Escape: `&` → `\&`, `%` → `\%`
   - Use `\href{url}{text}` for hyperlinks

### Step 5: Build

1. Backup current `resume.tex` to `.history/resume_$(date +%Y%m%d%H%M%S).tex`
2. Write generated content to `resume.tex`
3. Run `make en` to build PDF
4. Copy PDF to `output/` with job-specific name

### Step 6: English Version (if requested)

1. Remove xeCJK lines
2. Translate all content to English
3. Build PDF

---

## Add Experience Workflow (`/resume add [description]`)

Add or update experience data incrementally.

### Step 1: Classify Input

Determine what the user is adding:
- **New work experience** → create/update `experiences/work/*.md`
- **New personal project** → update `experiences/projects.md`
- **New skill** → update `experiences/skills.md`
- **New honor/certification** → update `experiences/honors.md`
- **Personal info change** → update `experiences/profile.md`

### Step 2: Read Existing File

Read the target file to understand current content and format.

### Step 3: Update

Apply updates following the file format conventions above. Keep raw details — don't trim prematurely.

### Step 4: Confirm

Show the diff to the user, confirm before writing.

### Step 5: Remind

After updating: "Experience updated. Run `/resume generate [target]` to regenerate your resume."
