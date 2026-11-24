---
name: task-e2e
description: Writes and runs end-to-end tests for a task, either in this repository or in a separate, existing e2e repository, as configured in CLAUDE.md. Use when the plan calls for e2e tests on a task, or when the user asks to write, update, or fix e2e tests.
argument-hint: <ISSUE-KEY> [TASK-ID]
---

# E2E tests for a task

Arguments: `$ARGUMENTS`, expected as `<ISSUE-KEY> [TASK-ID]`. If no task ID is given, use the only task in `.claude/work/<KEY>/plan.md` if there is just one; otherwise use the task marked `in-progress`, and ask if there is more than one.

If the user asks for e2e tests without an issue key, skip the plan and ask which user flow to cover.

## 1. Confirm e2e tests are needed

- Read the task's test strategy in `plan.md` and the acceptance criteria of the linked Jira issues.
- If the plan doesn't call for e2e tests and the user didn't ask for them explicitly, say so and ask before continuing. E2E tests are slow and costly to maintain; don't add them by default.
- List the scenarios to cover: the main path of each relevant acceptance criterion and its most important failure path. Nothing that a unit or integration test already covers.

## 2. Find where the tests live

Read the **E2E tests** section of CLAUDE.md:

- **`Location: local`**: tests live in this repository, in the path given.
- **`Location: external`**: tests live in a separate, existing repository. CLAUDE.md gives the repository, the path of its local clone, and its base branch.

If the section is missing or incomplete, **ask the user** where the e2e tests live, and offer to add the answer to CLAUDE.md. Never create a new e2e folder, framework, or repository when you haven't been told none exists.

## 3. Prepare the external repository (external only)

- Check that the local clone exists at the configured path. If it doesn't, ask the user for the path or for permission to clone it there.
- Check that its working tree is clean. If not, stop and tell the user; don't stash or discard their changes.
- Update its base branch, then create a branch with **the same name as the application branch**, so the two are easy to match.
- Read that repository's own CLAUDE.md and README. Its conventions win over the general principles below.

## 4. Learn the existing conventions

Before writing anything, find one or two existing tests for similar flows and follow their patterns: folder structure, file naming, page objects and helpers, how tests log in, how test data is created, how elements are selected. Reuse existing helpers instead of writing new ones.

## 5. Write the tests

- Test user-visible behavior, not implementation details.
- One scenario per test, named after the behavior, e.g. `user can reset password with a valid link`.
- Select elements by role, label, or test ID, never by CSS class or DOM structure. If the application needs a new test ID, add it to the application code as part of this task.
- No fixed sleeps. Wait for visible states or network responses.
- Each test creates and cleans up its own data. Tests must pass in any order and in parallel.

## 6. Run the tests

- Start the application from the task's branch, using the command in the E2E tests section of CLAUDE.md. For an external repository, point it at the local application as configured (for example, a base URL variable).
- Run the new tests, then run them a few more times to make sure they are not flaky.
- Run the existing tests for the same area to check that nothing else broke.
- If the environment can't be started, say so clearly. Never report tests as passing if you couldn't run them.

## 7. Commit

- Commit in the repository where the tests live, with clear messages. Follow the same rule as all commits in this workflow: **no AI attribution** (no `Co-Authored-By` trailer for Claude or any AI, no "Generated with" lines, no session links).
- Don't push. For an external repository, record the e2e branch under the task in `plan.md`, e.g. `E2E: <repository> @ <branch>`, so `/review` can include it and `/task-deliver` can open its PR.

## 8. Report back

Tell the user which scenarios are covered (mapped to acceptance criteria), where the tests are, how many runs passed, and anything that couldn't be tested. Suggest `/review <KEY> <TASK-ID>` next.
