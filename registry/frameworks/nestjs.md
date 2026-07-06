# NestJS Architecture Guidelines

These rules dictate how NestJS server applications must be structured. The absolute priority is strict layered architecture, Dependency Injection (DI) hygiene, and robust input validation.

## 1. Strict Layered Architecture
* **Controllers are Dumb:** Controllers must NEVER contain business logic. Their only responsibilities are routing HTTP requests, validating DTOs, and returning responses. They must immediately delegate work to a Service.
* **Services handle Business Logic:** Services (`@Injectable()`) contain the core business rules. They must not directly manipulate the HTTP `Request` or `Response` objects (keep them framework-agnostic).
* **Repository/DAO Layer:** Services should not interact directly with the database client (like Prisma). They must call a dedicated Data Access Layer or Repository service.

## 2. Dependency Injection & Modules
* **No Manual Instantiation:** Never use `new Service()` to instantiate a class that is part of the application logic. Everything must be registered in a `@Module()` and injected via the constructor.
* **Module Isolation:** Group related components (Controller, Service, Repository) into cohesive feature Modules (e.g., `UserModule`). Export only what is strictly necessary. Do not make everything global.

## 3. Input Validation (DTOs)
* **Strict DTOs:** Every incoming payload (POST/PUT) and query parameter must be mapped to a Data Transfer Object (DTO) class.
* **Validation Pipeline:** The application must have the global `ValidationPipe` enabled with `whitelist: true` and `forbidNonWhitelisted: true`. The IA must decorate DTO fields with `class-validator` decorators (e.g., `@IsString()`, `@IsEmail()`).

## 4. Error Handling
* **No Try/Catch in Controllers:** Do not wrap controller methods in `try/catch` blocks just to throw HTTP exceptions. Let the Services throw specific domain exceptions (or standard NestJS `HttpException`), and let NestJS's built-in global Exception Filters catch and format them.