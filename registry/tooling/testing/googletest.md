# Google Test (GTest) : Best Practices & Architecture

## 1. Assertion Types (EXPECT vs ASSERT)
- **EXPECT_\* (Non-fatal):** Use `EXPECT_EQ`, `EXPECT_TRUE`, etc., for standard verifications. If it fails, the test continues running, allowing you to see subsequent failures in the same test block.
- **ASSERT_\* (Fatal):** Use `ASSERT_EQ`, `ASSERT_NE`, etc., strictly when continuing the test is impossible or logical nonsense (e.g., verifying if a pointer is null: `ASSERT_NE(ptr, nullptr)` before dereferencing it). If an `ASSERT` fails, execution of the current function stops immediately.

## 2. Test Fixtures (TEST_F)
- **Isolation:** Use `TEST_F(FixtureClass, TestName)` instead of standard `TEST()` when multiple tests share configuration data or setup logic.
- **Memory Management:** Initialize objects inside the `SetUp()` method or the fixture's constructor. Always clean up raw resources, open sockets, or allocated memory inside the `TearDown()` method or the destructor to prevent memory leaks between test runs.

## 3. Mocking (GMock)
- **Dependency Inversion:** Use Google Mock (`gmock`) to isolate the class under test from its real dependencies (e.g., database wrappers, network layers).
- **Expectations First:** Always declare your expectations (`EXPECT_CALL(...)`) *before* triggering the action that invokes the mocked methods.

## 4. Floating-Point Comparisons
- **Strict Rule:** Never use `EXPECT_EQ` or `ASSERT_EQ` for `float` or `double` values due to precision errors inherent to binary representations.
- **Correct Usage:** Always use `EXPECT_NEAR(val1, val2, epsilon)` or `EXPECT_FLOAT_EQ` / `EXPECT_DOUBLE_EQ`.