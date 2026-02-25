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
|-----------|--------------
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
