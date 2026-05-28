# React and Vue Integration

Use this reference for component-based apps. The key rule is to create the view after the DOM container exists and destroy it when the component unmounts.

## React

```jsx
import { useEffect, useRef } from "react";
import Map from "@geoscene/core/Map.js";
import MapView from "@geoscene/core/views/MapView.js";
import "@geoscene/core/assets/geoscene/themes/light/main.css";

export function GeoSceneMap() {
  const containerRef = useRef(null);
  const viewRef = useRef(null);

  useEffect(() => {
    if (!containerRef.current || viewRef.current) return;

    const map = new Map({ basemap: "streets" });
    const view = new MapView({
      container: containerRef.current,
      map,
      center: [116.391, 39.907],
      zoom: 10
    });

    viewRef.current = view;

    return () => {
      viewRef.current?.destroy();
      viewRef.current = null;
    };
  }, []);

  return <div ref={containerRef} style={{ height: "100%", width: "100%" }} />;
}
```

React notes:
- Import SDK CSS once in the app entry when possible. Component-level CSS import is acceptable for small apps but can duplicate responsibility.
- In Next.js or other SSR frameworks, keep this component client-only and avoid importing view modules from server components.
- Store the view in a ref, not state. Updating React state with the view can trigger unnecessary renders.
- Include dependencies in `useEffect` only when they should recreate the view. For most maps, create once and update layers/properties separately.

## React With Dynamic Imports

Use dynamic imports when a framework evaluates static imports during SSR or when you want to lazy-load the SDK.

```jsx
useEffect(() => {
  let cancelled = false;
  let view;

  async function start() {
    const [{ default: Map }, { default: MapView }] = await Promise.all([
      import("@geoscene/core/Map.js"),
      import("@geoscene/core/views/MapView.js")
    ]);

    if (cancelled || !containerRef.current) return;

    view = new MapView({
      container: containerRef.current,
      map: new Map({ basemap: "streets" }),
      center: [116.391, 39.907],
      zoom: 10
    });
  }

  start();

  return () => {
    cancelled = true;
    view?.destroy();
  };
}, []);
```

## Vue 3

```vue
<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue";
import Map from "@geoscene/core/Map.js";
import MapView from "@geoscene/core/views/MapView.js";
import "@geoscene/core/assets/geoscene/themes/light/main.css";

const mapEl = ref(null);
let view;

onMounted(() => {
  const map = new Map({ basemap: "streets" });

  view = new MapView({
    container: mapEl.value,
    map,
    center: [116.391, 39.907],
    zoom: 10
  });
});

onBeforeUnmount(() => {
  view?.destroy();
  view = undefined;
});
</script>

<template>
  <div ref="mapEl" class="map-view"></div>
</template>

<style scoped>
.map-view {
  height: 100%;
  width: 100%;
}
</style>
```

Vue notes:
- Use `ref` for the DOM container and a module/local variable for the `view`.
- Do not make SDK view instances deeply reactive.
- If route params or props change, update existing map properties or layers when possible instead of destroying and recreating the whole view.

## Shared Component Rules

- Ensure parent layout gives the container a height.
- Destroy views in cleanup hooks.
- Remove custom event handles returned by `view.on()`, `reactiveUtils.watch()`, or similar watch helpers if they outlive the view.
- Avoid creating more than one view per container.
- Keep credentials, service URLs, and portal item IDs in config or environment-specific files.
