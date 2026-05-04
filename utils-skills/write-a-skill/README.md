# write-a-skill

## What it is

A meta-skill for **authoring new agent skills**: structure, progressive disclosure (split files when large), when to add scripts, and how to write a strong `description` (the main routing signal for agents).

## When to use it

Use when you want to:

- Create a new `SKILL.md` (and optional references or scripts) for your team or the ecosystem
- Improve an existing skill’s discoverability or maintainability
- Decide whether content belongs in one file or multiple

**When not to use it:** When you only need to *find* an existing skill—use [find-skills](../find-skills/README.md) instead.

## What the agent will do

Gather requirements, draft structure (`SKILL.md`, optional `REFERENCE.md` / `EXAMPLES.md` / `scripts/`), then review with you against a checklist (description triggers, length, terminology). Details and templates live in [SKILL.md](SKILL.md).

## Prerequisites

- Clear idea of the task domain and example user requests (or willingness to iterate)
- Optional: repository conventions for where skills are stored

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/write-a-skill
```

## Example prompts

```text
Help me write a new skill for a team deploy checklist with triggers for "release" and "production deploy".
```

```text
My SKILL.md is 400 lines—how should I split it using progressive disclosure?
```

```text
Review this skill description: is it clear enough for an agent to pick this skill over others?
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Process, structure diagram, template, checklist |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Align guidance with current Agent Skills / `skills.sh` conventions as they evolve.
