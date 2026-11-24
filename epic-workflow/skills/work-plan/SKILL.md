---
name: work-plan
description: Builds or revises the execution plan for a Jira issue (epic, story, task, or bug), breaking it into small, prioritized tasks that each fit one reviewable PR, with a test strategy per task. Use after work-analyze, or when the user asks to plan, prioritize, split, or re-plan work.
argument-hint: <ISSUE-KEY>
---

# Plan $ARGUMENTS

Do not change code in this step.

## Inputs

- `.claude/work/<KEY>/analysis.md`. If it is missing, tell the user to run `/work-analyze <KEY>` first.
- The issue (and, for an epic, its child stories) in Jira. Re-read the acceptance criteria and check the comments for answers to open questions.
- `.claude/work/<KEY>/plan-review.md`, if it exists and is newer than the plan. Then this is a revision: address every point in it, and record in the changelog how each one was handled (accepted, or rejected with the reason).
- Project conventions from CLAUDE.md.

## Size the plan to the work

- **Epic:** usually several tasks.
- **Story or task:** usually a single task, `T1`. Split it only if it would exceed the size rule below.
- **Bug:** usually a single task. Split only when the fix is large, or when a data fix or migration must ship separately from the code fix.

Don't add process the work doesn't need. A one-task plan is fine and expected for most tickets; keep it short.

## Rules for tasks

1. **One task = one PR.** Each task must be mergeable on its own and leave the base branch working. Use feature flags or dark code when needed.
2. **Small.** Aim for fewer than ~400 changed lines per task, not counting generated files and snapshots. If a task is bigger, split it. Good places to split: schema or migration first, refactor before feature, backend before frontend, read path before write path, behind a flag before enabling.
3. **Don't split for its own sake.** Split only when it makes review easier, lowers risk, or unblocks parallel work. If splitting creates pieces that can't be tested on their own, keep them together.
4. **Traceable.** Each task lists the Jira issues and acceptance criteria it covers. Every acceptance criterion must be covered by at least one task.
5. **Test strategy per task.** Choose unit, integration, and/or e2e for each task, with a one-line justification. Recommend e2e only when the task changes a critical user flow, crosses several layers or services, or has behavior that unit and integration tests can't catch.
6. **Bugs: regression test first.** The task starts with a test that reproduces the bug and fails, then the fix makes it pass. The fix addresses the root cause from the analysis. If the same pattern exists elsewhere, either include those spots or list them as follow-ups.
7. **Ordered by priority.** First what unblocks other work and reduces risk (unknowns, migrations, API contracts), then the core value, then polish.

## Write the plan

Create or update `.claude/work/<KEY>/plan.md`:

```markdown
# Plan: <KEY> — <title>
Type: Epic | Story | Task | Bug
Version: <n> · Updated: <date>

## Approach
<the technical approach and key decisions, briefly. For bugs: the root cause and how the fix addresses it>

## Tasks
| ID | Title | Issues | Priority | Depends on | Size | Tests | Status | PR |
|----|-------|--------|----------|------------|------|-------|--------|----|
| T1 | ...   | PROJ-12 | P1      | —          | S    | unit  | todo   |    |

### T1 — <title>
- Covers: PROJ-12 (AC1, AC2)
- Goal: ...
- Scope: files and modules in scope. Out of scope: ...
- Approach: ...
- Tests: unit | integration | e2e — <one-line justification>. Key cases: ...
- Done when: <verifiable conditions>
- Risks and notes: ...

## Acceptance criteria coverage
| Issue | Criterion | Task(s) |
|-------|-----------|---------|

## Parallel work
<only if there is more than one task: which tasks can be done at the same time without touching the same files>

## Changelog
- v1: initial plan
```

- Status values: `todo`, `in-progress`, `in-review`, `done`, `blocked`.
- Size: S (under 150 lines), M (150–400), L (over 400; must be justified or split).

## Report back

Summarize the number of tasks, their order, anything left uncovered, and the assumptions made. Then recommend the next step:

- Recommend `/work-critique-plan <KEY>` when the plan has **more than two tasks**, or touches something risky: migrations or data fixes, public APIs, authentication or permissions, payments, or code shared across teams.
- Otherwise, say the critique is optional and suggest `/task-implement <KEY>` directly.
