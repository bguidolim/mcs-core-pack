---
description: Create Pull Request
---

# Create Pull Request

Git-only workflow: stage, commit, push, open PR. Never build or test.

Arguments: $ARGUMENTS (optional, e.g. `target develop`, `skip commit`).

## 1. Analyze

Run in parallel: `git status` (no `-uall`), `git diff` (staged + unstaged), `git log origin/HEAD..HEAD --oneline` (fall back to `origin/main` if needed).

Extract the **ticket** from the branch (`__BRANCH_PREFIX__/{ticket}-*` or `{ticket}-*`) or commits. Ask if missing.

## 2. Search project knowledge

After analyzing, search available memory/knowledge tools using keywords from the branch and diff. Look for anything that should shape the PR: PR conventions and templates, CI quirks or required checks, review checklists, prior decisions in the touched modules, known gotchas, related past PRs/issues. Fold relevant findings into the body.

## 3. Commit

- Stage specific files (never `-A`, never `.env` or credentials).
- Re-check `git diff --staged` before writing the message — describe **only** what's actually staged.
- Use the conversation for the *why*, not the *what*.
- Message: one-line summary + up to 3 bullets. HEREDOC.
- Nothing staged → skip.

## 4. Push and pick base branch

In parallel:

**Push** with `-u` if needed.

**Detect base**: default branch from `gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'`. For each remote branch (`git branch -r --sort=-committerdate`, excluding the current branch and `HEAD`), compute distance from HEAD to the merge-base via `git rev-list --count <merge-base>..HEAD`. Smallest distance wins; strip `origin/`. Fall back to default on failure.

If the detected base **is not** the default, confirm with `AskUserQuestion`. Otherwise use it. Pass `--base <branch>` to `gh pr create`.

## 5. Create the PR

**Title**: `TICKET: brief description`, under 72 chars.

**Body**: write for a reviewer with zero context. Match length to the *complexity* of the change, not the size of the diff. Most PRs land in **2–5 sentences total**. A one-line rename or config bump is a one-line PR.

Judgment rules:

- Lead with **why** — symptom, goal, or constraint. Don't restate the title.
- Describe **behavior**, not implementation. No file/class/method/line names in the body — the diff shows those.
- **If a section would mostly restate what the diff already shows, omit it.** A "Changes" recap is almost never worth writing — the diff is the changes.
- **Don't list unchanged behavior.** If a bullet ends in "(unchanged)" or describes something the PR didn't touch, delete it. Reviewers assume unchanged behavior stays unchanged; call it out only when a reviewer would otherwise reasonably suspect it broke.
- **Tell the why once.** Don't write the same story at both the behavior level and the mechanism level in separate paragraphs — pick the level the reviewer needs and stay there.
- Don't *add* metadata lines that restate the title or branch (`Linked ticket: …`, `Ticket: …`, `JIRA: …`, `Branch: …`) — the title carries the ticket. If the template provides such a field, fill it; otherwise don't invent one.
- Don't report CI-verifiable output (test counts, lint, typecheck, coverage).
- Don't add checkmarks or task-list checkboxes unless the template provides them.
- Don't invent sections ("Context", "Background", "Design notes", "Out of scope", etc.) to pad the body.
- **Test plan**: imperatives + expected result, e.g. "Run `mcs sync` with a drifted lockfile → expect the migration-hint warning". Numbered if order matters; bullets otherwise. If nothing to verify manually, say so in one line — don't omit the section, but don't pad it either.

**Template**: check `.github/`, repo root, `docs/` for `PULL_REQUEST_TEMPLATE.md` (case-insensitive). If `.github/PULL_REQUEST_TEMPLATE/` has multiples, ask which. Use the template's headings and order; empty sections get `N/A` on one line — do not pad. Keep template-provided checkboxes. No template → use `## Why` + `## Test plan` only. Add a `## Changes` section **only** if there's something the diff genuinely can't convey (a behavior toggle, a migration step, a non-obvious sequencing) — never as a file-by-file recap.

Create with `gh pr create --base <branch>`, body via HEREDOC.

## 6. Report

Print the PR URL.

## 7. Evaluate learnings

If the session produced reusable knowledge, route it through the available memory/knowledge tools.
