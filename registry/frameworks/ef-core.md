# Entity Framework Core Architecture Guidelines

These rules dictate how data access using EF Core must be written. The absolute priority is to prevent memory leaks, eliminate N+1 query problems, and ensure asynchronous database interactions.

## 1. Tracking & Memory Optimization
* **`AsNoTracking` by Default:** For any read-only query (where the retrieved entities will not be updated or deleted in the same transaction), you MUST append `.AsNoTracking()`. This disables the EF Core change tracker, drastically reducing memory consumption and CPU overhead.
* **Projections over Entities:** When only a subset of columns is needed for an API response, do not fetch the entire entity. Use `.Select()` to project the data directly into a Data Transfer Object (DTO) or an anonymous type.

## 2. Preventing N+1 Queries
* **Explicit Eager Loading:** Never rely on lazy loading (it is strictly forbidden). If an entity requires its related data (children/parents), explicitly use `.Include()` and `.ThenInclude()`.
* **Batching:** If you need to fetch related data for a large list of items, structure the query to fetch them in a single database round-trip, rather than iterating through the list and querying the database inside a `foreach` loop.

## 3. Asynchronous Operations
* **Async I/O Only:** Every operation that communicates with the database must be asynchronous. Strictly use `ToListAsync()`, `FirstOrDefaultAsync()`, `AnyAsync()`, and `SaveChangesAsync()`.
* **Never Block Threads:** Do not use `.Wait()`, `.Result`, or synchronous equivalents like `.ToList()` on `IQueryable` database queries.
* **CancellationToken:** Always pass the `CancellationToken` from the API endpoint down to the EF Core async methods.

## 4. Architecture & IQueryable Leaks
* **Do Not Leak `IQueryable<T>`:** The `IQueryable<T>` interface represents an unexecuted query. It must never leak out of the Data Access Layer (or Repository layer). The query must be materialized (e.g., via `ToListAsync()`) before being returned to the business logic or UI layer. This prevents the UI from accidentally modifying the SQL query or causing unexpected database hits during rendering.

## 5. Migrations & Database Updates
* **No Runtime Migrations:** Never use `db.Database.Migrate()` or `db.Database.EnsureCreated()` automatically on application startup in production code. Database schema updates must be handled via an explicit CI/CD pipeline or migration script, completely separated from the application's runtime code.
* **Explicit Transactions:** For operations that modify multiple tables and require atomicity, explicitly wrap the operations in an `IDbContextTransaction` to ensure a safe rollback if any step fails.