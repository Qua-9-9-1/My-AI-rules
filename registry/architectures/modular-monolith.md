# Modular Monolith Architecture Guidelines

This project strictly follows the Modular Monolith architectural pattern. The absolute priority is to maintain strict isolation between domain modules (Bounded Contexts) within a single deployable unit.

## 1. Module Isolation (Bounded Contexts)
* **Strict Boundaries:** The application is divided into distinct, highly cohesive modules (e.g., `Billing`, `Inventory`, `Users`). 
* **No Internal Cross-Imports:** A module is strictly forbidden from importing internal classes, services, or entities from another module. 
* **Public Contracts:** Each module must expose a clearly defined public API (an interface, a facade, or an event publisher). Other modules can only interact with this public contract.

## 2. Database & Data Ownership
Data coupling is the primary cause of architectural decay in monoliths.
* **Exclusive Ownership:** Each module owns its data and its database schema (tables/collections). 
* **No Cross-Module Queries:** Module A is strictly forbidden from executing SQL queries or ORM calls against Module B's tables.
* **Data Integration:** If Module A needs data from Module B, it must either call Module B's public API or subscribe to Module B's domain events to keep a localized, read-only copy of the necessary data.

## 3. Inter-Module Communication
* **Synchronous Calls:** Use direct method calls via the public interfaces (Facades) for operations that must be strictly consistent and immediate.
* **Asynchronous / Event-Driven:** Use an in-memory event bus (e.g., MediatR in C#, EventEmitter in Node.js) for side effects and eventual consistency to decouple the modules further. 

## 4. The Shared Kernel
* **Restricted Scope:** Code shared across multiple modules must be placed in a `SharedKernel` or `Common` directory.
* **No Business Logic:** The Shared Kernel must only contain infrastructure abstractions, fundamental utility types (e.g., custom Date or Money value objects), or base classes. It must NEVER contain domain-specific business rules. Changes to the Shared Kernel affect the entire system and must be minimized.