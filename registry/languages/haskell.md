# Haskell Language Guidelines

These rules dictate how Haskell code must be written to ensure mathematical purity, prevent memory leaks caused by excessive laziness, and eliminate runtime crashes by enforcing total functions.

## 1. Purity and Side Effects (IO Boundary)
* **Isolate IO:** The core of the application must consist entirely of pure functions. The `IO` monad must be strictly confined to the outermost layer of the application (the entry point, network, and file system interactions). 
* **Do Not Mix Logic and Effects:** Never embed complex business or algorithmic logic directly inside a `do` block that operates in the `IO` monad. Extract the logic into pure functions and call them from the `IO` context.

## 2. Total Functions & Safety
Partial functions compile successfully but crash the program at runtime if given unexpected input. They are strictly forbidden.
* **No `head`, `tail`, `last`, or `init`:** Never use these prelude functions on lists. Use pattern matching or safe alternatives (e.g., from `Data.Maybe` or `safe` package) that return `Maybe a`.
* **Exhaustive Pattern Matching:** Every pattern match must explicitly cover all possible data constructors. Relying on implicit fallbacks or suppressing non-exhaustive pattern warnings is forbidden.

## 3. Laziness and Space Leaks
Haskell evaluates expressions lazily, building up "thunks" (unevaluated expressions) in memory until the result is strictly required. Accumulating too many thunks causes severe memory leaks.
* **Strict Accumulators:** When folding over large data structures (e.g., `foldl`), always use the strict version `foldl'` from `Data.List` to force evaluation at each step and prevent thunk buildup.
* **Strict Data Fields:** Use the strictness annotation (the `!` bang operator) on fields in `data` declarations unless laziness for that specific field is mathematically required and justified. (e.g., `data Point = Point !Double !Double`).

## 4. Type System & Readability
* **Mandatory Top-Level Signatures:** Every single top-level function, value, and typeclass instance must have an explicit type signature. Letting the compiler infer top-level types is forbidden, as signatures serve as verifiable documentation.
* **Newtype over Type:** Use `newtype` instead of `type` aliases when defining domain-specific wrappers around underlying types (e.g., `newtype Email = Email Text`). This provides zero-cost abstractions while preventing the compiler from accepting invalid generic text where an Email is required.

## 5. Tooling and Linting
* **HLint is Absolute:** The codebase must pass `hlint` with zero suggestions. If an HLint suggestion is intentionally ignored, it must be suppressed locally with a specific pragma and a mathematical justification.
* **Formatting:** All code must be automatically formatted using standard tools like `ormolu` or `fourmolu`. Manual alignment of equal signs or arrows is not permitted if it conflicts with the formatter.