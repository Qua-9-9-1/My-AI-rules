# Redis : Best Practices & Architecture

## 1. Time-To-Live (TTL) & Eviction
- **The Memory Trap:** Redis stores everything in RAM. If you use Redis as a cache without setting expirations, the server will eventually crash with an Out Of Memory (OOM) error.
- **Rule:** ALWAYS set a TTL (Time-To-Live) on cache keys using `EXPIRE` or `SETEX`. Configure an `maxmemory-policy` (like `allkeys-lru`) in the `redis.conf` to automatically evict the least recently used keys when memory is full.

## 2. The KEYS Command Threat
- **Blocking Operation:** Redis is single-threaded. Running the `KEYS *` command on a production server with millions of keys will freeze the entire server for seconds, dropping all other connections.
- **Rule:** Never use `KEYS` in production. Always use the `SCAN` cursor-based command to iterate over keys without blocking the event loop.

## 3. Data Structure Optimization
- **Do not serialize large objects:** Instead of storing a massive JSON string in a standard key, evaluate if a `HASH` (`HSET`) is better. Hashes allow you to fetch or update a single field (like a user's score) without downloading and parsing the entire object over the network.