---
name: bun-integration-tests
description: Set up Bun integration test infrastructure for TypeScript repos. Use when adding integration tests or scaffolding test setup from scratch.
---

# Bun Integration Test Setup

Applies the integration test pattern to any Bun + TypeScript repo.

**Default assumptions** (override if detected otherwise):
- ORM: Prisma (`db` client via `@/lib/clients/db` or similar)
- HTTP framework: Elysia → Eden Treaty client
- Test runner: `bun:test`
- Env injection: `infisical run --` (skip if no `.infisical.json` present)
- External mocks: only for queue (QStash-style) and storage (S3-style) if present

---

## Step 1 — Discover the repo

Read these files before generating anything:

1. `prisma/schema.prisma` → entity names + foreign key dependency order
2. `src/index.ts` or `src/app.ts` → detect HTTP framework
3. `package.json` → existing test scripts, infisical presence
4. `src/lib/clients/` or equivalent → find the db import path and any queue/storage clients

If Prisma schema is absent, ask the user what ORM they use before proceeding.

---

## Step 2 — Generate `tests/integration/setup.ts`

This is the core file. Generate it using the template in `references/setup-template.md`.

Key decisions to make before writing:
- **TestContext fields**: one array per top-level entity type (e.g. `workspaceIds`, `userIds`, `jobIds`)
- **Cleanup order**: reverse of foreign key dependency order from the schema. Children before parents.
- **Two modes**:
  - `setupIntegrationTest()` → `beforeEach`/`afterEach` (test isolation, slower)
  - `setupIntegrationSuite()` → `beforeAll`/`afterAll` (suite isolation, faster, use when tests don't mutate shared state)

---

## Step 3 — Generate factory helpers

Create `tests/integration/helpers/test-<entity>.ts` for each primary entity users will create in tests.

Pattern (from `references/factory-pattern.md`):
- Function receives `ctx: TestContext` + options with sensible defaults
- Creates entity via `db.<model>.create()`
- Pushes IDs into `ctx.<entityIds>` for automatic cleanup
- Returns the minimal fields callers need (id, key, slug, etc.)

Prioritize factories for: workspace/tenant, user/member, api-key, and whatever the app's main domain entity is.

---

## Step 4 — Generate HTTP client

**Elysia (Eden Treaty)**:
```ts
// src/lib/test-utils/treaty-client.ts
import { treaty } from "@elysiajs/eden"
import type { App } from "$/index"

export const api = treaty<App>("http://localhost:<PORT>")
```
Read the app's port from env or default (8080). Check how the app exports its type (`App`, `ElysiaApp`, etc.)

**Hono**:
```ts
import { testClient } from "hono/testing"
import { app } from "$/index"
export const api = testClient(app)
```

**Express/other**: use `fetch` directly with `http://localhost:<PORT>`.

---

## Step 5 — Update `package.json`

Add these scripts (adapt paths as needed):

```json
{
  "test": "bun run test:hermetic",
  "test:hermetic": "bun test --timeout 20000 src/**/*.spec.ts",
  "test:integration": "<infisical> bun test --timeout 20000 tests/integration/",
  "test:integration:ci": "bun test --timeout 20000 tests/integration/"
}
```

- `test:hermetic`: pure unit/model tests, no DB, no env needed
- `test:integration`: hits real DB, needs env — prefix with `infisical run --` if `.infisical.json` exists
- `test:integration:ci`: same glob without infisical (CI injects env directly)

If the repo uses Turbo, also add the task to `turbo.json` under `test:integration:ci`.

---

## Step 6 — Mock external services (if present)

Only mock services at the **system boundary** (not the DB). Common cases:

**Queue (QStash / similar)**:
```ts
// helpers/mock-queue.ts
let installed = false
export function installQueueMock() {
  if (installed) return
  // intercept the queue client's publish method
  installed = true
}
export function resetQueueMock() { /* restore */ }
```

**Storage (S3 / R2)**:
- Don't mock S3 in tests — use a real test bucket or localstack
- On cleanup, call `bucket.file(key).delete()` in a best-effort catch

---

## Conventions to follow

- No `try`/`catch` in test helpers unless it's best-effort cleanup
- No mocks for the database — tests hit the real DB
- `generateTestId()` → `test_${Date.now()}_${Math.random().toString(36).slice(2, 8)}`
- Test emails: `test-<role>-${testId}@test.local`
- Fake stripe IDs: `cus_test_${testId}`, `sub_test_${testId}`
- Do not add `afterAll(() => db.$disconnect())` in individual specs — only in `setupIntegrationSuite` or the root health spec

---

## Reference files

- `references/setup-template.md` — full `setup.ts` template with all lifecycle hooks
- `references/factory-pattern.md` — annotated factory helper example
- `references/writing-style.md` — exact conventions for writing spec files (naming, structure, patterns, anti-patterns)

Read these when generating the actual files. Always read `writing-style.md` before writing any spec.
