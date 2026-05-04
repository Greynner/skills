---
name: handle-coderabbit
description: Use when the user wants to triage, respond to, or apply CodeRabbit pull request review comments.
---

# handle-coderabbit

Help process CodeRabbit review feedback efficiently.

## Workflow

1. Gather the review comments and the relevant diff.
2. Classify each comment as: must fix, nice to have, question, or false positive.
3. Apply safe fixes directly when the codebase confirms the recommendation.
4. Avoid mechanical changes that conflict with project conventions.
5. For rejected comments, draft a short explanation.
6. Run relevant validation after edits.

## Response style

Summarize:

- comments addressed
- comments skipped and why
- files changed
- validation run

Ask before making broad refactors that go beyond the review.
