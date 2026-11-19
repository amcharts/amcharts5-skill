# Map Charts — Geographic Visualizations

> **As a JSON config:** see `references/json.md`. The settings below go into each object's `"settings"` there.


## Required imports

### ES modules
```ts
import * as am5 from "@amcharts/amcharts5";
import * as am5map from "@amcharts/amcharts5/map";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
import am5themes_ResponsiveMax from "@amcharts/amcharts5/themes/ResponsiveMax";
import am5geodata_worldLow from "@amcharts/amcharts5-geodata/worldLow";
```

### CDN
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/map.js"></script>
<script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
```

From an npm CDN (AI sandboxes, strict CSPs), the same files are under `https://cdn.jsdelivr.net/npm/@amcharts/amcharts5@5.23.0/dist/`, one pinned version for all: see SKILL.md "Plain HTML, no bundler".

## Geodata (map files)

NPM: `@amcharts/amcharts5-geodata`
CDN: `https://cdn.amcharts.com/lib/5/geodata/{mapName}.js`
npm CDN (AI sandboxes, strict CSPs): jsDelivr and unpkg serve the geodata package's `dist/` (`https://cdn.jsdelivr.net/npm/@amcharts/amcharts5-geodata@5.2.0/dist/{mapName}.js`). Files have the same path as below `geodata/` on the amCharts CDN (e.g. `dist/region/world/europeLow.js`) and the same globals.

| Map | ES import | CDN global |
|-----|----------|-----------|
| World (low detail) | `@amcharts/amcharts5-geodata/worldLow` | `am5geodata_worldLow` |
| World (high detail) | `@amcharts/amcharts5-geodata/worldHigh` | `am5geodata_worldHigh` |
| USA (low) | `@amcharts/amcharts5-geodata/usaLow` | `am5geodata_usaLow` |
| USA (high) | `@amcharts/amcharts5-geodata/usaHigh` | `am5geodata_usaHigh` |
| France (low) | `@amcharts/amcharts5-geodata/franceLow` | `am5geodata_franceLow` |
| Continents | `@amcharts/amcharts5-geodata/continentsLow` | `am5geodata_continentsLow` |

Pattern: camelCase country name + detail level. E.g. `germanyLow`, `canadaHigh`, `chinaLow`.

**IMPORTANT — regional/continent geodata:**
- Individual country maps: `geodata/{countryName}Low.js` > global `am5geodata_{countryName}Low`
- **There are NO continent-level maps** like `europeLow.js` or `asiaLow.js` at the top-level CDN path
- Regional maps are in subdirectories: `geodata/region/world/{regionName}Low.js`
  - CDN: `https://cdn.amcharts.com/lib/5/geodata/region/world/europeLow.js`
  - Global: `am5geodata_region_world_europeLow`
- For a map of European countries, use `worldLow.js` and filter with `include: ["DE", "FR", "IT", ...]`
- **US congressional districts** come in versioned packs under `geodata/region/usa/<pack>/`, each with `usaCongressionalLow.js` / `usaCongressionalHigh.js` (all states) and per-state `<st>Low.js` / `<st>High.js` (e.g. `txLow.js`). There is no top-level `geodata/usaCongressionalLow.js`.

| Pack | Districts |
|------|-----------|
| `congressional` | 117th Congress (the original set; the 116th used the same districts) |
| `congressional2022` | 118th |
| `congressional2025` | 119th |
| `congressional120` | 120th, the newest (includes proposed changes) |

**Global names** are `am5geodata_` + the path below `geodata/`, with `/` → `_` and case kept: `geodata/region/usa/congressional120/usaCongressionalLow.js` → `am5geodata_region_usa_congressional120_usaCongressionalLow`; ES import `@amcharts/amcharts5-geodata/region/usa/congressional120/usaCongressionalLow`. The npm package has the same maps (default exports, raw GeoJSON under `json/`, also on the CDN as `geodata/json/<path>.json`), and its `loadMap("region/usa/congressional120/txLow")` loads any map by path. Where the amCharts MCP server is connected, its `list_geodata(filter)` tool lists every map with its CDN path, global name and ES import.

## Core setup

```js
const root = am5.Root.new("chartdiv");
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);

const chart = root.container.children.push(
  am5map.MapChart.new(root, {
    panX: "rotateX",           // "translateX", "rotateX", "none"
    panY: "translateY",        // "translateY", "rotateY", "none"
    projection: am5map.geoMercator(),
    // homeGeoPoint: { latitude: 48.8566, longitude: 2.3522 },
    // homeZoomLevel: 5,
    // autoHome: true              // start at the home position
  })
);

// Polygon series (countries/regions)
const polygonSeries = chart.series.push(
  am5map.MapPolygonSeries.new(root, {
    geoJSON: am5geodata_worldLow
    // exclude: ["AQ"]             // exclude Antarctica (only if needed)
    // include: ["US", "CA", "MX"]  // show only these
  })
);

chart.appear(1000, 100);
```

## Available projections

```js
am5map.geoMercator()          // most common, rectangular
am5map.geoNaturalEarth1()     // rounded, good for world maps
am5map.geoAlbersUsa()         // USA with Alaska/Hawaii repositioned
am5map.geoOrthographic()      // globe
am5map.geoEqualEarth()        // equal-area
am5map.geoEquirectangular()   // simple lat/long grid
```

**IMPORTANT — Antarctica (`exclude: ["AQ"]`):**
- Do NOT exclude Antarctica by default. Many amCharts demos exclude it because they use Mercator projection where Antarctica appears disproportionately large, but this is a demo-specific choice, not a best practice.
- Only exclude Antarctica when the user explicitly asks for it, or when referencing a specific demo that excludes it.
- With non-Mercator projections (`geoNaturalEarth1`, `geoEqualEarth`, `geoOrthographic`, `geoEquirectangular`) Antarctica renders at a reasonable size and should be included.

## Series types

### MapPolygonSeries (countries, regions)

```js
const polygonSeries = chart.series.push(
  am5map.MapPolygonSeries.new(root, {
    geoJSON: am5geodata_worldLow,
    // exclude: ["AQ"],          // only if user requests Antarctica exclusion
    valueField: "value",        // for choropleth coloring
    calculateAggregates: true   // needed for heat rules
  })
);
// GeoJSON feature properties are data fields too: with valueField "value", a custom GeoJSON's
// "properties": { "value": 12 } gives the polygon its value without data rows.
// When include and exclude both list an item, exclude wins.

// Polygon appearance
polygonSeries.mapPolygons.template.setAll({
  tooltipText: "{name}",
  interactive: true,
  fill: am5.color(0xdadada),
  strokeWidth: 0.5
});

// Hover state
polygonSeries.mapPolygons.template.states.create("hover", {
  fill: am5.color(0x297373)
});

// Supplement geodata with custom values
polygonSeries.data.setAll([
  { id: "US", name: "United States", value: 331 },
  { id: "CN", name: "China", value: 1411 },
  { id: "IN", name: "India", value: 1380 }
]);
```

**Custom GeoJSON: exterior rings must be CLOCKWISE.** amCharts projects on a sphere (d3-geo), where a ring's winding order decides which side is "inside". The GeoJSON spec (RFC 7946) winds exterior rings counter-clockwise, and d3-geo reads a CCW ring as *the whole globe minus the polygon* — so every polygon fills the entire viewport and the real shape shows only as a sliver (looks like a zoom/fit bug, is not). GIS exports are usually already clockwise; programmatically generated GeoJSON (grids, cells, buffers) usually is not. amCharts does **not** auto-rewind, so reverse the polygons that come out inside out yourself:

```js
// Fix winding before handing custom GeoJSON to MapPolygonSeries: reverse only polygons that are inside out
geojson.features.forEach(function (f) {
  var g = f.geometry;
  var polys = g.type === "Polygon" ? [g.coordinates] : g.type === "MultiPolygon" ? g.coordinates : [];
  polys.forEach(function (poly) {
    if (am5map.getGeoArea({ type: "Polygon", coordinates: poly }) > 2 * Math.PI) {   // more than half the globe
      poly.forEach(function (ring) { ring.reverse(); });
    }
  });
});
polygonSeries.set("geoJSON", geojson);

// Fit the map to a custom region (no country geodata needed): left/right = longitude, top/bottom = latitude
chart.zoomToGeoBounds({ left: -8.6, right: 1.8, top: 60.9, bottom: 49.9 }, 500);
```

The area check makes this safe on data that is already clockwise, where a blind reverse floods the map. It also catches small rings that simplifying or rounding turned over, even in clockwise data: a flipped 4-point islet measured 4π and filled the whole map, while no worldLow country exceeds 0.42.

`zoomToGeoBounds(bounds, duration?, rotationX?, rotationY?)` is the most direct way to fit custom polygons. Bounds with `right` less than `left`, or past ±180, cross the date line; on a flat map the two sides meet only with a rotation passed, e.g. `zoomToGeoBounds(bounds, 500, -180)`. Called before the map is drawn, `zoomToGeoBounds()` and `zoomToGeoPoint()` zoom once it is. It uses the `duration` you pass and leaves the bounds object unchanged; `goHome()` also works when called before the map is ready or on a rotated map, and `autoHome: true` does the initial trip for you (see "Zoom controls").

### Choropleth (heat map) via heat rules

```js
polygonSeries.set("heatRules", [{
  target: polygonSeries.mapPolygons.template,
  dataField: "value",
  min: am5.color(0xf7e9b9),
  max: am5.color(0x5a1d6e),
  key: "fill"
}]);

// Heat legend
const heatLegend = chart.children.push(am5.HeatLegend.new(root, {
  orientation: "horizontal",
  startColor: am5.color(0xf7e9b9),
  endColor: am5.color(0x5a1d6e),
  startText: "Low",
  endText: "High",
  stepCount: 5
}));
```

**Keep the heat legend off the map.** In `chart.children` the legend is drawn over the map, and zooms fit into the whole chart, so a zoomed country runs under it: after `clickZoom` on Brazil, its south was 38 px under a bottom legend. Stack the legend below the map and clip the map to its own area:

```js
root.container.set("layout", root.verticalLayout);
var chart = root.container.children.push(am5map.MapChart.new(root, {
  projection: am5map.geoEqualEarth(),
  maskContent: true                        // the zoomed map doesn't draw into the legend's strip
}));
// ... series ...
var heatLegend = root.container.children.push(am5.HeatLegend.new(root, {   // pushed after the chart: below it
  orientation: "horizontal", startColor: am5.color(0xf7e9b9), endColor: am5.color(0x5a1d6e),
  width: 360, x: am5.percent(50), centerX: am5.percent(50), paddingBottom: 20
}));
```

For a scale through more than two colors, give the heat rule `stops` instead of `min`/`max` and the legend `colors` instead of `startColor`/`endColor`: see SKILL.md "Heat rules". `Adaptive` and `Monochrome` themes take `mapShade: true` for map land in a lighter shade of their base color.

**Styling heat-legend segments.** With `stepCount: N` the legend draws N discrete `RoundedRectangle` markers inside `heatLegend.markerContainer`, all styled through `heatLegend.markers.template` (it accepts the usual Graphics settings — `stroke`, `strokeWidth`, `strokeOpacity`, `fillOpacity`, `cornerRadius*`, `tooltipText` for a per-block hover tooltip). Segment **thickness** is the marker's cross-axis size: a **vertical** legend uses the marker `width` (default `15`) with `height: 100%`; a **horizontal** legend uses `height` (default `15`) with `width: 100%`. Style via the template — `heatLegend.markers.values` can read empty even while `markerContainer.children` holds the rectangles.

```js
heatLegend.markers.template.setAll({
  width: 30,                 // thicker bar on a vertical legend (use height on a horizontal one)
  stroke: am5.color(0xffffff),
  strokeWidth: 1,
  tooltipText: "{value}"
});
```

**A panel behind the heat legend.** When the legend stays over the map, it has no background, so a zoomed or panned map shows through it. `HeatLegend` is a `Container`, so give it a `background` and padding:

```js
var heatLegend = chart.children.push(am5.HeatLegend.new(root, {
  orientation: "horizontal",
  startColor: am5.color(0xeeeeee), endColor: am5.color(0x0000cc),
  x: am5.p50, centerX: am5.p50, y: am5.percent(100), centerY: am5.percent(100), width: am5.percent(50),
  paddingTop: 8, paddingBottom: 8, paddingLeft: 12, paddingRight: 12,
  background: am5.RoundedRectangle.new(root, { fill: am5.color(0xffffff), fillOpacity: 0.9 })
}));
```

The HeatLegend blocks above leave the end labels empty: set `startValue`/`endValue` from the series on `datavalidated` (SKILL.md "Heat rules") to show the numbers, or give `startText`/`endText` (e.g. "Low", "High"), which replace them.

**Countries without data** keep the template's fill, or the heat rule's `neutral` value, and `{value}` in their tooltip is empty ("Canada: "). Give them their own text with an adapter (docs: https://docs.amcharts.com/v5/charts/map-chart/map-polygon-series/#tooltips-for-polygons-without-data):

```js
polygonSeries.set("heatRules", [{
  target: polygonSeries.mapPolygons.template, dataField: "value",
  min: am5.color(0xffd19a), max: am5.color(0xb3261e),
  neutral: am5.color(0xdddddd),   // polygons with no value
  key: "fill"
}]);
polygonSeries.mapPolygons.template.adapters.add("tooltipText", function (text, target) {
  return target.dataItem && target.dataItem.get("value") == null ? "{name}: no data" : text;
});
```

### MapPointSeries (markers, bubbles, labels)

```js
const pointSeries = chart.series.push(
  am5map.MapPointSeries.new(root, {
    latitudeField: "latitude",
    longitudeField: "longitude"
  })
);

// Define bullet appearance
pointSeries.bullets.push(function(root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Circle.new(root, {
      radius: 5,
      fill: am5.color(0xff0000),
      tooltipText: "{name}"
    })
  });
});

// Add points via data. Fields named "latitude"/"longitude" are the series
// defaults; any other naming must be declared via
// latitudeField/longitudeField or the points silently won't render.
pointSeries.data.setAll([
  { latitude: 48.8566, longitude: 2.3522, name: "Paris" },
  { latitude: 40.7128, longitude: -74.0060, name: "New York" },
  { latitude: 35.6762, longitude: 139.6503, name: "Tokyo" }
]);

// OR: place points at polygon centers using polygonIdField
const pointSeries2 = chart.series.push(
  am5map.MapPointSeries.new(root, { polygonIdField: "polygonId" })
);
pointSeries2.data.setAll([
  { polygonId: "US", name: "USA" },
  { polygonId: "FR", name: "France" }
]);
```

`surfaceBullets: true` lays the bullets onto the map surface, and `altitudeField` raises points — see "Bullets on the surface" and "Raised lines and points" below.

**`affectsBounds`** says whether a series' shapes count when the map fits itself to its area: `true` on `MapPolygonSeries`, `false` on point, line, Sankey and night series (from the theme). Give overlay polygon series (highlights, storms) `affectsBounds: false` so the map fits the countries.

**`autoScale: true`** (default `false`) scales a point series' bullets with `zoomLevel`.

**A point's bullet sprite:** `dataItem.bullets` is a plain array property, not a setting (`dataItem.get("bullets")` is `undefined`), on every series type:

```js
var sprite = pointSeries.dataItems[0].bullets?.[0]?.get("sprite");   // the Circle made by the bullet function
```

A Container bullet with `tooltipText` gets a tooltip with no background: put `tooltipText` on a child with a fill (SKILL.md → "Shared vs per-sprite tooltip").

**Points on lines as data rows:** `MapPointSeries` has `lineIdField` (default `"lineId"`), `positionOnLineField` (default `"positionOnLine"`), `autoRotateField` (default `"autoRotate"`) and `autoRotateAngleField` (default `"autoRotateAngle"`), so a point on a `MapLine` can be a plain row — `{ lineId: "jfk-lhr", positionOnLine: 0.5, autoRotate: true }`, where `lineId` is the line's data `id` in a `MapLineSeries` of the same chart — instead of `pushDataItem({ lineDataItem, positionOnLine })`. Data values of `autoRotate`/`autoRotateAngle` win over the bullet's own setting. `polygonIdField` still has **no** default — declare it as above. See "Animating bullets along lines".

### MapLineSeries (connections, routes)

```js
const lineSeries = chart.series.push(
  am5map.MapLineSeries.new(root, {})
);

lineSeries.mapLines.template.setAll({
  stroke: am5.color(0xff0000),
  strokeWidth: 2,
  strokeOpacity: 0.5
});

// Using point data items for connections
const pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
const nyc = pointSeries.pushDataItem({ latitude: 40.71, longitude: -74.01 });
const london = pointSeries.pushDataItem({ latitude: 51.47, longitude: -0.45 });

lineSeries.pushDataItem({
  pointsToConnect: [nyc, london]
});

// OR using GeoJSON geometry
lineSeries.pushDataItem({
  geometry: {
    type: "LineString",
    coordinates: [[-74.01, 40.71], [-0.45, 51.47]]  // [lng, lat] pairs
  }
});

// OR name the points by id — data-driven, JSON-friendly, no data-item references needed
const pointSeries2 = chart.series.push(am5map.MapPointSeries.new(root, {}));   // ids come from the "id" data field
pointSeries2.data.setAll([
  { id: "JFK", latitude: 40.64, longitude: -73.78 },
  { id: "LAX", latitude: 33.94, longitude: -118.41 }
]);
const lineSeries2 = chart.series.push(am5map.MapLineSeries.new(root, {
  pointSeries: pointSeries2          // where pointIds are looked up
  // pointIdsField: "pointIds"       // default field name
}));
lineSeries2.data.setAll([
  { pointIds: ["JFK", "LAX"] }       // the line waits until every id has a point, then draws
]);
```

`pointsToConnect` wins if a data item has both. Fixed points (`fixed: true`) cannot be used with either. `altitude` on `mapLines.template` arcs lines above the surface — see "Raised lines and points". `lineType` (`"curved"` default, or `"straight"`) is set on the series or as a data field (`{ geometry, lineType: "straight" }`), not on `MapLine`: on `mapLines.template` it is ignored (and a TypeScript error). Changing it redraws lines already drawn.

Each leg follows the great circle (`lineType: "curved"`, the default), so a long east–west leg bows toward the pole: a 924 km leg along 41°N passes 15 km north of the parallel at its middle. `lineType: "straight"` (on the series or a data item) draws legs straight on screen. For a line that must follow a path drawn in lon/lat, such as a sea route kept off land, add intermediate points: shorter legs bow less.

### MapSankeySeries (geographic flow bands)

Draws variable-width curved bands between geographic points on a map. Band thickness represents a numeric value — like a Sankey diagram projected onto a map. Extends `MapPolygonSeries` and generates actual GeoJSON polygon geometries, so bands follow the map projection during pan, zoom, and rotation.

**Requires** a `MapPolygonSeries` to resolve country IDs to geographic centroids.

```js
// Create polygon series for countries (required for ID resolution)
var polygonSeries = chart.series.push(
  am5map.MapPolygonSeries.new(root, {
    geoJSON: am5geodata_worldLow
  })
);

// Create Sankey flow series
var sankeySeries = chart.series.push(
  am5map.MapSankeySeries.new(root, {
    polygonSeries: polygonSeries,
    maxWidth: 5               // max band width in geographic degrees
  })
);
```

#### Data format

Two data patterns — polygon IDs or explicit coordinates.

**Pattern 1: `sourceId` / `targetId` (country codes)**

IDs use ISO 3166-1 alpha-2 codes matching the geodata. Set data directly — `MapSankeySeries` auto-resolves `sourceId`/`targetId` once the referenced `polygonSeries` finishes loading, so data can be set at any time:

```js
sankeySeries.data.setAll([
  { sourceId: "BR", targetId: "DE", value: 350 },
  { sourceId: "BR", targetId: "US", value: 450 },
  { sourceId: "DE", targetId: "FR", value: 150 },
  { sourceId: "DE", targetId: "PL", value: 100 }
]);
```

**Pattern 2: Explicit coordinates**

When using `sourceLongitude`/`sourceLatitude`/`targetLongitude`/`targetLatitude`, centroid lookup is skipped entirely:
```js
sankeySeries.data.setAll([
  {
    sourceLongitude: 2.35, sourceLatitude: 48.86,    // Paris
    targetLongitude: -74.01, targetLatitude: 40.71,  // New York
    value: 100
  }
]);
```

**Waypoints** — route flows through intermediate points, each `{ longitude, latitude }` (not `lon`/`lat`):
```js
sankeySeries.data.setAll([
  {
    sourceId: "GB", targetId: "JP", value: 40,
    waypoints: [
      { longitude: 30, latitude: 65 },
      { longitude: 90, latitude: 55 }
    ]
  }
]);
```

**WARNING:** Never call `polygonSeries.data.setAll()` with custom objects — this replaces all geoJSON-derived data items, breaking MapSankeySeries centroid resolution. To set per-country properties (like colors), iterate existing data items after `datavalidated` instead:
```js
polygonSeries.events.on("datavalidated", function() {
  am5.array.each(polygonSeries.dataItems, function(di) {
    if (di.get("id") === "BR") {
      di.get("mapPolygon").setAll({ fill: am5.color(0x8fae7e) });
    }
  });
});
```

#### Key settings

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `polygonSeries` | MapPolygonSeries | — | Reference series for resolving country IDs |
| `maxWidth` | number | `5` | Maximum band width in geographic degrees |
| `controlPointDistance` | number | `0.5` | Bezier control point distance (0–0.5) |
| `controlPointDistanceSource` | number | — | Control point distance at source end |
| `controlPointDistanceTarget` | number | — | Control point distance at target end |
| `orientation` | string | `"horizontal"` | `"horizontal"` or `"vertical"` — controls departure/arrival direction |
| `resolution` | number | `50` | Sample points per bezier segment |
| `autoSort` | boolean | `true` | Sort bands by target latitude to prevent overlap |
| `nodeType` | string | `"circle"` | `"circle"` or `"bar"` — endpoint node shape |
| `nodeWidth` | number | `1` | Bar width in degrees (bar nodeType only) |
| `nodePadding` | number | `0.3` | Extra padding on node shapes in degrees |
| `antimeridian` | string | `"short"` | `"short"` for shortest path, `"long"` to avoid ±180° crossing |
| `linkColorMode` | string | `"solid"` | `"solid"`: every band takes `mapPolygons.template`'s fill; `"source"` / `"target"`: each band takes that node's fill, so the nodes need colors (see "Band appearance"). Followed also when node fills change |
| `sourceIdField` | string | `"sourceId"` | Data field for source polygon ID |
| `targetIdField` | string | `"targetId"` | Data field for target polygon ID |
| `sourceLongitudeField` | string | `"sourceLongitude"` | Data field for source longitude |
| `sourceLatitudeField` | string | `"sourceLatitude"` | Data field for source latitude |
| `targetLongitudeField` | string | `"targetLongitude"` | Data field for target longitude |
| `targetLatitudeField` | string | `"targetLatitude"` | Data field for target latitude |
| `waypointsField` | string | `"waypoints"` | Data field for waypoints array |

#### Band appearance

Configure flow bands via `mapPolygons.template`:

```js
sankeySeries.mapPolygons.template.setAll({
  fill: am5.color(0xff6b35),
  fillOpacity: 0.6,
  strokeOpacity: 0,
  tooltipText: "{sourceId} > {targetId}: {value}"
});
```

Bands colored like their nodes — map nodes get no colors of their own, so give them a `ColorSet` (before the data):

```js
var sankeySeries = chart.series.push(am5map.MapSankeySeries.new(root, {
  polygonSeries: polygonSeries,
  linkColorMode: "source"     // each band takes its source node's fill ("target": its target's)
}));
sankeySeries.nodes.set("colors", am5.ColorSet.new(root, {}));   // map nodes have no colors by default
```

Per-link control point overrides in data:
```js
sankeySeries.data.setAll([
  { sourceId: "CN", targetId: "US", value: 300, controlPointDistance: 0.2 },
  { sourceId: "CN", targetId: "DE", value: 200, controlPointDistanceSource: 0.4 }
]);
```

#### Node configuration

Each unique endpoint gets a node shape. Configure via `nodes.mapPolygons.template`:

```js
sankeySeries.nodes.mapPolygons.template.setAll({
  fill: am5.color(0x8b5e3c),
  fillOpacity: 0.9,
  stroke: am5.color(0xffffff),
  strokeWidth: 1.5
});
```

Bar-style nodes (like traditional Sankey):
```js
var sankeySeries = chart.series.push(
  am5map.MapSankeySeries.new(root, {
    polygonSeries: polygonSeries,
    nodeType: "bar",
    nodeWidth: 1.5   // bar width in degrees
  })
);
```

#### Animated bullets along bands

```js
sankeySeries.bullets.push(function(root, series, dataItem) {
  return am5.Bullet.new(root, {
    locationX: 0,
    autoRotate: true,
    sprite: am5.Circle.new(root, {
      radius: 3,
      fill: am5.color(0xffffff)
    })
  });
});

// Animate bullets along flow paths
sankeySeries.events.on("datavalidated", function() {
  am5.array.each(sankeySeries.dataItems, function(dataItem) {
    var bullets = dataItem.bullets;
    if (bullets) {
      am5.array.each(bullets, function(bullet) {
        bullet.animate({
          key: "locationX",
          from: 0,
          to: 1,
          duration: 3000,
          easing: am5.ease.linear,
          loops: Infinity
        });
      });
    }
  });
});
```

Bullets automatically hide on the back side of the globe (orthographic projection). They are drawn above the flows, and bullets that are not moving along their flow follow the map when it moves.

#### Multi-level flows

The same country can be both target and source — incoming and outgoing bands share unified stacking at intermediate nodes:

```js
sankeySeries.data.setAll([
  // Level 1: Producers > Hubs
  { sourceId: "BR", targetId: "DE", value: 350 },
  { sourceId: "VN", targetId: "DE", value: 200 },
  // Level 2: Hubs > Consumers
  { sourceId: "DE", targetId: "FR", value: 150 },
  { sourceId: "DE", targetId: "PL", value: 100 }
]);
```

### Animating bullets along lines (positionOnLine)

`MapPointSeries` data items have a `positionOnLine` setting (0–1) that positions the point along a line. Combined with `lineDataItem` (or a `lineId` data field) and `autoRotate`, this enables flight-path-style animations.

```js
// Create a point that follows a line
var planeSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
planeSeries.bullets.push(function(root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Graphics.new(root, {
      svgPath: "m2,106h28l24,30h72l-44,-133h35l80,132h98c21,0 21,34 0,34l-98,0 -80,134h-35l43,-133h-71l-24,30h-28l15,-47",
      scale: 0.04, centerY: am5.p50, centerX: am5.p50, fill: am5.color(0xff6600)
    })
  });
});

var plane = planeSeries.pushDataItem({
  lineDataItem: lineDataItem,  // a MapLineSeries data item
  positionOnLine: 0,
  autoRotate: true
});

// Animate along the line
plane.animate({ key: "positionOnLine", to: 1, duration: 3000,
  easing: am5.ease.inOut(am5.ease.cubic) });

// Track position for globe rotation or scale changes
plane.on("positionOnLine", function(pos) {
  // Center globe on plane's current position
  chart.set("rotationX", -plane.get("longitude"));
  chart.set("rotationY", -plane.get("latitude"));
});
```

**Data-row form + declared flight** — no data-item references and no `animate()` call, so the whole thing survives `ChartSerializer` (points added with `pushDataItem()` are not serialized; `data` rows are):

```js
var lineSeries = chart.series.push(am5map.MapLineSeries.new(root, {}));   // push before the point series
lineSeries.data.setAll([{ id: "jfk-lhr",
  geometry: { type: "LineString", coordinates: [[-73.78, 40.64], [-0.45, 51.47]] } }]);

var planeSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
planeSeries.bullets.push(function (root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Graphics.new(root, {
      svgPath: "m2,106h28l24,30h72l-44,-133h35l80,132h98c21,0 21,34 0,34l-98,0 -80,134h-35l43,-133h-71l-24,30h-28l15,-47",
      scale: 0.06, centerX: am5.p50, centerY: am5.p50,
      // loops: 0 = forever; yoyo flies back; keep `from` explicit (a row without positionOnLine would jump to `to`)
      animations: [{ target: "dataItem", key: "positionOnLine", from: 0, to: 1,
                     duration: 6000, loops: 0, yoyo: true, easing: "cubic", ease: "inOut" }]
    })
  });
});
planeSeries.data.setAll([{ lineId: "jfk-lhr", positionOnLine: 0, autoRotate: true }]);
```

**Direction on the way back:** an `autoRotate` point turns round to face its travel direction **only** while an `animations` entry on its bullet sprite (`target: "dataItem"`, `key: "positionOnLine"`) moves it back toward the line start. A point moved by `dataItem.animate()` from code keeps facing the line's direction — that code has to turn it (the globe flight-path example, `get_example(path: "examples/maps/globe-with-animated-flight-path")`, rotates its plane between segments itself). An endless loop started on a data item in code is saved by `ChartSerializer`: loops that differ per point are saved in each point's data row, under the bullet sprite's `templateField` (`"bulletSettings"` if none is set); one loop shared by all points still goes on the sprite. Where the rows can't hold them, the first one still goes on the sprite for all: points made from geodata, a sprite with its own `animations`, or a loop animating object values. Every point placed on a line follows the line when it changes (e.g. gets an `altitude`), not just one of them.

**IMPORTANT — Multi-segment lines and `positionOnLine`:**
- `positionOnLine` treats the entire line as one unit (0 = start, 1 = end). If a line has multiple segments (i.e. `pointsToConnect` has 3+ points), position 0.5 is the midpoint of the *entire* path.
- For advanced per-segment animations (e.g., scaling the bullet differently at the midpoint of each segment, or pausing between segments), use **single-segment lines** (2 points each) instead of one multi-segment line. This gives you full control: animate `positionOnLine` from 0 to 1 on each segment line sequentially, with independent timing, easing, and callbacks per segment.
- Example: instead of one line with points [A, B, C, D], create three lines: [A,B], [B,C], [C,D] and animate the bullet across them one at a time.

### Live updates: moving points, growing lines

- **Move a point:** set `latitude`/`longitude` on its data item. The bullet moves on the next frame, and no `markDirtyValues()` is needed. `series.data.setIndex(i, row)` animates the move under the Animated theme. Setting only `geometry` doesn't move it.
- **Change a line or polygon every frame:** set a new `geometry` object on the element (`dataItem.get("mapLine")` / `"mapPolygon"`). The same object, changed, goes unnoticed. `data.setIndex()` animates each field over `interpolationDuration` (600 ms in the Animated theme), and a geometry can't be animated, so it switches only when that time ends. A row replaced every frame keeps its first shape. With `setIndex`, set `interpolationDuration: 0` on the series.
- **A line that grows** (a track, a route drawn as it happens): keep one data item and set a new geometry on its line each frame, rather than replacing the data.

```js
var track = chart.series.push(am5map.MapLineSeries.new(root, {}));
track.data.setAll([{ geometry: { type: "LineString", coordinates: [[0, 0], [1, 0]] } }]);

function grow(coords) {   // call from your own timer
  track.dataItems[0].get("mapLine").set("geometry", { type: "LineString", coordinates: coords });
}
```

- **Many lines moving the same way** (traffic, flows): group them into a few lines with a `MultiLineString` each and animate those. With 1,500 two-point roads, one animated line per road ran at 12 fps; six bands ran at 31 fps (headless Chromium).

```js
var roads = chart.series.push(am5map.MapLineSeries.new(root, {}));
roads.mapLines.template.setAll({
  stroke: am5.color(0xe03030), strokeWidth: 2, strokeDasharray: [6, 4],
  animations: [{ key: "strokeDashoffset", from: 0, to: -10, duration: 1000, loops: 0 }]   // -10 = one dash + gap; 0 = forever
});
roads.data.setAll([
  { geometry: { type: "MultiLineString", coordinates: [[[-10, 40], [20, 50]], [[-10, 30], [20, 35]]] } },   // one band: all its roads
  { geometry: { type: "MultiLineString", coordinates: [[[30, 0], [40, 20], [50, 10]]] } }
]);
```

### Textures in map shapes (clouds, smoke, storms)

A `PicturePattern` on polygons shows a texture inside their shapes. Blobs from `am5map.getGeoCircle(point, radiusDegrees)` make soft outlines, and several overlapping ones a ragged edge.

```js
var storms = chart.series.push(am5map.MapPolygonSeries.new(root, { affectsBounds: false }));
storms.mapPolygons.template.setAll({
  fill: am5.color(0x996633), fillOpacity: 0.25,   // a haze under the texture
  strokeOpacity: 0,
  fillPattern: am5.PicturePattern.new(root, { src: textureUrl, width: 16, height: 16, fit: "pattern" })
});
storms.data.setAll([{ geometry: am5map.getGeoCircle({ longitude: 0, latitude: 0 }, 30) }]);
```

- The pattern is drawn over the fill, at its own opacity: `fillOpacity` dims only the fill. With a see-through texture, a full fill turns its gaps into one flat color.
- To move the shapes, set a new `geometry` on each `mapPolygon` (see "Live updates").

### Raised lines and points — `altitude`

`altitude` on a `MapLine` lifts each leg (one point to the next) into an arc, in **metres** at its middle (`400000` ≈ the ISS). Once set (even `0`), the line's ends sit at the `altitude` of the points it connects (or the third value of their coordinates); without it the line stays on the surface. Not applied to `lineType: "straight"`. On flat maps lines rise up the screen.

```js
var pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
pointSeries.bullets.push(function (root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Circle.new(root, { radius: 5, fill: am5.color(0xff621f), tooltipText: "{title}" })
  });
});
pointSeries.data.setAll([
  { id: "london", title: "London", latitude: 51.5072, longitude: -0.1276 },
  { id: "new-york", title: "New York", latitude: 40.7128, longitude: -74.006 },
  { id: "singapore", title: "Singapore", latitude: 1.3521, longitude: 103.8198 }
]);

var lineSeries = chart.series.push(am5map.MapLineSeries.new(root, { pointSeries: pointSeries }));
lineSeries.mapLines.template.setAll({
  altitude: 1000000,   // each route rises 1,000 km in its middle and lands on both cities
  stroke: am5.color(0xff621f),
  strokeWidth: 2
});
lineSeries.data.setAll([{ pointIds: ["london", "new-york"] }, { pointIds: ["london", "singapore"] }]);

// Points raised by a data value — altitudeField has NO default, so declare it
var satSeries = chart.series.push(am5map.MapPointSeries.new(root, { altitudeField: "altitude" }));
satSeries.bullets.push(function (root, series, dataItem) {
  return am5.Bullet.new(root, { sprite: am5.Circle.new(root, { radius: 4, fill: am5.color(0x0066cc) }) });
});
satSeries.data.setAll([{ latitude: 10, longitude: 0, altitude: 400000 }]);   // metres
```

A point on a line takes the line's altitude. `mapLine.positionToAltitude(position)` returns the altitude (m) at `0`–`1` along a line. Works best on a globe (`geoOrthographic`, see `map-altitude-arcs` in the npm package examples).

**Heights per coordinate:** once a line's `altitude` is set (`0` for none), the third value of each coordinate is that point's height in metres, and a non-zero `altitude` adds its arc to each leg on top. One line can then change height along its length (a descent from orbit, relief along a parallel); without `altitude` the third values are ignored. The demo "Globe with raised parallels" (`maps/globe-with-raised-parallels`) does this.

```js
var pathSeries = chart.series.push(am5map.MapLineSeries.new(root, {}));
pathSeries.mapLines.template.setAll({ altitude: 0, stroke: am5.color(0xff621f), strokeWidth: 2 });
pathSeries.data.setAll([{ geometry: { type: "LineString",
  coordinates: [[0, 0, 800000], [20, 10, 400000], [40, 15, 0]] } }]);   // [longitude, latitude, metres]
```

**Where a raised point is on screen:** for a `MapPointSeries` point, `dataItem.get("point")` is its raised position (`{ x, y }`) and `dataItem.get("clipped")` says whether it is hidden. For any other longitude, latitude and height `h` (MapUtils' `projectAltitude` is not exported): on a globe, `screen = T + (P − T) × (1 + h / R)`, with `P = chart.convert({ longitude, latitude })` the surface point, `T = chart.get("projection").translate()` the globe's center and `R = 6371008.8` m; on a flat map, `P` moved up by `h / R × projection.scale()`.

**`clipFront: true`** on a point series hides the points on the near side. With points raised high (`altitudeField`), what is left are the ones behind the globe that rise past its edge: a starfield behind the globe (`clipBack`, default `true`, still hides those the globe covers).

**`ClusteredPointSeries` with `altitudeField`:** a cluster's bullet floats at the average altitude of its points (the cluster data item gets `altitude`); points still cluster by their distance on screen.

### Bullets on the surface — `surfaceBullets`

`surfaceBullets: true` on a `MapPointSeries` lays its bullets onto the map: each turns, squashes and stretches with the projection, as if painted on — on a globe they flatten towards the edge, on Mercator they grow towards the poles. Default `false` (bullets stay upright and unscaled).

```js
var pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {
  surfaceBullets: true
}));
pointSeries.bullets.push(function (root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Graphics.new(root, {   // a pin with its tip at the point
      svgPath: "M0,0 C-5,-7 -9,-11 -9,-17 A9,9 0 1 1 9,-17 C9,-11 5,-7 0,0 Z M0,-20.5 A3.5,3.5 0 1 0 0,-13.5 A3.5,3.5 0 1 0 0,-20.5 Z",
      fill: am5.color(0xff621f),
      tooltipText: "{title}"
    })
  });
});
pointSeries.data.setAll([
  { title: "London", latitude: 51.5072, longitude: -0.1276 },
  { title: "Tokyo", latitude: 35.6895, longitude: 139.6917 }
]);
```

Heat-rule-sized circles work too (`polygonIdField` + a bullet `am5.Template` as the rule target) — circles that lie flat on a globe.

### Pixel maps — `PixelMapSeries`

`am5map.PixelMapSeries` extends `MapPolygonSeries` and draws its polygons as a grid of pixels that rotate and zoom with the map. Each pixel takes the look of the polygon it falls in, so **style `mapPolygons.template`, not the pixels** — heat rules, `templateField`, states and tooltips work as on `MapPolygonSeries` (the polygons stay, invisible, to take hover and clicks).

```js
var pixelSeries = chart.series.push(am5map.PixelMapSeries.new(root, {
  geoJSON: am5geodata_worldLow,
  pixelType: "circle",       // "square" (default), "circle", "diamond", "hexagon"
  step: 2,                   // degrees between pixel centers (default 2)
  uniform: true,             // an even on-screen grid of same-size pixels, like a pixel image of the map
  valueField: "value",
  calculateAggregates: true  // the heat rule needs the value range
}));
pixelSeries.mapPolygons.template.setAll({
  fill: root.interfaceColors.get("disabled"),   // countries without data
  tooltipText: "{name}: {value}"
});
pixelSeries.set("heatRules", [{
  target: pixelSeries.mapPolygons.template, dataField: "value",
  min: am5.color(0xffd19a), max: am5.color(0xb3261e), key: "fill"
}]);
pixelSeries.data.setAll([{ id: "US", value: 38 }, { id: "IN", value: 28 }, { id: "NG", value: 18 }]);
```

3D columns — the height of each polygon's columns comes from `MapPolygon` `pixelHeight` (`0`–`1` of `columnHeight`; set it with a heat rule or `templateField`); without it, from the polygon's value between the series' low and high (needs `calculateAggregates`):

```js
var columns = chart.series.push(am5map.PixelMapSeries.new(root, {
  geoJSON: am5geodata_worldLow,
  valueField: "value",
  calculateAggregates: true,
  columnHeight: 0.15      // tallest column = 15% of the globe's radius (0 = flat, the default)
}));
columns.set("heatRules", [{
  target: columns.mapPolygons.template, dataField: "value",
  min: 0.1, max: 1, key: "pixelHeight"
}]);
columns.mapPolygons.template.setAll({ stroke: am5.color(0xffffff), strokeWidth: 0.5 }); // outline each pixel
columns.data.setAll([{ id: "CN", value: 1410 }, { id: "IN", value: 1430 }, { id: "ID", value: 277 }]);
```

| Setting | Default | Notes |
|---------|---------|-------|
| `step` | `2` | Degrees between pixel centers (with `uniform`, measured at the map's center). Finest `0.001`; a step that would make more than **500,000 pixels** is coarsened, with a console warning — small maps can take a fine step (e.g. `0.01` for a small country) |
| `pixelSize` | `0.8` | Pixel size relative to `step`; `1` = neighbors touch |
| `pixelType` | `"square"` | `"circle"`, `"diamond"`, `"hexagon"` |
| `stagger` | unset | Shift every other row by half a pixel; unset = squares in columns, circles staggered; hexagons/diamonds always staggered |
| `equalArea` | `true` | Fewer pixels per row towards the poles; `false` = same count every row |
| `equalWidth` | `false` | Every pixel as wide as on the equator (overlap towards the poles); not with `uniform` |
| `uniform` | `false` | Even on-screen grid, upright same-size pixels; `equalArea` and `columnHeight` do not apply |
| `columnHeight` | `0` | Raises pixels into columns; tallest = this share of the globe's radius |
| `pixelHeightFunction` | — | `(dataItem, longitude, latitude) => 0..1` per pixel; overrides `pixelHeight`; **not saved** to JSON (heat-rule heights are) |

Pixels have no outline by default (the theme keeps pixel polygons at `strokeWidth: 0`); set `strokeWidth` (and `stroke`) on `mapPolygons.template` to outline each pixel. `pixelSeries.pixels` is the `Graphics` that draws them all: `am5.renderToSVG(pixelSeries.pixels)` exports just the pixels, with each polygon's pixels in a `<g id="US" data-name="United States">`. `include`/`exclude` work as on `MapPolygonSeries`.

**The classic dotted map** is `pixelType: "circle"` with `uniform: true`. Without `uniform` the pixels lie on the surface, so on projections such as `geoEqualEarth` the circles near the poles widen into ovals.

**`step` for one country:** `step` is in degrees, so the default `2` suits the world but leaves a country only a few pixels. Derive it from the area shown, its longer side divided by the pixels wanted across it:

```js
var geodata = am5geodata_germanyLow;
var b = am5map.getGeoBounds({ type: "GeometryCollection",
  geometries: geodata.features.map(function (f) { return f.geometry; }) });   // { left, right, top, bottom } in degrees
var countrySeries = chart.series.push(am5map.PixelMapSeries.new(root, {
  geoJSON: geodata,
  step: Math.max(b.right - b.left, b.top - b.bottom) / 60   // about 60 pixels across
}));
```

(`getGeoBounds` spans `-180`…`180` for a shape that crosses the 180th meridian.)

**Random column heights:** `pixelHeightFunction` runs again for every pixel whenever heights are recomputed (data changes, a hover state that sets `MapPolygon` `pixelHeight`, …), so `Math.random()` makes the columns jump. Use a deterministic hash of longitude and latitude, times the polygon's own height so per-polygon heights still apply:

```js
var hashed = chart.series.push(am5map.PixelMapSeries.new(root, {
  geoJSON: am5geodata_worldLow,
  columnHeight: 0.1,
  pixelHeightFunction: function (dataItem, longitude, latitude) {
    var h = Math.sin(longitude * 12.9898 + latitude * 78.233) * 43758.5453;
    return (h - Math.floor(h)) * dataItem.get("mapPolygon").get("pixelHeight", 1);
  }
}));
```

### Satellite images — `MapRasterSeries`

`am5map.MapRasterSeries` shows one image of the whole world, reprojected as the map turns and zooms (on the GPU with WebGL2 for orthographic, equirectangular, Mercator, Equal Earth and Natural Earth; on the CPU otherwise). With `nightSrc` and a sun position it shows day where the sun is up and night where it is down, blended across twilight.

```js
var raster = chart.series.push(am5map.MapRasterSeries.new(root, {
  src: "https://cdn.amcharts.com/lib/5/geodata/images/earthDay2048.jpg",       // the world by day
  nightSrc: "https://cdn.amcharts.com/lib/5/geodata/images/earthNight2048.jpg", // optional: city lights at night
  sunDate: "now"   // or sunPosition: { longitude, latitude } — needed only with nightSrc
}));
raster.events.on("loaded", function () { /* src (and nightSrc) are drawn */ });
raster.events.on("loaderror", function () { /* failed, or cross-origin without CORS */ });
```

- The image must be **equirectangular** ("plate carrée": longitude −180…180 left to right, latitude 90…−90 top to bottom). The CDN has `geodata/images/`, e.g. `https://cdn.amcharts.com/lib/5/geodata/images/moon2048.jpg`:
  - `earthDay`, `earthNight`, `moon`, `mars`, `venus`, `io` and `ganymede`, each at 1024, 2048 and 4096 px wide;
  - `jupiter` at 1024 and 2048 only.

  Use 1024 for small charts, 2048 for most charts and phones, and 4096 for large charts and zooming in; a 4096 image takes 32 MB of memory once decoded. The geodata npm package has them in `images/`, and a bundler turns an import into the URL: `import moon from "@amcharts/amcharts5-geodata/images/moon2048.jpg"`.

  All are NASA, JPL or USGS works in the public domain, and credit is appreciated, not required. For the Earth images, a credit line reads "Imagery: NASA Earth Observatory", e.g. in a small `am5.Label`.
- `cors` (default `"anonymous"`): a cross-origin image is drawn only if its server sends CORS headers. A page opened from disk (`file://`) cannot read an image file beside it — serve the page, or use the CDN copy.
- The series counts as the whole world in the map's bounds; on a regional map set `affectsBounds: false`. The projection needs `invert`, so nothing is drawn while `animateProjection()` runs.
- Draw country borders over it with a `MapPolygonSeries` pushed after it, `fillOpacity: 0` on `mapPolygons.template`. `twilight` (default `12` degrees of sun height) sets how wide the day/night blend is.

### Night shading — `NightSeries` and `getSunPosition()`

`am5map.NightSeries` shades the part of the map where it is night, with the twilight in steps, and shows the sun. It never affects the map's bounds.

```js
chart.series.push(am5map.MapPolygonSeries.new(root, { geoJSON: am5geodata_worldLow }));

var night = chart.series.push(am5map.NightSeries.new(root, {
  sunDate: "now",        // "now" (moves on every minute), a timestamp, a date string or a Date
  sunAltitude: 2000000   // metres: keeps the sun in view a little past a globe's edge (default 0)
}));
night.mapPolygons.template.setAll({ fill: am5.color(0x000033), fillOpacity: 0.6 }); // full night (default black, 0.5)
night.get("sun").setAll({ radius: 12 });   // the sun is a Circle the series makes; or set any sprite as `sun`

// …or a fixed moment instead of the clock — where the sun is overhead at a given time:
// am5map.NightSeries.new(root, { sunPosition: am5map.getSunPosition(new Date("2026-06-21T12:00:00Z")) })
```

- `sunDate` overrides `sunPosition`. To change a `Date`, set a new object — mutating the same one goes unnoticed.
- `twilight` (default `12`) and `twilightSteps` (default `4`, number of shades from day to full night).
- `sunAltitude` is for looks: `1000000`–`5000000` suits a globe.
- `am5map.getSunPosition(date?)` takes a `Date` (default now) and returns the `{ longitude, latitude }` where the sun is directly overhead — also for `MapRasterSeries` `sunPosition`, or to place a sun marker on a `MapPointSeries`.
- **JSON:** a saved chart keeps `sunDate`, never the `sunPosition` worked out from it, so a `"now"` map still follows the clock after parsing.

**Day and night on another body.** `sunDate` is the Earth's sun only, so for the Moon or Mars set `sunPosition` to that body's sub-solar point (from your own ephemeris), and leave `sunDate` unset (it wins). A small `twilight` gives an airless body's sharp edge:

```js
chart.series.push(am5map.MapRasterSeries.new(root, {
  src: "https://cdn.amcharts.com/lib/5/geodata/images/moon2048.jpg"
}));
var lunarNight = chart.series.push(am5map.NightSeries.new(root, {
  sunPosition: { longitude: 30, latitude: 1.5 },   // the Moon's sub-solar point
  twilight: 1.5                                    // default 12 (degrees)
}));
lunarNight.get("sun").set("forceHidden", true);    // hide the sun marker
```

## Zoom controls

```js
chart.set("zoomControl", am5map.ZoomControl.new(root, {}));
// its home button is hidden by default: zoomControl.homeButton.set("visible", true)

// Programmatic zoom
chart.zoomToGeoPoint({ latitude: 48.86, longitude: 2.35 }, 5, true);  // zoom level 5; true centers the point

// Zoom to a polygon (prefer clickZoom, below)
polygonSeries.mapPolygons.template.events.on("click", function(ev) {
  polygonSeries.zoomToDataItem(ev.target.dataItem);
});

// Home button
chart.chartContainer.children.push(am5.Button.new(root, {
  label: am5.Label.new(root, { text: "Home" })
})).events.on("click", function() {
  chart.goHome();
});

// Fit to a lat/long box (left/right = longitude, top/bottom = latitude)
chart.zoomToGeoBounds({ left: -10, right: 40, top: 70, bottom: 35 }, 800);
```

**Pointer zoom behaviors:** a double click/tap zooms in and shift + double click zooms out **by default** (`doubleClickZoom: true`; set `false` to turn it off). `boxZoom` lets the user drag a rectangle to zoom into — its value is the key that must be held: `"shift"`, `"ctrl"`, `"alt"`, or `"drag"` for no key (then set `panX`/`panY: "none"`, since a plain drag otherwise pans); default `"none"`. The rectangle being drawn is `chart.boxZoomSelection` (a `Rectangle`, style its `fill`/`stroke`).

```js
am5map.MapChart.new(root, {
  boxZoom: "shift",          // hold shift and drag to zoom into the box
  doubleClickZoom: false     // opt out of the default double-click zoom
});
chart.boxZoomSelection.setAll({ fill: am5.color(0x1e88e5), fillOpacity: 0.15 });
```

**Zoom to a clicked polygon — `clickZoom`.** One setting does what a click handler with `zoomToDataItem` and a background click would: a click zooms to the polygon (on a map panned by rotating — `panX: "rotateX"`/`panY: "rotateY"` — the globe also turns to it); clicking it again or the map background goes back home. The polygon zoomed to is `active`, so an `"active"` state marks it. Default `false`; works from JSON.

```js
var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
  geoJSON: am5geodata_worldLow,
  clickZoom: true
}));
polygonSeries.mapPolygons.template.setAll({ tooltipText: "{name}", interactive: true });
polygonSeries.mapPolygons.template.states.create("active", {
  fill: root.interfaceColors.get("primaryButtonActive")
});
```

`clickZoom` sets `active` itself, so `toggleKey: "active"` is not needed (with it, the two still agree).

**Start at the home position — `autoHome`.** Goes to the home position (`homeGeoPoint`, `homeZoomLevel`, `homeRotationX`/`homeRotationY`) once the map is loaded, as `goHome()` does — animated when `animationDuration` is set (the Animated theme sets it), at once otherwise. No `goHome()` call in a `datavalidated` handler needed, so a map loaded from JSON can start zoomed in. Default `false`.

```js
var chart = root.container.children.push(am5map.MapChart.new(root, {
  projection: am5map.geoMercator(),
  homeGeoPoint: { longitude: 14, latitude: 51 },   // central Europe…
  homeZoomLevel: 3.5,                              // …zoomed in 3.5 times
  autoHome: true
}));
```

With `autoHome` and no `homeZoomLevel`, home zoom is `1`, so a starting `zoomLevel` is overridden: set `homeZoomLevel` to the same value. Without `autoHome`, a `zoomLevel` given to `MapChart.new()` is the starting zoom.

**Detailed geodata: make the first fit instant.** Every frame of an animated zoom re-projects the whole map. A 1 s `zoomToGeoPoint()` on worldUltra cost 1.4 s of main-thread work against 0.2 s instant, and ran at about 3 fps with the CPU slowed 4×. Use `chart.goHome(0)` (safe before the map loads) or a duration of `0`, not `autoHome`, which animates under the Animated theme (`animationDuration` 1000).

**Home on a globe.** On a map that pans by rotating (`panX: "rotateX"`, `panY: "rotateY"`), `goHome()` and the home button restore the rotation only from `homeRotationX` / `homeRotationY` (−longitude / −latitude). `homeGeoPoint` alone leaves the rotation as it is and shifts the globe sideways.

```js
var globe = root.container.children.push(am5map.MapChart.new(root, {
  projection: am5map.geoOrthographic(),
  panX: "rotateX",
  panY: "rotateY",
  homeRotationX: -10,   // home = centered on 10°E…
  homeRotationY: -50    // …50°N
}));
```

**`zoomStep`** (default `2`) is a multiplier, not an increment: `zoomIn()` and the + button multiply `zoomLevel` by it, `zoomOut()` divides, and the mouse wheel and double clicks use it too, one step per wheel event whatever its size. So keep it above `1` (`1` does nothing, below `1` reverses); `zoomStep: 1.1`–`1.2` gives fine control, e.g. on trackpads, which fire many small wheel events. `wheelSensitivity` does not affect wheel zoom, only wheel rotation.

## Globe (orthographic projection)

```js
var chart = root.container.children.push(am5map.MapChart.new(root, {
  projection: am5map.geoOrthographic(),
  panX: "rotateX",     // drag to rotate horizontally
  panY: "rotateY",     // drag to rotate vertically
  rotationX: -70,      // initial longitude center
  rotationY: -30       // initial latitude center
}));

// Rotate to a specific point (center it on the globe)
// IMPORTANT: use NEGATIVE longitude and NEGATIVE latitude
var paris = { latitude: 48.8566, longitude: 2.3522 };
chart.animate({ key: "rotationX", to: -paris.longitude, duration: 1500, easing: am5.ease.out(am5.ease.cubic) });
chart.animate({ key: "rotationY", to: -paris.latitude, duration: 1500, easing: am5.ease.out(am5.ease.cubic) });
```

**IMPORTANT — Globe rotation direction:** To center the globe on a geographic point, set `rotationX` to **negative longitude** and `rotationY` to **negative latitude**. This is because rotation moves the globe under the camera: rotating X by -2.35 brings longitude 2.35 to the center. A common mistake is using positive coordinates, which rotates the globe in the wrong direction.

Setting `rotationX`/`rotationY`/`rotationZ` on every frame (e.g. a camera that follows a moving point) performs fine.

## GraticuleSeries (grid lines / ocean background)

```js
// Background (ocean)
var backgroundSeries = chart.series.unshift(am5map.MapPolygonSeries.new(root, {}));
backgroundSeries.mapPolygons.template.setAll({
  fill: am5.color(0xddeeff),
  stroke: am5.color(0xddeeff)
});
backgroundSeries.data.push({
  geometry: am5map.getGeoRectangle(90, 180, -90, -180) // full globe background
});

// Grid lines (graticule)
chart.series.push(am5map.GraticuleSeries.new(root, {}));
```

## Drill-down map (country > states)

```js
// World-level polygons
var worldSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
  geoJSON: am5geodata_worldLow
}));

// Country-level polygons (initially hidden)
var countrySeries = chart.series.push(am5map.MapPolygonSeries.new(root, {}));

// Click a country to drill down
worldSeries.mapPolygons.template.events.on("click", function(ev) {
  var country = ev.target.dataItem.dataContext.id; // ISO alpha-2 code, e.g., "US"
  // Load country geodata dynamically
  import("@amcharts/amcharts5-geodata/usaLow").then(function(geodata) {
    countrySeries.set("geoJSON", geodata.default);
    worldSeries.zoomToDataItem(ev.target.dataItem);
  });
});

// Back button
chart.chartContainer.children.push(am5.Button.new(root, {
  label: am5.Label.new(root, { text: "Back" })
})).events.on("click", function() {
  countrySeries.set("geoJSON", undefined);
  chart.goHome();
});
```

## Additional MapChart settings

```js
am5map.MapChart.new(root, {
  maxZoomLevel: 32,         // max zoom
  minZoomLevel: 1,          // min zoom
  maxPanOut: 0.4,           // how far user can pan beyond map (0-1)
  rotationX: 0,             // center longitude
  rotationY: 0,             // center latitude
  rotationZ: 0,             // tilt
  wheelY: "zoom",           // "zoom", "rotateX", "rotateY", "none"
  zoomStep: 2,              // zoom multiplier per step: buttons, wheel, double click (see "Zoom controls")
  wheelSensitivity: 1,      // how far the wheel rotates the map ("rotateX"/"rotateY" only), not zoom speed
  doubleClickZoom: true,    // double click zooms in, shift+double click out — default true
  boxZoom: "none",          // "none" | "drag" | "shift" | "ctrl" | "alt" — key held to drag a zoom box
  autoHome: false,          // true = go to homeGeoPoint/homeZoomLevel once loaded
})
```

`maxPanOut: 0` with `panX: "translateX"` stops all dragging at zoom level 1, on both axes. On a world map, pair it with `panX: "rotateX"`, which `maxPanOut` does not limit.

**Polygon IDs** use ISO 3166-1 alpha-2 codes (e.g., `"US"`, `"CN"`, `"DE"`, `"FR"`).

**Accessibility:** focusable map polygons can be focused, objects left out with `include`/`exclude` get no focus element, objects panned or zoomed out of view are not TAB stops until at least partly back in view, and the zoom level is announced to screen readers once zooming settles.

## Events on map elements

```js
// Polygon click
polygonSeries.mapPolygons.template.events.on("click", function(ev) {
  var id = ev.target.dataItem.dataContext.id;    // "US", "CN", etc.
  var name = ev.target.dataItem.dataContext.name; // "United States"
  console.log("Clicked:", name, id);
});

// Point/marker click
pointSeries.bullets.push(function(root, series, dataItem) {
  var circle = am5.Circle.new(root, { radius: 5, fill: am5.color(0xff0000) });
  circle.events.on("click", function() {
    console.log("Marker:", dataItem.dataContext);
  });
  return am5.Bullet.new(root, { sprite: circle });
});

// Map zoom — use settings watch, NOT events (there is no "zoomlevelchanged" event)
chart.on("zoomLevel", function(zoomLevel) {
  console.log("Zoom:", zoomLevel);
});

// Links without a click handler: urlField on MapPolygonSeries, MapLineSeries or MapPointSeries (bullets)
var linkedSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
  geoJSON: am5geodata_worldLow, urlField: "url", linkTarget: "_blank"   // linkTarget default "_self"
}));
linkedSeries.data.setAll([{ id: "US", url: "https://en.wikipedia.org/wiki/United_States" }]);
```

`urlField` has no default (links are off until set); the pointer cursor is set automatically unless you set `cursorOverStyle`; `javascript:`/`data:`/`vbscript:` URLs are never opened; override `series.openUrl(dataItem)` to intercept. `MapSankeySeries` nodes are not linked — keep a `click` handler there.

**Which pixels:** `chart.convert(geoPoint)` and `chart.invert(point)` work in the map's area, inside the chart's padding (`chart.seriesContainer`'s space). A pointer event's `ev.point` is relative to the root. So the place clicked is `chart.invert(chart.seriesContainer.toLocal(ev.point))`, and a geo point's page position is `chart.seriesContainer.toGlobal(chart.convert(geoPoint))` plus the chart div's `getBoundingClientRect()`. `chart.invert(ev.point)` on its own is right only for a chart at the root's top-left with no padding. Docs: https://docs.amcharts.com/v5/charts/map-chart/map-api/#converting-between-screen-and-geographical-coordinates.

## Disposal

In a React, Vue, Angular, Svelte or React Native app, use amCharts' framework package, which disposes the root with the component (SKILL.md, "Frameworks"). Otherwise dispose the root when the component goes away:

```js
root.dispose();
```


---

# amCharts 5 Map Charts — Working Examples

## Example 1: World choropleth map

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>World Choropleth Map</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);

    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "rotateX",
      panY: "translateY",
      projection: am5map.geoNaturalEarth1()
    }));

    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_worldLow,
      valueField: "value",
      calculateAggregates: true
    }));

    polygonSeries.mapPolygons.template.setAll({
      tooltipText: "{name}: {value}",
      interactive: true
    });

    polygonSeries.mapPolygons.template.states.create("hover", {
      fill: am5.color(0x677935)
    });

    // Heat rules for choropleth coloring
    polygonSeries.set("heatRules", [{
      target: polygonSeries.mapPolygons.template,
      dataField: "value",
      min: am5.color(0xcee8c0),
      max: am5.color(0x1d6e04),
      key: "fill"
    }]);

    // Sample data — population in millions
    polygonSeries.data.setAll([
      { id: "US", value: 331 }, { id: "CN", value: 1411 },
      { id: "IN", value: 1380 }, { id: "BR", value: 213 },
      { id: "RU", value: 146 }, { id: "JP", value: 126 },
      { id: "DE", value: 83 }, { id: "GB", value: 67 },
      { id: "FR", value: 67 }, { id: "AU", value: 26 }
    ]);

    // Heat legend
    var heatLegend = chart.children.push(am5.HeatLegend.new(root, {
      orientation: "horizontal",
      startColor: am5.color(0xcee8c0),
      endColor: am5.color(0x1d6e04),
      startText: "Low",
      endText: "High",
      stepCount: 5
    }));

    chart.set("zoomControl", am5map.ZoomControl.new(root, {}));
    chart.appear(1000, 100);
  </script>
</body>
</html>
```

## Example 2: Map with point markers (bubble map)

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Bubble Map</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);

    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "translateX",
      panY: "translateY",
      projection: am5map.geoMercator()
    }));

    // Background polygons — excluding Antarctica here because Mercator makes it disproportionately large
    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_worldLow,
      exclude: ["AQ"]
    }));
    polygonSeries.mapPolygons.template.setAll({
      fill: am5.color(0xdddddd),
      strokeWidth: 0.5,
      stroke: am5.color(0xffffff)
    });

    // Point series with scaled bubbles
    var pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {
      latitudeField: "latitude",
      longitudeField: "longitude"
    }));

    var cities = [
      { name: "New York", latitude: 40.71, longitude: -74.01, population: 8.3 },
      { name: "London", latitude: 51.51, longitude: -0.13, population: 9.0 },
      { name: "Tokyo", latitude: 35.68, longitude: 139.69, population: 13.5 },
      { name: "Paris", latitude: 48.86, longitude: 2.35, population: 2.2 },
      { name: "Sydney", latitude: -33.87, longitude: 151.21, population: 5.3 }
    ];

    // Scale bubble size by population
    pointSeries.bullets.push(function(root, series, dataItem) {
      var value = dataItem.dataContext.population;
      return am5.Bullet.new(root, {
        sprite: am5.Circle.new(root, {
          radius: Math.sqrt(value) * 4,
          fill: am5.color(0xff5733),
          fillOpacity: 0.5,
          strokeWidth: 1,
          stroke: am5.color(0xffffff),
          tooltipText: "{name}\nPopulation: {population}M"
        })
      });
    });

    pointSeries.data.setAll(cities);

    chart.set("zoomControl", am5map.ZoomControl.new(root, {}));
    chart.appear(1000, 100);
  </script>
</body>
</html>
```

## Example 3: US map with connection lines

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>US Flight Routes</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/usaLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);

    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "translateX",
      panY: "translateY",
      projection: am5map.geoAlbersUsa()
    }));

    // State polygons
    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_usaLow
    }));
    polygonSeries.mapPolygons.template.setAll({
      fill: am5.color(0xdadada),
      stroke: am5.color(0xffffff),
      strokeWidth: 1,
      tooltipText: "{name}"
    });

    // Point series for airports
    var pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
    pointSeries.bullets.push(function(root, series, dataItem) {
      return am5.Bullet.new(root, {
        sprite: am5.Circle.new(root, {
          radius: 4,
          fill: am5.color(0x3355ff),
          stroke: am5.color(0xffffff),
          strokeWidth: 1,
          tooltipText: "{name}"
        })
      });
    });

    var jfk = pointSeries.pushDataItem({ latitude: 40.64, longitude: -73.78, name: "JFK" });
    var lax = pointSeries.pushDataItem({ latitude: 33.94, longitude: -118.41, name: "LAX" });
    var ord = pointSeries.pushDataItem({ latitude: 41.97, longitude: -87.91, name: "ORD" });
    var atl = pointSeries.pushDataItem({ latitude: 33.64, longitude: -84.43, name: "ATL" });

    // Line series for routes
    var lineSeries = chart.series.push(am5map.MapLineSeries.new(root, {}));
    lineSeries.mapLines.template.setAll({
      stroke: am5.color(0x3355ff),
      strokeOpacity: 0.6,
      strokeWidth: 2
    });

    lineSeries.pushDataItem({ pointsToConnect: [jfk, lax] });
    lineSeries.pushDataItem({ pointsToConnect: [jfk, ord] });
    lineSeries.pushDataItem({ pointsToConnect: [ord, atl] });
    lineSeries.pushDataItem({ pointsToConnect: [atl, lax] });

    chart.appear(1000, 100);
  </script>
</body>
</html>
```

## More examples

Longer complete examples are in the amCharts MCP server's examples (https://mcp.amcharts.com/mcp; the same files are in the amcharts/amcharts5-mcp repository, under `extended/examples/`):
- Globe with animated flight path (positionOnLine + globe rotation): `get_example(path: "examples/maps/globe-with-animated-flight-path")`
- Map Sankey — Global Coffee Supply Chain (sourceId/targetId): `get_example(path: "examples/maps/map-sankey-coffee-supply-chain")`
- Map Sankey — Oil Export Flows with Waypoints (explicit coordinates): `get_example(path: "examples/maps/map-sankey-oil-export-flows")`
