# prd-to-plan

## What it is

A planning skill that turns a **PRD into a multi-phase implementation plan** using **tracer-bullet vertical slices** (thin end-to-end slices through all layers), written as Markdown under `./plans/`.

## When to use it

Use when you have (or will paste) a PRD and want:

- Phased rollout with demoable slices
- Architectural decisions hoisted to a shared header so every phase stays consistent
- User review of granularity before the plan file is written

**When not to use it:** When there is no PRD yet—consider [write-a-prd](../write-a-prd/README.md) first.

## What the agent will do

Confirm PRD context, explore the codebase if needed, extract durable architectural decisions, propose phases as vertical slices, **quiz you** on merge/split/granularity, then write `./plans/<feature>.md` using the template in [SKILL.md](SKILL.md).

## Prerequisites

- PRD in conversation or path to the file
- Write access to create `./plans/` in the target repository

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/prd-to-plan
```

## Example prompts

```text
Here's the PRD—break it into tracer-bullet phases and save the plan under ./plans/billing.md.
```

```text
Turn this PRD into a phased plan; ask me if the slice sizes feel right before you write the file.
```

```text
prd-to-plan: vertical slices only, no horizontal "just the API" phases.
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Process, vertical-slice rules, plan template |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep plan template and “no file-level detail in early phases” rules in sync with how your team plans.
