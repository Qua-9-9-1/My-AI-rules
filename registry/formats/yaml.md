# YAML (YAML Ain't Markup Language) : Best Practices & Architecture

## 1. Security: Arbitrary Code Execution
- **The Threat:** In many languages (especially Python and Ruby), default YAML parsers can instantiate arbitrary objects, leading to remote code execution (RCE) vulnerabilities.
- **Rule:** ALWAYS use safe loading functions. Never use `yaml.load()`. Strictly use `yaml.safe_load()` (or equivalent in your ecosystem) to ensure only standard data types are parsed.

## 2. The "Norway Problem" (Boolean Coercion)
- **The Trap:** In YAML 1.1, words like `yes`, `no`, `on`, `off`, and `NO` (Norway's country code) are implicitly converted to booleans (`true`/`false`).
- **Rule:** Always explicitly wrap string values that could be misinterpreted in quotes. Example: `country: "NO"`, `version: "2.0"` (to prevent it becoming the float `2.0`).

## 3. Formatting & Readability
- **Indentation:** Strictly use 2 spaces for indentation. Never use tabs. Rely on a `.editorconfig` file to enforce this across the repository.
- **Complexity Limit:** YAML is meant to be human-readable. Do not use advanced, obscure YAML features like complex node anchors (`&`) and aliases (`*`) if it makes the file difficult for a junior developer to read. Prefer explicitness over cleverness.