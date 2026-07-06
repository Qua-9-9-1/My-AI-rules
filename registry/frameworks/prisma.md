# Prisma ORM Architecture Guidelines

These rules dictate how Prisma must be used for data access. The absolute priority is preventing N+1 query problems, optimizing memory by selecting specific fields, and abstracting the Prisma Client.

## 1. Memory Optimization (Selections)
* **Explicit `select`:** When querying the database, NEVER fetch entire rows if only a few columns are needed. Always use the `select` object to return only the specific fields required by the frontend or the business logic. 
* *Why:* Node.js memory is limited. Fetching 50 unneeded text columns for 1000 users will crash the V8 engine.

## 2. Relational Data (Eager Loading)
* **Use `include` Explicitly:** To avoid the N+1 query problem, always fetch related data in a single round-trip using `include`. Never run a Prisma query inside a `for` or `.map()` loop.
* **Nested Pagination:** When including related records (e.g., fetching a user and their posts), apply limits to the relation if the dataset could be large (e.g., `posts: { take: 10 }`).

## 3. Prisma Client Abstraction
* **Dedicated PrismaService:** The `PrismaClient` must NEVER be instantiated directly in business logic services or controllers. It must be wrapped in an injectable `PrismaService` that handles connection management (`$connect` and `$disconnect` hooks).
* **Do Not Leak Prisma Types to Controllers:** The raw Prisma generated types (e.g., `User` from `@prisma/client`) should ideally be mapped to application-specific Entities or DTOs before leaving the Service layer. This prevents the UI/Controllers from becoming tightly coupled to the database schema.

## 4. Heavy Computations
* **Database over JavaScript:** If sorting, filtering, or aggregating (SUM, COUNT) can be done natively via Prisma's query engine, do it in the query. Do not fetch arrays of data into Node.js just to use `.filter()` or `.reduce()` in memory.