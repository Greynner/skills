# write-a-prd

## What it is

A product skill that produces a **written PRD** in the repository by combining: your problem description, **codebase exploration** to ground facts, **relentless structured interviewing** (design-tree style), and a fixed markdown template (problem, solution, extensive user stories, implementation decisions, scope, notes).

## When to use it

Use when you need a **durable requirements document** before implementation—especially when trade-offs and scope need to be nailed down with you in the loop.

**When not to use it:** When you already have a PRD and only need an implementation plan—use [prd-to-plan](../prd-to-plan/README.md).

## What the agent will do

Steps in [SKILL.md](SKILL.md): collect a detailed problem statement and ideas, verify against the repo, interview until shared understanding, then write `plans/prd-name.md` using the `<prd-template>` (user stories are intentionally long and numbered; implementation decisions avoid brittle file paths and code snippets).

## Prerequisites

- Willingness to answer follow-up questions (or delegate “grill me” intensity to this flow)
- A `plans/` directory or permission to create it for the output file

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/write-a-prd
```

## Example prompts

```text
I want a PRD for team shared snippets: interview me and then write plans/team-snippets-prd.md.
```

```text
Create a PRD from this rough idea—explore the repo first, then ask me one topic at a time until we're aligned.
```

```text
write-a-prd: problem is X, solution direction Y—fill the template in plans/feature-x.md.
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Step-by-step process and PRD template |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep output path conventions (`plans/…`) consistent with your org; update [SKILL.md](SKILL.md) if you standardize on another folder.
