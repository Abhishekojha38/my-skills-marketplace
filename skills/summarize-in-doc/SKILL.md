---
name: summarize-in-doc
description: Compact the current conversation into a summary document for another agent to pick up.
argument-hint: "What will the next session be used for?"
---

Write a summary document summarizing the current conversation, so a fresh agent can continue the work. Save to the current directory of project for which the summary is being generated.

Name of the summary doc must be in format: "summary.md".

Suggest the skills to be used, if any, by the next session.

Do not duplicate content already captured in other artifacts (PRDs, plans, issues, commits, etc. ). Reference them by path or URL instead.

Remove or redact any sensitive information, including API keys, passwords, and personally identifiable information (PII).

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.

Ensure a clear, structured format that allows the next agent to quickly understand the context, decisions made, and next steps.

Use markdown to format the document.
