---
name: generate-readme
description: >
  Use this skill whenever the user asks to create, write,
  improve, or update a README — even if phrased casually ("write docs for my
  repo", "add a README", "document this project", "make a readme for this").
  Supports both Markdown and AsciiDoc output formats; defaults to Markdown
  unless the user specifies .adoc, AsciiDoc, or the project already uses
  README.adoc.
---

# Generate README

Produce a clear, developer-friendly README — in Markdown (`.md`) or AsciiDoc
(`.adoc`) format — for any software project.

---

## Workflow

### 0. Determine output format

Decide whether to produce Markdown (`.md`) or AsciiDoc (`.adoc`) **before**
writing anything.

If ambiguous, default to **Markdown**. If the user uploads a `.adoc` file to
improve, match that format.

---

### 2. Gather context

Before writing, collect as much of the following as possible from what the user
has shared. If critical info is missing, ask — but ask once, in a single
message, not question by question.

| Signal | Where to look |
|---|---|
| Project name & purpose | Repo name, package.json `name`/`description`, pyproject.toml, Cargo.toml, etc. |
| Entry point / install command | `package.json scripts`, `Makefile`, `setup.py`, `Dockerfile` |
| Dependencies | `requirements.txt`, `package.json dependencies` |
| Build command | `Makefile`, `Dockerfile`, `build.sh` |
| License | `LICENSE` file, package metadata |
| Existing docs / comments | Inline docstrings, existing partial README |

If the user uploads files or pastes code, extract everything you can before
asking questions.

---

#### Core sections (in order)

```
# Project Name
[badges — build, version, license — only if verifiable]

One-sentence tagline.

## Version Table
To track changes in different versions of the project

| Version | Changes | Date |
|---|---|---|
| 1.0 | Initial release | 2022-01-01 |
| 1.1 | Added new feature | 2022-02-01 |
| 1.2 | Fixed bugs | 2022-03-01 |

## Overview / About
2–4 sentences: what it does, who it's for, why it exists.

## Features
Bullet list of 3–8 key capabilities. Be concrete, not marketing-speak.

## Requirements / Prerequisites
Language version, OS constraints, external services needed.

## Installation
Step-by-step shell commands in fenced code blocks.

## Build Instructions
Add Build Instructions

## Usage
The most common invocation first — code block with realistic example.
Add sub-sections for advanced usage if needed.

## Configuration
Environment variables or config file options, in a table if there are many.
(Omit for minimal projects.)

## API Reference
(Only for libraries.) Key public functions/classes with signatures and
one-line descriptions. Link to full docs if they exist elsewhere.

## Contributing
How to open issues, run tests, submit PRs. Reference CONTRIBUTING.md if
present. (Omit for personal/private projects unless asked.)

## License
One-liner + SPDX identifier. E.g.: "MIT — see [LICENSE](LICENSE)"
```

#### Sections to include only when warranted

- **Architecture / How it works** — add for projects where understanding
  internals helps contributors
- **Roadmap** — only if the user provides one or asks for it
- **FAQ** — only if there are known common questions
- **Screenshots / Demo** — include placeholder `![screenshot](docs/...)` if
  the user mentions a UI but hasn't provided images

---

### 5. Formatting rules 

**Markdown (`.md`)**
- Use ATX headings (`##`, not underline style).
- Fenced code blocks with a language hint: ` ```bash `, ` ```python `, etc.
- Keep lines ≤ 100 chars for readability in raw form.
- Don't invent badges or shield URLs unless the CI/package info is verifiable
  from the provided files.

**AsciiDoc (`.adoc`)** — use asciidoc skill

---

### 6. Output

- **Markdown**: save to current directory `README.md`
- **AsciiDoc**: save to current directory `README.adoc`
- After saving, call `present_files` so the user can download it.
- In your reply, note the format chosen and any sections omitted and why
  (e.g., "Generated AsciiDoc. Skipped API Reference — this is a CLI.").

---

## Quality checklist (review before saving)

- [ ] Project name matches actual name (not a placeholder)
- [ ] Install and usage commands are syntactically correct
- [ ] No fabricated version numbers, URLs, or badge links
- [ ] No empty sections
- [ ] Code blocks have language hints
- [ ] License matches what's in the repo (if detectable)
- [ ] Output file extension matches chosen format (`.md` or `.adoc`)
- [ ] line wrap is <= 80 chars
- [ ] 200 words limit
- [ ] Grammar and spelling are correct.
