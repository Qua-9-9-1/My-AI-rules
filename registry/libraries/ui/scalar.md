# Scalar (API Reference) : Best Practices & Architecture

## 1. Integration Isolation
- **Environment Targeting:** API documentation UIs are incredibly useful in development but can act as an attack map in production.
- **Rule:** Conditionally mount the Scalar UI route only in `development` or `staging` environments. If it must be exposed in `production`, place it behind a strict authentication middleware (e.g., HTTP Basic Auth or internal VPN restriction).

## 2. Endpoint Hiding
- **Internal APIs:** Not all endpoints belong in the public documentation (e.g., health checks, internal admin triggers).
- **Rule:** Use the framework's native tools (like `include_in_schema=False` in FastAPI) to explicitly hide utility endpoints from the OpenAPI JSON, ensuring Scalar only renders the actual business API.

## 3. Customization & Theme
- **Brand Consistency:** Do not settle for the default theme if building a public API portal. Utilize Scalar's configuration object to set explicit brand colors (`primary`, `background`) and a custom `layout` (e.g., `modern` or `classic`) to ensure the documentation feels native to your product's ecosystem.