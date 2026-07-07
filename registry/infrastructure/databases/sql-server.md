# Microsoft SQL Server Guidelines

These rules define standard practices for developing database interactions with Microsoft SQL Server, focusing on query performance, parameterized safety, and connection resiliency.

---

## 1. Security & Query Parameterization

* **Mandatory Parameterization**: Never concatenate variables directly into SQL command strings. Always use parameterized queries or command parameters (e.g., `SqlCommand.Parameters.AddWithValue` or EF Core interpolation) to eliminate SQL injection vulnerabilities and allow SQL Server to cache query execution plans.
* **Avoid SELECT \***: Explicitly define columns in select queries rather than using `SELECT *`. This decreases network load, minimizes IO footprint, and shields the application from structural database changes.

---

## 2. Connections & Resiliency

* **Maintain Connection Pooling**: Ensure connection pooling remains enabled. Dispose of database connections immediately upon work completion using `using` statements to return connections back to the pool.
* **Transient Fault Handling**: Configure retry logic for transient database connection errors (e.g., network drops, brief locks). When using Entity Framework Core, enable execution strategy retries (`options.EnableRetryOnFailure()`).

---

## 3. Query Optimization & Timeouts

* **Respect Timeout Settings**: Configure appropriate command timeouts depending on the operation's complexity. Massive analytical reports must use custom timeouts to avoid premature cancellation, while lightweight transactional queries should fail fast.
* **Indexing Strategy**: Ensure columns used inside `JOIN` clauses, `WHERE` criteria, or sorting `ORDER BY` operations have corresponding indexes. Avoid performing functional transformations on indexed columns within the `WHERE` clause (e.g., `WHERE YEAR(CreatedDate) = 2026`) as this prevents index usage (non-sargable query).
