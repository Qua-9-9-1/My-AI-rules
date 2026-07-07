# SQLite Infrastructure Guidelines

These guidelines define best practices for incorporating and managing SQLite as an embedded relational database, focusing on concurrency, performance optimization, and EF Core migrations.

---

## 1. Concurrency & Performance Tuning

* **Enable Write-Ahead Logging (WAL)**: By default, SQLite blocks readers during write transactions. Enforce WAL mode (`PRAGMA journal_mode=WAL;`) to allow concurrent reads while a write operation is active.
* **Configure Busy Timeout**: Since SQLite locks the database file during writes, write operations from multiple threads can trigger "Database is locked" exceptions. Set a busy timeout connection property (e.g., `busy_timeout=5000;`) to wait before throwing.
* **Tune Synchronization Settings**: For non-critical configuration stores where speed is prioritized over strict transactional hardware-crash safety, consider setting synchronous mode to `NORMAL` (`PRAGMA synchronous=NORMAL;`) when WAL is active.

---

## 2. EF Core Integration & Schema Migrations

* **Handle Schema Modification Limits**: SQLite has limited support for SQL DDL operations. Column drops, renaming, and primary/foreign key alterations cannot be executed directly via standard migrations on older engines. Design migrations carefully and test on target systems.
* **Keep Data Types Compact**: SQLite does not support native high-precision data types like `decimal`. Decimal values are typically converted to strings or floating-point numbers. Be mindful of precision loss when mapping EF Core decimals to SQLite columns.
* **In-Memory SQLite for Tests**: For fast-running integration tests, use an in-memory SQLite provider connection (`DataSource=:memory:`). Keep the physical connection open for the duration of the test run to prevent the database schema from being dropped from memory.
