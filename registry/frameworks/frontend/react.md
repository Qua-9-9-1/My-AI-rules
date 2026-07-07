# React & TSX Architecture Guidelines

These rules dictate how React applications must be structured. The absolute priority is to prevent unnecessary re-renders, separate business logic from the UI, and eliminate side-effect loops.

## 1. Separation of Concerns (UI vs. Logic)
* **Dumb Components:** UI components (files ending in `.tsx`) must be as "dumb" as possible. Their only responsibility is to map props to JSX. 
* **Custom Hooks for Logic:** All complex state mutations, API calls, and business logic must be extracted into custom hooks (e.g., `useUserSession.ts`). The component should only call the hook and render the returned data.
* **No Inline Logic in JSX:** Avoid complex mathematical or logical operations directly inside the JSX return statement. Compute these values beforehand and assign them to clearly named variables.

## 2. State Management & Derived Data
* **Single Source of Truth:** Never duplicate data in the state. If a value can be computed (derived) from existing state or props, it must be calculated on the fly, not stored in a separate `useState`.
* **Immutability is Absolute:** Never mutate state arrays or objects directly (e.g., `state.push(item)`). Always return a new reference using the spread operator (`...`) or array methods like `.map()` and `.filter()`.
* **State Proximity:** Keep state as close as possible to where it is used. Do not lift state to global contexts or parent components unless multiple unlinked components require it.

## 3. The `useEffect` Strict Rules
The `useEffect` hook is an escape hatch for synchronizing with external systems, not a tool for data flow.
* **No State Syncing:** Never use `useEffect` to update a state variable based on the change of another state variable. This causes a chained reaction of unnecessary re-renders. Compute the change during the render phase instead.
* **Exhaustive Dependencies:** The dependency array must be strictly exhaustive. Never suppress the `eslint-disable-next-line react-hooks/exhaustive-deps` warning. If a dependency triggers an unwanted loop, fix the logic, do not lie to the linter.
* **Mandatory Cleanups:** Any effect that subscribes to an external event, sets a timeout, or opens a connection must return a cleanup function to prevent memory leaks when the component unmounts.

## 4. Performance & Memoization
* **Stable References:** Be aware that passing new object or function references as props to child components breaks `React.memo`. Use `useCallback` for functions and `useMemo` for complex derived data or objects passed as props down the tree.
* **Key Prop Uniqueness:** When rendering lists using `.map()`, the `key` prop must be a unique, stable identifier (like a database ID). Never use the array `index` as a key, as it corrupts the virtual DOM diffing algorithm if the list order changes.

## 5. TypeScript Integration (TSX)
* **Explicit Prop Interfaces:** Every component must have a strictly typed `interface` for its props, named `[ComponentName]Props`.
* **Return Types:** Standardize the return type of functional components. Prefer implicit return typing (letting TS infer `JSX.Element`) or strictly define it, but be consistent across the codebase.