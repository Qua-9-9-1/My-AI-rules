# PostgreSQL : Best Practices & Architecture

## 1. Connection Pooling
- **The Thread Limit:** PostgreSQL forks a heavy OS process for every connection. Having your server open hundreds of direct connections will crash the database due to memory exhaustion.
- **Rule:** Always place a connection pooler (like PgBouncer) in front of the database, or strictly limit the pool size in your application's ORM (e.g., maximum 10-20 connections per Node.js instance).

## 2. Indexing Strategy
- **Foreign Keys:** Always index foreign keys. PostgreSQL does not do this automatically. Deleting a parent row will result in a full table scan of the child table if the foreign key is not indexed, causing severe locks.
- **JSONB:** When storing JSON data, use the `JSONB` binary format (never `JSON`). Create GIN (Generalized Inverted Index) indexes on JSONB columns to allow ultra-fast querying of nested keys.

## 3. Migrations & Schema Changes
- **Transactional Migrations:** DDL operations (like `ALTER TABLE`) in PostgreSQL are transactional. Always wrap schema migrations in a transaction (`BEGIN; ... COMMIT;`).
- **Zero-Downtime:** Never lock a massive table in production. Use `CREATE INDEX CONCURRENTLY` instead of standard `CREATE INDEX` to build indexes in the background without blocking writes.