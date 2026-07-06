# C++ Language Guidelines

These rules enforce Modern C++ (C++17/C++20) standards. The absolute priority is to prevent memory leaks, eliminate undefined behavior (UB), and maintain strict type safety using modern idioms.

## 1. Memory Management & RAII
* **RAII is Mandatory:** Resource Acquisition Is Initialization (RAII) must be used for all resource management (memory, file handles, mutexes). 
* **No Raw `new` or `delete`:** Manual memory allocation using `new` and `delete` is strictly forbidden. 
* **Smart Pointers:** Always use `std::unique_ptr` for exclusive ownership and `std::shared_ptr` (only when shared ownership is mathematically proven to be necessary). Use `std::make_unique` and `std::make_shared` for instantiation.

## 2. Safety & Undefined Behavior (UB)
* **No C-Style Casts:** Never use `(Type)variable`. Use `static_cast`, `dynamic_cast`, or `reinterpret_cast` to make the conversion intent explicit and traceable.
* **No `NULL` or `0` for Pointers:** Strictly use `nullptr` for null pointer representation.
* **Array Bounds:** Prefer `std::array` or `std::vector` over raw C-style arrays (`T arr[]`). Use the `.at()` method for bounds-checked access instead of the `[]` operator when the index is dynamic.

## 3. Const Correctness & Semantics
* **`const` Everything Possible:** Variables, pointers, references, and class methods must be declared `const` by default unless they strictly require mutation. This allows the compiler to optimize and catch unintended side effects.
* **Pass by Const Reference:** Always pass complex objects by `const Type&` to avoid unnecessary copies. Only pass by value for fundamental types (int, float) or when a copy is explicitly required.
* **Use `[[nodiscard]]`:** Apply the `[[nodiscard]]` attribute to functions returning values that the caller must not ignore (e.g., error codes, allocated resources).

## 4. Modern Features & Typing
* **Almost Always `auto` (AAA):** Use `auto` for variable type deduction when the type is obvious from the right-hand side of the assignment (e.g., `auto ptr = std::make_unique<MyClass>();`) or for complex iterator types. Do not use `auto` if it reduces readability.
* **Rule of Zero / Rule of Five:** If a class defines a destructor, copy constructor, copy assignment operator, move constructor, or move assignment operator, it must define or `= delete` all five. Prefer the "Rule of Zero" by designing classes that do not require custom resource management.

## 5. Tooling & Compilation
* **Strict Flags:** The code must be designed to compile cleanly with `-Wall -Wextra -Werror -Wpedantic`. Treat all compiler warnings as fatal errors.