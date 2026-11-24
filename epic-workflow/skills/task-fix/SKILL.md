---
name: task-fix
description: Applies fixes from a review report or from PR review comments, triaging each item before changing code. Use when the user asks to fix review findings or address PR comments.
argument-hint: <ISSUE-KEY> [TASK-ID] [pr]
---

# Fix review feedback

Arguments: `$ARGUMENTS`, expected as `<ISSUE-KEY> [TASK-ID]`, optionally followed by `pr` to work from the open PR's comments. If no task ID is given, use the only task in the plan if there is just one; otherwise ask.

## 1. Collect the feedback

- Default: the review report at `.claude/work/<KEY>/reviews/<TASK-ID>.md` (items not yet marked fixed).
- With `pr`: read the PR's comments and review threads with `gh`, for example `gh pr view --comments` for general comments and `gh api repos/{owner}/{repo}/pulls/<number>/comments` for inline comments. Skip resolved threads.

## 2. Triage before changing anything

Classify each item:

- **Fix:** valid and within the task's scope
- **Discuss:** you disagree or it is ambiguous; explain why in one or two sentences
- **Follow-up:** valid but outside the task's scope; propose a ticket

Show the triage table and wait for the user to confirm.

## 3. Fix

- Apply the confirmed fixes, most severe first. Add a test for each bug fixed where possible.
- Run the test, lint, type-check, and build commands in CLAUDE.md.
- Commit with messages that point to the feedback, e.g. `fix: handle empty list (review #3)`. No AI attribution in commits (no `Co-Authored-By` trailer for Claude or any AI, no "Generated with" lines).
- Findings about e2e tests in an external repository are fixed in that repository, on the task's e2e branch.
- Don't reply to or resolve PR threads yourself. Draft a reply per thread for the user instead.

## 4. Report

- In the review file, set the status of each fixed item to `fixed`.
- Tell the user what was fixed (with commits), what is waiting for discussion (with draft replies), and the proposed follow-up tickets.
- If the changes were significant, suggest running `/review <KEY> <TASK-ID>` again.
