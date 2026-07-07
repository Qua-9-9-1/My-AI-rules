# GraphQL : Best Practices & Architecture

## 1. The N+1 Problem (Performance)
- **The Threat:** Resolvers executing database queries for nested fields inside a list will trigger a separate query for each item, instantly crashing database performance.
- **Rule:** NEVER write resolvers that hit the database directly inside a list context. Always use the **DataLoader** pattern to batch and cache related IDs into a single SQL query (`SELECT * WHERE id IN (...)`).

## 2. Security & Resource Exhaustion
- **Depth Limiting:** Attackers can craft deeply nested queries (e.g., `user { friends { user { friends... } } }`) to exhaust server RAM. Always enforce a strict query depth limit (e.g., max 5 to 7 levels).
- **Cost Analysis:** Implement query cost analysis (assigning a "weight" to expensive fields) to reject queries that demand too much compute power before execution.

## 3. Mutation Architecture
- **Single Input Object:** Always structure mutations to accept a single, strongly-typed `input` object rather than multiple scalar arguments. 
  - *Bad:* `createUser(name: String, age: Int)`
  - *Good:* `createUser(input: CreateUserInput!)`
- **Standardized Payloads:** Mutations should return a dedicated Payload object (e.g., `CreateUserPayload`) containing both the mutated node and standard error fields, allowing future schema expansion without breaking clients.

## 4. Nullability Strictness
- Be highly conservative with the non-null modifier (`!`). Only mark fields as non-null if the entire object is fundamentally invalid without them. If a field depends on a third-party service or a database join that could fail, leave it nullable so partial data can still be returned.