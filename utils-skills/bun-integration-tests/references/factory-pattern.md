# Factory Helper Pattern

Template for `tests/integration/helpers/test-<entity>.ts`.

## Rules

1. Always accept `ctx: TestContext` as first argument
2. Push every created ID into the right `ctx.<entity>Ids` array
3. Return only what callers need — don't return the full DB row
4. Use sensible defaults so most tests can call `createTest<Entity>(ctx)` with no options
5. Create related entities inline (e.g. user + member + billing) — don't make callers chain multiple factories unless the combination is genuinely optional

## Annotated example: workspace factory

```ts
import { db, generateTestId, type TestContext } from "../setup"

export type TestWorkspaceResult = {
  workspaceId: string
  apiKey: string
  apiKeyId: string
}

export async function createTestWorkspace(
  ctx: TestContext,
  options: {
    // expose only the options that tests realistically vary
    tier?: "FREE" | "PRO" | "ENTERPRISE"
    apiKeyEnabled?: boolean
  } = {},
): Promise<TestWorkspaceResult> {
  const id = generateTestId()
  const { tier = "PRO", apiKeyEnabled = true } = options

  // 1. Create user (owner)
  const user = await db.user.create({
    data: {
      id: `user_${id}`,
      email: `test-owner-${id}@test.local`,
      name: `Test Owner ${id}`,
    },
  })
  ctx.userIds.push(user.id) // register for cleanup

  // 2. Create workspace
  const workspace = await db.workspace.create({
    data: {
      id: `ws_${id}`,
      name: `Test Workspace ${id}`,
      slug: `test-ws-${id}`,
      tier,
    },
  })
  ctx.workspaceIds.push(workspace.id) // register for cleanup

  // 3. Create membership
  await db.workspaceMember.create({
    data: {
      userId: user.id,
      workspaceId: workspace.id,
      role: "ADMIN",
    },
  })
  // workspaceMember is cleaned up via workspace cascade — no need to track separately

  // 4. Create API key
  const keyPlaintext = `test_key_${id}_${Math.random().toString(36).slice(2, 15)}`
  const apiKey = await db.apiKey.create({
    data: {
      key: keyPlaintext,
      workspaceId: workspace.id,
      enabled: apiKeyEnabled,
    },
  })
  ctx.apiKeyIds.push(apiKey.id)

  return {
    workspaceId: workspace.id,
    apiKey: keyPlaintext,
    apiKeyId: apiKey.id,
  }
}
```

## Minimal factory (for simpler entities)

```ts
export async function createTestUser(
  ctx: TestContext,
  options: { role?: "ADMIN" | "MEMBER" } = {},
) {
  const id = generateTestId()
  const user = await db.user.create({
    data: {
      id: `user_${id}`,
      email: `test-${id}@test.local`,
      name: `Test User ${id}`,
    },
  })
  ctx.userIds.push(user.id)
  return user
}
```

## Mutation helpers (not factories)

For helpers that modify existing entities rather than create them, don't use the factory pattern — just export plain async functions:

```ts
export async function disableApiKey(apiKeyId: string): Promise<void> {
  await db.apiKey.update({
    data: { enabled: false },
    where: { id: apiKeyId },
  })
}

export async function expireApiKey(apiKeyId: string): Promise<void> {
  await db.apiKey.update({
    data: { expiresAt: new Date(Date.now() - 1_000) },
    where: { id: apiKeyId },
  })
}
```

These don't register to ctx because they don't create anything — cleanup of the parent entity covers them.
