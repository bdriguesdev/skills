---
name: task-implement
description: Implements one task from an epic's plan, including its tests, and keeps the plan status up to date. Use when the user asks to implement, start, or continue a task or story from the plan.
argument-hint: <EPIC-KEY> [TASK-ID]
---

# Implement a task

Arguments: `$ARGUMENTS`, expected as `<EPIC-KEY> [TASK-ID]`.

## 1. Pick the task

- Read `.claude/epics/<EPIC-KEY>/plan.md`. If it does not exist, stop and suggest `/epic-plan <EPIC-KEY>`.
- If a task ID was given, use it. Otherwise, propose the highest-priority `todo` task whose dependencies are `done` or `in-review`, and confirm with the user.
- If the task's dependencies are not finished, warn the user before continuing.
- Re-read the acceptance criteria of the linked Jira stories.

## 2. Prepare

- Make sure the working tree is clean. Create a branch from the base branch, following the naming convention in CLAUDE.md.
- Set the task's status to `in-progress` in `plan.md`.
- Read the code in the task's scope.

## 3. Propose before editing

Show a short plan: the files to change, the approach, and the test cases (at least one per acceptance criterion covered). **Wait for the user's approval.** If what you find in the code contradicts the plan, say so now instead of improvising.

## 4. Implement

- Write the tests first, following the task's test strategy. For e2e tests, follow the `e2e-tests` skill.
- Implement until all tests pass. Run the test, lint, type-check, and build commands listed in CLAUDE.md.
- Stay within the task's scope. If you find something outside it (a bug, a needed refactor, a missing piece), don't fix it; note it for the summary.
- Prefer the patterns already used in the codebase over new ones.
- Commit in small, logical commits with clear messages.

## 5. Finish

- Keep the status at `in-progress` (it becomes `in-review` when the PR is opened). Under the task in `plan.md`, record any notes or deviations from the plan.
- Summarize for the user:
  - what changed (files and behavior)
  - the acceptance criteria covered, and the test that proves each one
  - deviations from the plan, and why
  - out-of-scope findings, with suggested follow-up tickets
  - what deserves the closest human review
- Suggest running `/review <EPIC-KEY> <TASK-ID>` next.
