---
name: epic-analyze
description: Analyzes a Jira epic and its child stories against the codebase to check that the work is clear and ready to plan. Use when the user wants to start an epic, understand an epic, or find gaps and open questions before planning.
argument-hint: <EPIC-KEY>
---

# Analyze epic $ARGUMENTS

Goal: decide whether this epic is clear enough to plan, and surface everything that must be answered first. Do not write a plan and do not change code in this step.

If no epic key was given, ask for one. If `.claude/epics/<EPIC-KEY>/analysis.md` already exists, update it (for example, record answers to open questions) instead of starting over.

## 1. Gather the requirements

- Use the Jira tools to read the epic: description, acceptance criteria, links, attachments, comments, and every child story with its own acceptance criteria and comments.
- If Jira tools are not available, stop and ask the user to paste the epic and its stories.

## 2. Map it to the code

- Explore the areas of the codebase this epic touches. For broad exploration, use Explore subagents so only conclusions come back into this conversation.
- For each story, identify what already exists, what must be built or changed, and the main files and modules involved.
- Note constraints you find: shared modules, public APIs, database schema, feature flags, i18n, permissions, code owned by other teams.

## 3. Assess clarity

Check each story for:

- Acceptance criteria that are missing, vague, untestable, or contradictory
- Undefined behavior: errors, empty states, edge cases, permissions, limits, copy and translations, analytics
- Conflicts between stories, or between a story and how the code behaves today
- Hidden dependencies: other teams, external services, migrations, design assets
- Scope that looks larger than the epic suggests

## 4. Write the analysis

Create `.claude/epics/<EPIC-KEY>/analysis.md`:

```markdown
# Epic analysis: <EPIC-KEY> — <title>
Date: <today>
Readiness: READY | READY WITH ASSUMPTIONS | BLOCKED

## Summary
<2–4 sentences: what the epic delivers and the overall technical approach>

## Stories
### <STORY-KEY> — <title>
- Exists today: ...
- To build or change: ...
- Main areas: `path/...`
- Clarity: OK | Issues: ...

## Open questions
| # | Question | Who answers (PO / Design / Team X) | Blocks | Proposed default |
|---|----------|------------------------------------|--------|------------------|

## Assumptions
<what will be assumed if questions stay unanswered>

## Risks and dependencies
- ...
```

## 5. Report back

Tell the user the readiness verdict, the open questions (blocking ones first), and the top risks. Keep it short; the details live in the file. Suggest running `/epic-plan <EPIC-KEY>` once the blocking questions are answered.
