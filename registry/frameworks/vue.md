# Vue.js Architecture Guidelines

These rules dictate how Vue applications must be structured. The absolute priority is to enforce Vue 3 modern standards, maintain reactivity integrity, and ensure strict one-way data flow.

## 1. The Composition API Standard
* **Strictly `<script setup>`:** All Single-File Components (SFCs) must use the `<script setup lang="ts">` syntax. The legacy Options API (`export default { data(), methods: {} }`) is strictly forbidden.
* **Composables for Business Logic:** Do not bloat the component script with complex logic. Extract state management, API calls, and domain logic into isolated, reusable Composables (e.g., `useUserSession.ts`). The component should only act as a bridge between the Composable and the template.

## 2. Reactivity & State
Vue's reactivity system relies on Proxies. Mishandling them breaks the UI silently.
* **Default to `ref`:** Always use `ref()` for state declaration, even for objects and arrays. It provides consistency and prevents reactivity loss when reassigning the variable. Use `reactive()` only for highly cohesive state objects that are never reassigned.
* **No Destructuring:** Never destructure a `reactive` object or the `props` object directly in the script block (e.g., `const { name } = props;`), as this destroys reactivity. If destructuring is mathematically necessary, use `toRefs()`.

## 3. Computed Properties vs. Watchers
* **Prefer `computed`:** If a value can be derived from an existing reactive state, always use a `computed()` property. 
* **Limit `watch` and `watchEffect`:** Watchers are for side effects (e.g., triggering a network request when an ID changes, or manual DOM manipulation). Never use a `watch` to simply update another piece of local state based on a change; use `computed` instead to prevent infinite update loops.

## 4. Props, Emits & One-Way Data Flow
* **Props are Read-Only:** Never attempt to mutate a prop inside a child component. This violates the one-way data flow principle. If a child needs to modify a prop, it must emit an event to the parent.
* **Explicit Emits:** All events emitted by a component must be explicitly declared using `defineEmits`. Magic strings in `$emit` inside the template should be avoided in favor of typed script-side functions.

## 5. TypeScript Integration (SFCs)
* **Type-Only Prop Declarations:** Always use TypeScript's type-based declaration for props and emits to leverage compiler safety.
    * *Good:* `defineProps<{ id: number; name: string }>()`
    * *Bad:* `defineProps({ id: Number, name: String })`
* **V-Model Typing:** When creating custom inputs, strictly type the `v-model` using `defineModel` (if available in the Vue version) or explicitly type the `modelValue` prop and `update:modelValue` emit.