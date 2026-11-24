---
name: task-deliver
description: Delivers a finished task by pushing the branch, opening the PR with a short, clear title and description, updating the Jira issues, and updating the plan. Run only when the user explicitly asks.
argument-hint: <ISSUE-KEY> [TASK-ID]
disable-model-invocation: true
---

# Deliver a task

Arguments: `$ARGUMENTS`, expected as `<ISSUE-KEY> [TASK-ID]`. If no task ID is given, use the only task in the plan if there is just one; otherwise use the task marked `in-progress`.

## 1. Pre-flight checks

- The test, lint, type-check, and build commands in CLAUDE.md all pass. If not, stop.
- Read the latest review at `.claude/work/<KEY>/reviews/<TASK-ID>.md`. If it is missing, or still has open blockers or majors, warn the user and ask whether to continue.
- No commit on the branch has AI attribution (check with `git log --format=%B <base>..HEAD`). If one does, stop and tell the user.
- The working tree is clean and the branch is up to date with the base branch, using the rebase or merge convention in CLAUDE.md. Ask before rewriting history that has already been pushed.

## 2. Open the PR

The PR must be **short and easy to scan**. A reviewer should understand it in under a minute; the diff and the Jira issue hold the details.

### Title

- Follow the format in CLAUDE.md (default: `<ISSUE-KEY>: <short description>`, using the story or ticket the task covers).
- At most ~70 characters. Imperative mood, describing the outcome: `PROJ-125: Add CSV export to invoices`, not `PROJ-125: Changes to invoice module and some fixes`.

### Description

Use the project's PR template if one exists, filled in briefly. Otherwise use only the sections that apply:

```markdown
<1–2 sentences: what changes and why. For bugs: the root cause and the fix.>

Jira: <ISSUE-KEY link>

- <key change or decision>
- <key change or decision>

**How to test:** <only if it isn't obvious from the tests>
**Heads-up:** <only if there are migrations, flags, breaking changes or follow-ups>
```

Rules:

- **Omit any section that would be empty or obvious.** No placeholders, no "N/A".
- At most 3–5 bullets. Don't list every file or repeat the commit messages.
- No marketing language, no restating the title, no summary of the tests unless they are the point of the PR.
- **No AI attribution**: no "Generated with Claude Code" line or similar, no session links.

### Create it

- Show the user the title and description and ask for confirmation. Then push the branch and create the PR with `gh pr create` against the base branch.
- **E2E tests in an external repository:** if the task in `plan.md` records an e2e branch (`E2E: <repository> @ <branch>`), push that branch too and open its PR in the e2e repository, with the same short format. Link the two PRs to each other in their descriptions.

## 3. Update Jira

- Comment on each issue the task covers with the PR link and a one-line summary.
- Move an issue to the review status defined in CLAUDE.md only if all of its tasks in the plan are now `in-review` or `done`. Otherwise, only comment. For a single-task ticket, this means moving it right away.
- Never change the status of an epic; that is left to the team.
- If Jira tools are not available, give the user the comment text to paste.

## 4. Update the plan

- In `plan.md`, set the task's status to `in-review` and add the PR link.
- Report the PR link (and the e2e PR link, if any), the Jira updates made, and the next task suggested by the plan.
