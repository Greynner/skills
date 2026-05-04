# find-skills

## What it is

A utility skill that teaches the agent how to discover, vet, and install capabilities from the open [skills.sh](https://skills.sh/) ecosystem using the Skills CLI (`npx skills`). It emphasizes quality signals (install counts, source reputation) before recommending anything.

## When to use it

Use when you want the agent to:

- Search for an existing skill instead of improvising a large workflow from scratch
- Explain how to install or update skills globally or per project
- Compare options from the leaderboard or `npx skills find`

**When not to use it:** When the task is clearly one-off, repo-specific, or already covered by a skill you have installed. When the user explicitly wants custom instructions only, not ecosystem packages.

## What the agent will do

Follow a structured flow: infer domain and task, check the public leaderboard, run targeted CLI searches, verify quality, then present install commands and links. See [SKILL.md](SKILL.md) for the full procedure and example responses.

## Prerequisites

- Node.js and network access for `npx skills`
- Optional: familiarity with [skills.sh](https://skills.sh/) for browsing

## How to install

From this repository (adjust URL if you use a fork):

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/find-skills
```

## Example prompts

```text
Find a skill that helps with Playwright E2E tests and show me how to install it.
```

```text
I keep doing the same changelog work—is there a skill for that on skills.sh?
```

```text
How do I search the skills ecosystem for Next.js performance guidance?
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Agent instructions, CLI commands, quality checklist |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep CLI examples aligned with current `npx skills` behavior.
- Bump or extend [SKILL.md](SKILL.md) frontmatter `description` if triggers change.
