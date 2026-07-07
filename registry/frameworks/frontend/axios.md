# Axios : Best Practices & Architecture

## 1. Instantiation
- Never use the global `axios` object for API calls.
- Always create dedicated instances using `axios.create()`. This allows configuring specific base URLs, timeouts, and headers per service.

## 2. Interceptors
- Use interceptors for cross-cutting concerns: Inject authentication tokens (Request Interceptor) and handle global errors like 401 Unauthorized (Response Interceptor) in one central place.
- Keep interceptors clean: Do not put business logic inside interceptors.

## 3. Error Handling
- Avoid silent failures: Catch `AxiosError` specifically and map it to a domain-level error before passing it to the UI or calling function.
- Standardize responses: Create a wrapper function around Axios calls to return a consistent data structure (e.g., `{ data, error }`).

## 4. Typing (TypeScript)
- Always pass generics to Axios calls to type the expected response: `axios.get<MyUserType>('/users')`.