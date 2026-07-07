# Vite : Best Practices & Architecture

## 1. Architecture & Native ESM
- **Leverage Unbundled Dev:** Understand that Vite relies on native ESM for development. Keep source files unbundled during dev.
- **Dependency Pre-Bundling:** Let Vite pre-bundle node_modules dependencies using esbuild. If a dynamic dependency is missed, declare it explicitly in `optimizeDeps.include`.

## 2. Configuration & Plugins
- **Conditional Config:** Use the configuration function export `export default defineConfig(({ command, mode }) => { ... })` when plugins or options need to differ between development and production.
- **Plugin Minimalisms:** Keep the plugin array clean. Avoid redundant plugins; verify if a feature (like CSS nesting or asset importing) is already natively supported by Vite before adding a plugin.

## 3. Production Optimization
- **Rollup Tweaks:** Vite uses Rollup for production. Use `build.rollupOptions` to fine-tune chunk splitting strategies (`output.manualChunks`) to maximize browser caching efficiency.
- **Asset Threshold:** Adjust `build.assetsInlineLimit` (default 4KB) to avoid embedding too many small assets as Base64 strings inside the JS bundle, which delays parsing time.

## 4. Environment Variables
- **Strict Prefix:** Only variables prefixed with `VITE_` are exposed to your client-side code via `import.meta.env`. Never bypass this restriction to avoid leaking sensitive server-side environment tokens.