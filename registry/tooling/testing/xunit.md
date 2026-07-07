# xUnit.net : Best Practices & Architecture

## 1. Test Architecture (Facts vs Theories)
- **[Fact]:** Use the `[Fact]` attribute for invariant tests — tests that require no arguments and test a single, static condition.
- **[Theory]:** Use the `[Theory]` attribute for data-driven tests. Combine it with `[InlineData(arg1, arg2)]`, `[ClassData]`, or `[MemberData]` to pass parameters to the test method.

## 2. State Isolation (Constructor/Dispose Pattern)
- **No SetUp/TearDown Attributes:** xUnit intentionally removes NUnit's legacy `[SetUp]` and `[TearDown]` attributes in favor of standard object-oriented principles.
- **Rule:** - **Setup:** Put initialization logic inside the test class **Constructor**. xUnit creates a brand-new instance of the class for every single test method, ensuring perfect isolation.
  - **Teardown:** Implement the `IDisposable` interface and put cleanup logic inside the `Dispose()` method.

## 3. Shared Context (Class Fixtures)
- **Heavy Resource Reuse:** If a setup operation is too heavy to run for every test (e.g., spinning up a Docker container or initializing a database), use the `IClassFixture<T>` interface. This shares a single instance of the fixture context across all tests inside the class.

## 4. Async Testing
- **Avoid Async Void:** Never write `public async void TestMethod()`. It causes uncatchable exceptions and breaks the xUnit test runner.
- **Rule:** Always return `Task`. Write `public async Task TestMethod()` when utilizing `await` inside your assertions.