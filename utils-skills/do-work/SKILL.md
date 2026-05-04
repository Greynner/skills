---
name: do-work
description: Use when the user wants an agent to execute an implementation plan carefully with validation after each meaningful change.
---

# do-work

Execute planned engineering work in small, safe increments.

## Principles

- Understand the current repo before editing.
- Make the smallest useful change first.
- Validate frequently with tests, linters, type checks, or targeted commands.
- Keep the user informed about completed steps and blockers.
- Do not broaden scope without confirmation.

## Workflow

1. Restate the objective and identify the next concrete task.
2. Inspect relevant files and existing patterns.
3. Edit code or docs.
4. Run the narrowest meaningful validation.
5. Repeat until the requested work is complete.
6. Summarize changed files, validation performed, and remaining risks.

If a command is destructive or requires credentials, ask before running it.
