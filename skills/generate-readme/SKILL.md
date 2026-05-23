---
name: readme-generator
description: >
  Generate polished, professional README.md files for any software project. Use this skill
  whenever the user asks to create, write, generate, or improve a README — even if they just
  say "add a README to my project", "document this repo", "make a readme for this", or "help
  me write documentation". Also trigger when the user shares code/a repo and asks for
  documentation or wants to publish something. This skill produces complete, structured READMEs
  that follow best practices for open-source and internal projects alike.
---

# README Generator

Produces professional, complete `README.md` files. The output should feel human-crafted,
concise, and tailored — never generic boilerplate.

---

## Step 1 — Gather Context

Before writing anything, collect the following. Extract what you can from files/code already
in context; ask only for what's genuinely missing.

**Required (always ask if not clear):**
- Project name
- One-sentence description: what does it do and who is it for?
- Primary language / framework / stack
- License (MIT, Apache 2.0, proprietary, etc.) — default to MIT if OSS and user is unsure

**Ask only if not obvious from code:**
- Is this open-source or internal/private?
- Does it have a live demo or deployed URL?
- Any screenshots or logo available?
- Any special installation gotchas (env vars, OS requirements, Docker, etc.)?

**Never ask for things you can infer** — if you see `package.json`, you know it's Node; if
you see `pyproject.toml`, it's Python. Read the file tree and infer prerequisites.

---

## Step 2 — Pick the Right Template Variant

| Project type | Use variant |
|---|---|
| Open-source library / tool | **OSS** (full shields, contributing guide, acknowledgments) |
| Internal / company project | **Internal** (no shields, no contributing, add team/owner section) |
| CLI tool | **OSS** + emphasize Usage with terminal examples |
| API / SDK | **OSS** + emphasize Usage with code samples, link to API docs |
| Simple script / utility | **Minimal** (skip roadmap, contributing, acknowledgments) |

---

## Step 3 — Write the README

Use the template in `assets/README_TEMPLATE.md` as your structural foundation, but
**adapt freely** — omit sections that don't apply, merge thin sections, and write in a tone
that matches the project's personality (playful OSS tool vs serious enterprise SDK).

### Section guidance

**Header block**
- Include shields only for OSS projects. Pick relevant ones from the shields list in
  `references/shields.md`. Don't dump every possible shield — 3–5 is the sweet spot.
- Logo: include `<img>` tag placeholder only if user mentions they have one.

**About the Project**
- Lead with the *problem*, then the solution. 2–4 sentences max. No filler.
- Screenshot placeholder: include it. It's one of the highest-value README elements.

**Built With**
- Shield badges for major frameworks/languages only. Use the badge list in `references/shields.md`.
- Don't list every dependency — only the headline tech.

**Getting Started**
- Prerequisites: only things the user must install manually (not transitive deps).
- Tools required for compilation and execution of the project.
- Installation: numbered steps, working code blocks. Test mentally — would this actually work?
- If the project needs env vars, show an `.env.example` snippet.

**Build Instructions**
- If there is a build step, add it here.
- **Note**: This section is optional and should only be included if the project has a build step.

**Usage**
- This is the most important section. Show real, copy-pasteable examples.
- For CLIs: show terminal session examples.
- For libraries: show import + minimal working code snippet.
- For web apps: link to demo + show a screenshot.

**Roadmap**
- Use `- [ ]` / `- [x]` checkboxes. Suggest 3–5 plausible items if user doesn't provide them.
- Add a line linking to Issues for community suggestions.

**Contributing** (OSS only)
- Standard fork → branch → PR flow. Keep it to the standard 5 steps.

**License**
- State the license name and link to `LICENSE` or `LICENSE.txt`.

**Contact**
- Fill in placeholders; note that the user should replace them.

**Acknowledgments** (OSS only)
- Suggest 3–5 genuinely relevant resources. Don't just copy the template's generic list.

### Writing rules
- Use second person ("Run `npm install`") not third ("The user should run…").
- Code blocks must specify the language for syntax highlighting.
- Every `<p align="right">(<a href="#readme-top">back to top</a>)</p>` goes at the end of each
  H2 section (omit for very short READMEs, it looks cluttered).
- Placeholders: wrap in `<YOUR_VALUE>` style so they're obvious.
- No filler phrases like "This project was created to…" or "Welcome to…".

---

## Step 4 — Output

Return the complete README as a fenced markdown code block **and** save it to a file named
`README.md` using the file creation tool so the user can download it directly.

After presenting the file, offer:
> "Want me to adjust the tone, swap any sections, or add something specific?"

---

## Reference files

- `assets/README_TEMPLATE.md` — Full annotated template to build from
- `references/shields.md` — Curated shield badge list for common languages/frameworks