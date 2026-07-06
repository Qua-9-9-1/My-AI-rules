# ASP.NET Core Architecture Guidelines

These rules dictate how ASP.NET Core Web APIs and backend services must be structured. The absolute priority is to ensure stateless scalability, strict Dependency Injection (DI) hygiene, and asynchronous safety.

## 1. API Design & Architecture
* **Prefer Minimal APIs / REPR Pattern:** For modern web services, prefer Minimal APIs over traditional MVC Controllers. Structure the API using the REPR (Request-Endpoint-Response) pattern where each endpoint is isolated in its own class or method, rather than grouping unrelated endpoints in massive `Controller` classes.
* **Statelessness:** The server must remain strictly stateless. Do not use in-memory Session state (`ISession`) for business logic. Authentication must rely on stateless tokens (e.g., JWT).

## 2. Dependency Injection (DI) Hygiene
Improper DI lifetimes cause memory leaks and concurrency crashes.
* **No Captive Dependencies:** Never inject a `Scoped` or `Transient` service into a `Singleton` service. 
* **Scope Definition:** * `Transient`: For lightweight, stateless helper services.
    * `Scoped`: For anything relying on the HTTP request context or the database (`DbContext`).
    * `Singleton`: Strictly for cache, configurations, or heavy factory objects intended to live for the application's entire lifetime.
* **No Service Locator:** Never inject `IServiceProvider` into a class to resolve dependencies manually. Always use constructor injection.

## 3. Middleware & Pipeline Execution
* **Strict Ordering:** The middleware pipeline in `Program.cs` is order-dependent. The exception handler must be the very first middleware. Authentication (`UseAuthentication`) must absolutely precede Authorization (`UseAuthorization`), and both must precede endpoint mapping.
* **Global Exception Handling:** Do not write `try/catch` blocks in every endpoint just to return a 500 error. Use a global exception handling middleware or `IExceptionHandler` to intercept unhandled exceptions, log them centrally, and return a standardized ProblemDetails JSON response.

## 4. Configuration & Options Pattern
* **The Options Pattern:** Never inject `IConfiguration` directly into business logic or services to read `appsettings.json`. Always bind configuration sections to strongly typed classes and inject them using `IOptions<T>`, `IOptionsSnapshot<T>`, or `IOptionsMonitor<T>`.
* **No Hardcoded Secrets:** Never hardcode connection strings, API keys, or JWT secrets in the code or commit them to source control. They must be loaded via Environment Variables or a Secret Manager.

## 5. Async & Resource Management
* **Mandatory CancellationTokens:** Every single asynchronous endpoint must accept a `CancellationToken` as a parameter. This token must be explicitly passed down to all downstream async methods (database calls, HTTP clients) to immediately cancel operations if the client disconnects.
* **HttpClientFactory:** Never manually instantiate `new HttpClient()`. This leads to socket exhaustion. Always use `IHttpClientFactory` or typed clients registered in the DI container.