# Python Language Guidelines

These rules enforce strict typing, explicit error handling, and idiomatic performance practices in Python. The goal is to make the codebase predictable and prevent runtime crashes.

## 1. Type Hinting & Static Analysis
* **Mandatory Type Hints:** Every function signature must include explicit type hints for all arguments and the return value (e.g., `def process_data(items: list[str]) -> int:`).
* **No `Any`:** The use of `typing.Any` is strictly forbidden. Use `typing.Union`, `typing.Optional`, or generics/TypeVars if the exact type is variable.
* **MyPy Validation:** The code must be designed to pass strict `mypy` static type checking without errors.

## 2. Tooling & Formatting
* **PEP 8 Compliance:** All code must strictly adhere to the PEP 8 standard. Assume the use of modern formatters like `Black` or `Ruff` (line length is strictly enforced, typically 88 characters).
* **Imports Structuring:** Group imports logically: standard library first, third-party packages second, and local application imports last.

## 3. Error Handling
* **No Naked Exceptions:** Never use a bare `except:` clause. Always catch the specific exception class expected (e.g., `except ValueError:`). Catching `Exception` is only allowed at the highest level of the application for fatal error logging.
* **No Silent Failures:** The use of `except: pass` is strictly forbidden. If an exception is caught and intentionally ignored, it must be documented with a comment explaining the exact mathematical or business logic reason.

## 4. Idiomatic Python & Performance
* **Comprehensions over Loops:** Prefer list, dict, and set comprehensions for simple transformations instead of `for` loops with `.append()`. It is faster and more readable.
* **Generators for Large Data:** If processing large datasets or reading large files, use generators (`yield`) instead of loading all data into memory at once.
* **Explicit Resource Management:** Always use context managers (`with` statements) for file I/O, database connections, and network sessions to guarantee resources are properly closed.

## 5. Data Structures & Mutability
* **Immutable Default Arguments:** Never use mutable types (lists, dicts) as default function arguments (e.g., `def foo(items=[]):` is forbidden). Use `None` as the default and initialize the list inside the function.
* **Dataclasses over Dictionaries:** Use `@dataclass` (preferably with `frozen=True` for immutability) when passing structured data between internal functions, rather than relying on unstructured dictionaries.