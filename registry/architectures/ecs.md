# ECS (Entity-Component-System) : Best Practices & Architecture

## 1. The Golden Rule of ECS (Data-Oriented Design)
- **Composition over Inheritance:** ECS entirely rejects traditional Object-Oriented Programming (OOP). You do not create a `class Player inherits Entity`.
- **Strict Separation:**
  - **Entities:** Are nothing but unique integer IDs. They contain NO data and NO logic.
  - **Components:** Are pure data structures (Structs). They contain NO logic or methods.
  - **Systems:** Are pure functions. They contain ALL the logic, but hold NO state themselves.

## 2. Component Granularity
- **Keep them atomic:** Components should represent a single, atomic trait. Do not create a massive `PlayerComponent`. Instead, break it down into `Position`, `Velocity`, `Health`, and `Renderable`.
- **Reusability:** By keeping components atomic, a `Velocity` component can be attached to a Player, an Asteroid, or a Particle without duplicating code.

## 3. System Execution & Queries
- **Operate on Arrays:** Systems must iterate over contiguous arrays of components. For example, a `MovementSystem` queries the ECS registry for *all* entities that have both a `Position` and a `Velocity` component, and updates the positions in a tight loop.
- **Order of Execution:** Systems run every frame (or tick). You must explicitly define the order in which Systems execute to prevent race conditions (e.g., the `InputSystem` must run before the `MovementSystem`, which runs before the `RenderSystem`).

## 4. Performance & Cache Locality
- **Why ECS is fast:** The primary purpose of ECS is CPU Cache friendliness. By packing identical components tightly in memory arrays, the CPU can process thousands of entities without encountering cache misses.
- **Rule:** Do not store pointers/references to other objects inside your Components, as this breaks memory contiguity. Store Entity IDs instead.