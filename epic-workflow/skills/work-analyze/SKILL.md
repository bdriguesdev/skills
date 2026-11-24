---
name: work-analyze
description: Analyzes a Jira issue (epic, story, task, or bug) against the codebase to check that the work is clear and ready to plan; for bugs, also reproduces and finds the root cause. Use when the user wants to start work on an issue, understand it, or find gaps and open questions before planning.
argument-hint: <ISSUE-KEY>
---

# Analyze $ARGUMENTS

Goal: decide whether this work is clear enough to plan, and surface everything that must be answered first. Do not write a plan and do not fix anything in this step.

If no issue key was given, ask for one. If `.claude/work/<KEY>/analysis.md` already exists, update it (for example, record answers to open questions) instead of starting over.

## 1. Gather the requirements

Use the Jira tools to read the issue and find its type. If Jira tools are not available, stop and ask the user to paste the issue (and its child issues, for an epic).

- **Epic:** read the epic (description, acceptance criteria, links, attachments, comments) and every child story with its own acceptance criteria and comments.
- **Story or task:** read the issue, its sub-tasks, and its linked issues (blocks, is blocked by, relates to).
- **Bug:** read the issue, its linked issues, and everything that helps reproduce it: steps, expected and actual behavior, environment, logs, screenshots, affected versions.

## 2. Map it to the code

- Explore the areas of the codebase this work touches. For broad exploration, use Explore subagents so only conclusions come back into this conversation.
- For each story (or for the single issue), identify what already exists, what must be built or changed, and the main files and modules involved.
- Note constraints you find: shared modules, public APIs, database schema, feature flags, i18n, permissions, code owned by other teams.

### Bugs only: reproduce and find the root cause

- Try to reproduce the bug: with an existing or new failing test if possible, otherwise by tracing the code path with the reported inputs. Don't change production code.
- Find the **root cause**, not just the line where the error appears. Explain why the code behaves this way.
- Assess the impact: who is affected, since when (check recent changes with `git log` on the relevant files), and whether data may already be corrupted or need fixing.
- Look for the same pattern elsewhere in the code: the same bug may exist in other places.
- If you can't reproduce it or find the cause with confidence, say so clearly and list what information is missing.

## 3. Assess clarity

Check each story or issue for:

- Acceptance criteria that are missing, vague, untestable, or contradictory. For bugs: is the expected behavior clear?
- Undefined behavior: errors, empty states, edge cases, permissions, limits, copy and translations, analytics
- Conflicts between stories, or between the issue and how the code behaves today
- Hidden dependencies: other teams, external services, migrations, design assets
- Scope that looks larger than the issue suggests. If a single story or task is really several PRs of work, say so.

## 4. Write the analysis

Create `.claude/work/<KEY>/analysis.md`:

```markdown
# Analysis: <KEY> — <title>
Type: Epic | Story | Task | Bug
Date: <today>
Readiness: READY | READY WITH ASSUMPTIONS | BLOCKED

## Summary
<2–4 sentences: what this work delivers (or fixes) and the overall technical approach>

## Bug diagnosis  (bugs only)
- Reproduced: yes (how) | no (why not)
- Root cause: ...
- Impact: ...
- Same pattern elsewhere: ...

## Scope
### <STORY-KEY or KEY> — <title>
- Exists today: ...
- To build or change: ...
- Main areas: `path/...`
- Clarity: OK | Issues: ...

## Open questions
| # | Question | Issue | Who answers (PO / Design / Team X) | Blocks | Proposed default | Status |
|---|----------|-------|------------------------------------|--------|------------------|--------|

## Assumptions
<what will be assumed if questions stay unanswered>

## Risks and dependencies
- ...
```

For a story, task, or bug, the Scope section has a single entry.

## 5. Report back

Tell the user the readiness verdict, the open questions (blocking ones first), and the top risks. For bugs, lead with the root cause. Keep it short; the details live in the file.

## 6. Offer to ask the open questions in Jira

If there are open questions, offer to draft Jira comments that ask them. If the user accepts:

1. **Group the questions by issue.** A question about one story goes on that story; questions about the epic as a whole, or that span several stories, go on the epic. For a single ticket, everything goes on that ticket.
2. **Draft one comment per issue**, ready to paste:
   - Write it in the language the issue itself is written in.
   - Open with one line of context, e.g. "While analyzing this before implementation, a few points need clarification:".
   - Number the questions, blocking ones first, and mark them as blocking.
   - Make each question self-contained and easy to answer: say what is unclear, and offer options or the proposed default from the analysis when there is one (e.g. "Should an empty list show X or Y? If we don't hear back, we'll go with X.").
   - Keep it short and polite. No mention of AI, Claude, or internal file names.
   - Suggest who to mention (the reporter, the assignee, or the person in the "Who answers" column), but leave the mention for the user to confirm.
3. **Show the drafts to the user** and let them edit.
4. **Post only with explicit confirmation.** If the user confirms and Jira tools are available, post each comment on its issue and report the links. Otherwise, leave the drafts for the user to paste.
5. In `analysis.md`, mark each question as `asked in <ISSUE-KEY> on <date>`.

When the answers come in, running `/work-analyze <KEY>` again updates the analysis with them.

Suggest running `/work-plan <KEY>` once the blocking questions are answered. If only non-blocking questions remain, planning can start using the proposed defaults.
