# Work workflow skills for Claude Code

A set of Claude Code skills that take any Jira issue (an epic, a story, a task, or a bug) from analysis to delivered PRs, with Claude doing the execution and you making the decisions and reviewing.

The same skills work for every issue type. The process scales to the work: an epic becomes a plan with several PR-sized tasks, while a single ticket usually becomes a one-task plan, and the plan critique becomes optional.

## The flow

### An epic (or any large issue)

```
PLAN        /work-analyze PROJ-100          → .claude/work/PROJ-100/analysis.md
            /work-plan PROJ-100             → plan.md (several tasks)
            /work-critique-plan PROJ-100    → plan-review.md   (fresh context)
            /work-plan PROJ-100             → plan.md v2       (applies the critique)

FOR EACH    /clear
TASK        /task-implement PROJ-100 T1     → branch, unit/integration tests, code, plan status
            /task-e2e PROJ-100 T1           → e2e tests, only when the plan calls for them
            /review PROJ-100 T1             → reviews/T1.md    (3 reviewers in parallel)
            /task-fix PROJ-100 T1           → fixes from the review
            /task-deliver PROJ-100 T1       → PR + Jira + plan  (manual only)
            /task-fix PROJ-100 T1 pr        → fixes from human PR comments
```

### A single story, task, or bug

```
/work-analyze PROJ-200      → for bugs: reproduction and root cause
/work-plan PROJ-200         → usually a single task, T1
/task-implement PROJ-200    → no task ID needed when there is only one
/task-e2e PROJ-200          (only when the plan calls for e2e tests)
/review PROJ-200
/task-fix PROJ-200          (if needed)
/task-deliver PROJ-200
```

`/work-plan` recommends `/work-critique-plan` only when the plan has more than two tasks or touches something risky (migrations, public APIs, auth, payments, shared code).

### Bugs

Bugs get extra care at every step:

- **Analyze:** reproduce the bug, find the root cause (not just where the error shows up), assess the impact, and look for the same pattern elsewhere.
- **Plan and implement:** a regression test that fails first, then the fix for the root cause.
- **Review:** checks that the fix addresses the cause rather than hiding the symptom, and that the regression test would fail without it.

The files in `.claude/work/<KEY>/` are the contract between skills: each step reads the previous step's output, so you can `/clear` between tasks (or come back days later) without losing context.

## Skills

| Skill | Phase | What it does | Invocation |
|-------|-------|--------------|------------|
| `work-analyze` | Plan | Checks that the issue is clear; lists open questions, risks, and what exists in the code. For bugs: reproduction and root cause | `/` or automatic |
| `work-plan` | Plan | Breaks the work into small, prioritized, PR-sized tasks with a test strategy each; applies the plan critique | `/` or automatic |
| `work-critique-plan` | Plan | Skeptical review of the plan in a separate subagent, so it doesn't grade its own work | `/` or automatic |
| `task-implement` | Implement | Implements one task: proposes first, writes tests first, stays in scope, updates the plan | `/` or automatic |
| `task-e2e` | Implement | Writes and runs e2e tests for a task, in this repository or in a separate e2e repository (set in CLAUDE.md) | `/` or automatic |
| `review` | Review | Runs the three reviews below in parallel and writes one consolidated report | `/` or automatic |
| `review-technical` | Review | Defects, tests, i18n, performance, best practices (uses `checklist.md`) | `/` or via `review` |
| `review-acceptance` | Review | Acceptance criteria coverage, plan adherence, and (for bugs) root-cause fix | `/` or via `review` |
| `review-security` | Review | Exploitable vulnerabilities (uses `checklist.md`) | `/` or via `review` |
| `task-fix` | Review | Triages and applies feedback from the review report or the PR | `/` or automatic |
| `task-deliver` | Deliver | Opens a short, clear PR (and the e2e PR, if tests live in another repository), updates Jira and the plan | Manual only |

## Setup

1. **Copy the skills** into your project's `.claude/skills/` folder:

   ```bash
   mkdir -p /path/to/your-repo/.claude/skills
   cp -r epic-workflow/skills/* /path/to/your-repo/.claude/skills/
   ```

   To use them in all your projects instead, copy them to `~/.claude/skills/`.
2. **Add the sections in `CLAUDE.md.snippet`** to your project's `CLAUDE.md` and fill them in (commands, branch and PR conventions, Jira statuses). If you don't have a `CLAUDE.md` yet, run `/init` first.
3. **Turn off Claude's attribution** in commits and PRs: copy `settings.json` to `.claude/settings.json` in your project (or merge its `attribution` block into the one you have). The skills also tell Claude not to add attribution, but the setting makes it reliable. To apply it to all your projects, put the block in `~/.claude/settings.json` instead.
4. **Fill in the `FILL IN` sections** in:
   - `.claude/skills/review-technical/checklist.md`
   - `.claude/skills/review-security/checklist.md`
5. **Connect Jira** to Claude Code through an MCP server (for example, Atlassian's). Without it, the skills ask you to paste the issues.
6. **Install and authenticate the GitHub CLI** (`gh auth login`) for `task-deliver` and `task-fix ... pr`.
7. **Decide what to commit:** commit `.claude/skills/` so the whole team shares the workflow. You may want to add `.claude/work/` to `.gitignore` if you don't want plans and reviews in the repository.

## E2E tests

`/task-e2e` writes e2e tests where your team keeps them, as set in the **E2E tests** section of `CLAUDE.md`:

- **`local`**: in a folder of this repository.
- **`external`**: in a separate, existing e2e repository. The skill uses your local clone, creates a branch with the same name as the application branch, follows that repository's own conventions, and runs the tests against your local app. `/review` includes that branch in the review, and `/task-deliver` opens its PR and links it to the application PR.

If the section is missing, the skill asks where the tests live instead of creating a new setup.

## Tips

- Run `/clear` between tasks. The plan file carries the context forward.
- Review every diff yourself. The review skills are a first pass, not a replacement for a human reviewer.
- When something the skills do keeps annoying you, edit the `SKILL.md`. These files are meant to evolve with your team.
- For checks that must always happen (lint or tests after every edit), use Claude Code hooks instead of relying on instructions.
- Independent tasks (see "Parallel work" in the plan) can run in separate Claude Code sessions using git worktrees.
