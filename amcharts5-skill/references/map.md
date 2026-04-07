# Map Charts — Geographic Visualizations


## Required imports

### ES modules
```ts
import * as am5 from "@amcharts/amcharts5";
import * as am5map from "@amcharts/amcharts5/map";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
import am5geodata_worldLow from "@amcharts/amcharts5-geodata/worldLow";
```

### CDN
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/map.js"></script>
<script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

## Geodata (map files)

NPM: `@amcharts/amcharts5-geodata`
CDN: `https://cdn.amcharts.com/lib/5/geodata/{mapName}.js`

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
- US congressional districts: `geodata/usaCongressionalLow.js` (may not be available on all CDN versions)

## Core setup

```js
const root = am5.Root.new("chartdiv");
root.setThemes([am5themes_Animated.new(root)]);

const chart = root.container.children.push(
  am5map.MapChart.new(root, {
    panX: "rotateX",           // "translateX", "rotateX", "none"
    panY: "translateY",        // "translateY", "rotateY", "none"
    projection: am5map.geoMercator(),
    // homeGeoPoint: { latitude: 48.8566, longitude: 2.3522 },
    // homeZoomLevel: 5
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

### MapPointSeries (markers, bubbles, labels)

```js
const pointSeries = chart.series.push(
  am5map.MapPointSeries.new(root, {
    latitudeField: "latitude",
    longitudeField: "longitude"
  })
);

// Define bullet appearance
pointSeries.bullets.push(function() {
  return am5.Bullet.new(root, {
    sprite: am5.Circle.new(root, {
      radius: 5,
      fill: am5.color(0xff0000),
      tooltipText: "{name}"
    })
  });
});

// Add points via data — requires latitudeField/longitudeField on the series
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
```

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

IDs use ISO 3166-1 alpha-2 codes matching the geodata. **CRITICAL:** Data MUST be set inside `polygonSeries.events.once("datavalidated", ...)` — the polygon series needs to finish parsing geoJSON before MapSankeySeries can resolve country IDs to centroids. Without this wrapper, `getDataItemById()` returns null and flows silently don't appear (no errors, no bands).

```js
polygonSeries.events.once("datavalidated", function() {
  sankeySeries.data.setAll([
    { sourceId: "BR", targetId: "DE", value: 350 },
    { sourceId: "BR", targetId: "US", value: 450 },
    { sourceId: "DE", targetId: "FR", value: 150 },
    { sourceId: "DE", targetId: "PL", value: 100 }
  ]);
});
```

**Pattern 2: Explicit coordinates (no timing needed)**

When using `sourceLongitude`/`sourceLatitude`/`targetLongitude`/`targetLatitude`, data can be set immediately — no `datavalidated` wrapper required:
```js
sankeySeries.data.setAll([
  {
    sourceLongitude: 2.35, sourceLatitude: 48.86,    // Paris
    targetLongitude: -74.01, targetLatitude: 40.71,  // New York
    value: 100
  }
]);
```

**Waypoints** — route flows through intermediate points:
```js
polygonSeries.events.once("datavalidated", function() {
  sankeySeries.data.setAll([
    {
      sourceId: "GB", targetId: "JP", value: 40,
      waypoints: [
        { longitude: 30, latitude: 65 },
        { longitude: 90, latitude: 55 }
      ]
    }
  ]);
});
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
sankeySeries.bullets.push(function() {
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

Bullets automatically hide on the back side of the globe (orthographic projection).

#### Multi-level flows

The same country can be both target and source — incoming and outgoing bands share unified stacking at intermediate nodes:

```js
polygonSeries.events.once("datavalidated", function() {
  sankeySeries.data.setAll([
    // Level 1: Producers > Hubs
    { sourceId: "BR", targetId: "DE", value: 350 },
    { sourceId: "VN", targetId: "DE", value: 200 },
    // Level 2: Hubs > Consumers
    { sourceId: "DE", targetId: "FR", value: 150 },
    { sourceId: "DE", targetId: "PL", value: 100 }
  ]);
});
```

### Animating bullets along lines (positionOnLine)

`MapPointSeries` data items have a `positionOnLine` setting (0–1) that positions the point along a line. Combined with `lineDataItem` and `autoRotate`, this enables flight-path-style animations.

```js
// Create a point that follows a line
var planeSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
planeSeries.bullets.push(function() {
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

**IMPORTANT — Multi-segment lines and `positionOnLine`:**
- `positionOnLine` treats the entire line as one unit (0 = start, 1 = end). If a line has multiple segments (i.e. `pointsToConnect` has 3+ points), position 0.5 is the midpoint of the *entire* path.
- For advanced per-segment animations (e.g., scaling the bullet differently at the midpoint of each segment, or pausing between segments), use **single-segment lines** (2 points each) instead of one multi-segment line. This gives you full control: animate `positionOnLine` from 0 to 1 on each segment line sequentially, with independent timing, easing, and callbacks per segment.
- Example: instead of one line with points [A, B, C, D], create three lines: [A,B], [B,C], [C,D] and animate the bullet across them one at a time.

## Zoom controls

```js
chart.set("zoomControl", am5map.ZoomControl.new(root, {}));

// Programmatic zoom
chart.zoomToGeoPoint({ latitude: 48.86, longitude: 2.35 }, 5);  // zoom level 5

// Zoom to a polygon
polygonSeries.mapPolygons.template.events.on("click", function(ev) {
  polygonSeries.zoomToDataItem(ev.target.dataItem);
});

// Home button
chart.chartContainer.children.push(am5.Button.new(root, {
  label: am5.Label.new(root, { text: "Home" })
})).events.on("click", function() {
  chart.goHome();
});
```

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
    chart.zoomToDataItem(ev.target.dataItem);
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
  wheelY: "zoom",           // "zoom", "none"
  wheelSensitivity: 1,      // zoom speed multiplier
})
```

**Polygon IDs** use ISO 3166-1 alpha-2 codes (e.g., `"US"`, `"CN"`, `"DE"`, `"FR"`).

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
```

## Disposal

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
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

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
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

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
  <style>#chartdiv { width: 100%; height: 500px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

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
    pointSeries.bullets.push(function() {
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

## Example 4: Globe with animated flight path (positionOnLine + globe rotation)

Demonstrates: orthographic projection, single-segment lines for per-segment animation control, `positionOnLine` animation, globe auto-rotation to follow the plane, bullet scale animation, and city expand/shrink effects.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Globe Flight Path</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/continentsLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <style>#chartdiv { width: 100%; height: 600px; background: #0a0a1a; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);
    var orange = am5.color(0xff6b35);
    var cityColor = am5.color(0xcc4a1a);

    var cities = [
      { name: "London", latitude: 51.5074, longitude: -0.1278 },
      { name: "Dubai", latitude: 25.2048, longitude: 55.2708 },
      { name: "Singapore", latitude: 1.3521, longitude: 103.8198 },
      { name: "Sydney", latitude: -33.8688, longitude: 151.2093 },
      { name: "Tokyo", latitude: 35.6762, longitude: 139.6503 },
      { name: "Honolulu", latitude: 21.3069, longitude: -157.8583 },
      { name: "Los Angeles", latitude: 34.0522, longitude: -118.2437 },
      { name: "São Paulo", latitude: -23.5505, longitude: -46.6333 },
      { name: "New York", latitude: 40.7128, longitude: -74.006 },
      { name: "London", latitude: 51.5074, longitude: -0.1278 }
    ];

    // Globe centered on first city (note: NEGATIVE lat/lon for rotation)
    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "rotateX",
      panY: "rotateY",
      projection: am5map.geoOrthographic(),
      rotationX: -cities[0].longitude,
      rotationY: -cities[0].latitude
    }));

    // Ocean background
    var bgSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {}));
    bgSeries.mapPolygons.template.setAll({
      fill: am5.color(0x1a1a2e), fillOpacity: 1, strokeOpacity: 0
    });
    bgSeries.data.push({ geometry: am5map.getGeoRectangle(90, 180, -90, -180) });

    // Graticule
    var graticuleSeries = chart.series.push(am5map.GraticuleSeries.new(root, {}));
    graticuleSeries.mapLines.template.setAll({
      stroke: am5.color(0x4a4a65), strokeOpacity: 0.5, strokeWidth: 0.8
    });

    // Continents
    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_continentsLow
    }));
    polygonSeries.mapPolygons.template.setAll({
      fill: am5.color(0x2a2a45), stroke: am5.color(0x3a3a55),
      strokeWidth: 0.5, strokeOpacity: 0.6
    });

    // Dashed route lines — one per segment for animation control
    var lineSeries = chart.series.push(am5map.MapLineSeries.new(root, {}));
    lineSeries.mapLines.template.setAll({
      stroke: orange, strokeOpacity: 0.35, strokeWidth: 1.5, strokeDasharray: [3, 3]
    });

    // City points with pulse + label
    var pointSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
    pointSeries.bullets.push(function () {
      var container = am5.Container.new(root, {});
      container.children.push(am5.Circle.new(root, {
        radius: 3.5, fill: cityColor, fillOpacity: 0.9,
        stroke: orange, strokeWidth: 4, strokeOpacity: 0
      }));
      container.children.push(am5.Label.new(root, {
        text: "{name}", fill: am5.color(0xffffff), fontSize: 11,
        fontWeight: "600", centerX: am5.p50, centerY: am5.p100,
        dy: -8, opacity: 0, populateText: true
      }));
      return am5.Bullet.new(root, { sprite: container });
    });

    var pointsToConnect = [];
    for (var i = 0; i < cities.length; i++) {
      if (i === cities.length - 1) {
        pointsToConnect.push(pointsToConnect[0]); // loop back
      } else {
        pointsToConnect.push(pointSeries.pushDataItem({
          latitude: cities[i].latitude, longitude: cities[i].longitude,
          name: cities[i].name
        }));
      }
    }

    // Single-segment lines (one per city pair) for per-segment control
    var segmentLines = [];
    var numSegments = cities.length - 1;
    for (var i = 0; i < numSegments; i++) {
      segmentLines.push(lineSeries.pushDataItem({
        pointsToConnect: [pointsToConnect[i], pointsToConnect[i + 1]]
      }));
    }

    // Plane series — one per segment, all hidden initially
    var planeSeries = chart.series.push(am5map.MapPointSeries.new(root, {}));
    planeSeries.bullets.push(function () {
      return am5.Bullet.new(root, {
        sprite: am5.Graphics.new(root, {
          svgPath: "m2,106h28l24,30h72l-44,-133h35l80,132h98c21,0 21,34 0,34l-98,0 -80,134h-35l43,-133h-71l-24,30h-28l15,-47",
          scale: 0.04, centerY: am5.p50, centerX: am5.p50,
          fill: orange, forceHidden: true
        })
      });
    });

    var planes = [];
    for (var i = 0; i < numSegments; i++) {
      planes.push(planeSeries.pushDataItem({
        lineDataItem: segmentLines[i], positionOnLine: 0, autoRotate: true
      }));
    }

    var baseScale = 0.04, peakScale = 0.065;

    // Haversine distance for proportional timing
    function haversine(lat1, lon1, lat2, lon2) {
      var R = 6371, dLat = (lat2 - lat1) * Math.PI / 180,
          dLon = (lon2 - lon1) * Math.PI / 180;
      var a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
        Math.sin(dLon / 2) * Math.sin(dLon / 2);
      return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    }

    var msPerKm = 0.4;
    var segDurations = [];
    for (var i = 0; i < numSegments; i++) {
      var d = haversine(cities[i].latitude, cities[i].longitude,
                        cities[i + 1].latitude, cities[i + 1].longitude);
      segDurations.push(Math.max(800, Math.round(d * msPerKm)));
    }

    var activeCityIdx = -1;

    function expandCity(idx) {
      var di = pointsToConnect[idx];
      if (!di || !di.bullets || !di.bullets[0]) return;
      var container = di.bullets[0].get("sprite");
      var dot = container.children.getIndex(0);
      var label = container.children.getIndex(1);
      activeCityIdx = idx;
      dot.animate({ key: "radius", to: 14, duration: 400, easing: am5.ease.out(am5.ease.cubic) });
      dot.animate({ key: "fillOpacity", to: 0, duration: 400, easing: am5.ease.out(am5.ease.cubic) });
      dot.animate({ key: "strokeOpacity", to: 1, duration: 400, easing: am5.ease.out(am5.ease.cubic) });
      label.setAll({ opacity: 0, dy: 0 });
      label.animate({ key: "opacity", to: 1, duration: 300, easing: am5.ease.out(am5.ease.cubic) });
      label.animate({ key: "dy", to: -10, duration: 300, easing: am5.ease.out(am5.ease.cubic) });
    }

    function shrinkCity(idx) {
      var di = pointsToConnect[idx];
      if (!di || !di.bullets || !di.bullets[0]) return;
      var container = di.bullets[0].get("sprite");
      var dot = container.children.getIndex(0);
      var label = container.children.getIndex(1);
      dot.animate({ key: "radius", to: 3.5, duration: 500, easing: am5.ease.cubic });
      dot.animate({ key: "fillOpacity", to: 0.9, duration: 500, easing: am5.ease.cubic });
      dot.animate({ key: "strokeOpacity", to: 0, duration: 500, easing: am5.ease.cubic });
      label.animate({ key: "opacity", to: 0, duration: 400, easing: am5.ease.cubic });
      activeCityIdx = -1;
    }

    var lastRotation = null;
    var activeFlightDisposer = null;

    function startFlight(plane, sprite, idx) {
      if (activeCityIdx >= 0) shrinkCity(activeCityIdx);
      var nextIdx = (idx + 1) % numSegments;
      var expanded = false;

      if (activeFlightDisposer) { activeFlightDisposer.dispose(); activeFlightDisposer = null; }

      // Track position: rotate globe + scale bullet + expand destination city early
      activeFlightDisposer = plane.on("positionOnLine", function (pos) {
        chart.set("rotationX", -plane.get("longitude"));
        chart.set("rotationY", -plane.get("latitude"));
        var s = baseScale + (peakScale - baseScale) * Math.sin(pos * Math.PI);
        if (plane.bullets && plane.bullets[0]) {
          plane.bullets[0].get("sprite").set("scale", s);
        }
        if (!expanded && pos >= 0.85) { expanded = true; expandCity(nextIdx); }
      });

      var anim = plane.animate({
        key: "positionOnLine", to: 1, duration: segDurations[idx],
        easing: am5.ease.inOut(am5.ease.cubic)
      });
      anim.events.on("stopped", function () {
        lastRotation = sprite.get("rotation", 0);
        sprite.set("forceHidden", true);
        flySegment(nextIdx);
      });
    }

    function flySegment(idx) {
      var plane = planes[idx];
      var sprite = plane.bullets[0].get("sprite");
      plane.set("positionOnLine", 0);
      sprite.set("forceHidden", false);

      if (lastRotation === null) {
        sprite.set("opacity", 0);
        sprite.animate({ key: "opacity", to: 1, duration: 400, easing: am5.ease.out(am5.ease.cubic) });
      }

      if (lastRotation !== null) {
        var targetRotation = sprite.get("rotation", 0);
        plane.set("autoRotate", false);
        sprite.set("rotation", lastRotation);
        var diff = targetRotation - lastRotation;
        while (diff > 180) diff -= 360;
        while (diff < -180) diff += 360;
        var rotAnim = sprite.animate({
          key: "rotation", to: lastRotation + diff, duration: 500,
          easing: am5.ease.out(am5.ease.cubic)
        });
        rotAnim.events.on("stopped", function () {
          plane.set("autoRotate", true);
          startFlight(plane, sprite, idx);
        });
      } else {
        startFlight(plane, sprite, idx);
      }
    }

    chart.appear(1000, 100);
    expandCity(0);
    setTimeout(function () { flySegment(0); }, 1500);
  </script>
</body>
</html>
```

## Example 5: Map Sankey — trade flows between countries

Demonstrates: `MapSankeySeries` with geographic flow bands, node circles, tooltips, and multi-level flows.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Map Sankey — Trade Flows</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <style>#chartdiv { width: 100%; height: 600px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "rotateX",
      panY: "translateY",
      projection: am5map.geoNaturalEarth1()
    }));

    // Background countries
    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_worldLow
    }));
    polygonSeries.mapPolygons.template.setAll({
      fill: am5.color(0xdddddd),
      stroke: am5.color(0xffffff),
      strokeWidth: 0.5
    });

    // Sankey flow series
    var sankeySeries = chart.series.push(am5map.MapSankeySeries.new(root, {
      polygonSeries: polygonSeries,
      maxWidth: 4,
      controlPointDistance: 0.4,
      autoSort: true
    }));

    // Band appearance
    sankeySeries.mapPolygons.template.setAll({
      fill: am5.color(0xff6b35),
      fillOpacity: 0.5,
      strokeOpacity: 0,
      tooltipText: "{sourceId} > {targetId}: {value}B"
    });

    // Node appearance
    sankeySeries.nodes.mapPolygons.template.setAll({
      fill: am5.color(0x8b5e3c),
      fillOpacity: 0.9,
      stroke: am5.color(0xffffff),
      strokeWidth: 1.5
    });

    // Set data AFTER polygonSeries parses geoJSON (required for sourceId/targetId)
    polygonSeries.events.once("datavalidated", function() {
      sankeySeries.data.setAll([
        // Producers > Trade hubs
        { sourceId: "BR", targetId: "DE", value: 350 },
        { sourceId: "BR", targetId: "US", value: 450 },
        { sourceId: "CN", targetId: "DE", value: 500 },
        { sourceId: "CN", targetId: "US", value: 600 },
        { sourceId: "AU", targetId: "JP", value: 200 },
        { sourceId: "AU", targetId: "CN", value: 300 },
        // Hubs > Consumers
        { sourceId: "DE", targetId: "FR", value: 250 },
        { sourceId: "DE", targetId: "GB", value: 300 },
        { sourceId: "US", targetId: "CA", value: 400 },
        { sourceId: "JP", targetId: "KR", value: 100 }
      ]);
    });

    chart.set("zoomControl", am5map.ZoomControl.new(root, {}));
    chart.appear(1000, 100);
  </script>
</body>
</html>
```

## Example 6: Map Sankey — explicit coordinates with waypoints

Demonstrates: `MapSankeySeries` with explicit `sourceLongitude`/`sourceLatitude` coordinates, waypoints for routing, and animated bullets. No `datavalidated` timing needed when using explicit coordinates.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Map Sankey — Oil Flows</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/map.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/geodata/worldLow.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <style>body { background: #1a2a3a; margin: 0; } #chartdiv { width: 100%; height: 600px; }</style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

    var chart = root.container.children.push(am5map.MapChart.new(root, {
      panX: "rotateX",
      panY: "rotateY",
      projection: am5map.geoOrthographic(),
      rotationX: -54,
      rotationY: -25
    }));

    var bgSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {}));
    bgSeries.mapPolygons.template.setAll({ fill: am5.color(0x111d2d), fillOpacity: 1, strokeOpacity: 0 });
    bgSeries.data.push({ geometry: am5map.getGeoRectangle(90, 180, -90, -180) });

    var polygonSeries = chart.series.push(am5map.MapPolygonSeries.new(root, {
      geoJSON: am5geodata_worldLow
    }));
    polygonSeries.mapPolygons.template.setAll({
      fill: am5.color(0x2c4a6a),
      stroke: am5.color(0x1a2a3a),
      strokeWidth: 0.5
    });

    var sankeySeries = chart.series.push(am5map.MapSankeySeries.new(root, {
      polygonSeries: polygonSeries,
      maxWidth: 3,
      controlPointDistance: 0.3,
      resolution: 60
    }));

    sankeySeries.mapPolygons.template.setAll({
      fill: am5.color(0xd4a017),
      fillOpacity: 0.6,
      strokeOpacity: 0,
      tooltipText: "{source} > {target}: {value}M barrels/day"
    });

    sankeySeries.nodes.mapPolygons.template.setAll({
      fill: am5.color(0xd4a017),
      stroke: am5.color(0xf0e6d0),
      strokeWidth: 1.5,
      fillOpacity: 0.9,
      tooltipText: "{name}"
    });

    // Helper for explicit-coordinate data with optional waypoints
    function flow(src, srcName, tgt, tgtName, val, wp) {
      var result = {
        sourceLongitude: src.lon, sourceLatitude: src.lat,
        targetLongitude: tgt.lon, targetLatitude: tgt.lat,
        source: srcName, target: tgtName, value: val
      };
      if (wp) result.waypoints = wp;
      return result;
    }

    var saudiPort  = { lon: 50.2, lat: 26.6 };
    var hormuz     = { lon: 56.3, lat: 26.6 };
    var india      = { lon: 72.8, lat: 19.1 };
    var china      = { lon: 121.5, lat: 31.2 };
    var japan      = { lon: 139.7, lat: 35.7 };
    var europe     = { lon: 9.5, lat: 44.4 };
    var suez       = { lon: 32.3, lat: 30.0 };

    // No datavalidated wrapper needed — explicit coordinates resolve immediately
    sankeySeries.data.setAll([
      flow(saudiPort, "Saudi Arabia", china, "China", 1.8,
        [hormuz, { lon: 65, lat: 20 }, { lon: 80, lat: 15 }, { lon: 105, lat: 20 }]),
      flow(saudiPort, "Saudi Arabia", japan, "Japan", 1.2,
        [hormuz, { lon: 70, lat: 22 }, { lon: 100, lat: 18 }, { lon: 125, lat: 28 }]),
      flow(saudiPort, "Saudi Arabia", india, "India", 0.9,
        [hormuz, { lon: 62, lat: 22 }]),
      flow(saudiPort, "Saudi Arabia", europe, "Europe", 1.0,
        [{ lon: 43, lat: 28 }, suez, { lon: 20, lat: 36 }])
    ]);

    // Animated bullets along flow paths
    sankeySeries.bullets.push(function() {
      return am5.Bullet.new(root, {
        locationX: 0,
        autoRotate: true,
        sprite: am5.Circle.new(root, {
          radius: 2,
          fill: am5.color(0xd4a017)
        })
      });
    });

    sankeySeries.events.on("datavalidated", function() {
      am5.array.each(sankeySeries.dataItems, function(dataItem) {
        var bullets = dataItem.bullets;
        if (bullets) {
          am5.array.each(bullets, function(bullet) {
            bullet.animate({
              key: "locationX",
              from: 0, to: 1,
              duration: 3000 + Math.random() * 3000,
              easing: am5.ease.linear,
              loops: Infinity
            });
          });
        }
      });
    });

    chart.set("zoomControl", am5map.ZoomControl.new(root, {}));
    chart.appear(1000, 100);
  </script>
</body>
</html>
```
