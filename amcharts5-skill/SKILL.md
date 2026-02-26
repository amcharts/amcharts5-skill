---
name: amcharts5
description: >
  Build any chart with the amCharts 5 JavaScript library — XY (line, area, bar,
  column, candlestick, scatter), pie, donut, funnel, pyramid, geographic maps,
  hierarchy (treemap, sunburst, force-directed), flow (Sankey, chord, arc), radar,
  gauge, stock/financial, word cloud, Venn, Gantt, and timeline charts. Use this
  skill whenever the user mentions amCharts, asks for amCharts 5 code, needs a
  JavaScript chart built with amCharts, wants to create any data visualization
  using the amCharts library, is migrating from amCharts v4 to v5, or needs help
  with amCharts configuration, theming, exporting, tooltips, legends, or axis
  setup. Also use when the user mentions chart types that amCharts supports, even
  if they don't say "amCharts" explicitly, if amCharts is already present in the
  project or was discussed in the conversation.
metadata:
  author: amCharts
  version: 1.0.0
  category: code
  tags: [charts, maps]
  documentation: https://amcharts.com/docs/v5
  support: contact@amcharts.com
---

# amCharts 5

Build any chart with the amCharts 5 JavaScript charting library.

Docs: https://www.amcharts.com/docs/v5/

## Critical rules — apply to ALL chart types

1. **Always use amCharts 5** — never v3 or v4. The APIs are completely different.
2. **Use `.new()` factory** — never `new ClassName()`. Every object is created via `ClassName.new(root, { settings })`.
3. **Root is always the first argument** to `.new()` (except Root itself, which takes a div ID).
4. **Set data last** — once data is set, objects are created. Configuration applied after may not take effect.
5. **Colors use `am5.color()`** — e.g. `am5.color(0xff0000)` or `am5.color("#ff0000")`. Never raw hex strings.
6. **Percent values use `am5.percent()`** — e.g. `am5.percent(50)`, not `50` or `"50%"`.
7. **Every axis needs a renderer** — `am5xy.AxisRendererX.new(root, {})` or `AxisRendererY`.
8. **CategoryAxis must receive data** — call `xAxis.data.setAll(data)` in addition to series data. Forgetting this is the #1 bug.
9. **DateAxis values must be timestamps** — use `new Date().getTime()`, not Date objects.
10. **Disposal is mandatory in SPAs** — call `root.dispose()` on component unmount. Not `chart.dispose()`.
11. **Canvas-rendered** — CSS cannot style chart internals. Use amCharts settings/templates instead.
12. **Dark backgrounds require Dark theme** — If the user requests or the page clearly has a dark background, always add `am5themes_Dark.new(root)` to `root.setThemes()` alongside Animated. Without it, labels, grid, and tooltips will be invisible. If the background is not clearly dark, default to white/light and do NOT add the Dark theme unless asked.
13. **Use default amCharts colors** — Do NOT invent custom color palettes unless the user explicitly asks for specific colors. amCharts assigns colors automatically from its built-in ColorSet. If you need a color programmatically, use `chart.get("colors").getIndex(index)` or `chart.get("colors").next()`. For pie/percent charts, use `series.get("colors")` instead.

## Package / module map

| Chart family | ES module import | CDN script | Main classes |
|-------------|-----------------|-----------|-------------|
| Core | `@amcharts/amcharts5` | `index.js` | Root, Theme, Legend, Tooltip, Label, Container |
| XY charts | `@amcharts/amcharts5/xy` | `xy.js` | XYChart, axes, series |
| Pie / Sliced | `@amcharts/amcharts5/percent` | `percent.js` | PieChart, SlicedChart, PieSeries, FunnelSeries, PyramidSeries, PictorialStackedSeries |
| Map | `@amcharts/amcharts5/map` | `map.js` | MapChart, MapPolygonSeries, MapPointSeries, MapLineSeries |
| Hierarchy | `@amcharts/amcharts5/hierarchy` | `hierarchy.js` | Treemap, ForceDirected, Sunburst, Pack, Partition, Tree |
| Flow | `@amcharts/amcharts5/flow` | `flow.js` | Sankey, Chord, ChordDirected, ChordNonRibbon, ArcDiagram |
| Radar | `@amcharts/amcharts5/radar` | `radar.js` | RadarChart, AxisRendererCircular, AxisRendererRadial |
| Stock | `@amcharts/amcharts5/stock` | `stock.js` | StockChart, StockPanel, StockToolbar |
| Timeline | `@amcharts/amcharts5/timeline` | `timeline.js` | CurveChart, SerpentineChart, SpiralChart, CurveLineSeries, CurveColumnSeries |
| Word cloud | `@amcharts/amcharts5/wc` | `wc.js` | WordCloud |
| Venn | `@amcharts/amcharts5/venn` | `venn.js` | VennDiagram, Venn |
| Gantt | `@amcharts/amcharts5/gantt` | `gantt.js` | Gantt (also needs xy) |
| Geodata | `@amcharts/amcharts5-geodata/*` | `geodata/*.js` | worldLow, usaLow, etc. |
| Themes | `@amcharts/amcharts5/themes/Animated` | `themes/Animated.js` | am5themes_Animated |
| Exporting | `@amcharts/amcharts5/plugins/exporting` | `plugins/exporting.js` | Exporting, ExportingMenu |

## Which reference to read

Based on the chart type the user is building, read the relevant reference file for detailed API, patterns, and examples:

| User wants | Read |
|-----------|------|
| Line, area, bar, column, candlestick, OHLC, scatter, stacked charts | `references/xy.md` |
| Pie, donut, semi-circle, funnel, pyramid, pictorial stacked charts | `references/pie.md` |
| World map, country map, choropleth, bubble map, point map | `references/map.md` |
| Treemap, sunburst, force-directed, pack, partition, tree, org chart | `references/hierarchy.md` |
| Sankey, chord, arc diagram, alluvial, flow visualization | `references/flow.md` |
| Radar, spider, polar chart, gauge, speedometer, meter | `references/radar.md` |
| Financial stock chart, candlestick with indicators, trading chart | `references/stock.md` |
| Serpentine, spiral, curve chart, custom-shape timeline | `references/timeline.md` |
| Gantt chart, project timeline, task management chart | `references/gantt.md` |
| Word cloud, tag cloud, sentence cloud | `references/wordcloud.md` |
| Venn diagram, set overlap visualization | `references/venn.md` |

If the chart type is unclear, start with `references/xy.md` — XY charts are by far the most common.

If the user asks about core setup (theming, colors, exporting, legends, tooltips, disposal, responsive) without a specific chart type, the information below is sufficient — no reference file needed.

## Root element setup

```js
const root = am5.Root.new("chartdiv"); // div id, not a DOM element

// Apply theme(s)
root.setThemes([am5themes_Animated.new(root)]);

// Set locale (optional)
import am5locales_de_DE from "@amcharts/amcharts5/locales/de_DE";
root.locale = am5locales_de_DE;

// Set date/number formats (optional)
root.dateFormatter.setAll({ dateFormat: "yyyy-MM-dd" });
root.numberFormatter.setAll({ numberFormat: "#,###.##" });
```

**Available themes:** `Animated`, `Dark`, `Frozen`, `Dataviz`, `Material`, `Moonrise`, `Spirited`, `Kelly`, `Micro`, `Responsive`.

## Colors

```js
// From hex
am5.color(0xff0000)
am5.color("#ff0000")

// From RGB
am5.color({ r: 255, g: 0, b: 0 })

// Lighten / darken
am5.color(0xff0000).lighten(0.3)
am5.color(0xff0000).darken(0.3)

// Color sets (auto-cycle)
chart.get("colors").getIndex(0)   // first color in palette
chart.get("colors").next()        // next color

// Custom color set
chart.get("colors").set("colors", [
  am5.color(0x095256),
  am5.color(0x087f8c),
  am5.color(0x5aaa95),
  am5.color(0x86a873),
  am5.color(0xbb9f06)
]);
```

## Legend

```js
const legend = chart.children.push(am5.Legend.new(root, {
  centerX: am5.percent(50),
  x: am5.percent(50),
  layout: root.horizontalLayout  // or verticalLayout, gridLayout
}));
// XY charts:
legend.data.setAll(chart.series.values);
// Pie/Percent charts:
legend.data.setAll(series.dataItems);
```

## Tooltip

```js
series.set("tooltip", am5.Tooltip.new(root, {
  labelText: "{name}: {valueY}"   // data placeholders in {}
}));
```

**Placeholders:** `{name}`, `{valueX}`, `{valueY}`, `{categoryX}`, `{categoryY}`, `{value}`, `{category}`, `{valuePercentTotal}`, `{sum}`.

**Inline formatting:** `"[bold]{name}[/]: [fontSize: 20px]{value}[/]"`

## Exporting

```js
import am5plugins_exporting from "@amcharts/amcharts5/plugins/exporting";

const exporting = am5plugins_exporting.Exporting.new(root, {
  menu: am5plugins_exporting.ExportingMenu.new(root, {}),
  filePrefix: "my-chart",
  pngOptions: { quality: 0.8 },
  pdfOptions: { addURL: true }
});
```

## Responsive rules

```js
import am5plugins_responsive from "@amcharts/amcharts5/plugins/responsive";

const responsive = am5plugins_responsive.Responsive.new(root);
responsive.addRule({
  relevant: am5plugins_responsive.WidthXS,  // < 300px
  applying: function() {
    legend.setAll({ visible: false });
  },
  removing: function() {
    legend.setAll({ visible: true });
  }
});
root.setThemes([am5themes_Animated.new(root), responsive]);
```

## Heat rules

```js
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  min: am5.color(0xe5dc36),
  max: am5.color(0x5faa46),
  key: "fill"
}]);
```

## Animations

```js
series.appear(1000);        // duration in ms
chart.appear(1000, 100);    // duration, delay
```

## Events

```js
// Interaction events on elements
series.columns.template.events.on("click", (ev) => {
  console.log("Clicked:", ev.target.dataItem.dataContext);
});

// Common events: click, pointerover, pointerout, pointerdown, globalpointermove

// Series/chart lifecycle events
series.events.on("datavalidated", () => {
  // Fires after data is processed — safe to read dataItems
});

// Axis zoom event
xAxis.events.on("rangechanged", () => {
  console.log("Zoomed/scrolled");
});

// Setting change watch
sprite.on("width", (width) => {
  console.log("Width changed to", width);
});

// One-time listener
series.events.once("datavalidated", () => { /* runs once */ });

// Remove listener
const disposer = sprite.events.on("click", handler);
disposer.dispose(); // removes the listener
```

## Settings API

```js
// Set after creation
sprite.set("fill", am5.color(0xff0000));
sprite.setAll({ fill: am5.color(0xff0000), strokeWidth: 2 });

// Read current value
const fill = sprite.get("fill");
const width = sprite.getPrivate("width"); // read-only internal values
```

## Dynamic data

```js
// Replace all data (triggers full redraw)
series.data.setAll(newData);

// Add items
series.data.push({ category: "New", value: 42 });

// Update item at index (animates the change)
series.data.setIndex(0, { category: "Updated", value: 99 });

// Remove item at index
series.data.removeIndex(2);

// Insert at specific position
series.data.insertIndex(1, { category: "Inserted", value: 50 });

// Iterating dataItems — dataItems is a PLAIN ARRAY, not a List
// ❌ series.dataItems.each(fn)                              — will throw, .each() does not exist
// ✅ am5.array.each(series.dataItems, function(dataItem) {}) — amCharts array utility
// ✅ series.dataItems.forEach(function(dataItem) {})         — standard JS
```

## Adapters

```js
// Dynamically modify a setting value before it's applied
series.columns.template.adapters.add("fill", (fill, target) => {
  return target.dataItem.get("valueY") > 100
    ? am5.color(0x00cc00)
    : am5.color(0xcc0000);
});

// Adapter on axis labels
xAxis.get("renderer").labels.template.adapters.add("text", (text, target) => {
  return text + "!";
});
```

## States

```js
// Hover state — applied automatically on pointer over
series.columns.template.states.create("hover", {
  fillOpacity: 0.8,
  scale: 1.05
});

// Active state — toggled via click
series.columns.template.states.create("active", {
  fill: am5.color(0xff0000)
});

// Built-in state names: "hover", "active", "disabled", "hidden"
// State animation: stateAnimationDuration, stateAnimationEasing
```

## Custom themes

```js
const myTheme = am5.Theme.new(root);
myTheme.rule("Label").setAll({ fontSize: 12, fill: am5.color(0x555555) });
myTheme.rule("Grid").setAll({ stroke: am5.color(0xe0e0e0) });
root.setThemes([am5themes_Animated.new(root), myTheme]);
// Theme order matters: later themes override earlier ones
```

## Dark theme

When the page has a dark background, add the Dark theme so labels, grid, and tooltips are readable:

```js
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_Dark.new(root)        // must come after Animated
]);
```

Import: `import am5themes_Dark from "@amcharts/amcharts5/themes/Dark"` or CDN `themes/Dark.js`.

**Rule:** Only add Dark theme when the background is clearly dark. Default to white/light background with no Dark theme.

## ColorSet — using default colors

amCharts assigns colors automatically via a built-in ColorSet. Do not invent custom palettes unless the user asks.

```js
// Get the chart's default color set
var colors = chart.get("colors");       // XY, radar, etc.
var colors = series.get("colors");      // pie/percent charts use series-level colors

// Get a specific color by index (does not advance internal counter)
var color = colors.getIndex(0);         // first default color
var color = colors.getIndex(3);         // fourth default color

// Get next color in sequence (advances internal counter)
var color = colors.next();

// Reset counter back to start
colors.reset();

// Override the default palette (only if user requests specific colors)
colors.set("colors", [
  am5.color(0x095256),
  am5.color(0x087f8c),
  am5.color(0x5aaa95)
]);
```

## Data processor

```js
// Auto-convert fields when loading external / API data
series.data.processor = am5.DataProcessor.new(root, {
  dateFields: ["date"],
  dateFormat: "yyyy-MM-dd",
  numericFields: ["value", "count"],
  colorFields: ["color"],
  emptyAs: 0  // replace null/empty with 0
});
// Configure processor BEFORE setting data
```

## Accessibility

```js
series.columns.template.setAll({
  focusable: true,
  ariaLabel: "{categoryX}: {valueY}",
  role: "figure"
});
// Keyboard: TAB to navigate focusable elements, ENTER to click
// focusableGroup: "series1" — TAB to first, arrows within group
```

## Container & Layout

```js
// Containers organize child elements
const container = root.container.children.push(am5.Container.new(root, {
  width: am5.percent(100),
  height: am5.percent(100),
  layout: root.verticalLayout  // also: horizontalLayout, gridLayout
}));

// Padding and margin
container.setAll({
  paddingTop: 10,
  paddingBottom: 10,
  marginLeft: 20
});
```

## Disposal (SPA frameworks)

```js
// React
useLayoutEffect(() => {
  const root = am5.Root.new("chartdiv");
  // ... build chart
  return () => { root.dispose(); };
}, []);

// Angular
ngOnDestroy() { this.root?.dispose(); }

// Vue
onUnmounted(() => { root.dispose(); });
```

**Always call `root.dispose()`** — this cleans up all children, series, and event listeners.

## v4 → v5 migration pitfalls

| v4 pattern (WRONG) | v5 equivalent (CORRECT) |
|-----------|--------------|
| `am4core.create("div", am4charts.XYChart)` | `am5.Root.new("div")` + `am5xy.XYChart.new(root, {})` |
| `new am4charts.LineSeries()` | `am5xy.LineSeries.new(root, {})` |
| `series.dataFields.valueY = "val"` | `valueYField: "val"` in `.new()` settings |
| `chart.data = [...]` | `series.data.setAll([...])` |
| `am4core.color("#f00")` | `am5.color(0xff0000)` |
| `am4core.percent(50)` | `am5.percent(50)` |
| `chart.dispose()` | `root.dispose()` |
| `chart.legend = new am4charts.Legend()` | `chart.children.push(am5.Legend.new(root, {}))` |

## Common pitfalls

1. **Using `new` keyword** — always use `.new()` factory.
2. **Forgetting `CategoryAxis.data.setAll()`** — axis will be empty.
3. **Passing Date objects to DateAxis** — must be timestamps (ms).
4. **Missing axis renderer** — every axis needs `renderer: am5xy.AxisRendererX.new(root, {})`.
5. **Setting data before configuration** — configure everything, then set data last.
6. **Raw hex strings for colors** — use `am5.color()`.
7. **Mixing v4 and v5 API** — they are completely incompatible.
8. **Calling `chart.dispose()` instead of `root.dispose()`** — always dispose root.
9. **Using CSS to style chart elements** — amCharts renders on Canvas, not DOM.
10. **Wrong package import** — check the package map table above.
11. **Calling `.each()` on `dataItems`** — `series.dataItems` is a plain array, not an amCharts `List`. Use `am5.array.each(series.dataItems, fn)` or standard `forEach`/`for` loops. Only `series.data` (the `ListData` object) has `.each()`.
12. **CDN script load order** — `index.js` must load first, then `xy.js`, then any package that depends on it (`radar.js`, `timeline.js`, `gantt.js`). Wrong order causes runtime errors. Correct order: `index.js` → `xy.js` → `radar.js` / `timeline.js` / `gantt.js` → `themes/*.js`.
13. **No `minorGrid` / `minorTicks` / `minorLabels` objects** — These do not exist on axes. Minor grid is enabled via boolean flags on the **renderer**: `minorGridEnabled: true` and optionally `minorLabelsEnabled: true`. Styling is done through theme rules targeting the `"minor"` tag, not through separate object properties.
14. **Flow chart animated bullets go on `series.bullets`, NOT `series.links.template.bullets`** — To animate labels/circles flowing along Sankey or Chord links, use `series.bullets.push(function(...) { ... })`. Animate `bullet.locationX` from 0→1 with `loops: Infinity`. Use an adapter on opacity for fade effect. See `references/flow.md` → "Animated bullets along links".
15. **`MapPointSeries` needs `latitudeField`/`longitudeField` when using `data.setAll()`** — If point data has `latitude`/`longitude` fields, the series must declare them: `am5map.MapPointSeries.new(root, { latitudeField: "latitude", longitudeField: "longitude" })`. Without these, points silently won't appear. This is NOT needed when using `pushDataItem({ latitude: ..., longitude: ... })` which passes coordinates directly.
