---
name: review-security
description: Reviews code changes for security vulnerabilities such as injection, broken access control, exposed secrets, and unsafe data handling. Use when the user asks for a security review of changes, a branch, or a PR.
argument-hint: [base-branch]
context: fork
---

# Security review

Review the changes between the base branch (the argument, or the one in CLAUDE.md, default `main`) and HEAD, plus any uncommitted changes. Read the related code (routes, middleware, data access, configuration) to understand how data flows into and out of the changed code.

Report only. Do not change code.

## How to review

- Focus on what is **exploitable in this context**, not on theoretical issues.
- For each finding, describe the attack: who can do what, through which input, with what impact.
- Go through [checklist.md](checklist.md), but don't limit yourself to it.

## Output

Return your findings, most severe first:

```
- [severity] path/to/file:line — short title
  area: security
  finding: the vulnerability and the attack scenario
  fix: concrete suggestion
```

Severities:
- `blocker`: exploitable, with real impact
- `major`: likely exploitable, or an important control is missing
- `minor`: defense in depth
- `nit`: hardening suggestion

If you find nothing, say so explicitly and list what you checked.
