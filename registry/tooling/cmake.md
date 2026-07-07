# CMake : Best Practices & Architecture

## 1. Modern CMake Principles (Targets and Properties)
- **Strict Rule:** Always use Modern CMake syntax focusing on targets and properties (`target_link_libraries`, `target_include_directories`). 
- **Avoid Globals:** Never use legacy global commands like `include_directories()`, `link_directories()`, or `add_definitions()`. They pollute subdirectories and break dependency encapsulation.
- **Scope Visibility:** Always specify `PRIVATE`, `PUBLIC`, or `INTERFACE` when setting target properties to control dependency propagation strictly.

## 2. Project Structure & Out-of-Source Builds
- **Out-of-Source Only:** Never allow building inside the source directory. Enforce out-of-source builds (e.g., in a `build/` directory) to keep the repository clean.
- **Minimum Version:** Always declare a precise and modern minimum version at the top of the root file: `cmake_minimum_required(VERSION 3.15...)`.
- **Modularization:** For multi-module projects, use a main `CMakeLists.txt` at the root and include subdirectories using `add_subdirectory()`, each containing its own isolated target definitions.

## 3. Dependency Management
- **FindPackage & FetchContent:** Use `find_package()` for system-installed dependencies. For external Git repositories, use the `FetchContent` module to fetch, configure, and compile dependencies at build time automatically.

## 4. Compiler Flags & Options
- **Strict Warnings:** Enable high compiler warning levels (`-Wall -Wextra` for GCC/Clang) explicitly via target compile options: `target_compile_options(my_target PRIVATE -Wall -Wextra)`.
- **Build Types:** Do not hardcode optimization flags. Rely on standard CMake build types (`Debug`, `Release`, `RelWithDebInfo`) to let the build system manage configurations naturally.