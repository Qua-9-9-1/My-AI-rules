# Domain-Driven Design (DDD) Guidelines

This project strictly adheres to Domain-Driven Design tactical patterns. The absolute priority is to model the business accurately using Ubiquitous Language, encapsulate invariants, and strictly avoid Anemic Domain Models.

## 1. Rich Domain Models (Anti-Anemia)
* **Behavior, Not Just Data:** Domain Entities must encapsulate both state and behavior. 
* **No Public Setters:** The properties of an Entity must NEVER have public setters. State mutation must only occur through explicitly named public methods that represent real business actions (e.g., `user.ChangePassword(newPassword)` instead of `user.Password = newPassword`).
* **Always Valid State:** An Entity must guarantee its own invariants. It must be impossible to instantiate an Entity in an invalid state. Constructors or Factory methods must strictly validate all inputs before creating the object.

## 2. Aggregates and Aggregate Roots
* **Consistency Boundaries:** Group related Entities and Value Objects into Aggregates. 
* **The Root Entity:** Each Aggregate must have a single Aggregate Root. External objects are strictly forbidden from holding references to anything inside the Aggregate other than the Root. 
* **Transactional Scope:** A single database transaction should only modify a single Aggregate. If modifying one Aggregate requires changes to another, use Domain Events to achieve eventual consistency.

## 3. Value Objects (Primitive Obsession)
* **Eradicate Primitives:** Do not use primitive types (string, int) to represent domain concepts if they have specific rules. (e.g., do not use a `string` for an Email address; create an `Email` Value Object that validates itself upon creation).
* **Immutability:** Value Objects must be strictly immutable. If a value needs to change, a completely new instance of the Value Object must be created and assigned.
* **Equality:** Value Objects are defined by their attributes, not their identity. Two `Money` objects with the same currency and amount must evaluate as mathematically equal.

## 4. Domain Events
* **Side Effects:** When a significant business action occurs within an Aggregate (e.g., `OrderPlaced`), the Aggregate should generate and record a Domain Event. 
* **Decoupling:** Do not trigger external APIs, send emails, or write to external databases directly from within the Domain Entity. The Entity only raises the event; a separate Event Handler (in the Application or Infrastructure layer) listens for the event and performs the external action.

## 5. Ubiquitous Language
* **Business Terminology:** The names of classes, methods, and variables MUST strictly match the terminology used by the business/domain experts. Do not invent technical synonyms (e.g., if the business says "Guest", do not name the class `UnregisteredUser`).