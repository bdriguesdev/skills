---
name: review-technical
description: Reviews code changes for defects, test quality, maintainability, performance, i18n, and project best practices, using the team checklist. Use when the user asks for a technical or code-quality review.
argument-hint: [base-branch]
context: fork
---

# Technical review

Review the changes between the base branch (the argument, or the one in CLAUDE.md, default `main`) and HEAD, plus any uncommitted changes. Read the surrounding code to judge the changes in context; don't review the changed lines in isolation.

Report only. Do not change code.

## What to look for, in order

1. **Defects:** logic errors, wrong conditions, off-by-one errors, null or undefined handling, race conditions, unhandled errors or promise rejections, resource leaks, broken edge cases, regressions in callers of the changed code.
2. **Tests:** do they really test the behavior? Missing edge and error cases; tests that would still pass if the code were wrong.
3. **Everything in [checklist.md](checklist.md).**

## Rules

- Real problems before style. Don't report what the linter or formatter already enforces.
- Every finding needs a location and a concrete fix.
- If you are not sure something is a bug, say what should be verified instead of guessing.

## Output

Return your findings, most severe first, in this format:

```
- [severity] path/to/file:line — short title
  area: technical
  finding: what is wrong and why it matters
  fix: concrete suggestion
```

Severities:
- `blocker`: a bug, or something that breaks existing behavior
- `major`: a likely bug or a significant quality problem
- `minor`: worth fixing, low risk
- `nit`: optional polish

If you find nothing, say so explicitly and list what you checked.
