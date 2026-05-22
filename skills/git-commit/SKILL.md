---
name: git-commit
description: Use this skill whenever the user wants help with git commits — writing commit messages, staging files, committing changes, amending commits, squashing history, or understanding what to commit. Trigger when the user mentions "commit", "git add", "staged changes", "commit message", "squash", "amend", or asks to "save my changes", "push my work", or "write a commit".
---

# Git Commit Skill

Helps users write great commit messages, stage changes correctly, and manage commit history cleanly.

---

## Step 1: Understand the Context

Before writing a message or running any git commands, gather context:

1. **What changed?** Run `git diff --staged` (or `git diff` if nothing staged yet) to see actual changes.
2. **What's the repo state?** Run `git status` for untracked/unstaged files.
3. **What branch?** Run `git branch --show-current` — branch name often hints at the task (e.g. `feat/login`, `fix/invoice-null`).
4. **Is there a ticket/issue number?** Ask if not obvious from branch name or context.

Never write a commit message from a vague description alone — always inspect the diff first.

---

## Step 2: Staging the Right Files

Guide the user to stage intentionally:

```bash
git add <specific-file>        # preferred: stage by file
git add -p                     # interactive: hunk-by-hunk (great for mixed changes)
git add .                      # only if ALL changes belong in one commit
```

**One commit = one logical unit of work.** If the diff contains unrelated changes (e.g. a bug fix + a refactor), suggest splitting into two commits.

---

## Step 3: Write the Commit Message

### Conventional Commits format (recommended default)

```
<type>(<scope>): <short summary>

[optional body]

[optional footer: BREAKING CHANGE, Closes #123]
```

**Types:**
| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code restructure, no behavior change |
| `test` | Adding or fixing tests |
| `chore` | Build, deps, tooling, CI |
| `perf` | Performance improvement |
| `revert` | Reverting a previous commit |

**Scope** is optional — use the module, file, or area affected (e.g. `auth`, `api`, `navbar`).

### Summary line rules
- 50 characters or fewer (hard limit: 72)
- Imperative mood: "Add login form" not "Added" or "Adds"
- No period at the end
- Capitalize the first word after the colon

### Body (when needed)
- Wrap at 72 characters
- Explain **why**, not what (the diff shows what)
- Use bullet points for multiple changes

### Examples

```
feat(auth): add OAuth2 login with Google

Replaces the previous username/password-only flow. Users can now
authenticate via Google OAuth2, which reduces friction and removes
the need to manage passwords for SSO organizations.

Closes #42
```

```
fix(cart): prevent duplicate items on rapid click

Debounce the "Add to cart" button by 300ms. Without this, quick
double-clicks were inserting two rows in the orders table.
```

```
chore: upgrade eslint to v9 and migrate config
```

---

## Step 4: Run the Commit

```bash
git commit -m "type(scope): summary"

# Multi-line (opens editor):
git commit

# With inline body:
git commit -m "type(scope): summary" -m "Body paragraph here."
```

---

## Common Fixes & Operations

### Amend the last commit (not yet pushed)
```bash
# Fix message only:
git commit --amend -m "corrected message"

# Add forgotten file:
git add forgotten.txt
git commit --amend --no-edit
```

### Undo last commit, keep changes staged
```bash
git reset --soft HEAD~1
```

### Split a commit into two
```bash
git reset HEAD~1           # unstage last commit
git add -p                 # stage first logical chunk
git commit -m "first part"
git add .
git commit -m "second part"
```

---

## Commit Message Anti-Patterns to Avoid

| Bad | Why | Better |
|-----|-----|--------|
| `fix stuff` | Vague | `fix(auth): resolve token expiry on refresh` |
| `WIP` | Not a real commit | Squash or stash instead |
| `Added some changes` | Past tense + vague | `feat: add dark mode toggle` |
| `asdfgh` | Placeholder | Always write a real message |
| Huge 47-file commit | Un-reviewable | Split by concern |

---

## Quick Reference

```bash
git status                    # What's changed?
git diff                      # Unstaged changes
git diff --staged             # Staged changes (what will be committed)
git log --oneline -10         # Recent commits
git show HEAD                 # Last commit details
```

---

## Tips

- **Atomic commits**: Each commit should pass tests and be deployable on its own.
- **Draft early, clean up before PR**: Use `git rebase -i` to polish history.
- **Co-author**: If pair-programming, add `Co-authored-by: Name <email>` in the body.
- **Sign commits**: If the repo requires it, use `git commit -S`.

---

## Guardrails & Constraints

These are hard rules — never skip or work around them, even if the user asks.

### Never run destructive commands without explicit confirmation

Before suggesting any of the following, state clearly what will happen and ask the user to confirm:

| Command | Risk |
|---------|------|
| `git reset --hard` | Permanently discards uncommitted changes |
| `git push --force` | Overwrites remote history — can delete others' work |
| `git clean -fd` | Deletes untracked files permanently |


### Never run `git add .` without reviewing the diff first

`git add .` stages everything including unintended files (secrets, build artifacts, temp files). Always inspect with `git status` and `git diff` first. If a `.gitignore` is missing entries, flag it.

### Warn before staging sensitive-looking files

If the diff or file list includes any of the following, warn the user before proceeding:
- `.env`, `.env.local`, `*.pem`, `*.key`, `id_rsa`, `credentials.*`
- Files containing strings like `password`, `secret`, `token`, `api_key` in the filename

Say: *"This file may contain sensitive data. Are you sure you want to commit it? Consider adding it to `.gitignore`."*
