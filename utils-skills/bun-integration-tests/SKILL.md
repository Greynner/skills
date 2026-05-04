---
name: bun-integration-tests
description: Use when the user wants to add or improve Bun integration tests for a TypeScript application.
---

# bun-integration-tests

Guide the setup of integration tests for Bun-based TypeScript projects.

## Workflow

1. Inspect the app stack: framework, database layer, environment loading, and existing tests.
2. Create a `tests/integration/` area only if the project does not already have a convention.
3. Add shared setup for lifecycle hooks, test data cleanup, and environment validation.
4. Add helper factories for entities that tests need to create often.
5. Prefer real database integration for persistence behavior.
6. Mock external services such as queues, email, storage, and third-party APIs.
7. Add package scripts for local and CI execution.
8. Run a small example test to prove the setup works.

## Common scripts

Adapt names to the repo, but a typical setup is:

```json
{
  "test:integration": "bun test --timeout 20000 tests/integration/",
  "test:integration:ci": "bun test --timeout 20000 tests/integration/"
}
```

## Safety

Never point tests at production resources. Confirm database URLs and secrets before running destructive cleanup.
