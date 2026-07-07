# Jest : Best Practices & Architecture

## 1. Test Isolation
- **The Global Leak:** Jest runs tests in parallel across workers, but tests within the same file run sequentially. 
- **Rule:** Re-instantiate your test subjects inside the `beforeEach` block. Do not initialize classes or state at the root of the test file, as mutations in `test A` will corrupt the initial state for `test B`.

## 2. Snapshot Testing Prudence
- **The Fragility Problem:** Snapshot tests (`toMatchSnapshot`) take a text dump of an object or UI component. Overusing them leads to "snapshot fatigue", where developers blindly update snapshots (`jest -u`) without checking what broke.
- **Rule:** Reserve snapshot testing strictly for pure UI components or complex static data structures. Never use snapshots for data containing timestamps, random UUIDs, or memory references, as they will fail on every run.

## 3. Asynchronous Code
- **Dangling Promises:** A common Jest error is tests finishing before the async code completes.
- **Rule:** Always use `async/await` syntax for asynchronous tests. If testing an expected error, use `await expect(promise).rejects.toThrow()`. Do not use `try/catch` blocks inside tests to catch expected errors, as a passing promise will bypass the catch block and silently produce a false-positive test result.