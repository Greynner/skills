# AGENTS

## Repo shape
- This repo is a skill catalog, not an app: each skill lives in `utils-skills/<skill-name>/` with exactly `SKILL.md` (agent instructions + frontmatter) and `README.md` (human overview).
- Current canonical skill list is the 8 directories under `utils-skills/`; keep names consistent across directory name, `SKILL.md` frontmatter `name`, root `README.md` table, and CI matrix.

## Source of truth and validation
- Treat `uvx --from skills-ref agentskills validate <skill-dir>` as the spec validator for skills (this is what CI runs).
- Validate all skills locally with:
```bash
for d in utils-skills/*/; do
  uvx --from skills-ref agentskills validate "${d%/}"
done
```
- CI workflow is `.github/workflows/validate-skills.yml` and validates only explicitly listed skills; when adding/renaming/removing a skill, update the matrix there or CI coverage will drift.

## High-signal edit rules
- Prefer editing `SKILL.md` and `README.md` together inside a skill so agent-facing behavior and human docs stay aligned.
- If you add a new skill, also add it to the root `README.md` Skills table and to `.github/workflows/validate-skills.yml` matrix in the same change.
- Root `README.md` includes install commands by skill path; keep paths exact (`utils-skills/<skill-name>`).
