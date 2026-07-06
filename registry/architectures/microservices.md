# Microservices Architecture Guidelines

This project strictly follows the Microservices architectural pattern. The absolute priority is service autonomy, independent deployability, and fault tolerance against network unreliability.

## 1. Absolute Service Autonomy (Data Isolation)
* **Database per Service:** This is a non-negotiable rule. Multiple microservices must NEVER connect to the same physical database or schema. 
* **Data Duplication over Coupling:** If Service A needs data from Service B, it must not query Service B's database. Service A must subscribe to Service B's events and keep a local, eventually consistent projection of the required data.

## 2. Inter-Service Communication
* **Asynchronous by Default:** Prefer event-driven communication (e.g., Kafka, RabbitMQ) over synchronous HTTP/gRPC calls for business workflows. This prevents temporal coupling and cascading failures.
* **Synchronous Calls Restriction:** If a synchronous call (HTTP/REST or gRPC) is mathematically necessary (e.g., querying real-time data for an API Gateway), it must be treated as highly unstable. 

## 3. Resilience and Fault Tolerance
The network is inherently unreliable. The AI must design all external calls with the assumption that they will fail.
* **Timeouts:** Every synchronous network call MUST have a strict, short timeout configured. Infinite or default long timeouts are forbidden.
* **Circuit Breakers & Retries:** All synchronous calls must be wrapped in a Circuit Breaker pattern and implement exponential backoff retries for transient faults. 
* **Graceful Degradation:** If a downstream service is unavailable, the calling service must handle the failure gracefully (e.g., returning cached data or a default fallback response) instead of crashing.

## 4. Observability and Traceability
Debugging a distributed system requires strict tracing mechanisms.
* **Correlation IDs:** Every incoming request at the API Gateway must be assigned a unique Correlation ID (or Trace ID). This ID must be explicitly passed along in all subsequent HTTP headers or message broker payloads across all services.
* **Centralized Logging:** Logs must be structured (JSON format) and always include the Correlation ID, the Service Name, and the environment.

## 5. API Gateways & Boundaries
* **No Direct Client-to-Service Calls:** External clients (Web, Mobile) must never call individual microservices directly. All external traffic must route through an API Gateway (or BFF - Backend For Frontend) which handles authentication, routing, and rate limiting.