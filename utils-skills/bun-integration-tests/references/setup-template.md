# setup.ts Template

Full template for `tests/integration/setup.ts`. Replace `<Entity>` placeholders with actual entity names from the repo's Prisma schema.

```ts
import { afterAll, afterEach, beforeAll, beforeEach } from "bun:test"
import { db } from "$/lib/clients/db" // adjust import path
// import { installQueueMock, resetQueueMock } from "./helpers/mock-queue"

// ─── Context ────────────────────────────────────────────────────────────────

type TestContext = {
  // Add one array per root entity type in the schema.
  // Order here doesn't matter — cleanup order is what matters (see below).
  workspaceIds: string[]
  userIds: string[]
  // <entityType>Ids: string[]
}

function createTestContext(): TestContext {
  return {
    workspaceIds: [],
    userIds: [],
    // <entityType>Ids: [],
  }
}

// ─── Cleanup ─────────────────────────────────────────────────────────────────
//
// IMPORTANT: Delete in reverse dependency order (children before parents).
// Read the Prisma schema's foreign keys to determine correct order.
// If you delete a parent before its children, Prisma will throw a FK violation.

async function cleanupTestContext(ctx: TestContext): Promise<void> {
  // Example order (adapt to this repo's schema):
  //
  // 1. leaf tables (no FK children)
  // if (ctx.jobIds.length > 0) {
  //   await db.jobLog.deleteMany({ where: { jobId: { in: ctx.jobIds } } })
  //   await db.job.deleteMany({ where: { id: { in: ctx.jobIds } } })
  // }
  //
  // 2. workspace children
  // if (ctx.workspaceIds.length > 0) {
  //   await db.workspaceMember.deleteMany({ where: { workspaceId: { in: ctx.workspaceIds } } })
  //   await db.workspace.deleteMany({ where: { id: { in: ctx.workspaceIds } } })
  // }
  //
  // 3. users (usually last, no parent)
  if (ctx.userIds.length > 0) {
    await db.user.deleteMany({ where: { id: { in: ctx.userIds } } })
  }
}

// ─── ID generator ────────────────────────────────────────────────────────────

function generateTestId(): string {
  return `test_${Date.now()}_${Math.random().toString(36).slice(2, 8)}`
}

// ─── waitFor util ────────────────────────────────────────────────────────────

async function waitFor(
  condition: () => Promise<boolean>,
  options: { timeoutMs?: number; intervalMs?: number } = {},
): Promise<void> {
  const { timeoutMs = 5_000, intervalMs = 100 } = options
  const start = Date.now()

  while (Date.now() - start < timeoutMs) {
    // biome-ignore lint/performance/noAwaitInLoops: polling loop
    if (await condition()) return
    // biome-ignore lint/performance/noAwaitInLoops: polling loop
    await Bun.sleep(intervalMs)
  }

  throw new Error(`waitFor timed out after ${timeoutMs}ms`)
}

// ─── Setup modes ─────────────────────────────────────────────────────────────

/**
 * Per-test isolation. Use when tests mutate data that would conflict with
 * each other. Slower (cleanup runs after every test).
 */
function setupIntegrationTest(): { getContext: () => TestContext } {
  let ctx: TestContext

  beforeAll(() => {
    // installQueueMock()
  })

  beforeEach(() => {
    ctx = createTestContext()
  })

  afterEach(async () => {
    // resetQueueMock()
    await cleanupTestContext(ctx)
  })

  return { getContext: () => ctx }
}

/**
 * Per-suite isolation. Use when all tests in the file can share setup
 * without interfering. Faster (cleanup runs once after all tests).
 */
function setupIntegrationSuite(): { getContext: () => TestContext } {
  let ctx: TestContext

  beforeAll(() => {
    // installQueueMock()
    ctx = createTestContext()
  })

  afterAll(async () => {
    // resetQueueMock()
    await cleanupTestContext(ctx)
    await db.$disconnect()
  })

  return { getContext: () => ctx }
}

// ─── Exports ─────────────────────────────────────────────────────────────────

export { db } from "$/lib/clients/db" // re-export for convenience in specs
export type { TestContext }
export {
  cleanupTestContext,
  createTestContext,
  generateTestId,
  setupIntegrationSuite,
  setupIntegrationTest,
  waitFor,
}
```

## Usage in a spec file

```ts
import { describe, expect, test } from "bun:test"
import { api } from "$/lib/test-utils/treaty-client"
import { createTestWorkspace } from "./helpers/test-workspace"
import { setupIntegrationTest } from "./setup"

describe("My Feature", () => {
  const { getContext } = setupIntegrationTest()

  test("does something", async () => {
    const ctx = getContext()
    const { apiKey } = await createTestWorkspace(ctx)

    const res = await api.v1.something.get({
      headers: { "X-Api-Key": apiKey },
    })

    expect(res.status).toBe(200)
  })
})
```
