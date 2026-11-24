# Skills

A collection of [Claude Code](https://code.claude.com/docs/en/overview) skills.

## Skill sets

| Set | What it does |
|-----|--------------|
| [epic-workflow](epic-workflow/) | Takes any Jira issue (epic, story, task, or bug) from analysis to delivered PRs: analyze, plan, critique the plan, implement task by task, review (technical, acceptance criteria, security), fix, and deliver. Bugs get reproduction, root-cause analysis, and regression tests. |

## Installing a skill set

Each set has a `skills/` folder. Copy its contents into one of these places:

- `<your-repo>/.claude/skills/`: available in that repository (commit it to share with your team)
- `~/.claude/skills/`: available in all your projects on this machine

```bash
git clone https://github.com/bdriguesdev/skills.git
mkdir -p /path/to/your-repo/.claude/skills
cp -r skills/epic-workflow/skills/* /path/to/your-repo/.claude/skills/
```

Then open Claude Code in the repository and type `/` to see the new skills. Each set's own README covers its setup.
