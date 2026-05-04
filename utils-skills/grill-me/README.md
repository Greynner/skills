# grill-me

## What it is

A facilitation skill that drives **structured, exhaustive design review** by interviewing the user one question at a time until decisions and dependencies are resolved. The agent is instructed to give a recommended answer per question and to prefer codebase exploration over guessing when facts live in the repo.

## When to use it

Use when you want to:

- Stress-test an architecture or product plan before building
- Surface hidden assumptions and ordering constraints between decisions
- Pair with another skill that produces a PRD or plan (this skill focuses on *questioning*, not on writing the final doc—unless you ask for that next)

**When not to use it:** When you need a fast gut check only, or when requirements are already frozen and you only want execution (consider [do-work](../do-work/README.md) instead).

## What the agent will do

Ask **one question at a time**, walk branches of the design tree, and resolve dependencies between decisions. If a question can be answered from the repository, the agent should inspect the code instead of asking you to speculate. Full wording is in [SKILL.md](SKILL.md).

## Prerequisites

- A concrete topic or plan to grill (even a rough outline is enough to start)
- Access to the relevant codebase if questions should be grounded in implementation

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/grill-me
```

## Example prompts

```text
Grill me on this API design until we're aligned on error handling and versioning.
```

```text
Interview me relentlessly about the migration plan—one question at a time, with your recommendation each time.
```

```text
Stress-test my auth model: ask one question at a time until every branch is resolved.
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Core agent behavior (single-question loop, explore-codebase rule) |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep [SKILL.md](SKILL.md) triggers (`description` field) aligned with how users invoke the skill.
