# Three.js & WebGL Architecture Guidelines

These rules dictate how 3D scenes using Three.js (and React Three Fiber) must be structured. The absolute priority is to maintain 60+ FPS, prevent WebGL memory leaks, and optimize the render loop.

## 1. WebGL Memory Management (Disposal)
Unlike standard JavaScript, the browser's Garbage Collector cannot automatically free WebGL resources (VRAM) when a 3D object is removed from the scene.
* **Explicit Disposal:** You MUST explicitly call `.dispose()` on every `BufferGeometry`, `Material`, and `Texture` when a `Mesh` is removed from the scene or destroyed. 
* **Resource Sharing:** Never instantiate a new identical material or geometry for multiple meshes. Instantiate it once, store it in a variable, and pass the reference to all meshes that share that exact appearance.

## 2. The Render Loop (Performance)
The `requestAnimationFrame` loop runs 60 to 120 times per second. Any inefficiency here immediately degrades performance.
* **Zero Instantiation in Loop:** NEVER use the `new` keyword (e.g., `new THREE.Vector3()`, `new THREE.Quaternion()`) inside the render loop. Pre-allocate these mathematical objects globally or at the class/component level, and mutate them using `.copy()`, `.set()`, or `.lerp()` inside the loop.
* **Avoid Heavy Computations:** Do not run complex array filtering or physics calculations directly in the main render thread. Offload heavy structural updates outside the loop or to a Web Worker.

## 3. Lighting and Shadows
* **Limit Dynamic Lights:** Dynamic lights (PointLight, SpotLight) and especially shadow maps are extremely expensive. Limit their number. Prefer baked lighting (Lightmaps) for static environments.
* **Shadow Configuration:** Do not enable `castShadow` and `receiveShadow` globally. Only enable them on specific meshes that absolutely require them for visual fidelity.

## 4. React Three Fiber (R3F) Specifics
If using `@react-three/fiber` alongside React:
* **No React State for Animations:** NEVER use React `useState` or `setState` to drive 60fps animations (like updating a mesh's position or rotation). This causes a full React component tree re-render on every frame. 
* **Use Refs and useFrame:** To animate objects, attach a `useRef` to the mesh and mutate `ref.current.position` or `ref.current.rotation` directly inside the `useFrame` hook.
* **Component Isolation:** Wrap heavy 3D elements in their own isolated React components to prevent unrelated state changes from triggering re-renders of complex 3D trees.