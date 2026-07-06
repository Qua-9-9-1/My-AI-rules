# Event-Driven & Serverless Architecture Guidelines

This project strictly follows the Event-Driven and Serverless architectural patterns. The absolute priority is to ensure total function ephemerality, strict message schema validation, and defensive handling of distributed state.

## 1. Absolute Statelessness & Ephemerality
* **No Local State:** Serverless functions (e.g., AWS Lambda, Azure Functions) are ephemeral. They must never store state in local variables or memory across invocations. Every invocation must assume a completely clean environment.
* **Fast Startup (Cold Starts):** Minimize package size and dependencies within individual functions. Avoid heavy initialization logic inside the function handler. Move database connection pooling and configuration loading outside the handler to leverage container reuse.

## 2. Strict Event Schemas & Versioning
In an event-driven system, the event payload is the public contract. Breaking the schema breaks the entire system silently.
* **Schema Registry:** Every event payload must strictly adhere to a predefined schema (e.g., JSON Schema, Avro, or Protocol Buffers). 
* **Backward Compatibility:** When modifying an event structure, changes must be backward-compatible (e.g., adding optional fields is allowed, removing fields or changing types is forbidden). If a breaking change is mathematically mandatory, a new event type or version (e.g., `OrderPlacedV2`) must be created.

## 3. Idempotency (At-Least-Once Delivery)
Distributed message brokers (Kafka, RabbitMQ, SQS) generally guarantee "at-least-once" delivery, meaning the same event can and will be delivered multiple times due to network retries.
* **Mandatory Idempotency:** Every event handler must be strictly idempotent. Processing the exact same event multiple times must produce the exact same system state.
* **Deduplication Mechanism:** Implement an explicit deduplication mechanism using an idempotency key (e.g., storing the unique `EventID` in a fast cache like Redis or a database unique constraint before executing the business logic).

## 4. Error Handling & Dead Letter Queues (DLQ)
A crashing function in an event-driven pipeline can block the entire queue or cause infinite retry loops.
* **Never Drop Events:** Unhandled exceptions must not cause events to vanish. 
* **Dead Letter Queues:** If an event fails processing after a strictly defined number of retries (e.g., 3 retries with exponential backoff), the infrastructure must automatically route the poisonous event to a Dead Letter Queue (DLQ) for human inspection and manual triage.

## 5. Distributed Tracing & Correlation IDs
* **Trace Propagation:** Because execution flows asynchronously across multiple isolated functions, every event payload must include metadata containing a unique `CorrelationID` and `SourceServiceID`. 
* **Logging:** The function must immediately extract this `CorrelationID` upon invocation and inject it into every log statement to allow end-to-end tracing of a single user action across the entire event graph.