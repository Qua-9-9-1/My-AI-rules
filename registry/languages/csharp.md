# C# & .NET Language Guidelines

These rules dictate how C# code must be written to ensure type safety, prevent runtime null exceptions, and maintain asynchronous correctness. The code must target modern .NET standards.

## 1. Nullability & Type Safety
The compiler must explicitly prove that a null value cannot cause a runtime crash.
* **Nullable Reference Types (NRT):** The project is assumed to have `<Nullable>enable</Nullable>`. All reference types must be treated as non-nullable by default. If a type can logically be null, it must be explicitly marked with `?` (e.g., `string?`).
* **No Null-Forgiving Operator:** The use of the bang operator (`!`) to suppress null warnings (e.g., `var item = list.FirstOrDefault()!;`) is strictly forbidden. Always use explicit pattern matching (`if (item is not null)`) to handle potential nulls.

## 2. Asynchronous Programming
Incorrect use of async/await leads to deadlocks and thread-pool starvation.
* **No `async void`:** Never use `async void` except for strictly necessary event handlers (e.g., UI clicks). Always return `Task` or `Task<T>`.
* **Async All The Way:** If a method calls an asynchronous method, it must also be asynchronous. Do not block async code with `.Wait()` or `.Result`.
* **CancellationToken:** All asynchronous methods performing I/O or network operations must accept a `CancellationToken` to allow operation cancellation and prevent resource locking.

## 3. LINQ & Performance Optimization
LINQ uses deferred execution. Misunderstanding this causes severe performance degradation through multiple database queries or redundant memory allocations.
* **Materialize Carefully:** Do not repeatedly enumerate an `IEnumerable<T>`. If the result of a LINQ query needs to be accessed multiple times, materialize it immediately using `.ToList()` or `.ToArray()`.
* **Prefer `Any()` over `Count()`:** When checking if a sequence contains elements, always use `.Any()` instead of `.Count() > 0` to prevent enumerating the entire collection.

## 4. Resource Management
* **`IDisposable` and `using` Statements:** Any object that implements `IDisposable` (streams, database connections, HTTP clients) must be instantiated within a `using` declaration to guarantee deterministic resource release, even in the event of an exception. Prefer the modern `using var` syntax over nested `using` blocks to reduce indentation.

## 5. Dependency Injection (DI) & Architecture
* **Interface-Driven:** Components must depend on interfaces (`IUserService`), not concrete implementations (`UserService`). This ensures the code remains modular and testable via mocking.
* **Constructor Injection:** All dependencies must be injected via the constructor. Property injection or the Service Locator pattern are forbidden.