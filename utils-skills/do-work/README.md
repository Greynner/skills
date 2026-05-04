# do-work

## What it is

An execution skill for completing a **single unit of work end-to-end**: understand context, plan if needed, implement (TDD-style for backend, direct implementation for frontend as specified), run checks, and commit.

## When to use it

Use when you want the agent to **ship a scoped change**—feature slice, bugfix, or plan phase—with validation and a commit, not just a suggestion.

**When not to use it:** When you are only exploring or documenting (use planning or PRD skills first). When the repo uses different test/typecheck commands—override the examples in [SKILL.md](SKILL.md) for your project.

## What the agent will do

Read plans/PRDs if present, explore the codebase, optionally draft a short plan, implement with backend tracer-bullet TDD steps, run typecheck and tests, then commit when green. Full steps are in [SKILL.md](SKILL.md).

## Prerequisites

- Clear task or pointer to a plan issue/section
- **Adapt validation commands**—examples use `bun run typecheck` and `bun run test`

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/do-work
```

## Example prompts

```text
Implement phase 2 from plans/user-onboarding.md: run typecheck and tests, then commit.
```

```text
Fix the bug where empty search crashes the results page—TDD on the backend path, then commit.
```

```text
Do the work: add the feature flag and wire it through API and UI, validate, commit.
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Workflow: understand → implement → validate → commit |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep TDD vs frontend guidance in [SKILL.md](SKILL.md) aligned with team standards.
