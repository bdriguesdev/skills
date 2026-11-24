---
name: epic-plan
description: Builds or revises the execution plan for a Jira epic, breaking it into small, prioritized tasks that each fit one reviewable PR, with a test strategy per task. Use after epic-analyze, or when the user asks to plan, prioritize, split, or re-plan an epic's work.
argument-hint: <EPIC-KEY>
---

# Plan epic $ARGUMENTS

Do not change code in this step.

## Inputs

- `.claude/epics/<EPIC-KEY>/analysis.md`. If it is missing, tell the user to run `/epic-analyze <EPIC-KEY>` first.
- The epic and its stories in Jira. Re-read the acceptance criteria and check the comments for answers to open questions.
- `.claude/epics/<EPIC-KEY>/plan-review.md`, if it exists and is newer than the plan. Then this is a revision: address every point in it, and record in the changelog how each one was handled (accepted, or rejected with the reason).
- Project conventions from CLAUDE.md.

## Rules for tasks

1. **One task = one PR.** Each task must be mergeable on its own and leave the base branch working. Use feature flags or dark code when needed.
2. **Small.** Aim for fewer than ~400 changed lines per task, not counting generated files and snapshots. If a task is bigger, split it. Good places to split: schema or migration first, refactor before feature, backend before frontend, read path before write path, behind a flag before enabling.
3. **Don't split for its own sake.** Split only when it makes review easier, lowers risk, or unblocks parallel work. If splitting creates pieces that can't be tested on their own, keep them together.
4. **Traceable.** Each task lists the Jira stories and acceptance criteria it covers. Every acceptance criterion in the epic must be covered by at least one task.
5. **Test strategy per task.** Choose unit, integration, and/or e2e for each task, with a one-line justification. Recommend e2e only when the task changes a critical user flow, crosses several layers or services, or has behavior that unit and integration tests can't catch.
6. **Ordered by priority.** First what unblocks other work and reduces risk (unknowns, migrations, API contracts), then the core value, then polish.

## Write the plan

Create or update `.claude/epics/<EPIC-KEY>/plan.md`:

```markdown
# Plan: <EPIC-KEY> — <title>
Version: <n> · Updated: <date>

## Approach
<the technical approach and key decisions, briefly>

## Tasks
| ID | Title | Stories | Priority | Depends on | Size | Tests | Status | PR |
|----|-------|---------|----------|------------|------|-------|--------|----|
| T1 | ...   | PROJ-12 | P1       | —          | S    | unit  | todo   |    |

### T1 — <title>
- Covers: PROJ-12 (AC1, AC2)
- Goal: ...
- Scope: files and modules in scope. Out of scope: ...
- Approach: ...
- Tests: unit | integration | e2e — <one-line justification>. Key cases: ...
- Done when: <verifiable conditions>
- Risks and notes: ...

## Acceptance criteria coverage
| Story | Criterion | Task(s) |
|-------|-----------|---------|

## Parallel work
<which tasks can be done at the same time without touching the same files>

## Changelog
- v1: initial plan
```

- Status values: `todo`, `in-progress`, `in-review`, `done`, `blocked`.
- Size: S (under 150 lines), M (150–400), L (over 400; must be justified or split).

## Report back

Summarize the number of tasks, their order, anything left uncovered, and the assumptions made. Recommend running `/epic-critique-plan <EPIC-KEY>` before implementing.
