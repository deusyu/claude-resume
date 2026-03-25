# LaTeX Commands Reference (resume.cls)

## Layout Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `\name{text}` | `\name{John Doe}` | Centered large name heading |
| `\basicInfo{items}` | `\basicInfo{\email{...} \phone{...}}` | Contact info line |
| `\datedsubsection{title}{date}` | `\datedsubsection{\textbf{Company}}{2024}` | Company/school + date |
| `\role{dept}{title}` | `\role{Engineering}{Senior Engineer}` | Department + role |
| `\rolewithdate{dept}{title}{date}` | `\rolewithdate{Eng}{Lead}{2024}` | Dept + role + date (multi-role) |

## Contact Commands

| Command | Example |
|---------|---------|
| `\email{addr}` | `\email{john@example.com}` |
| `\phone{num}` | `\phone{+86 138-0000-0000}` |
| `\wechat{id}` | `\wechat{johnwechat}` |
| `\homepage[display]{url}` | `\homepage[john.dev]{https://john.dev}` |
| `\github[display]{url}` | `\github[john]{https://github.com/john}` |
| `\linkedin[display]{url}` | `\linkedin[john]{https://linkedin.com/in/john}` |

## CJK Font Setup

Chinese version requires these three lines after `\documentclass{resume}`:

```latex
\usepackage{xeCJK}
\setCJKmainfont{SourceHanSerifSC-Medium}
\setCJKsansfont{SourceHanSerifSC-Medium}
\setCJKmonofont{SourceHanSerifSC-Medium}
```

Remove these three lines for English-only resumes.

## Build Commands

```bash
make en      # English PDF (resume.pdf)
make zh      # Chinese PDF (resume-zh.pdf)
make all     # Both
make clean   # Remove build artifacts
```
