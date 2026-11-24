---
name: review-acceptance
description: Checks whether the current changes fully meet the Jira acceptance criteria and follow the epic plan for the task. Use when the user asks if a task is done, meets its requirements, or matches the plan.
argument-hint: <EPIC-KEY> [TASK-ID]
context: fork
---

# Acceptance criteria and plan review

Arguments: `$ARGUMENTS`, expected as `<EPIC-KEY> [TASK-ID]`. If no task ID is given, use the task marked `in-progress` in the plan.

Report only. Do not change code.

## Inputs

- The task in `.claude/epics/<EPIC-KEY>/plan.md`: goal, scope, criteria covered, test strategy, and "done when" conditions.
- The linked Jira stories: the source of truth for acceptance criteria.
- The changes between the base branch (from CLAUDE.md, default `main`) and HEAD, plus uncommitted changes, including the tests.

## What to check

1. **Each acceptance criterion the task covers.** Is it implemented? Is there a test that proves it? Mark it covered, partial, or missing, with evidence (file:line or test name). Read each criterion literally and for intent, and flag code that meets the letter but not the intent.
2. **Plan adherence.** Did the changes stay within the task's scope? Were the planned approach and test strategy followed? Look for unplanned changes (scope creep) and planned items that are missing.
3. **"Done when" conditions** from the plan: are they all met?
4. **Behavior a reviewer or QA would try right away:** empty states, errors, permissions, and edge inputs mentioned in the story.

## Output

1. A criteria table:

```
| Criterion | Covered / Partial / Missing | Evidence |
```

2. Plan deviations: what changed, and whether it seems justified.
3. Findings, most severe first:

```
- [severity] path/to/file:line — short title
  area: acceptance
  finding: what is wrong and why it matters
  fix: concrete suggestion
```

A missing criterion is a `blocker`; a partial one is a `major`. Other severities: `minor`, `nit`.
