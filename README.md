# Skills Marketplace

A personal skills marketplace for GitHub Copilot CLI — a collection of skills that teach Copilot how to complete specialized tasks in a repeatable, consistent way.

> Skills work with **GitHub Copilot CLI**, Copilot cloud agent, and agent mode in VS Code.

## About

Agent skills are folders of instructions, scripts, and resources that Copilot loads when relevant to improve performance on specialized tasks. The [Agent Skills specification](https://github.com/agentskills/agentskills) is an open standard supported by multiple AI systems.

For more information, see [About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills).

## Getting Started

### Option 1 — Plugin Marketplace (recommended)

First push this repo to GitHub, then register it as a plugin marketplace inside Copilot CLI:

```
/plugin marketplace add abhishekojha38/my-skills-marketplace
```

Install the document-skills plugin:

```
/plugin install document-skills@aojha-skills
```

### Option 2 — Personal Skills (no repo required)

Copy any skill directory directly to your personal skills folder:

```bash
cp -r skills/ascii-doc ~/.copilot/skills/
```

Then reload skills inside a Copilot CLI session:

```
/skills reload
```

### Option 3 — Project Skills (repo-scoped)

Copy a skill into your project's `.github/skills/` directory:

```bash
cp -r skills/ascii-doc /path/to/your-project/.github/skills/
```

Copilot will automatically discover it when you work in that project.

### Verify Installation

```
/skills list
/skills info ascii-doc
```

## Plugins

| Plugin | Description | Skills |
|--------|-------------|--------|
| `document-skills` | AsciiDoc document creation and editing | `asciidoc` |
| `code-review-skills` | Gerrit code review workflows | `gerrit` |

## Skills

### Document Skills

| Skill | Description |
|-------|-------------|
| [`asciidoc`](./skills/asciidoc/) | Write and edit AsciiDoc (`.adoc`) documents processed by Asciidoctor. Covers headers, sections, tables, code blocks, admonitions, images, includes, and document attributes. |

### Code Review Skills

| Skill | Description |
|-------|-------------|
| [`gerrit`](./skills/gerrit/) | Submit, amend, and review Gerrit changes using git-review CLI. Covers patchsets, downloading changes, and rebase operations. |

## Using a Skill

Once installed, Copilot will automatically select the right skill based on your prompt. You can also invoke a skill explicitly:

```
Use the /asciidoc skill to create a weekly status report
```

```
Use the /gerrit skill to download change 12345
```

## Adding New Skills

1. Copy `template/SKILL.md` into a new folder under `skills/`
2. Fill in `name`, `description` (frontmatter), and the instructions body
3. Add the skill path to the relevant plugin in `.claude-plugin/marketplace.json`

## Repository Structure

```
skills-marketplace/
├── .claude-plugin/
│   └── marketplace.json     # Plugin registry (used by /plugin command)
├── skills/
│   ├── asciidoc/
│   │   └── SKILL.md         # AsciiDoc document skill
│   └── gerrit/
│       └── SKILL.md         # Gerrit code review skill
├── template/
│   └── SKILL.md             # Starter template for new skills
├── .gitignore
└── README.md
```

## License

Apache 2.0
