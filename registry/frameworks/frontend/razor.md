# Blazor & Razor Architecture Guidelines

These rules dictate how Blazor applications (WebAssembly or Server) must be structured. The absolute priority is enforcing the Separation of Concerns (UI vs. Logic) and preventing direct data access from the presentation layer.

## 1. Strict Code-Behind Separation
* **No Complex `@code` Blocks:** The `@code { ... }` block inside `.razor` files must only be used for trivial UI bindings (e.g., toggling a boolean). 
* **Mandatory Partial Classes:** All logic, state management, and lifecycle methods must be extracted into a strictly typed code-behind file (e.g., `Component.razor.cs` as a `partial class`).

## 2. Dependency Injection & Data Access
* **No Direct DB Access:** UI components must never inject `DbContext` or interact with the database directly. 
* **Service Layer Only:** Components must only inject Abstractions/Interfaces (e.g., `IUserService`) to fetch or mutate data. The component's only job is to display the data provided by the service.

## 3. Component Lifecycle & Async Safety
* **Acknowledge `OnAfterRenderAsync`:** Do not attempt to interact with the DOM or perform JavaScript Interop (`IJSRuntime`) during `OnInitialized` or `OnParametersSet`. The DOM only exists in `OnAfterRender(Async)`.
* **StateHasChanged Limit:** Calling `StateHasChanged()` manually is a code smell. It indicates that the state reactivity is not properly bound or that background threads are mutating state without synchronization. Only use it when bridging external non-Blazor events (like a vanilla JS callback or a global `Action` delegate).

## 4. Parameters and Data Flow
* **Immutable Parameters:** Never mutate a `[Parameter]` property from within the component itself. Parameters represent data flowing down from the parent. If the child needs to modify this data, it must use an `EventCallback` to notify the parent to make the change.
* **Limit Cascading Parameters:** Use `[CascadingParameter]` sparingly and strictly for truly global UI context (e.g., Theme, Layout, User Identity). Do not use it as a shortcut to bypass standard parameter passing, as it degrades rendering performance and obscures data dependencies.

## 5. JavaScript Interop (JSInterop)
* **Isolation:** JavaScript calls must be strictly encapsulated. Never call `IJSRuntime.InvokeVoidAsync` directly inside a component's logic. Wrap the interop logic inside a dedicated C# service (e.g., `BrowserStorageService`) to keep the components pure and testable.