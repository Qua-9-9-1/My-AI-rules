# Blazor WebAssembly Guidelines

These rules dictate how Blazor WebAssembly components, view models, and UI logic must be structured to ensure responsive, testable, and memory-efficient client-side web applications.

---

## 1. Component Structure & Razor Hygiene

* **Separate View Logic from Markup**: Keep Razor markup clean. Extract complex business rules, UI event calculations, and data loading into partial C# backend classes (`MyComponent.razor.cs`) or dedicated ViewModels.
* **Parameter Validation**: Validate all component parameters (`[Parameter]`) inside `OnParametersSet` or `OnParametersSetAsync`. Check for nulls and enforce component input contracts.
* **Component Size and Reusability**: Decompose oversized pages and layouts into small, focused, reusable child components that address a single UI concern (e.g., custom form inputs, search filters, loading overlays).

---

## 2. Component Lifecycle & Async Rendering

* **Asynchronous Initialization**: Utilize `OnInitializedAsync` for fetching initial data. Keep CPU-bound blocking operations out of this method to avoid freezing the UI thread during rendering.
* **Dispose Subscription Resources**: If a component implements `IDisposable` or subscribes to static event publishers or shared state events, always unsubscribe from these events in the `Dispose` method to prevent memory leaks.
* **Safe UI Thread Scheduling**: When modifying UI-bound variables from async background tasks (e.g., timers, hub connections, or background services), wrap the assignments inside `InvokeAsync(StateHasChanged)` to guarantee execution on the Blazor synchronization context.

---

## 3. Dependency Injection in Blazor WASM

* **Understand Client-Side Scopes**: In Blazor WebAssembly, `Scoped` services behave identically to `Singleton` services because the entire application executes inside a single browser tab context. Design services accordingly.
* **Avoid Hardcoded HTTP Clients**: Do not configure base URLs directly inside components. Inject pre-configured typed `HttpClient` instances or use `IHttpClientFactory` to communicate with backend endpoints.

---

## 4. Component Communication & State Management

* **Prefer EventCallback**: Use `EventCallback` or `EventCallback<T>` for child-to-parent communication. Avoid passing action delegates (`Action` / `Func`), as `EventCallback` automatically triggers re-rendering of the parent component.
* **State Container Pattern**: For complex cross-component state synchronization (e.g., active user sessions, global theme preferences, or temporary workflows), implement a centralized state container service and inject it where needed, rather than chaining parameters down multiple levels.
