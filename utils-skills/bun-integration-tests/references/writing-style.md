# Test Writing Style Guide

Conventions extracted from the actual integration test files in this codebase.
Follow these exactly when writing new specs.

---

## File structure

```ts
/**
 * <Feature Name> Integration Tests
 *
 * One-line description of what this file covers.
 */

import { describe, expect, test } from "bun:test"
import { api } from "$/lib/test-utils/treaty-client"
import { createTestWorkspace } from "./helpers/test-workspace"
import { setupIntegrationTest } from "./setup"
```

Import order:
1. `bun:test` named imports (only what's used)
2. Internal app imports (`$/...`)
3. Local test helpers (`./helpers/...`)
4. Setup (`./setup`)

---

## Describe nesting

Outer `describe` = feature/domain name.
Inner `describe` = HTTP method + path, e.g. `"GET /v1/credits/balance"`.

```ts
describe("Credits API Integration", () => {
  const { getContext } = setupIntegrationTest()

  describe("GET /v1/credits/balance", () => {
    test("returns zero balance for new workspace", async () => { ... })
    test("reflects granted credits", async () => { ... })
    test("returns 401 without API key", async () => { ... })
  })

  describe("GET /v1/credits/transactions", () => {
    test("lists credit transactions", async () => { ... })
  })
})
```

---

## Test body pattern

Always follow this order inside a test:

```ts
test("reflects granted credits", async () => {
  // 1. get context
  const ctx = getContext()

  // 2. create fixtures via factories
  const { apiKey, workspaceId } = await createTestWorkspace(ctx)

  // 3. set up state (grant credits, create related records, etc.)
  await grantTestCredits(workspaceId, 5000)

  // 4. make the HTTP call
  const res = await api.v1.credits.balance.get({
    headers: { "X-Api-Key": apiKey },
  })

  // 5. assert
  expect(res.status).toBe(200)
  expect(res.data).toHaveProperty("balance", 5000)
})
```

---

## Test naming

Use lowercase, full sentence, behavior-focused. Describe what the system does, not what you're testing.

```ts
// Good
test("returns zero balance for new workspace", ...)
test("reflects granted credits", ...)
test("returns 401 without API key", ...)
test("each workspace has independent balance", ...)
test("cannot access another workspace's job usage", ...)

// Bad
test("test balance endpoint", ...)
test("should return 200", ...)
test("creditBalance", ...)
```

---

## Comments within tests

Use single-line `//` comments to label logical sections, not to explain obvious code.

```ts
// Grant credits
await grantTestCredits(workspaceId, 5000)

// Create media and job to reserve credits
const { mediaId } = await createTestMedia(ctx, workspaceId)

// Check balance - should show reserved credits
const res = await api.v1.credits.balance.get(...)
```

Do NOT add comments that just repeat what the code says:
```ts
// Bad
// call the api
const res = await api.v1.credits.balance.get(...)
```

---

## Guard clauses for optional data

Use `if` guards (not non-null assertions `!`) when the response data might be absent:

```ts
const jobId = createRes.data?.jobId

if (jobId) {
  ctx.jobIds.push(jobId)

  const res = await api.v1.credits.usage({ jobId }).get({
    headers: { "X-Api-Key": apiKey },
  })

  expect(res.status).toBe(200)
}
```

For required entities (missing = bug in the test setup), use early throw:
```ts
const owner = await db.apiKey.findUnique({ where: { id: apiKeyId } })
if (!owner) throw new Error(`Expected api key ${apiKeyId}`)
```

---

## Parallel creation

Use `Promise.all` + `Array.from` for creating multiple records of the same type:

```ts
await Promise.all(
  Array.from({ length: 5 }, () => grantTestCredits(workspaceId, 100)),
)
```

---

## Optional chaining in assertions

Use `?.` when the response type doesn't guarantee the field (Treaty types):

```ts
expect(res.data?.balance).toBe(5000)
expect(res.data?.transactions?.length).toBeGreaterThanOrEqual(2)
```

Use `toHaveProperty` when you also want to assert presence:
```ts
expect(res.data).toHaveProperty("balance", 5000)
expect(res.data).toHaveProperty("transactions")
```

---

## Multi-workspace isolation tests

Pattern for cross-workspace access tests:

```ts
test("cannot access another workspace's resources", async () => {
  const ctx = getContext()

  const workspace1 = await createTestWorkspace(ctx, { name: "Workspace 1" })
  const workspace2 = await createTestWorkspace(ctx, { name: "Workspace 2" })

  // action with workspace1 key
  const res = await api.v1.thing({ id: "..." }).get({
    headers: { "X-Api-Key": workspace2.apiKey }, // wrong workspace
  })

  expect(res.status).toBe(404) // or 403
})
```

---

## Local helpers in a spec file

If a helper is only needed in one spec file, define it at the top of the file (before `describe`), not exported:

```ts
async function createTestEntityForWorkspace(workspaceId: string, label: string) {
  const entityId = `entity_${Date.now()}_${Math.random().toString(36).slice(2, 8)}`
  await db.entity.create({
    data: {
      id: entityId,
      workspaceEntities: { create: { label, workspaceId } },
    },
  })
  return entityId
}

describe("Matching Analysis API Integration", () => { ... })
```

---

## Local afterEach for spec-scoped entities

When a spec creates entities not tracked in `TestContext`, add a local `afterEach`:

```ts
describe("Matching Analysis API Integration", () => {
  const { getContext } = setupIntegrationTest()
  const createdEntityIds: string[] = []

  afterEach(async () => {
    if (createdEntityIds.length > 0) {
      await db.entity.deleteMany({ where: { id: { in: createdEntityIds } } })
      createdEntityIds.length = 0
    }
  })

  // tests push to createdEntityIds manually
})
```

---

## Auth tests pattern

One test per auth failure mode, all at the same describe level:

```ts
test("returns 401 for missing API key", async () => {
  const res = await api.v1.credits.balance.get({ headers: {} })
  expect(res.status).toBe(401)
})

test("returns 403 for invalid API key (not found in DB)", async () => {
  const res = await api.v1.credits.balance.get({
    headers: { "X-Api-Key": "not-a-valid-key" },
  })
  expect(res.status).toBe(403)
})

test("returns 403 for disabled API key", async () => {
  const ctx = getContext()
  const { apiKey, apiKeyId } = await createTestWorkspace(ctx)
  await disableApiKey(apiKeyId)

  const res = await api.v1.credits.balance.get({
    headers: { "X-Api-Key": apiKey },
  })
  expect(res.status).toBe(403)
})
```

---

## What NOT to do

```ts
// ✗ try/catch in tests
test("...", async () => {
  try {
    const res = await api.v1.thing.get(...)
    expect(res.status).toBe(200)
  } catch (e) {
    fail("should not throw")
  }
})

// ✗ non-null assertion instead of guard
const data = res.data!.balance

// ✗ shared workspace across tests (breaks isolation)
describe("Credits", () => {
  const workspace = await createTestWorkspace(ctx) // ← outside test body
  test("...", async () => { /* uses workspace */ })
})

// ✗ duplicating logic from the implementation
test("calculates available credits", async () => {
  const available = balance - reserved // ← duplicates app logic
  expect(res.data?.available).toBe(available)
})

// ✗ testing implementation details
test("calls the credit service", async () => {
  expect(creditServiceSpy).toHaveBeenCalled()
})
```
