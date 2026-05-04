# handle-coderabbit

## What it is

A workflow skill for **triaging and fixing CodeRabbit feedback** on a pull request: discover the PR, fetch bot comments and reviews via GitHub CLI, apply minimal code fixes, validate, commit, and optionally reply on the PR.

## When to use it

Use when:

- CodeRabbit has left review comments you want resolved in code
- You say things like “handle coderabbit”, “address review comments”, or “fix coderabbit feedback”

**When not to use it:** When you do not use CodeRabbit or `gh` CLI, or when feedback should be discussed rather than implemented as-is (negotiate scope first).

## What the agent will do

Identify the PR (`gh pr view`), pull CodeRabbit-authored threads, group actionable vs nitpick vs clarification, implement **minimal** fixes, then run project checks and push. The exact `gh api` patterns and triage rules are in [SKILL.md](SKILL.md).

## Prerequisites

- [GitHub CLI](https://cli.github.com/) (`gh`) authenticated for the repository
- CodeRabbit installed on the repo and posting as the expected bot user
- **Adapt validation commands** in [SKILL.md](SKILL.md) to your stack—the bundled examples use `bun run typecheck` and `bun run test`; replace with `npm`, `pnpm`, `cargo`, etc. as needed

## How to install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/handle-coderabbit
```

## Example prompts

```text
Handle CodeRabbit on this PR: fix everything actionable and push.
```

```text
Fetch CodeRabbit comments for the current branch’s PR and list what needs code changes vs replies.
```

```text
Address coderabbit feedback on PR 42, then run our test suite and commit.
```

## Files in this folder

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | End-to-end workflow and example `gh` commands |
| [README.md](README.md) | This human-facing overview |

## Maintenance

- Keep bot username / API filters aligned with CodeRabbit’s GitHub identity if it changes.
- Consider parameterizing validate commands for monorepos (document in SKILL.md for your org).
