# Webpack : Best Practices & Architecture

## 1. Modular Configuration
- **Environment Separation:** Never use a single monolithic `webpack.config.js` for both development and production. Create three distinct files: `webpack.common.js`, `webpack.dev.js`, and `webpack.prod.js`.
- **Composition:** Merge configurations dynamically using the `webpack-merge` utility library within the deployment scripts.

## 2. Performance & Code Splitting
- **Dynamic Imports:** Enforce the use of dynamic `import()` syntax for route-level components or heavy third-party libraries to enable automatic code splitting.
- **Optimization Block:** Configure `optimization.splitChunks` to extract common code and vendor libraries into separate chunks, preventing code duplication across entry points.

## 3. Asset Management (Loaders vs. Plugins)
- **Loaders:** Use loaders exclusively for file-level transformations (e.g., passing `.ts` to `ts-loader` or `.scss` to `sass-loader`). Keep loaders isolated via `include` and `exclude` rules (e.g., excluding `node_modules`).
- **Plugins:** Use plugins for global bundle manipulations (e.g., minification, asset copying, or environment injection).

## 4. Cache & Compilation Speed
- **Persistent Caching:** Enable filesystem caching in Webpack 5 (`cache: { type: 'filesystem' }`) to drastically cut down subsequent build times during development.
- **Deterministic Hashes:** Use `[name].[contenthash].js` for production output filenames to ensure accurate browser cache invalidation only when file contents actually change.