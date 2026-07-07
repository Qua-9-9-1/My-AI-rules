# Vitest : Best Practices & Architecture

## 1. Concurrency Awareness
- **Parallel Execution:** Unlike Jest, Vitest heavily utilizes worker threads by default. This makes it incredibly fast, but requires strict isolation.
- **Rule:** Never rely on global mutable state or shared local files across different test files. If tests read/write to the same SQLite database or modify `process.env`, they will cause race conditions and fail randomly.

## 2. Mocking Boundaries
- **vi.mock() Hoisting:** Understand that `vi.mock()` is hoisted to the top of the file during compilation. You cannot use dynamic variables from your test scope inside the `vi.mock` factory function unless they are explicitly passed via `vi.hoisted()`.
- **Reset State:** Always call `vi.resetAllMocks()` or `vi.restoreAllMocks()` in an `afterEach` block to ensure mock counters and implementations do not leak between test cases.

## 3. In-Source Testing
- Vitest allows writing tests in the same file as the source code using `if (import.meta.vitest)`.
- **Rule:** Restrict in-source testing to pure, mathematical utility functions. For components, controllers, or API endpoints, enforce strict separation by creating dedicated `.test.ts` or `.spec.ts` files to maintain architectural clarity.