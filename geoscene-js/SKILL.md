---
name: geoscene-js
description: Build and debug GeoScene Maps SDK for JavaScript WebGIS applications. Use when working with GeoScene JS SDK projects, 2D MapView or 3D SceneView maps, WebMap or WebScene loading, FeatureLayer/TileLayer/MapImageLayer/GraphicsLayer/GeoJSONLayer/SceneLayer usage, layer queries, hitTest interactions, PopupTemplate configuration, or React/Vue integration patterns for GeoScene.
---

# GeoScene JS

Use this skill to build GeoScene Maps SDK for JavaScript applications with the official SDK patterns. Prefer the user's existing framework and package manager. When behavior is version-sensitive, check the installed `@geoscene/core` version or the official docs before changing APIs.

Official docs entry points:
- GeoScene JS docs: https://doc.geoscene.cn/javascript/4.29/
- API reference: https://doc.geoscene.cn/javascript/4.29/api-reference/index.html

## Workflow

1. Identify the integration style:
   - Bundled app with NPM/ESM
   - Browser-only sample with online ESM
   - Legacy or no-build app with AMD loader
   - React or Vue component integration
2. Load the relevant reference:
   - Setup, CSS, assets, and import modes: `references/setup-and-imports.md`
   - 2D/3D views, maps, scenes, and layers: `references/views-and-layers.md`
   - Queries, hit testing, and popups: `references/queries-and-popups.md`
   - React and Vue lifecycle patterns: `references/frameworks.md`
3. Implement the smallest working map first, then add layers, popups, and queries.
4. Clean up view instances in component or page teardown with `view.destroy()` to avoid duplicate WebGL contexts and event handlers.

## Defaults

- Prefer NPM/ESM with `@geoscene/core` for modern apps.
- Prefer `MapView` for 2D maps and `SceneView` for 3D scenes.
- Use `view.when()` before acting on loaded views or layers.
- Keep layer URLs, portal item IDs, API keys, tokens, and service endpoints configurable rather than hard-coded.
- Import SDK CSS exactly once in the app entry, not inside repeatedly mounted components.

## Common Checks

- If the map container is blank, verify the container has a real height, SDK CSS is imported, and the view is created after the DOM node exists.
- If ESM imports fail, confirm the installed SDK version and inspect the package's `assets` and theme CSS paths.
- If a component remount creates warnings or memory growth, confirm `view.destroy()` runs on cleanup.
- If queries fail, confirm the layer is loaded, `outFields` includes requested attributes, and service capabilities allow query operations.
