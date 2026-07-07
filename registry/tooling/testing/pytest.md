# Pytest : Best Practices & Architecture

## 1. Native Assertions
- **Simplicity:** Reject legacy `self.assertEqual()` patterns. Pytest intercepts standard Python `assert` statements natively and provides rich, introspective cryptographic diffs automatically.
- **Readability:** Keep assertions clear: `assert calculated_value == expected_value`.

## 2. Fixture Management
- **Explicit Injection:** Use fixtures for setup and teardown operations. Pass them explicitly as arguments to test functions instead of relying on global objects.
- **Teardown via Yield:** Use the `yield` keyword instead of `return` in fixtures when cleanup is required. Everything after the `yield` statement will execute after the test finishes, guaranteed.

```python
@pytest.fixture
def db_session():
    session = create_connection()
    yield session
    session.close() # Automatic cleanup
```

## 3. Parameterization
- **DRY Principle:** Do not write multiple test functions to test the same logic with different inputs.
- **Rule:** Use the `@pytest.mark.parametrize` decorator to inject data matrices into a single test structure, decoupling test logic from test data.

## 4. Exception Verifications
- **Strict Boundary:** When testing code that must fail, always wrap the call inside the `with pytest.raises(ExpectedException):` context manager. Keep the context block as small as possible (ideally a single line) to avoid catching the same exception triggered by an unintended secondary line of code.