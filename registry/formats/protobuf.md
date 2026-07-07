# Protocol Buffers (Protobuf) : Best Practices & Architecture

## 1. Tag Mutability (CRITICAL)
- **The Golden Rule:** Never, under any circumstances, change the integer tag number of an existing field. The tag number is the only way Protobuf identifies fields across the wire. Changing it will break backward and forward compatibility, corrupting data.

## 2. Handling Deletions (Deprecation)
- **Do not delete fields:** If a field is no longer used, do not simply delete its line. 
- **Rule:** Mark the field as `[deprecated = true]` or delete it and explicitly add its tag number and name to the `reserved` list. 
  - *Example:* `reserved 2, 15, 9 to 11;`
  - *Example:* `reserved "old_field_name";`
  This prevents future developers from accidentally reusing the tag number.

## 3. Schema Design
- **Flat is Better:** Keep your message structures flat. Avoid deeply nested message definitions. Define messages at the top level and reference them, which makes them reusable across different RPCs.
- **Enums:** Always define the first value (tag `0`) of an enum as an `UNKNOWN` or `UNSPECIFIED` default state. Protobuf initializes enums to `0` by default; if `0` is a valid business state, you cannot distinguish between an intentionally set value and a missing value.