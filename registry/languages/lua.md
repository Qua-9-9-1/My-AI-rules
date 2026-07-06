# Lua Language Guidelines

These rules dictate how Lua code must be written to ensure performance, predictability, and memory safety. The primary focus is eliminating unintended global scope leaks and standardizing table usage.

## 1. Scope & Variable Declaration
* **`local` is Mandatory:** All variables and functions must be declared explicitly with the `local` keyword. Global variables are strictly forbidden unless absolutely required by the host environment API.
* **Module Encapsulation:** When writing modules, do not use the deprecated `module()` function. Instead, return a local table containing the module's functions and data.

## 2. Table Management (Data Structures)
Lua uses tables for both arrays and dictionaries. Mixing these paradigms within the same table causes iteration unpredictability and performance degradation.
* **Strict Separation:** Never mix array-like integer keys and dictionary-like string keys in the same table. 
* **Iteration:** * Use `ipairs()` strictly for sequentially indexed arrays (1-based integer keys).
    * Use `pairs()` strictly for associative arrays/dictionaries.
* **Preallocation:** If creating a large table where the size is known, avoid dynamic resizing in loops when possible (though Lua handles this internally, keeping table structures predictable aids the garbage collector).

## 3. Error Handling
Lua does not have native `try/catch` blocks.
* **Fail Fast:** Use `assert(condition, "Error message")` extensively for input validation and state checking to fail immediately upon invalid states.
* **Safe Execution:** For operations that might fail and should not crash the program (e.g., file I/O, external network calls), use `pcall()` or `xpcall()`. Never let an error bubble up to the host environment unhandled.

## 4. Object-Oriented Programming (OOP) & Metatables
* **Prefer Composition over Inheritance:** If implementing OOP using metatables and the `__index` metamethod, keep the hierarchy flat. Deep inheritance chains in Lua are slow and difficult to debug.
* **Explicit Metatables:** Only use metatables when necessary for behavior (like operator overloading or strict OOP). Do not use them for simple data storage structures.

## 5. Tooling & Linting
* **Luacheck:** Code must be written to pass `luacheck` without warnings. Unused variables should be named `_` to explicitly indicate they are ignored.
* **1-Based Indexing:** Always respect Lua's native 1-based indexing for arrays and string operations. Do not attempt to force 0-based logic.