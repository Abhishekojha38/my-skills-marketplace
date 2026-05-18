---
name: asciidoctor
description: >
  Use this skill whenever the user wants to create, edit, review, reformat, or
  improve AsciiDoc (.adoc) files. Triggers include: any mention of ".adoc",
  "AsciiDoc", "Asciidoctor", "adoc file", or requests to write technical
  documentation, READMEs, manuals, or structured documents in AsciiDoc format.
  Also triggers for: wrapping lines at 80 characters in .adoc files, grammar
  checking AsciiDoc content, adding or formatting tables in AsciiDoc, converting
  bullet lists to AsciiDoc syntax, applying text highlighting or admonition
  blocks, linting or cleaning up .adoc formatting, and converting Markdown or
  plain text into AsciiDoc. When in doubt, use this skill for any task touching
  an .adoc file — even if the user just says "fix my doc" and there's an .adoc
  file present.
---

# AsciiDoctor Skill

This skill enables AI agents to create, modify, review, and format AsciiDoc
(`.adoc`) files to a professional standard. It covers the full editing
lifecycle: line wrapping, grammar checking, structural formatting (tables,
lists, headings), text highlighting, and admonition blocks.

---

## Workflow Overview

1. **Read** the `.adoc` file (or accept content inline)
2. **Detect** which tasks apply (wrap, grammar, structure, highlighting)
3. **Apply** changes in order: structure → grammar → formatting → line wrap
4. **Output** the corrected `.adoc` file and summarise changes made

Always apply line wrapping **last**, after all structural edits, to avoid
re-wrapping after content insertions.

---

## 1. Reading and Writing `.adoc` Files

Use `bash_tool` or `view` to read `.adoc` files from disk.

```bash
cat /path/to/file.adoc
```

Write changes using `str_replace` for targeted edits, or `create_file` when
rewriting the whole document.

Always preserve the document header block (lines starting with `=`, `:author:`,
`:revdate:`, `:doctype:`, etc.) exactly — do not reformat it.

---

## 2. Line Wrapping at 80 Characters

**Rule:** Every content line must be ≤ 80 characters. This is a hard limit.

### What to wrap
- Paragraph prose
- List item text that exceeds 80 chars
- Table cell content (wrap inside the cell, not at the `|` delimiter)
- Admonition body text

### What NOT to wrap
- Document header attributes (`:key: value` lines)
- Block delimiters (`----`, `====`, `****`, `____`, `++++`)
- Macro calls on a single line (`image::`, `include::`, `link:`)
- URLs inside angle brackets (`<https://...>`)
- Code blocks (inside `[source]` + `----` fences) — never rewrap code
- Table delimiter rows (`|===`)
- Section titles (`== Title`, `=== Subtitle`)

### Wrapping rules
- Break at a word boundary; never split a word across lines.
- Continuation lines in a paragraph need **no** special character — a bare
  newline continues the paragraph in AsciiDoc.
- For list items longer than 80 chars, indent the continuation with `+` or
  just a two-space indent on the next line:

```adoc
* This is a long list item that needs to be wrapped at the eighty character
  boundary so it stays readable.
```

### Automated wrap (bash helper)

When wrapping a large file, use `fold` or `fmt` carefully — only on prose
paragraphs, never on code blocks:

```bash
# Quick check: list lines over 80 chars with line numbers
awk 'length > 80 {print NR": "length" chars: "$0}' file.adoc
```

Review output manually; do not blindly pipe the whole file through `fold`.

---

## 3. Grammar Check

Perform a grammar review on all prose content. Focus on:

- Subject–verb agreement
- Correct article usage (a/an/the)
- Consistent tense (prefer present tense for technical docs)
- Spelling errors
- Oxford comma in lists of three or more items
- Avoid passive voice where active is clearer
- Remove redundant words ("in order to" → "to", "due to the fact that" →
  "because")

**Do not** change technical terms, product names, command strings, or content
inside code blocks (`----` fences), inline code (`` `code` ``), or attribute
values.

When reporting grammar changes, list them as a brief summary after the edited
file, e.g.:

```
Grammar changes:
- Line 14: "it are" → "it is"
- Line 31: removed redundant "in order to"
- Line 55: "an user" → "a user"
```

---

## 4. Tables

Use AsciiDoc's `|===` table syntax. Follow these conventions:

### Basic table

```adoc
[cols="1,2,1", options="header"]
|===
| Column A | Column B           | Column C

| Row 1    | Some longer value  | X
| Row 2    | Another value      | Y
|===
```

### When to add a table

Convert to a table when the content is a **comparison**, a **property list**,
or a **multi-attribute description** with 3 or more items that currently sit
in prose or a bullet list. Ask yourself: would a reader scan this faster as a
table? If yes, convert it.

### Column widths

- Use relative integer ratios in `cols=`: `"1,3,1"` means narrow / wide /
  narrow.
- For simple equal-width tables, omit `cols=` entirely.
- Always add `options="header"` when the first row is a header.

### Cell wrapping

Long cell content wraps naturally inside the cell. You do not need explicit
line breaks. Keep individual cell lines ≤ 80 chars where possible.

### Advanced features (use when appropriate)

```adoc
// Merged cell (colspan)
2+| This cell spans two columns

// Vertical alignment
[cols="^,<,>"]   // center, left, right

// Asciidoc content inside a cell
a| This cell can contain *bold*, lists, etc.
```

---

## 5. Bullet Points and Lists

### Unordered list (bullets)

```adoc
* First item
* Second item
** Nested item
** Another nested item
* Third item
```

Use `*` for bullets. Use `**` for one level of nesting, `***` for two, etc.

### Ordered list

```adoc
. Step one
. Step two
.. Sub-step
. Step three
```

### Description list (definition list)

```adoc
Term one::
  Definition of term one.

Term two::
  Definition of term two.
```

### When to add a list

Convert prose to a list when:

- Three or more parallel items appear in a sentence joined by commas or "and"
- Steps must be performed in order → use ordered list (`.`)
- You are enumerating features, options, or requirements

Do **not** convert to a list when:

- There are only two items (keep as prose: "X and Y")
- The items are part of flowing narrative with connective reasoning
- The content is already in a table

### Continuation blocks

To attach a code block or paragraph to a list item, use `+`:

```adoc
. Install the package:
+
[source,bash]
----
npm install my-package
----

. Verify the installation.
```

---

## 6. Text Highlighting and Emphasis

### Inline formatting

| Effect            | Syntax                  | Example output     |
|-------------------|-------------------------|--------------------|
| Bold              | `*word*`                | **word**           |
| Italic            | `_word_`                | _word_             |
| Monospace/code    | `` `word` ``            | `word`             |
| Highlighted       | `#word#`                | highlighted        |
| Strikethrough     | `[line-through]#word#`  | ~~word~~           |
| Superscript       | `^word^`                | word^              |
| Subscript         | `~word~`                | word_              |

### When to apply emphasis

- **Bold** (`*...*`): key terms on first use, UI element names, warnings
  inline in prose.
- _Italic_ (`_..._`): book/doc titles, foreign words, subtle stress.
- `` `Code` ``: command names, file paths, variable names, config keys —
  **always** use monospace for these, never bold or plain text.
- `#Highlight#`: use sparingly for genuinely critical callouts that don't
  warrant a full admonition block.

Never bold entire sentences or paragraphs. If a whole block needs emphasis,
use an admonition instead.

### Admonition blocks

Use admonitions for structured callouts:

```adoc
NOTE: This is a general note for additional context.

TIP: This is a helpful suggestion or shortcut.

IMPORTANT: This must not be overlooked.

WARNING: This could cause data loss or unexpected behaviour.

CAUTION: Proceed carefully; verify before continuing.
```

Or the block form (preferred for multi-sentence content):

```adoc
[WARNING]
====
Deleting this directory will remove all user data.
Make sure you have a backup before proceeding.
====
```

**Admonition selection guide:**

- `NOTE` — supplementary info, "by the way" context
- `TIP` — optional improvement, shortcut, best practice
- `IMPORTANT` — required step or condition that is easy to miss
- `WARNING` — potential for data loss, security risk, or breaking change
- `CAUTION` — reversible but risky action; user should double-check

---

## 7. Document Structure Conventions

### Header block

```adoc
= Document Title
Author Name <author@example.com>
:revdate: 2025-01-01
:doctype: article
:toc:
:toc-title: Table of Contents
:icons: font
:source-highlighter: rouge
```

Always keep the header intact when editing. Add `:toc:` if the document has
three or more sections and it is absent.

### Section levels

```adoc
= Document Title        (level 0 — document title only)
== Section              (level 1)
=== Subsection          (level 2)
==== Sub-subsection     (level 3)
```

Do not skip levels (e.g., never go from `==` directly to `====`).

### Blank lines

- One blank line between paragraphs.
- One blank line before and after any block (list, table, code block,
  admonition, section title).
- No trailing whitespace on any line.
- End the file with exactly one newline.

---

## 8. Code Blocks

Always use a named source block with a language tag:

```adoc
[source,bash]
----
echo "Hello, world"
----

[source,python]
----
def greet(name):
    return f"Hello, {name}"
----
```

- Never wrap or reformat content inside `----` fences.
- Add `[source,<lang>]` if it is missing and the language is identifiable.
- Use `....` (literal block) for plain pre-formatted text with no syntax.

---

## 9. Review Checklist

Run through this checklist before finalising any `.adoc` file:

- [ ] All lines ≤ 80 characters (except exempt lines listed in §2)
- [ ] No trailing whitespace
- [ ] File ends with a single newline
- [ ] No skipped heading levels
- [ ] All code blocks have `[source,<lang>]` annotation
- [ ] Tables have `options="header"` where the first row is a header
- [ ] Admonitions use the correct type for the content
- [ ] Inline code (`` `...` ``) used for all commands, paths, and config keys
- [ ] Grammar checked; passive voice minimised
- [ ] Blank lines present before and after all blocks
- [ ] No bare URLs — wrap in `<url>` or use `link:url[label]`

---

## 10. Output Format

When returning an edited file:

1. Output the full corrected `.adoc` content (use `create_file` to write it).
2. Follow with a **Changes Summary** section in chat:

```
Changes Summary
───────────────
Line wrap:    43 lines wrapped to ≤ 80 chars
Grammar:      5 corrections (see details below)
Tables:       1 new table added (lines 34–45)
Lists:        2 prose enumerations converted to bullet lists
Highlighting: 3 inline code spans added; 1 WARNING admonition added
Structure:    Added :toc: to document header
```

If no changes were needed in a category, omit that line.