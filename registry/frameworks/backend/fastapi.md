# FastAPI : Best Practices & Architecture

## 1. Dependency Injection (DI)
- **The Core Rule:** Never instantiate database sessions, external API clients, or stateful services globally or directly inside route functions.
- **Usage:** Always use FastAPI's `Depends()` to inject dependencies. This guarantees that resources (like DB connections) are isolated per request, easily mockable in testing, and automatically closed when the request ends via `yield` generators.

## 2. Separation of Models (Pydantic vs. ORM)
- **Strict Boundary:** Never leak your ORM models (e.g., SQLAlchemy/SQLModel classes) directly into your API responses.
- **DTOs:** Always define explicit Pydantic V2 `BaseModel` classes for incoming requests (`UserCreate`) and outgoing responses (`UserResponse`). This prevents accidental data leaks (like returning hashed passwords) when the database schema evolves.

## 3. Asynchronous Execution (The Event Loop)
- **Blocking the Loop:** FastAPI runs on an asynchronous event loop (AnyIO/Uvicorn). If you perform a heavy CPU-bound task (e.g., image resizing, heavy math, or synchronous file I/O) inside an `async def` route, you will block the entire server for all other users.
- **Rule:** If a function does not use `await` (e.g., synchronous DB driver, heavy computation), declare the route as a standard `def` instead of `async def`. FastAPI will automatically push it to an external thread pool.

## 4. Modular Routing
- **No Monoliths:** Never write all endpoints in `main.py`.
- **APIRouter:** Group domain-specific endpoints (e.g., `/users`, `/products`) using `APIRouter` in separate files, and include them in the main app using `app.include_router(prefix="/api/v1/...")`.