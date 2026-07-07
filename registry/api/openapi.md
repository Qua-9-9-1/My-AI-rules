# OpenAPI (Specification) : Best Practices & Architecture

## 1. Schema-First vs. Code-First
- **Code-First (FastAPI):** If the framework generates the OpenAPI spec automatically, ensure you utilize all available metadata decorators (tags, summaries, descriptions) in the code to keep the generated spec rich and human-readable.
- **Schema-First:** If writing the `openapi.yaml` manually, this file becomes the single source of truth. The backend code must be tested against this contract to ensure no drift occurs between the documentation and the actual implementation.

## 2. Rich Descriptions & Examples
- **Mandatory Fields:** Every endpoint must have a `summary` and a detailed `description`. 
- **Examples:** Always provide realistic `example` or `examples` objects for complex JSON bodies. This drastically accelerates frontend development and third-party integration by removing ambiguity.

## 3. Strict Status Code Mapping
- **Do not default to 200/500:** Explicitly document all possible failure states (400, 401, 403, 404, 422, 429). 
- **Error Schemas:** Define a standardized `HTTPValidationError` or `ProblemDetails` schema and reuse it across all error responses using `$ref`.

## 4. Security Definitions
- **Explicit Security Requirements:** Define global `securitySchemes` (e.g., HTTP Bearer, OAuth2). For endpoints that are public, explicitly override the security requirement with an empty array (`security: []`) to clearly signal that no authentication is required.