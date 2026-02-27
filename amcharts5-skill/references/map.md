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
- Individual country maps: `geodata/{countryName}Low.js` → global `am5geodata_{countryName}Low`
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
    geoJSON: am5geodata_worldLow,
    exclude: ["AQ"]             // exclude Antarctica
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

## Series types

### MapPolygonSeries (countries, regions)

```js
const polygonSeries = chart.series.push(
  am5map.MapPolygonSeries.new(root, {
    geoJSON: am5geodata_worldLow,
    exclude: ["AQ"],
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

// Rotate to a specific country
chart.animate({ key: "rotationX", to: -2.35, duration: 1500, easing: am5.ease.out(am5.ease.cubic) });
chart.animate({ key: "rotationY", to: -48.86, duration: 1500, easing: am5.ease.out(am5.ease.cubic) });
```

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

## Drill-down map (country → states)

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
  import("@amcharts/amcharts5-geodata/region/usa/usaLow").then(function(geodata) {
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

// Map zoom event
chart.events.on("zoomlevelchanged", function(ev) {
  console.log("Zoom:", chart.get("zoomLevel"));
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
      exclude: ["AQ"],
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

    // Background polygons
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
