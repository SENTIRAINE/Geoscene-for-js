# Views and Layers

Use this reference for creating 2D/3D views and adding common GeoScene layers.

## Minimal 2D MapView

```js
import Map from "@geoscene/core/Map.js";
import MapView from "@geoscene/core/views/MapView.js";

const map = new Map({
  basemap: "streets"
});

const view = new MapView({
  container: "viewDiv",
  map,
  center: [116.391, 39.907],
  zoom: 10
});

await view.when();
```

Use `MapView` for 2D operational maps, dashboards, editing workflows, and most query-heavy business apps.

## Minimal 3D SceneView

```js
import Map from "@geoscene/core/Map.js";
import SceneView from "@geoscene/core/views/SceneView.js";

const map = new Map({
  basemap: "satellite",
  ground: "world-elevation"
});

const view = new SceneView({
  container: "viewDiv",
  map,
  camera: {
    position: [116.391, 39.907, 2000],
    tilt: 65
  }
});

await view.when();
```

Use `SceneView` for 3D terrain, buildings, underground/aboveground assets, skyline inspection, or scene services.

## WebMap and WebScene

Prefer portal-authored maps/scenes when layer composition, renderers, popups, labels, or bookmarks should be managed outside code.

```js
import WebMap from "@geoscene/core/WebMap.js";
import MapView from "@geoscene/core/views/MapView.js";

const webmap = new WebMap({
  portalItem: { id: "PORTAL_ITEM_ID" }
});

const view = new MapView({
  container: "viewDiv",
  map: webmap
});
```

```js
import WebScene from "@geoscene/core/WebScene.js";
import SceneView from "@geoscene/core/views/SceneView.js";

const scene = new WebScene({
  portalItem: { id: "PORTAL_ITEM_ID" }
});

const view = new SceneView({
  container: "viewDiv",
  map: scene
});
```

## Common Layer Patterns

Feature services:

```js
import FeatureLayer from "@geoscene/core/layers/FeatureLayer.js";

const parcels = new FeatureLayer({
  url: "https://example.com/server/rest/services/Parcels/FeatureServer/0",
  outFields: ["*"],
  popupTemplate: {
    title: "{NAME}",
    content: "{ADDRESS}"
  }
});

map.add(parcels);
```

Tiled map services:

```js
import TileLayer from "@geoscene/core/layers/TileLayer.js";

map.add(new TileLayer({
  url: "https://example.com/server/rest/services/BaseMap/MapServer"
}));
```

Dynamic map services:

```js
import MapImageLayer from "@geoscene/core/layers/MapImageLayer.js";

map.add(new MapImageLayer({
  url: "https://example.com/server/rest/services/Planning/MapServer"
}));
```

Client-side graphics:

```js
import Graphic from "@geoscene/core/Graphic.js";
import GraphicsLayer from "@geoscene/core/layers/GraphicsLayer.js";

const graphicsLayer = new GraphicsLayer();
map.add(graphicsLayer);

graphicsLayer.add(new Graphic({
  geometry: {
    type: "point",
    longitude: 116.391,
    latitude: 39.907
  },
  symbol: {
    type: "simple-marker",
    color: "#e11d48",
    size: 10
  }
}));
```

GeoJSON:

```js
import GeoJSONLayer from "@geoscene/core/layers/GeoJSONLayer.js";

map.add(new GeoJSONLayer({
  url: "/data/features.geojson"
}));
```

3D scene layers:

```js
import SceneLayer from "@geoscene/core/layers/SceneLayer.js";

map.add(new SceneLayer({
  url: "https://example.com/server/rest/services/Buildings/SceneServer"
}));
```

## Lifecycle

- Create a view only after the container element exists.
- Await `view.when()` before interacting with loaded UI, layer views, or camera state.
- Await `layer.when()` before relying on fields, capabilities, full extent, or renderer metadata.
- Call `view.destroy()` when removing the page or component.
- Remove watches, handles, and event listeners when the surrounding component unmounts.
