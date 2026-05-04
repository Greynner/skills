# bun-integration-tests

Sets up Bun integration test infrastructure for TypeScript repos. Covers `setup.ts`, `TestContext`, factory helpers, cleanup chains, mock injection, and `package.json` scripts.

## When to use

- Adding integration tests to a repo that doesn't have them
- Setting up test infrastructure from scratch
- Asking how to structure tests
- Applying the integration test pattern to a new project

## Default stack

| Layer | Default | Adapts to |
|-------|---------|-----------|
| ORM | Prisma | Drizzle |
| HTTP framework | Elysia (Eden Treaty) | Hono, plain fetch |
| Test runner | `bun:test` | — |
| Env injection | Project-specific env loader | optional; use direct `bun test` if none exists |
| External mocks | Queue (QStash), Storage (S3) | only if present |

## Install

```bash
npx skills add https://github.com/Greynner/skills.git --skill utils-skills/bun-integration-tests
```

## What gets generated

```
tests/integration/
├── setup.ts                          # TestContext, lifecycle hooks, cleanup chains
└── helpers/
    ├── test-<entity>.ts              # Factory helpers per primary entity
    └── mock-queue.ts                 # Queue mock (if queue client detected)
src/lib/test-utils/
└── treaty-client.ts                  # HTTP test client (Elysia) or equivalent
```

`package.json` scripts added:

```json
{
  "test": "bun run test:hermetic",
  "test:hermetic": "bun test --timeout 20000 src/**/*.spec.ts",
  "test:integration": "<env-loader> bun test --timeout 20000 tests/integration/",
  "test:integration:ci": "bun test --timeout 20000 tests/integration/"
}
```

## Key conventions

- No mocks for the database — tests hit the real DB
- Cleanup order follows reverse foreign key dependency order (children before parents)
- Two isolation modes: `setupIntegrationTest()` (per-test) and `setupIntegrationSuite()` (per-suite)
- `generateTestId()` → `test_${Date.now()}_${Math.random().toString(36).slice(2, 8)}`

## Reference files

- [`references/setup-template.md`](references/setup-template.md) — full `setup.ts` template
- [`references/factory-pattern.md`](references/factory-pattern.md) — annotated factory helper example
- [`references/writing-style.md`](references/writing-style.md) — conventions for writing spec files
