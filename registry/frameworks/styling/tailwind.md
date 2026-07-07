# Tailwind CSS : Best Practices & Architecture

## 1. The Utility-First Principle
- Avoid `@apply`: Do not use `@apply` in custom CSS files unless absolutely necessary (e.g., for third-party library overrides). It defeats the purpose of Tailwind and bloats the CSS bundle.
- Keep classes in HTML/JSX: Write classes directly on the elements to ensure the compiler can purge unused styles perfectly.

## 2. Component Extraction
- Extract via components, not CSS: If a button style is reused, create a React/Vue Component (e.g., `<Button>`) instead of creating a `.btn` CSS class.

## 3. Configuration & Tokens
- Use `tailwind.config.js` as the single source of truth for design tokens.
- Extend, do not overwrite: Always use the `extend` key in the theme configuration to add custom colors or fonts, preserving default Tailwind utilities.

## 4. Readability
- Order classes logically: Group classes by layout, spacing, typography, then visual modifiers (or use plugins like `prettier-plugin-tailwindcss` to enforce this automatically).