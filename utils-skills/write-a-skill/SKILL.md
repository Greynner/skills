---
name: write-a-skill
description: Use when the user wants to create, edit, validate, or publish an agent skill.
---

# write-a-skill

Help create high-quality agent skills.

## Skill requirements

A skill directory should include:

- `SKILL.md` with YAML frontmatter containing `name` and `description`
- `README.md` for humans
- optional `references/`, `assets/`, or `scripts/` only when they add clear value

## Authoring guidance

- Make the description trigger-oriented: describe when the skill should be used.
- Put the most important instructions in `SKILL.md`.
- Keep instructions actionable and specific.
- Move long examples or reference material into `references/`.
- Avoid private information, secrets, or project-specific assumptions unless the skill is intentionally private.

## Validation

Recommend validating with:

```bash
uvx --from skills-ref agentskills validate <skill-path>
```
