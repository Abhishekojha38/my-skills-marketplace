---
name: asciidoc
description: "Use this skill whenever the user wants to create, write, edit, or convert documents using AsciiDoc format (files ending in .adoc or .asciidoc). Triggers include: requests to write AsciiDoc documents, technical documentation, README files in AsciiDoc, books, articles, man pages, or any structured document that will be processed by Asciidoctor. Also use when converting Markdown or plain text to AsciiDoc, or when asking about AsciiDoc syntax, attributes, macros, or Asciidoctor features. Do NOT use for Markdown (.md), HTML, Word (.docx), or PDF output directly — AsciiDoc is the source format; Asciidoctor converts it."
---

# AsciiDoc Document Skill

## Overview

AsciiDoc is a lightweight markup language for writing technical documentation, books, articles, and man pages. [Asciidoctor](https://asciidoctor.org) is the standard processor that converts `.adoc` files to HTML5, PDF, DocBook, EPUB, and more.

**File extension:** `.adoc` (preferred) or `.asciidoc`
**Reference:** https://docs.asciidoctor.org/asciidoc/latest/

---

## Document Header

The header is optional but recommended. It must appear at the top with **no blank lines** between header elements. One blank line ends the header and begins the body.

```asciidoc
= Document Title
Author Name <author@example.com>
v1.0, 2025-01-10
:toc:
:toc-title: Table of Contents
:sectnums:
:source-highlighter: rouge
:icons: font
:doctype: article

First paragraph of the document body.
```

### Key Header Attributes

| Attribute | Effect |
|-----------|--------|
| `:toc:` | Generates a table of contents |
| `:toc-title: My TOC` | Custom TOC heading |
| `:sectnums:` | Auto-numbers sections |
| `:source-highlighter: rouge` | Syntax highlighting in code blocks |
| `:icons: font` | Enables Font Awesome icons for admonitions |
| `:doctype: article` | Document type: `article` (default), `book`, `manpage` |
| `:author: Name` | Override author (if not set in header line 2) |
| `:revdate: 2025-01-10` | Revision date |
| `:imagesdir: images/` | Base path for all images |
| `:data-uri:` | Embed images as data URIs in HTML output |

---

## Section Titles

Sections use `=` signs. The document title is level 0; body sections start at level 1.

```asciidoc
= Document Title          (level 0 — document title only)

== Level 1 Section

=== Level 2 Section

==== Level 3 Section

===== Level 4 Section

====== Level 5 Section
```

For a **discrete heading** (not part of the section hierarchy):

```asciidoc
[discrete]
=== Standalone Heading
```

---

## Text Formatting

```asciidoc
*bold text*                  Bold (constrained)
**b**old                     Bold (unconstrained, mid-word)

_italic text_                Italic (constrained)
__i__talic                   Italic (unconstrained)

`monospace text`             Inline monospace
``m``onospace                Monospace (unconstrained)

*_bold italic_*              Bold and italic combined
`*_all three_*`              Monospace bold italic

#highlighted text#           Highlight
[.underline]#underlined#     Underlined
[.line-through]#struck#      Strikethrough

^super^script                Superscript
~sub~script                  Subscript

"`double curved quotes`"     Typographic double quotes
'`single curved quotes`'     Typographic single quotes
```

---

## Links and Cross-References

```asciidoc
https://example.com                           Autolink
https://example.com[Link Text]                Named link
https://example.com[Open,window=_blank]       New tab

link:relative/path.html[Local File]           Relative link

<<section-id>>                                Cross-reference by ID
<<section-id,Custom Text>>                    Cross-reference with label

[[my-anchor]]                                 Define inline anchor
[#my-anchor]                                  Block anchor (preferred)
```

---

## Lists

### Unordered (Bullet) Lists

```asciidoc
* First item
* Second item
** Nested item
*** Deeper nested item
* Third item
```

### Ordered Lists

```asciidoc
. Step one
. Step two
.. Sub-step A
.. Sub-step B
. Step three
```

### Checklist

```asciidoc
* [x] Completed task
* [ ] Pending task
* [*] Also completed
```

### Description List

```asciidoc
Term one::
  Definition of term one.

Term two::
  Definition of term two.
```

### List Continuation (attach content to list item)

```asciidoc
* First item
+
This paragraph is attached to the first item.
+
----
code block also attached
----

* Second item
```

---

## Code Blocks

### Listing Block (no syntax highlighting)

```asciidoc
----
plain listing block
no syntax highlighting
----
```

### Source Block (with syntax highlighting)

```asciidoc
[source,python]
----
def greet(name):
    return f"Hello, {name}!"

print(greet("world"))
----
```

### Source Block with Title and Callouts

```asciidoc
.server.py
[source,python]
----
import http.server  // <1>

PORT = 8080  // <2>

httpd = http.server.HTTPServer(("", PORT), http.server.SimpleHTTPRequestHandler)  // <3>
httpd.serve_forever()
----
<1> Import the standard library HTTP server.
<2> Define the port number.
<3> Create and start the server.
```

### Literal Block (preserves spacing, no highlighting)

```asciidoc
....
$ git status
On branch main
nothing to commit, working tree clean
....
```

### Inline Monospace

```asciidoc
Use the `--verbose` flag for more output.
```

---

## Tables

```asciidoc
|===
|Column 1 |Column 2 |Column 3

|Row 1, Col 1
|Row 1, Col 2
|Row 1, Col 3

|Row 2, Col 1
|Row 2, Col 2
|Row 2, Col 3
|===
```

### Table with Title and Column Widths

```asciidoc
.Supported Formats
[cols="1,2,1",options="header"]
|===
|Format |Description |Extension

|HTML5
|Web-ready output via Asciidoctor
|.html

|PDF
|Print-ready via asciidoctor-pdf
|.pdf

|DocBook
|XML interchange format
|.xml
|===
```

### Column Specifiers

| Specifier | Meaning |
|-----------|---------|
| `1,2,1` | Relative widths (proportional) |
| `~,~,~` | Auto widths |
| `h` prefix | Header-style column (e.g., `h,1,1`) |
| `>` | Right-align column |
| `^` | Center-align column |
| `<` | Left-align (default) |
| `a` | AsciiDoc content in cell |
| `m` | Monospace column |
| `e` | Emphasis (italic) column |

### Cell with AsciiDoc Content

```asciidoc
[cols="1a,1"]
|===
|AsciiDoc Cell |Normal Cell

|
* bullet one
* bullet two

NOTE: Admonitions work too.

|Plain text cell
|===
```

---

## Admonitions

```asciidoc
NOTE: A general note for the reader.

TIP: A helpful tip or shortcut.

IMPORTANT: Something the reader must not overlook.

CAUTION: Warns of a potential pitfall.

WARNING: Indicates a risk of data loss or damage.
```

### Admonition Block (multi-paragraph)

```asciidoc
[WARNING]
====
This operation is irreversible.

Make sure you have a backup before proceeding.
====
```

---

## Images

```asciidoc
image::path/to/image.png[]                     Block image

image::path/to/image.png[Alt text]             With alt text

image::path/to/image.png[Screenshot,400,300]   With width and height

.Figure Caption
image::path/to/image.png[Alt text,600]         With title/caption

image:inline.png[icon] followed by text        Inline image (single colon)
```

---

## Includes

```asciidoc
include::other-file.adoc[]              Include full file

include::snippets/code.rb[tag=example]  Include tagged region only

include::data.csv[lines=1..5]           Include specific lines
```

### Tagged Region in Source File

```asciidoc
# tag::example[]
def hello():
    print("Hello")
# end::example[]
```

---

## Blocks and Delimiters

### Sidebar

```asciidoc
.Sidebar Title
****
Sidebar content — supplementary material set apart from the main text.
****
```

### Example Block

```asciidoc
.Example Title
====
Content of the example block.
====
```

### Quote / Blockquote

```asciidoc
[quote,Author Name,Source Title]
____
The quoted text goes here.
____

"Short inline quote."
-- Attribution, Source
```

### Passthrough Block (raw HTML/content)

```asciidoc
++++
<div class="custom">Raw HTML passed through as-is.</div>
++++
```

---

## Attributes (Variables)

```asciidoc
:product-name: Skills Marketplace
:version: 2.0

The {product-name} version {version} is now available.
```

Unset an attribute with a leading `!`:

```asciidoc
:!toc:
```

---

## Macros

### Keyboard, Button, Menu (requires `:experimental:`)

```asciidoc
= Doc Title
:experimental:

Press kbd:[Ctrl+S] to save.

Click btn:[Submit] to continue.

Select menu:File[Save As > PDF].
```

---

## Document Structure Examples

### Article (default)

```asciidoc
= Technical Guide
Author Name <author@example.com>
v1.0, 2025-01-10
:toc:
:sectnums:
:source-highlighter: rouge
:icons: font

== Introduction

Brief introduction to the topic.

== Installation

=== Prerequisites

* Ruby 3.0+
* Bundler

=== Steps

. Clone the repository.
. Run `bundle install`.
. Run `bundle exec asciidoctor document.adoc`.

== Configuration

[source,yaml]
----
server:
  port: 8080
  host: localhost
----

[NOTE]
====
All configuration values can be overridden via environment variables.
====

== Reference

.Available Commands
[cols="1m,2",options="header"]
|===
|Command |Description

|asciidoctor doc.adoc
|Convert to HTML5

|asciidoctor -b docbook doc.adoc
|Convert to DocBook XML
|===
```

### README (minimal)

```asciidoc
= Project Name

A one-line description.

== Quick Start

[source,bash]
----
git clone https://github.com/user/repo
cd repo
make install
----

== Documentation

See the link:docs/guide.adoc[User Guide] for full details.

== License

MIT
```

---

## Guidelines

1. **File extension:** Use `.adoc` for all AsciiDoc files.
2. **Header first:** Place the document header (title, author, attributes) at the top with no blank lines between elements; one blank line ends the header.
3. **Blank lines matter:** A blank line separates paragraphs, ends lists, and ends most blocks.
4. **Attributes over hardcoding:** Use document attributes (`:variable: value`) for values used more than once.
5. **Prefer block macros over inline for images:** Use `image::` (two colons) for standalone images; `image:` (one colon) only when the image is part of a sentence.
6. **Source blocks always specify language:** `[source,python]`, `[source,bash]`, etc. — enables correct syntax highlighting.
7. **`:sectnums:` for technical docs:** Always suggest numbered sections for technical documentation and books.
8. **`:toc:` placement:** Place `:toc:` in the document header; for left-positioned TOC use `:toc: left`.
9. **Admonitions sparingly:** Use `NOTE`, `TIP`, `IMPORTANT`, `CAUTION`, `WARNING` to call attention to specific content — don't overuse.
10. **Never mix Markdown syntax:** AsciiDoc and Markdown look similar but are incompatible. Never use `#` for headings, `**` for bold, or `---` for horizontal rules in AsciiDoc.
