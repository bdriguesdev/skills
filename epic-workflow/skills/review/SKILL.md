---
name: review
description: Runs a full review of the current changes (technical quality, acceptance criteria and plan, security) in parallel and writes one consolidated report. Use when the user asks to review a task, branch, or PR before delivering it.
argument-hint: <EPIC-KEY> [TASK-ID]
---

# Full review

Arguments: `$ARGUMENTS`, expected as `<EPIC-KEY> [TASK-ID]`. If no task ID is given, use the task marked `in-progress` in `.claude/epics/<EPIC-KEY>/plan.md`, and ask if there is more than one.

## 1. Define the scope

- The base branch comes from CLAUDE.md (default `main`). The changes to review are `git diff <base>...HEAD` plus any uncommitted changes.
- If there are no changes, stop and say so.

## 2. Run three reviews in parallel

Launch three subagents at the same time. Give each one the epic key, the task ID, and the base branch, and tell it to follow exactly one of these files. They sit next to this skill's folder, in the same skills directory (normally `.claude/skills/`):

- `review-technical/SKILL.md`
- `review-acceptance/SKILL.md`
- `review-security/SKILL.md`

Pass each subagent the full path of its file.

Each one returns its findings in the format defined in those files. Don't review the code yourself while they run.

## 3. Consolidate

- Merge the findings, remove duplicates, and sort by severity: `blocker`, `major`, `minor`, `nit`.
- Quickly check each blocker and major against the code. Drop findings that are clearly wrong or not actionable.
- Write `.claude/epics/<EPIC-KEY>/reviews/<TASK-ID>.md`:

```markdown
# Review: <EPIC-KEY> <TASK-ID>
Date: <today>
Verdict: READY | READY AFTER FIXES | NOT READY
Acceptance criteria: <covered>/<total>

## Findings
| # | Severity | Area | Location | Finding | Suggested fix | Status |
|---|----------|------|----------|---------|---------------|--------|

## Acceptance criteria
| Criterion | Covered / Partial / Missing | Evidence (test or code) |
|-----------|-----------------------------|-------------------------|

## Plan deviations
- ...
```

Verdict rules: any blocker or missing criterion means NOT READY; any major or partial criterion means READY AFTER FIXES.

## 4. Report back

Give the verdict, the blockers and majors, and the criteria not fully covered. Suggest `/task-fix <EPIC-KEY> <TASK-ID>` if anything needs fixing, or `/task-deliver <EPIC-KEY> <TASK-ID>` if it is ready.
