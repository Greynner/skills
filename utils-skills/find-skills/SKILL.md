---
name: find-skills
description: Use when the user wants to discover, inventory, or choose agent skills from a repository.
---

# find-skills

Find available skills and recommend which ones fit the user's task.

## Workflow

1. Search for `SKILL.md` files.
2. Read frontmatter names and descriptions.
3. Group skills by purpose.
4. Recommend one or more skills for the user's goal.
5. If installing, provide exact `npx skills add` commands.

## Output

Include:

- skill name
- path
- purpose
- when to use it
- install command when relevant

Do not assume a skill exists; inspect the repository first when possible.
