# MongoDB : Best Practices & Architecture

## 1. Schema Design (Embedding vs. Referencing)
- **Data That is Accessed Together, Stored Together:** If an entity's data is only relevant in the context of its parent (e.g., an address inside a user profile), embed it as a sub-document. 
- **The Unbounded Array Trap:** Never embed an array that can grow infinitely (e.g., embedding millions of `logs` inside a `server` document). MongoDB documents have a hard 16MB limit. For unbounded data, use referencing (store ObjectIDs) and query them separately.

## 2. Indexing and Sorting
- **ESR Rule:** When creating compound indexes, follow the Exact-Sort-Range (ESR) rule. Place fields tested for Exact matches first, fields used for Sorting second, and fields used for Range filters (`$gt`, `$lt`) last. This optimizes the B-Tree traversal.

## 3. Aggregation vs. Application-Side Joins
- **Network Efficiency:** Do not fetch thousands of documents into your application's RAM to filter or group them in Python or JavaScript. 
- **Rule:** Use the MongoDB Aggregation Pipeline (`$match`, `$group`, `$lookup`) to let the database engine process the data in C++ and return only the final computed result.