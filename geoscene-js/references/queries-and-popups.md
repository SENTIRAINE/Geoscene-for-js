# Queries and Popups

Use this reference when implementing layer queries, click picking, popup templates, and popup actions.

## Attribute Query

```js
await layer.when();

const query = layer.createQuery();
query.where = "STATUS = 'ACTIVE'";
query.outFields = ["OBJECTID", "NAME", "STATUS"];
query.returnGeometry = true;

const result = await layer.queryFeatures(query);
console.log(result.features);
```

Prefer `layer.createQuery()` because it inherits sensible defaults such as the layer's definition expression. Set `outFields` explicitly for attributes used by UI or popups.

## Spatial Query

```js
const query = layer.createQuery();
query.geometry = view.extent;
query.spatialRelationship = "intersects";
query.outFields = ["*"];
query.returnGeometry = true;

const result = await layer.queryFeatures(query);
```

For user-drawn geometry, pass the draw/sketch geometry into `query.geometry`. Make sure the service supports the requested query capabilities.

## Query Object IDs or Count

```js
const query = layer.createQuery();
query.where = "1=1";

const count = await layer.queryFeatureCount(query);
const objectIds = await layer.queryObjectIds(query);
```

Use counts and object IDs for summaries, pagination, or deferred detail loading.

## Hit Testing

Use `view.hitTest()` for pointer interactions against visible graphics or rendered features.

```js
view.on("click", async (event) => {
  const response = await view.hitTest(event);
  const hit = response.results.find((item) => item.graphic?.layer === layer);

  if (!hit) return;

  const graphic = hit.graphic;
  view.openPopup({
    features: [graphic],
    location: event.mapPoint
  });
});
```

Use hit testing for interaction. Use `queryFeatures()` for authoritative service-side filtering, large result sets, geometry relationships, or attributes not loaded in the current view.

## PopupTemplate

```js
layer.popupTemplate = {
  title: "{NAME}",
  content: [
    {
      type: "fields",
      fieldInfos: [
        { fieldName: "STATUS", label: "Status" },
        { fieldName: "UPDATED_AT", label: "Updated" }
      ]
    }
  ],
  actions: [
    {
      id: "zoom-to-feature",
      title: "Zoom to",
      className: "geoscene-icon-zoom-in-magnifying-glass"
    }
  ]
};
```

Keep popup templates declarative when possible. They are portable to web maps and easier to inspect than custom DOM-heavy popups.

## Dynamic Popup Content

Use a function only when content depends on async data or custom formatting that field templates cannot express.

```js
layer.popupTemplate = {
  title: "{NAME}",
  content: async (event) => {
    const attributes = event.graphic.attributes;
    const extra = await fetch(`/api/assets/${attributes.OBJECTID}`).then((r) => r.json());
    return `<section><strong>${attributes.NAME}</strong><br>${extra.summary}</section>`;
  }
};
```

When returning HTML strings, sanitize any untrusted content. Prefer creating safe DOM nodes if the content includes user-provided text.

## Popup Actions

```js
view.popup.on("trigger-action", (event) => {
  if (event.action.id !== "zoom-to-feature") return;

  const feature = view.popup.selectedFeature;
  if (feature?.geometry) {
    view.goTo(feature.geometry);
  }
});
```

Keep action IDs stable and handle missing `selectedFeature` defensively.
