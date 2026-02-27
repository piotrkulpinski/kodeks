# Testing Best Practices

Detailed guidance for writing high-quality tests with `bun:test`.

## Mocking Strategies

- **Mock at boundaries**: Focus on mocking external systems like third-party APIs (Stripe, Resend, IPinfo), file system access, or network requests.
- **Avoid mocking internals**: Don't mock your own database (use a test database or Prisma's client features), local utility functions, or internal business logic modules.
- **`mock()` vs `mock.module()`**:
    - Use `mock()` for simple function mocks or spies.
    - Use `mock.module()` for entire module replacements.
    - **CRITICAL**: `mock.module()` leaks across files in the same Bun process. Avoid using it for service modules in orchestrator-level tests where parallel execution might occur.

```typescript
import { mock, it, expect } from "bun:test"

const myMock = mock(() => "result")
expect(myMock()).toBe("result")
expect(myMock).toHaveBeenCalled()
```

## Test Isolation

- **No shared state**: Every test should be able to run in any order. Avoid global variables that change during tests.
- **`beforeEach` reset**: Always clear mocks and reset database state before each test.
- **Independent data**: Create the data you need within the test or in a `beforeEach` block rather than relying on existing state.

```typescript
import { beforeEach, mock } from "bun:test"
import { myService } from "~/lib/services/myService"

mock.module("~/lib/services/externalApi", () => ({
  fetchData: mock(() => Promise.resolve({ success: true }))
}))

beforeEach(() => {
  // Clear mock history between tests
})
```

## Data Patterns

- **Realistic data**: Use data that resembles production values. Instead of "test", "foo", or "bar", use "user_123", "example.com", or "192.168.1.1".
- **Minimal relevance**: Only include fields in your test objects that are relevant to the behavior being tested.
- **Fixed expectations**: Hard-code your expected results. If you calculate them in the test, you might just be duplicating the bug in the production code.

## Common Anti-patterns

- **Testing implementation**: If you're checking private methods or specific internal variables, your tests will break when you refactor, even if the feature still works.
- **Over-mocking**: Mocking everything makes your tests pass even if the real integration is broken.
- **Piggyback assertions**: Adding unrelated checks to a single test makes it harder to diagnose failures. One test, one behavior.

## framework-specific notes for bun:test

- **`mock()`**: Creates a mock function.
- **`spyOn()`**: Wraps an existing method to track calls while preserving original behavior.
- **`mock.module()`**: Replaces a module for the entire process.
- **Snapshots**: Use `expect(value).toMatchSnapshot()` for complex object comparisons.
- **Cleanup**: Bun automatically handles some cleanup, but manual mock restoration is often safer in complex suites.
