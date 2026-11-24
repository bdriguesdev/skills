---
name: task-deliver
description: Delivers a finished task by pushing the branch, opening the PR with a full description, updating the Jira issues, and updating the plan. Run only when the user explicitly asks.
argument-hint: <ISSUE-KEY> [TASK-ID]
disable-model-invocation: true
---

# Deliver a task

Arguments: `$ARGUMENTS`, expected as `<ISSUE-KEY> [TASK-ID]`. If no task ID is given, use the only task in the plan if there is just one; otherwise use the task marked `in-progress`.

## 1. Pre-flight checks

- The test, lint, type-check, and build commands in CLAUDE.md all pass. If not, stop.
- Read the latest review at `.claude/work/<KEY>/reviews/<TASK-ID>.md`. If it is missing, or still has open blockers or majors, warn the user and ask whether to continue.
- The working tree is clean and the branch is up to date with the base branch, using the rebase or merge convention in CLAUDE.md. Ask before rewriting history that has already been pushed.

## 2. Open the PR

- Use the project's PR template if one exists. Otherwise use:

```markdown
## What
<1–3 sentences>

## Why
<KEY> (and the related issue keys), with links. For bugs: the root cause in one or two sentences.

## How
<key decisions; anything non-obvious>

## Acceptance criteria
- [x] <criterion> — <test that proves it>
<for bugs: the regression test that reproduces the reported scenario>

## How to test
<steps for the reviewer>

## Risks and notes
<flags, migrations, rollout, follow-ups>
```

- The title follows the format in CLAUDE.md (default: `<ISSUE-KEY>: <short description>`, using the story or ticket the task covers).
- Show the user the title and description and ask for confirmation. Then push the branch and create the PR with `gh pr create` against the base branch.

## 3. Update Jira

- Comment on each issue the task covers with the PR link and a one-line summary.
- Move an issue to the review status defined in CLAUDE.md only if all of its tasks in the plan are now `in-review` or `done`. Otherwise, only comment. For a single-task ticket, this means moving it right away.
- Never change the status of an epic; that is left to the team.
- If Jira tools are not available, give the user the comment text to paste.

## 4. Update the plan

- In `plan.md`, set the task's status to `in-review` and add the PR link.
- Report the PR link, the Jira updates made, and the next task suggested by the plan.
