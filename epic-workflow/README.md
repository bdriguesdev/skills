# Epic workflow skills for Claude Code

A set of Claude Code skills that take a Jira epic from analysis to delivered PRs, with Claude doing the execution and you making the decisions and reviewing.

## The flow

```
PLAN        /epic-analyze PROJ-100        → .claude/epics/PROJ-100/analysis.md
            /epic-plan PROJ-100           → plan.md
            /epic-critique-plan PROJ-100  → plan-review.md   (fresh context)
            /epic-plan PROJ-100           → plan.md v2       (applies the critique)

FOR EACH    /clear
TASK        /task-implement PROJ-100 T1   → branch, tests, code, plan status
            /review PROJ-100 T1           → reviews/T1.md    (3 reviewers in parallel)
            /task-fix PROJ-100 T1         → fixes from the review
            /task-deliver PROJ-100 T1     → PR + Jira + plan  (manual only)
            /task-fix PROJ-100 T1 pr      → fixes from human PR comments
```

The files in `.claude/epics/<EPIC-KEY>/` are the contract between skills: each step reads the previous step's output, so you can `/clear` between tasks (or come back days later) without losing context.

## Skills

| Skill | Phase | What it does | Invocation |
|-------|-------|--------------|------------|
| `epic-analyze` | Plan | Checks that the epic is clear; lists open questions, risks, and what exists in the code | `/` or automatic |
| `epic-plan` | Plan | Breaks the epic into small, prioritized, PR-sized tasks with a test strategy each; applies the plan critique | `/` or automatic |
| `epic-critique-plan` | Plan | Skeptical review of the plan in a separate subagent, so it doesn't grade its own work | `/` or automatic |
| `task-implement` | Implement | Implements one task: proposes first, writes tests first, stays in scope, updates the plan | `/` or automatic |
| `e2e-tests` | Implement | Project conventions for e2e tests, loaded when Claude writes them | Automatic only |
| `review` | Review | Runs the three reviews below in parallel and writes one consolidated report | `/` or automatic |
| `review-technical` | Review | Defects, tests, i18n, performance, best practices (uses `checklist.md`) | `/` or via `review` |
| `review-acceptance` | Review | Acceptance criteria coverage and plan adherence | `/` or via `review` |
| `review-security` | Review | Exploitable vulnerabilities (uses `checklist.md`) | `/` or via `review` |
| `task-fix` | Review | Triages and applies feedback from the review report or the PR | `/` or automatic |
| `task-deliver` | Deliver | Opens the PR, updates Jira and the plan | Manual only |

## Setup

1. **Copy the skills** into your project's `.claude/skills/` folder:

   ```bash
   mkdir -p /path/to/your-repo/.claude/skills
   cp -r epic-workflow/skills/* /path/to/your-repo/.claude/skills/
   ```

   To use them in all your projects instead, copy them to `~/.claude/skills/`.
2. **Add the sections in `CLAUDE.md.snippet`** to your project's `CLAUDE.md` and fill them in (commands, branch and PR conventions, Jira statuses). If you don't have a `CLAUDE.md` yet, run `/init` first.
3. **Fill in the `FILL IN` sections** in:
   - `.claude/skills/e2e-tests/SKILL.md`
   - `.claude/skills/review-technical/checklist.md`
   - `.claude/skills/review-security/checklist.md`
4. **Connect Jira** to Claude Code through an MCP server (for example, Atlassian's). Without it, the skills ask you to paste the issues.
5. **Install and authenticate the GitHub CLI** (`gh auth login`) for `task-deliver` and `task-fix ... pr`.
6. **Decide what to commit:** commit `.claude/skills/` so the whole team shares the workflow. You may want to add `.claude/epics/` to `.gitignore` if you don't want plans and reviews in the repository.

## Tips

- Run `/clear` between tasks. The plan file carries the context forward.
- Review every diff yourself. The review skills are a first pass, not a replacement for a human reviewer.
- When something the skills do keeps annoying you, edit the `SKILL.md`. These files are meant to evolve with your team.
- For checks that must always happen (lint or tests after every edit), use Claude Code hooks instead of relying on instructions.
- Independent tasks (see "Parallel work" in the plan) can run in separate Claude Code sessions using git worktrees.
