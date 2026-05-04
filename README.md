# Agent Skills

Public collection of agent skills for development workflows.

Each skill is a directory with:

- `SKILL.md` — agent-facing instructions with required frontmatter
- `README.md` — human-facing overview

## Skills

| Skill | Purpose | Docs |
|---|---|---|
| `write-a-prd` | Draft or refine product requirements documents. | [README](utils-skills/write-a-prd/README.md) |
| `prd-to-plan` | Convert a PRD into an implementation plan. | [README](utils-skills/prd-to-plan/README.md) |
| `do-work` | Execute an implementation plan in small validated steps. | [README](utils-skills/do-work/README.md) |
| `write-a-skill` | Create or improve an agent skill. | [README](utils-skills/write-a-skill/README.md) |
| `find-skills` | Discover useful skills in a repository. | [README](utils-skills/find-skills/README.md) |
| `grill-me` | Stress-test an idea, plan, PRD, or implementation. | [README](utils-skills/grill-me/README.md) |
| `handle-coderabbit` | Triage and apply CodeRabbit review comments. | [README](utils-skills/handle-coderabbit/README.md) |
| `bun-integration-tests` | Scaffold Bun integration test infrastructure for TypeScript apps. | [README](utils-skills/bun-integration-tests/README.md) |

## Install

Install one skill by path:

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/write-a-prd
```

Replace the path with the skill you want.

## Validate locally

```bash
for d in utils-skills/*/; do
  uvx --from skills-ref agentskills validate "${d%/}"
done
```
