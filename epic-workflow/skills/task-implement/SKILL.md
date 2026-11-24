---
name: task-implement
description: Implements one task from the plan of a Jira issue (epic, story, task, or bug), including its tests, and keeps the plan status up to date. Use when the user asks to implement, start, continue, or fix a planned issue or task.
argument-hint: <ISSUE-KEY> [TASK-ID]
---

# Implement a task

Arguments: `$ARGUMENTS`, expected as `<ISSUE-KEY> [TASK-ID]`.

## 1. Pick the task

- Read `.claude/work/<KEY>/plan.md`. If it does not exist, stop and suggest `/work-plan <KEY>`.
- If a task ID was given, use it.
- If not, and the plan has a single task, use it without asking.
- Otherwise, propose the highest-priority `todo` task whose dependencies are `done` or `in-review`, and confirm with the user.
- If the task's dependencies are not finished, warn the user before continuing.
- Re-read the acceptance criteria of the linked Jira issues. For a bug, also re-read the root cause in `analysis.md`.

## 2. Prepare

- Make sure the working tree is clean. Create a branch from the base branch, following the naming convention in CLAUDE.md.
- Set the task's status to `in-progress` in `plan.md`.
- Read the code in the task's scope.

## 3. Propose before editing

Show a short plan: the files to change, the approach, and the test cases (at least one per acceptance criterion covered). **Wait for the user's approval.** If what you find in the code contradicts the plan, say so now instead of improvising.

## 4. Implement

- Write the unit and integration tests first, following the task's test strategy. E2E tests are not written in this step: they have their own skill, `/task-e2e`.
- **Bugs:** first write a regression test that reproduces the bug, run it, and confirm it fails for the expected reason. Only then fix the root cause. If the test passes before the fix, the bug isn't reproduced yet: stop and tell the user.
- Implement until all tests pass. Run the test, lint, type-check, and build commands listed in CLAUDE.md.
- Stay within the task's scope. If you find something outside it (a bug, a needed refactor, a missing piece), don't fix it; note it for the summary.
- Prefer the patterns already used in the codebase over new ones.
- Commit in small, logical commits with clear messages.

## Commits: no AI attribution

Every commit must look like it was written by the developer alone. This overrides Claude Code's default attribution.

- No `Co-Authored-By` trailer naming Claude or any other AI.
- No "Generated with Claude Code" (or similar) lines, and no session links.
- Before finishing, check with `git log --format=%B <base>..HEAD`. If a commit still has attribution and hasn't been pushed, fix its message with `git commit --amend` (last commit) or tell the user which commits need rewording. Never rewrite commits that were already pushed without asking.

## 5. Finish

- Keep the status at `in-progress` (it becomes `in-review` when the PR is opened). Under the task in `plan.md`, record any notes or deviations from the plan.
- Summarize for the user:
  - what changed (files and behavior)
  - the acceptance criteria covered, and the test that proves each one (for bugs: the regression test, and that it failed before the fix)
  - deviations from the plan, and why
  - out-of-scope findings, with suggested follow-up tickets
  - what deserves the closest human review
- Suggest the next step: `/task-e2e <KEY> <TASK-ID>` if the task's test strategy includes e2e tests, otherwise `/review <KEY> <TASK-ID>`.
