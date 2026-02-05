# Agent Instructions for CAF (C++ Actor Framework)

This document outlines the build, test, and style guidelines for AI agents working in this repository.

## 1. Build, Lint, and Test Commands

### Build System
The project uses **CMake**.
- **Configure**: `cmake -S . -B build` (or `./configure` on UNIX)
- **Build**: `cmake --build build` (compiles all targets)

### Testing
Tests use the internal `libcaf_test` library.
- **Run All Tests**:
  ```bash
  ctest --test-dir build
  ```
- **Run Single Test Suite**:
  Test suites are named based on the file path (dots replace slashes).
  Use `ctest -R <regex>` to match the test suite name.
  ```bash
  # To run tests from 'libcaf_core/caf/timestamp.test.cpp' (suite: caf.timestamp)
  ctest --test-dir build -R caf.timestamp
  ```
- **Verbose Output**: Use `-V` or `--output-on-failure` with `ctest` if debugging.

### Linting and Formatting
- **Formatter**: `clang-format` (version enforced by CI).
  - Config: `.clang-format` in root.
  - Run: `git ls-files *.hpp *.cpp | xargs clang-format -i`
- **Linter**: `clang-tidy` is configured (`.clang-tidy`).
- **Compiler Warnings**: Code must compile clean with `-Wall -Wextra -pedantic`.

## 2. Code Style Guidelines

Strictly adhere to the conventions in `CONTRIBUTING.md`.

### General C++ Rules
- **Standard**: C++17 (or C++20/23 depending on build config, but assume C++17 baseline compatibility).
- **No Exceptions**: Use `caf::error` and `caf::expected<T>` for error handling.
- **Resource Management**: Never use `new`/`delete`. Use `std::unique_ptr`, `std::shared_ptr`, or `caf::intrusive_ptr`.
- **Casts**: No C-style casts. Use `static_cast`, `const_cast`, etc.
- **Const Correctness**: Use "West const" (`const T&` not `T const&`).
- **Auto**: Use `auto` for variable declarations unless a specific type conversion is intended.
- **Variables**: One variable declaration per line.
- **Inline**: Implement small functions (<= 5 lines) inline in headers.

### Naming Conventions
- **Variables & Functions**: `snake_case` (e.g., `actor_system`, `spawn_typed`).
- **Types/Classes**: `snake_case` (e.g., `scoped_actor`, `event_based_actor`).
- **Template Parameters**: `CamelCase` (e.g., `Message`, `Handler`).
- **Private Members**: `snake_case_` (trailing underscore, e.g., `mutex_`, `value_`).
- **Getters/Setters**:
  - Getter: `value()` (no `get_` prefix).
  - Setter: `value(new_value)` (no `set_` prefix).
- **Macros**: `CAF_COMPONENT_NAME` (e.g., `CAF_LOG_TRACE`).

### File Organization
- **Extensions**:
  - Headers: `.hpp`
  - Sources: `.cpp`
  - Tests: `.test.cpp`
- **Imports**:
  - Internal: `#include "caf/path/to/file.hpp"`
  - External: `#include <system/header>`
  - Project Headers: `#include "caf/..."` (even inside the library).
- **Header Guards**: `#pragma once`.
- **Namespaces**:
  - Public: `caf`, `caf::net`, etc.
  - Internal (shared across translation units): `caf::internal` or `caf::net::internal`.
  - Implementation details (public header visible): `caf::detail`.

### Documentation
- **Format**: Doxygen with `///` comments.
- **Syntax**: Markdown allowed in comments.
- **Commands**: Use `@param`, `@return` (omit `@return` if brief starts with "Returns").

### Test Files
- **Macro**: Use `CAF_TEST_MAIN()` in the main test driver (handled by build system usually).
- **Structure**:
  ```cpp
  #include "caf/test/test.hpp"
  #include "caf/my_header.hpp"

  TEST("description of feature being tested") {
    check_eq(1 + 1, 2);
  }
  ```

## 3. Environment & Tool Specific Rules

### Cursor Rules (from .cursor/rules/general.mdc)

**How to build this project:**
- `cmake --build build`

**How to run tests:**
- `ctest --test-dir build` to run everything
- `ctest --test-dir build -R <name>` to run specific tests

**Coding conventions:**
- Adhere to `CONTRIBUTING.md`.

### Additional Context
- **Git**: Squash commits before PR. Message format: "Imperative summary" (50 chars), blank line, detailed description.
- **Platform**: Cross-platform (Linux, macOS, Windows). Ensure changes are portable.
