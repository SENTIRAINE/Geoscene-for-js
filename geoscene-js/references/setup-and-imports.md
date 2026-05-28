# Setup and Imports

Use this reference when choosing how to load GeoScene Maps SDK for JavaScript.

## NPM and ESM

Prefer this mode for Vite, Webpack, Rollup, Vue, React, Next.js client components, and other bundled apps.

```bash
npm install @geoscene/core
```

Import modules from `@geoscene/core/...` and include the SDK theme CSS once in the application entry.

```js
import Map from "@geoscene/core/Map.js";
import MapView from "@geoscene/core/views/MapView.js";
import FeatureLayer from "@geoscene/core/layers/FeatureLayer.js";
import "@geoscene/core/assets/geoscene/themes/light/main.css";
```

If the CSS import path fails, inspect the installed package under `node_modules/@geoscene/core/assets/` and match the official docs for the installed SDK version. Do not guess a theme path after a package upgrade.

For Vite and similar tools, keep SDK imports in browser-only code. Avoid importing view modules from server-rendered files because `MapView` and `SceneView` require browser DOM/WebGL APIs.

## Online ESM

Use online ESM for simple HTML demos, documentation examples, or reproductions where there is no local build pipeline.

```html
<link rel="stylesheet" href="https://js.geoscene.cn/4.29/geoscene/themes/light/main.css">
<script type="module">
  import Map from "https://js.geoscene.cn/4.29/@geoscene/core/Map.js";
  import MapView from "https://js.geoscene.cn/4.29/@geoscene/core/views/MapView.js";

  const map = new Map({ basemap: "streets" });
  const view = new MapView({
    container: "viewDiv",
    map,
    center: [116.391, 39.907],
    zoom: 10
  });
</script>
```

Keep the SDK version in CSS and module URLs aligned. Use a fixed version for production examples instead of floating to latest.

## AMD Loader

Use AMD for legacy pages or no-build apps that already follow the SDK's traditional `require([...], function (...) {})` style.

```html
<link rel="stylesheet" href="https://js.geoscene.cn/4.29/geoscene/themes/light/main.css">
<script src="https://js.geoscene.cn/4.29/"></script>
<script>
  require([
    "geoscene/Map",
    "geoscene/views/MapView",
    "geoscene/layers/FeatureLayer"
  ], function (Map, MapView, FeatureLayer) {
    const map = new Map({ basemap: "streets" });
    const view = new MapView({
      container: "viewDiv",
      map,
      center: [116.391, 39.907],
      zoom: 10
    });
  });
</script>
```

Keep AMD examples self-contained. Do not mix AMD `require()` modules with NPM ESM imports in the same implementation.

## CSS and Container Requirements

The view container must exist before creating the view and must have explicit dimensions.

```css
html,
body,
#viewDiv {
  padding: 0;
  margin: 0;
  height: 100%;
  width: 100%;
}
```

In component apps, style the container with a fixed height, viewport height, or parent-driven layout. A zero-height container is the most common cause of a blank map.

## Assets and Workers

For modern bundled apps, the SDK usually resolves assets from the installed package. If the build tool cannot serve SDK assets correctly, configure the SDK asset path according to the official docs for the installed version before creating views. Keep this setup in one app entry or GeoScene bootstrap module.
