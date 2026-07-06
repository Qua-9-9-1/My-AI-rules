# TypeScript Language Guidelines

These rules dictate how TypeScript code must be written. The primary objective is to maximize compile-time safety and eliminate runtime type errors by strictly enforcing the type system.

## 1. Strict Typing Constraints
* **`any` is Strictly Forbidden:** Never use the `any` type. It disables type checking. If the type is truly unknown ahead of time, use `unknown` and perform explicit type narrowing (type guards) before accessing its properties.
* **Strict Compiler Options:** Assume `strict: true` is enabled in `tsconfig.json`. This implies `noImplicitAny`, `strictNullChecks`, and `strictBindCallApply` are fully enforced.

## 2. Type Definitions: Interfaces vs. Types
* **Use `interface` for Object Shapes:** Default to `interface` when defining the shape of an object or a class contract. Interfaces are more performant for the compiler and produce cleaner error messages.
* **Use `type` for Unions/Intersections:** Only use the `type` alias keyword when defining unions (e.g., `type Status = "idle" | "loading"`), intersections, or complex utility types.

## 3. Nullability & Safety
* **No Non-Null Assertions:** The use of the non-null assertion operator (`!`) is strictly forbidden (e.g., `user!.name`). It lies to the compiler. Always use optional chaining (`?.`) or explicit null checks (`if (user) { ... }`).
* **Handle Undefined Explicitly:** When returning or passing optional values, clearly define them in the signature (e.g., `data: string | undefined`).

## 4. Async & Promises
* **Always use `async/await`:** Do not use raw `.then()` and `.catch()` chains. `async/await` makes the control flow synchronous-looking and easier to read.
* **Awaited Types:** When typing a function that returns a Promise, always explicitly type the return value as `Promise<T>` (e.g., `async getUser(): Promise<User>`).

## 5. Arrays and Mutability
* **Readonly by Default:** When passing arrays or objects as arguments to a pure function, type them as `ReadonlyArray<T>` or `Readonly<T>` to mathematically guarantee the function does not mutate the inputs.