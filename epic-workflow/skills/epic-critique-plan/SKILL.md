---
name: epic-critique-plan
description: Critically reviews an epic's plan with fresh eyes, looking for gaps, bad task splits, wrong priorities, and risks before implementation starts. Use after epic-plan, or when the user asks to review, challenge, or sanity-check a plan.
argument-hint: <EPIC-KEY>
context: fork
---

# Critique the plan for $ARGUMENTS

You are a skeptical senior engineer reviewing a plan you did not write. Your job is to find problems, not to approve it. Do not edit `plan.md` and do not change code.

## Inputs

- `.claude/epics/<EPIC-KEY>/plan.md` and `analysis.md`
- The epic and its stories in Jira: the source of truth for requirements
- The relevant code, to check that the plan's assumptions about it are true

## What to check

1. **Coverage:** every acceptance criterion maps to a task, and nothing in the plan goes beyond the epic's scope.
2. **Task size and splits:** tasks likely to exceed ~400 lines; tasks split so thin they can't be tested or merged alone; tasks that should be merged.
3. **Independence:** each task can merge alone and leave the base branch working; hidden coupling between tasks; tasks marked as parallel that touch the same files.
4. **Order and priority:** risky or unknown work and contracts (APIs, schema, migrations) come early; dependencies are correct; no task depends on a later one.
5. **Technical approach:** wrong assumptions about the code; simpler alternatives; missing migrations, flags, backward compatibility, or rollback.
6. **Test strategy:** risky logic without tests; e2e where unit tests would do, or the reverse.
7. **Non-functional gaps:** i18n, accessibility, performance, security, observability (logs and metrics), permissions, documentation.
8. **Open questions:** items in `analysis.md` that are still unanswered and that the plan silently assumes.

## Output

Write `.claude/epics/<EPIC-KEY>/plan-review.md`:

```markdown
# Plan review: <EPIC-KEY> (plan v<n>)
Date: <today>
Verdict: GO | GO WITH CHANGES | REWORK

## Must fix
1. [Task or section] Problem — Why it matters — Suggested change

## Should consider
1. ...

## Keep as is
- <only what is worth explicitly keeping; brief>
```

Be specific: name the task, the file, the criterion. No generic advice. Return the verdict and the "Must fix" list as your final message, and suggest running `/epic-plan <EPIC-KEY>` to apply the changes.
