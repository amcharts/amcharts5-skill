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
  setup, including amCharts in React, Next.js, Vue, Nuxt, Angular, Svelte or
  React Native apps. Also use when the user mentions chart types that amCharts supports, even
  if they don't say "amCharts" explicitly, if amCharts is already present in the
  project or was discussed in the conversation.
metadata:
  author: amCharts
  version: 1.0.0
  category: code
  tags: [charts, maps]
  documentation: https://docs.amcharts.com/v5/
  support: contact@amcharts.com
---

# amCharts 5

Build any chart with the amCharts 5 JavaScript charting library.

Docs: https://docs.amcharts.com/v5/

## Critical rules — apply to ALL chart types

1. **Check demos first (if you can browse the web)** — Before building a chart, search or browse https://www.amcharts.com/demos/ for a demo that matches the user's request. Demo source code is the most reliable starting point — adapt it rather than writing from scratch. Skip this step if you have no web access.
2. **Read the docs (if you can browse the web)** — For unfamiliar features or configuration, check https://docs.amcharts.com/v5/ for guides and tutorials. The docs explain concepts, patterns, and options that the class reference alone does not. Skip if you have no web access.
3. **Always use amCharts 5** — never v3 or v4. The APIs are completely different.
4. **Use `.new()` factory** — never `new ClassName()`. Every object is created via `ClassName.new(root, { settings })`.
5. **Root is always the first argument** to `.new()` (except Root itself, which takes a div ID).
6. **Set data last** — once data is set, objects are created. Configuration applied after may not take effect.
7. **Colors use `am5.color()`** — e.g. `am5.color(0xff0000)`, `am5.color("#ff0000")`, or `am5.color("rgb(255,0,0)")`. Never raw hex strings.
8. **Percent values use `am5.percent()`** — e.g. `am5.percent(50)`, not `50` or `"50%"`.
9. **Every axis needs a renderer** — `am5xy.AxisRendererX.new(root, {})` or `AxisRendererY`.
10. **CategoryAxis must receive data** — call `xAxis.data.setAll(data)` in addition to series data. Forgetting this is the #1 bug.
11. **DateAxis values must be timestamps** — use `new Date().getTime()`, not Date objects.
12. **Disposal is mandatory in SPAs** — in React, Vue, Angular, Svelte or React Native use amCharts' framework package, which creates and disposes the root for you (see "Frameworks"); without one, call `root.dispose()` on component unmount. Not `chart.dispose()`.
13. **Style through amCharts, not CSS** — charts draw on canvas by default, so CSS cannot reach chart internals. Even with the optional SVG renderer the shapes carry no per-element classes or ids, are rewritten as the chart redraws, and exports ignore page CSS. Use amCharts settings/templates/themes instead.
14. **Dark backgrounds require Dark theme** — If the user requests or the page clearly has a dark background, always add `am5themes_Dark.new(root)` to `root.setThemes()` after Animated and ResponsiveMax. Without it, labels, grid, and tooltips will be invisible. If the background is not clearly dark, default to white/light and do NOT add the Dark theme unless asked.
15. **Use default amCharts colors** — Do NOT invent custom color palettes unless the user explicitly asks for specific colors. amCharts assigns colors automatically from its built-in ColorSet. If you need a color programmatically, use `chart.get("colors").getIndex(index)` or `chart.get("colors").next()`. For pie/percent charts, use `series.get("colors")` instead. With 3 or more series in one XY chart, the default colors are close blues and violets (hues 18° apart): set `chart.get("colors").set("step", 3)` before adding series, or use the `Colorblind` theme (see "ColorSet").
16. **Every chart gets `Animated` and `ResponsiveMax`** — `root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)])`, with a palette or `Dark` theme after them. ResponsiveMax fits labels and legends at any size and adapts the chart on small screens: axis labels are spaced to fit, legends shrink and then scroll, and scrollbars give way to pinch zoom on phones. Import `@amcharts/amcharts5/themes/ResponsiveMax`, or load `themes/ResponsiveMax.js` after `themes/Animated.js`. One exception: a `Micro` sparkline keeps `Micro` alone.

## Package / module map

| Chart family | ES module import | CDN script | Main classes |
|-------------|-----------------|-----------|-------------|
| Core | `@amcharts/amcharts5` | `index.js` | Root, Theme, Legend, Tooltip, Label, Container |
| XY charts | `@amcharts/amcharts5/xy` | `xy.js` | XYChart, axes, series |
| Pie / Sliced | `@amcharts/amcharts5/percent` | `percent.js` | PieChart, SlicedChart, PieSeries, FunnelSeries, PyramidSeries, PictorialStackedSeries |
| Map | `@amcharts/amcharts5/map` | `map.js` | MapChart, MapPolygonSeries, MapPointSeries, MapLineSeries, MapSankeySeries, PixelMapSeries, MapRasterSeries, NightSeries |
| Hierarchy | `@amcharts/amcharts5/hierarchy` | `hierarchy.js` | Treemap, VoronoiTreemap, ForceDirected, Sunburst, Pack, Partition, Tree |
| Flow | `@amcharts/amcharts5/flow` | `flow.js` | Sankey, Chord, ChordDirected, ChordNonRibbon, ArcDiagram |
| Radar | `@amcharts/amcharts5/radar` | `radar.js` | RadarChart, AxisRendererCircular, AxisRendererRadial |
| Stock | `@amcharts/amcharts5/stock` | `stock.js` | StockChart, StockPanel, StockToolbar |
| Timeline | `@amcharts/amcharts5/timeline` | `timeline.js` | CurveChart, SerpentineChart, SpiralChart, CurveLineSeries, CurveColumnSeries |
| Word cloud | `@amcharts/amcharts5/wc` | `wc.js` | WordCloud |
| Venn | `@amcharts/amcharts5/venn` | `venn.js` | Venn |
| Gantt | `@amcharts/amcharts5/gantt` | `gantt.js` | Gantt (also needs `xy.js` and `plugins/colorPicker.js`, both before `gantt.js`) |
| Geodata | `@amcharts/amcharts5-geodata/*` | `geodata/*.js` | worldLow, usaLow, etc. |
| Themes | `@amcharts/amcharts5/themes/Animated`, `@amcharts/amcharts5/themes/ResponsiveMax` | `themes/Animated.js`, `themes/ResponsiveMax.js` | am5themes_Animated, am5themes_ResponsiveMax |
| Exporting | `@amcharts/amcharts5/plugins/exporting` | `plugins/exporting.js` | Exporting, ExportingMenu |
| JSON | `@amcharts/amcharts5/plugins/json` | `plugins/json.js` | ChartSerializer, JsonParser |
| Chart element | `@amcharts/amcharts5/element` | `element.js` (after `plugins/json.js`) | `<am5-chart>` — see "Serializing to JSON" |
| Framework packages | `@amcharts/amcharts5-react`, `-vue`, `-angular`, `-svelte`, `-react-native` (separate npm packages) | — | `useAmChart`, `<AmChart>`, `[amChart]`, `attachAmChart` — see "Frameworks" |

**Plain HTML, no bundler.** Load the script files ("CDN script" above) with `<script>` tags: `index.js` first, then the chart packages, then themes. They are on the amCharts CDN (`https://cdn.amcharts.com/lib/5/index.js`), and in the npm package's `dist/` folder, so npm CDNs serve them. On jsDelivr they are under `https://cdn.jsdelivr.net/npm/@amcharts/amcharts5@5.23.0/dist/`, with the same file names: `index.js`, `xy.js`, `themes/Animated.js`, `themes/ResponsiveMax.js`.

- **Which CDN:** cdn.amcharts.com by default. Where only npm CDNs are allowed (Claude artifacts and other AI sandboxes, strict CSPs), use jsDelivr or unpkg (`https://unpkg.com/@amcharts/amcharts5@5.23.0/dist/index.js`). The files are also at `node_modules/@amcharts/amcharts5/dist/`.
- **Pin one version for every file,** and use classic script tags, not `type="module"`: the lazy parts (PDF, Excel and annotation export) load from the `deps/` folder beside `index.js`.
- **Maps:** the package's `dist/geodata/` has only `worldLow.js` and `usaLow.js`. Other maps are in the `@amcharts/amcharts5-geodata` package's `dist/`, at the same path as below `geodata/` on the amCharts CDN (`dist/worldLow.js`, `dist/region/world/europeLow.js`), on jsDelivr (`https://cdn.jsdelivr.net/npm/@amcharts/amcharts5-geodata@5.2.0/dist/worldLow.js`) and unpkg.
- **PDF fonts** (`am5fonts_*`) are only on cdn.amcharts.com.

**Bundling.** Keep code splitting on (esbuild: `splitting: true`, `format: "esm"`). With the stock toolbar's drawing or indicator controls, `<am5-chart>`, the JSON plugin or the exporting plugin, a single-file bundle inlines the exporting plugin's lazy parts (PDF, Excel, annotations), about 1 MB gzipped more: a stock chart with those controls loads about 275 KB gzipped split, 1.4 MB as one file (the toolbar itself and `PeriodSelector` add almost nothing). So does `import * as am5stock` when the whole namespace object is kept (assigned to a variable or `window`); `am5stock.StockChart` and the like keep only what they use. A stock chart without those controls bundles to about 130–200 KB, core + `xy` to about 170 KB. The script files gzipped: `index.js` + `xy.js` 182 KB, + `stock.js` 260 KB. Docs: https://docs.amcharts.com/v5/getting-started/#bundling.

**The npm package also ships** `AGENTS.md`, `docs/` (a bundled copy of these notes, possibly older) and `examples/<name>/index.ts` (62 also as `config.json`; `examples/examples.json` lists what each shows). When the package is installed, those examples are verified starting points.

## Which reference to read

Based on the chart type the user is building, read the relevant reference file for detailed API, patterns, and examples:

| User wants | Read |
|-----------|------|
| Line, area, bar, column, candlestick, OHLC, scatter, stacked charts | `references/xy.md` |
| Pie, donut, semi-circle, funnel, pyramid, pictorial stacked charts | `references/pie.md` |
| World map, country map, choropleth, bubble map, point map, map sankey, pixel/dot map, satellite image or day/night map, globe with raised arcs | `references/map.md` |
| Treemap, sunburst, force-directed, pack, partition, tree, org chart | `references/hierarchy.md` |
| Sankey, chord, arc diagram, alluvial, flow visualization | `references/flow.md` |
| Radar, spider, polar chart, gauge, speedometer, meter | `references/radar.md` |
| Financial stock chart, candlestick with indicators, trading chart | `references/stock.md` |
| Serpentine, spiral, curve chart, custom-shape timeline | `references/timeline.md` |
| Gantt chart, project timeline, task management chart | `references/gantt.md` |
| Word cloud, tag cloud, sentence cloud | `references/wordcloud.md` |
| Venn diagram, set overlap visualization | `references/venn.md` |
| Interactive controls: buttons, sliders, steppers, color pickers | `references/ui-elements.md` |
| A chart as a JSON config: "JSON config", "amCharts JSON", a `<am5-chart>` element, a framework package's `config` prop, a Chart Maker link | `references/json.md` (with the chart type's reference for its settings) |

If the chart type is unclear, start with `references/xy.md` — XY charts are by far the most common.

If the user asks about core setup (theming, colors, exporting, legends, tooltips, disposal, responsive) without a specific chart type, the information below is sufficient — no reference file needed. For a chart in a React, Vue, Angular, Svelte or React Native app, read "Frameworks" below as well as the chart type's reference.

## Root element setup

```js
const root = am5.Root.new("chartdiv"); // div id or HTMLElement reference

// Apply theme(s): Animated and ResponsiveMax on every chart; a palette or Dark after them
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);

// Set locale (optional)
import am5locales_de_DE from "@amcharts/amcharts5/locales/de_DE";
root.locale = am5locales_de_DE;

// Set date/number formats (optional)
root.dateFormatter.setAll({ dateFormat: "yyyy-MM-dd" });
root.numberFormatter.setAll({ numberFormat: "#,###.##" });

// Time zone (optional): date axes, formatters, data grouping and the stock
// PeriodSelector/DateRangeSelector follow it; root.utc = true for UTC
root.timezone = am5.Timezone.new("America/New_York");
```

One Root per DOM element: a second `am5.Root.new()` on the same element throws "You cannot have multiple Roots on the same DOM node", so dispose the old root first. Separate elements on one page can each have their own Root (for example, a chart in a modal), and each is disposed on its own.

**Canvas or SVG.** Charts draw on canvas by default. `am5.SVGRenderer` draws the same chart as live SVG instead — it can only be chosen when the root is created:

```js
const root = am5.Root.new("chartdiv", { renderer: am5.SVGRenderer });
// or, before creating any root, for every chart on the page:
am5.Root.defaultRenderer = am5.SVGRenderer;
```

Charts look and behave the same with either (text is measured the same way, interaction and image export work as usual); only blend modes `DST_OVER`, `SRC_ATOP` and `XOR` are canvas-only (drawn as normal in SVG, with a console warning). Keep the canvas default unless the user asks for SVG in the page. An SVG **file** does not need it — `am5.renderToSVG()` and the exporting menu make SVG from either renderer (see "Exporting"). In SVG mode pointer input is still resolved by amCharts (the `<svg>` layers have `pointer-events="none"`), so attach handlers with `events.on()`, not DOM listeners.

**Available themes:**

| Kind | Themes |
|------|--------|
| Behavioral | `Animated` (animations), `ResponsiveMax` (on every chart: fits labels, legends and scrollbars; see "Responsive rules"), `Micro` (stripped-down micro charts, alone), `Responsive` (a size-based theme, which mostly hides parts on small charts) |
| Light palettes | `Dataviz`, `Frozen`, `Kelly`, `Material`, `Moonrise`, `Spirited`, `Ember`, `Nord`, `Pastel`, `Petroleum`, `Savanna`, `Colorblind` (Okabe-Ito, color-blindness safe), `Patterns` (pattern fills instead of flat colors) |
| Dark | `Dark`, `Midnight`, plus a dark variant of most palettes: `DatavizDark`, `FrozenDark`, `KellyDark`, `MaterialDark`, `MoonriseDark`, `SpiritedDark`, `NordDark`, `PastelDark`, `ColorblindDark`, `PatternsDark` |
| Parameterized | `Monochrome`, `Adaptive` — see below |

Palette themes are additive: put them after `Animated` and `ResponsiveMax`, e.g. `root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root), am5themes_Nord.new(root)])`. The `*Dark` variants already carry dark interface colors, so do NOT also add `Dark`.

## Colors

```js
// From hex integer or CSS string — only these forms are valid
am5.color(0xff0000)
am5.color("#ff0000")
am5.color("rgb(255, 0, 0)")

// WRONG — {r,g,b} objects are NOT accepted
// am5.color({ r: 255, g: 0, b: 0 })  // throws!

// Lighten / darken — STATIC methods on am5.Color (NOT instance methods)
am5.Color.lighten(am5.color(0xff0000), 0.3)   // lighter
am5.Color.lighten(am5.color(0xff0000), -0.3)  // darker (use negative value; there is NO .darken())
am5.Color.brighten(am5.color(0xff0000), 0.3)  // brighter
// WRONG: am5.color(0xff0000).lighten(0.3) — this does NOT work, lighten is not an instance method

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

// A color on a scale through several colors: position 0–1, clamped, colors evenly spaced; a third argument "hsl" blends in HSL (default "rgb")
var ic = root.interfaceColors;
var mid = am5.Color.interpolateColors(0.5, [ic.get("negative"), ic.get("warning"), ic.get("positive")]);   // = warning
```

The interface color `warning` is a yellow between `negative` (red) and `positive` (green), for the middle of a bad-to-good scale. Prefer `interpolateColors()` through it over `am5.Color.interpolate(t, negative, positive)`, whose middle is brown.

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

**A legend that fits: `autoFit: true`.** As room runs out, it fits the legend in steps:
1. Empty value labels give their room back.
2. A legend that fits at three quarters of its size or more gets smaller.
3. One that wouldn't fit even then scrolls instead, usually at full size, and item names too wide are shortened.
4. When not even one row fits, the legend is hidden.

Above or below the chart it takes at most 30% of the chart's height, beside it at most 40% of its width. A `width`, `height`, `maxWidth`, `maxHeight`, `scale` or `verticalScrollbar` you set on the legend is kept. Without it, a long legend below a pie can take the whole chart and leave the pie no room.

**A right-to-left chart**, for Hebrew or Arabic: set the Root's `direction` when you create it.

```js
const root = am5.Root.new("chartdiv", { direction: "rtl" });
```

- Text with no `direction` of its own is drawn right to left.
- Legends start on the right: a row's first item is on the right, and a vertical legend lines its items up on its right edge. Each item reads from the right: the marker, then the name, then the value.
- `"ltr"` keeps everything left to right, e.g. an English chart on a `dir="rtl"` page.
- Not set, text follows the CSS direction of the chart's element, and legends stay left to right.

Set it in `Root.new()` (or a JSON config's `root` section), not later. Don't also set `reverseChildren` on legend items: the two undo each other and the legend flips back.

A `Label` with a `width` keeps its text inside its box for every `textAlign` and `direction`: `left` and `right` are the box's sides, `start` and `end` follow the direction.

## Tooltip

```js
series.set("tooltip", am5.Tooltip.new(root, {
  labelText: "{name}: {valueY}"   // data placeholders in {}
}));
```

**Placeholders:** `{name}`, `{valueX}`, `{valueY}`, `{categoryX}`, `{categoryY}`, `{value}`, `{category}`, `{valuePercentTotal}`, `{sum}`.

**Inline formatting:** `"[bold]{name}[/]: [fontSize: 20px]{value}[/]"`

Numbers in placeholders get thousands separators (`{year}` shows `2,024`): see "Number formats".

**A tooltip with no text renders as an empty bubble.** Enabling a `am5.Tooltip` without `labelText` (or `series.tooltipText`) shows a blank tooltip. Series text lives on `tooltip.labelText` or `series.set("tooltipText", ...)`; for column/bar charts the text usually goes on `series.columns.template.set("tooltipText", "{categoryX}: {valueY}")`.

### Styling a tooltip

The tooltip background is a **`PointedRectangle`** (NOT a `RoundedRectangle`), so it has a single `cornerRadius` — there are no `cornerRadiusTL/TR/BL/BR`:

```js
const tooltip = am5.Tooltip.new(root, { labelText: "{valueY}" });

// Background color + shape — must turn OFF getFillFromSprite, else bg copies the series color
tooltip.set("getFillFromSprite", false);
tooltip.get("background").setAll({
  fill: am5.color(0x000000),
  fillOpacity: 0.8,
  stroke: am5.color(0xffffff),
  strokeOpacity: 0.3,
  cornerRadius: 6        // single radius — PointedRectangle, not four corners
});

// Text color — must turn OFF autoTextColor (it auto-picks for contrast), then set on the label
tooltip.set("autoTextColor", false);
tooltip.label.setAll({ fill: am5.color(0xffffff) });   // tooltip.label is read-only accessor

series.set("tooltip", tooltip);
```

`getStrokeFromSprite` (default `false`) copies the sprite's stroke color when `true`.

### Shared vs per-sprite tooltip

Setting **only** `tooltipText` on a sprite (no `tooltip:` instance anywhere on it) is enough to get a hover tooltip: the sprite lazily resolves the **Root's shared default `Tooltip`** (`sprite.getTooltip()` returns it). You only need your own `am5.Tooltip.new(root, {...})` when that element's tooltip must **look different** from the shared one, or on XY charts, where series/axis tooltips are **cursor-driven** rather than hover-driven and need their own instance.

So for non-XY charts do not create a Tooltip per series — set `tooltipText` on the right template and style the look once on the shared tooltip:

```js
// Where tooltipText goes on non-XY charts
pieSeries.slices.template.set("tooltipText", "{category}: {value}");   // pie / funnel / venn slices
hierarchySeries.nodes.template.set("tooltipText", "{category}: {sum}"); // hierarchy nodes
flowSeries.nodes.nodes.template.set("tooltipText", "{name}");           // flow nodes
flowSeries.links.template.set("tooltipText", "{sourceId} → {targetId}: {value}"); // flow links
wordCloud.labels.template.set("tooltipText", "{category}: {value}");    // word cloud words
polygonSeries.mapPolygons.template.set("tooltipText", "{name}");        // map polygons (map points: the bullet sprite)

// Style the shared tooltip once — it lives on root.container (applies everywhere no dedicated Tooltip was set)
var shared = root.container.get("tooltip");
shared.set("getFillFromSprite", false);
shared.get("background").setAll({ fill: am5.color(0x000000), fillOpacity: 0.8 });
```

**A tooltip on a `Container` has no background.** The tooltip takes its color from the target's `fill` (`getFillFromSprite`, default `true`), and a Container has none, even with a `background`: only black text shows. Put `tooltipText` on a child that has a fill. An invisible circle as the last child still takes hover and gives a bigger target. Or set `getFillFromSprite: false` and a background fill (above).

```js
pointSeries.bullets.push(function (root, series, dataItem) {
  var box = am5.Container.new(root, {});
  box.children.push(am5.Circle.new(root, { radius: 8, fill: am5.color(0xcc3300) }));
  box.children.push(am5.Label.new(root, { text: "{name}", populateText: true, x: 10, centerY: am5.p50, fontSize: 12 }));
  box.children.push(am5.Circle.new(root, {
    radius: 12, fill: am5.color(0xcc3300), fillOpacity: 0,   // invisible; its fill colors the tooltip
    tooltipText: "{name}"
  }));
  return am5.Bullet.new(root, { sprite: box });
});
```

## Number formats

Numbers in `{placeholders}` use `root.numberFormatter`'s `numberFormat` (default `"#,###.#####"`: separators, at most 5 decimals). Format one placeholder with `{valueY.formatNumber('#,###.00')}`; a `ValueAxis` has its own `numberFormat` and `tooltipNumberFormat`. A `numberFormatter` on a tooltip or a template doesn't change placeholders. Docs: https://docs.amcharts.com/v5/concepts/formatters/formatting-numbers/#rounding-to-whole-numbers, https://docs.amcharts.com/v5/concepts/formatters/formatting-numbers/#years-without-thousands-separators.

| Format | 6708.044000000005 | 1.125 | 2024 |
|---|---|---|---|
| `#,###` | 6,708.044 | 1.125 | 2,024 |
| `#,###.` | 6,708 | 1 | 2,024 |
| `#,###.#` | 6,708 | 1.1 | 2,024 |
| `#,###.00` | 6,708.04 | 1.13 | 2,024.00 |
| `#` | 6708.044 | 1.125 | 2024 |

- The part after `.` decides rounding. With no `.` the decimals stay, unlike Excel's `#,###`, and only floating-point noise is dropped; a format ending in `.` rounds to a whole number. For animated values (`{valueXWorking}` in a bar race) use `'#,###.'`.
- Every number placeholder gets a thousands separator, so a year `{year}` shows `2,024`. Use `{year.formatNumber('#')}`, or keep the year a string in the data.
- Positive, negative and zero formats are split by `|`, not `;` (`"#,###;'-'#,###;0"` prints `500;-500;0`). The negative part still gets a minus unless it has the `s` modifier: `"#,###|(#,###s)|'-'"` gives `1,000`, `(1,000)`, `-`.
- Prices: `root.numberFormatter.set("numberFormat", "#,###.00##")` shows cents, and up to four decimals for small prices (`48.50`, `0.0123`). A value axis formats its labels by its grid step instead; `preferredDecimals: [0, 2]` on it makes them whole numbers or cents (`80`, `100`; `48.50`, `49.00`). See `references/stock.md`.

## Chart title

Do NOT add titles as HTML elements — they are outside the chart and won't appear in exports. Add an `am5.Label` to the container BEFORE the chart, and set `verticalLayout`:

```js
// Option 1: directly on root.container
root.container.children.push(am5.Label.new(root, {
  text: "Chart Title",
  fontSize: "1.3em",
  x: am5.p50,
  centerX: am5.p50
}));
root.container.set("layout", root.verticalLayout);

var chart = root.container.children.push(am5xy.XYChart.new(root, { ... }));

// Option 2: with a wrapper container
var container = root.container.children.push(am5.Container.new(root, {
  width: am5.p100,
  height: am5.p100,
  layout: root.verticalLayout
}));
container.children.push(am5.Label.new(root, {
  text: "Chart Title",
  fontSize: "1.3em",
  x: am5.p50,
  centerX: am5.p50
}));
var chart = container.children.push(am5xy.XYChart.new(root, { ... }));
```

## Exporting

```js
import * as am5plugins_exporting from "@amcharts/amcharts5/plugins/exporting";

const exporting = am5plugins_exporting.Exporting.new(root, {
  menu: am5plugins_exporting.ExportingMenu.new(root, {}),
  filePrefix: "my-chart",
  pngOptions: { quality: 0.8 },
  pdfOptions: { addURL: true }
});
```

**SVG export** works whichever renderer draws the chart. The `ExportingMenu` lists "SVG" by default (`svgOptions: { disabled: true }` removes it; `svgOptions: { precision: 3 }` sets the decimals); in code use `exporting.download("svg")`, `exporting.getSVG()` (markup) or `exporting.exportSVG()` (data URI). Without the plugin, the core has `am5.renderToSVG()`:

```js
// The chart as last drawn — after a change, wait for the frame to be drawn
root.events.once("frameended", function () {
  const svg = am5.renderToSVG(root);                              // SVG markup string
  const part = am5.renderToSVG(series, { background: "#ffffff" }); // one element, where it sits on the chart
});
```

Options: `width`, `height`, `precision` (default `3`), `idPrefix` (`"am5-"`; vary it when several SVGs share a page), `title` (screen-reader name; default the Root `ariaLabel`), `background` (CSS color; default transparent), `includeNonExportable` (default `false`: also write elements marked `exportable: false`, such as scrollbar grips). Left out: elements with `exportable: false` (unless `includeNonExportable`), cross-origin images without CORS, and fully transparent elements. The SVG suits design tools such as Figma: fonts are attributes with sizes in pixels, text is placed without `dominant-baseline`, patterns are drawn as shapes, and layers are named after series, data items, axes and the legend.

## Responsive rules

**`am5themes_ResponsiveMax` goes on every chart** (Critical rule 16). It fits labels and legends at any size, and adapts the chart on small screens, hiding parts only as the last step:

```js
import am5themes_ResponsiveMax from "@amcharts/amcharts5/themes/ResponsiveMax";   // script tag: themes/ResponsiveMax.js
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);
```

What it does:
- It turns on the settings in the table below: `autoLabels` on axis renderers and on series (pie labels included), `autoFit` on legends and stock toolbars, and `autoHide` on a chart's scrollbars.
- It sets the root's `touchGestures` to `"auto"`: on a phone, a vertical swipe over the chart scrolls the page. See "Touch" below.
- A vertical scrollbar hides on a chart 400 px wide or less, a horizontal one on a chart 300 px tall or less, or 400 px wide or less. Pinching zooms instead. A stock chart is measured whole, not by panel.
- A stock chart 400 px wide or less gets less padding, and less space above each panel.
- When the chart's div is 400 px or less in either direction, text is drawn at 85% of its size (`fontSize: "0.85em"`).
- Scrollbar margins shrink too: a horizontal scrollbar's when the div is 400 px tall or less, a vertical one's when it is 400 px wide or less.
- Labels you add with your own `fontSize`, such as a title, shrink when the div is smaller than 600 × 450 px, down to three quarters of their size.
- Axis labels get `keepInView`: x axis labels on a chart 400 px wide or less, y axis labels on one 400 px tall or less.
- A Gantt chart 600 px wide or less drops its expand, collapse and color buttons. Its two scrollbars hide at 500 px wide or less, and the horizontal one also at 400 px tall or less; its plot is dragged to move.

A setting you set in code wins over the theme: `autoLabels: false` on one series or renderer turns that part off. The theme also has `addRule()`, as below.

The settings also work on their own. All are off by default:

| Setting | On | Does |
|---|---|---|
| `autoLabels: true` | any axis renderer (`AxisRendererX`, `AxisRendererY`, radar's) | Spaces labels by their own size. A short axis gets more labels (up to about eight). A category axis shows every label that fits, even closer than `minGridDistance`. Long labels are spaced by their width, so there can be fewer than without it. A label that would be cut off, or overlap another axis's labels, is hidden. |
| `autoLabels: true` | `PieSeries` | Moves the pie aside or makes it smaller (down to half the chart's shorter side) to make room. Labels then wrap, are shortened or are hidden, smallest slices first. See `references/pie.md`, "Labels". |
| `autoLabels: true` | any other series | Hides a bullet's label while it overlaps an earlier label or is cut off. The bullet's shape stays. |
| `keepInView: true` | axis labels (`renderer.labels.template`) | A label whose category or date period is only partly in view, such as the first month after zooming, moves into the part in view instead of hiding. It hides only where that part is too short for it. For unrotated labels inside their cell (`location` or `multiLocation` between 0 and 1), as they are by default when every category or period has a label. Where the grid steps over several (every 2 months), labels sit on grid lines unless `multiLocation` is set, e.g. `0.5`. Not for value axis labels. On by default for a Gantt chart's date rows. |
| `autoFit: true` | `Legend` | See "Legend". |
| `autoFit: true` | `StockToolbar` | Folds the controls that don't fit in one row, a step at a time: first the labels of Indicators, Draw, Comparison and the series type control (their icons stay), then the period buttons into one button that lists them. Needs the toolbar's default CSS. |
| `autoHide: 300` | a chart's `scrollbarX` / `scrollbarY` | Hides the scrollbar when the chart is that many pixels across it or less: a `scrollbarX` by the chart's height, a `scrollbarY` by its width. Meanwhile it turns on `pinchZoomX` / `pinchZoomY`, unless that is set in code. |

`ChartSerializer` saves these settings when you set them in code. It does not save what they decide (hidden labels, a smaller legend, the pinch zoom), nor what a theme turns on, so a config saved from a small chart opens full size.

**Touch: `root.touchGestures`** decides who gets a touch gesture that starts on the chart:
- `"chart"` (default): the chart, wherever it drags, pans, zooms or selects, so the page doesn't scroll by a swipe there.
- `"tap"`: the page, until the chart is tapped; then the chart, until `tapToActivateTimeout` passes without a touch.
- `"auto"`: a vertical swipe scrolls the page. A swipe that starts sideways, or uses two fingers, works the chart. Scrollbar grips, buttons, stock drawing tools and radar charts take any swipe that starts on them.

ResponsiveMax sets `"auto"` unless `touchGestures` or `tapToActivate` is set in code. So with ResponsiveMax, don't set `root.tapToActivate`: `true` is the same as `"tap"`. To keep the chart taking every gesture, set `root.touchGestures = "chart"`.

**Rules of your own** go on the `am5themes_ResponsiveMax` instance (`am5themes_Responsive` takes them too, but also hides parts of its own):

```js
const responsive = am5themes_ResponsiveMax.new(root);
responsive.addRule({
  relevant: am5themes_ResponsiveMax.widthXS,  // <= 200px
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

Color or size elements by value. Requires `calculateAggregates: true` on the series (unless `minValue`/`maxValue` are set manually).

```js
// Color columns by value
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  min: am5.color(0xe5dc36),
  max: am5.color(0x5faa46),
  key: "fill"
}]);

// Scale bullet radius by value
series.set("heatRules", [{
  target: bulletTemplate,        // am5.Template.new({})
  dataField: "value",
  min: 3,
  max: 30,
  key: "radius"
}]);

// Through more than two values: `stops` in place of min/max, e.g. red–yellow–green
var ic = root.interfaceColors;
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  stops: [ic.get("negative"), ic.get("warning"), ic.get("positive")],
  key: "fill"
}]);
```

A `radius` rule is linear in radius: areas grow with the square of the value, and the smallest value gets `min`, not zero. For bubbles whose area matches the value, set the radius in the bullet function instead: `radius: maxRadius * Math.sqrt(value / maxValue)` (see `references/xy.md`, "Scatter / Bubble chart").

**Heat rule settings:**

| Setting | Type | Description |
|---------|------|-------------|
| `target` | Template | Element template to apply heat to |
| `key` | string | Setting to modify: `"fill"`, `"radius"`, `"opacity"`, `"strokeWidth"`, `"fontSize"`, etc. |
| `dataField` | string | Data field for the value: `"valueY"`, `"value"`, `"valueX"`, etc. |
| `min` | color/number | Value applied at the lowest data value |
| `max` | color/number | Value applied at the highest data value |
| `neutral` | color/number | Value for elements whose data item has no value (otherwise they keep the template's) |
| `stops` | array | Values for a scale through more than two, in place of `min`/`max`: the lowest data value gets the first, the highest the last, blended between. Numbers, percents or colors, all of one kind |
| `minValue` | number | Override auto-calculated min (skips `calculateAggregates`; `0` is honored) |
| `maxValue` | number | Override auto-calculated max (skips `calculateAggregates`; `0` is honored) |
| `logarithmic` | boolean | Spreads the values between `min` and `max` on a logarithmic scale; zero and below get `min` (default `false`) |
| `customFunction` | function | `(sprite, min, max, value)` — full control over the rule |

Values outside `minValue` / `maxValue` get `min` / `max`.

**Using heat rules on bullets** — bullets need an explicit `am5.Template`, and the series must track the field the rule reads:

```js
var circleTemplate = am5.Template.new({});

series.bullets.push(function(root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Circle.new(root, {
      fill: series.get("fill"),
      tooltipText: "{valueX}: {valueY}"
    }, circleTemplate)              // pass template as 3rd arg
  });
});

series.set("heatRules", [{
  target: circleTemplate,
  dataField: "valueY",
  min: 3,
  max: 25,
  key: "radius"
}]);
```

Rules that make bullet heat rules work (each failure is **silent** — every bullet just comes out the same size):
- `target` must be the **`Template`** the bullet sprites are built from (3rd argument of `am5.Circle.new(...)`), not the sprite and not `series.bullets`.
- `dataField` is the data item **property key** (`"valueY"`, `"valueX"`, `"value"`, …), **not** the raw column name. To size XY bullets by a column that is not already an axis value, register it as the base value field: `series.set("valueField", "population")` and use `dataField: "value"`.
- `calculateAggregates: true` is required (unless `minValue`/`maxValue` are given) — without it `valueLow`/`valueHigh` are never computed and every bullet gets the midpoint size.
- Setting `valueField` (or any `*Field`) **after** the series has processed data does not re-read the data — re-set it: `series.data.setAll(series.data.values.slice())`.
- Bullet heat rules survive `ChartSerializer`.

**HeatLegend:**

```js
var heatLegend = chart.children.push(am5.HeatLegend.new(root, {
  orientation: "horizontal",       // or "vertical"
  startColor: am5.color(0xe5dc36),
  endColor: am5.color(0x5faa46),
  stepCount: 5                     // number of color stops
}));

// Sync legend range with series data: the end labels show these numbers
series.events.on("datavalidated", function() {
  heatLegend.set("startValue", series.getPrivate("valueLow"));
  heatLegend.set("endValue", series.getPrivate("valueHigh"));
});

// Through several colors: `colors` in place of startColor/endColor, matching a heat rule's `stops`
var ic = root.interfaceColors;
var scaleLegend = chart.children.push(am5.HeatLegend.new(root, {
  orientation: "horizontal",
  colors: [ic.get("negative"), ic.get("warning"), ic.get("positive")]
  // stepCount: 1 (default) = a gradient through them; with stepCount > 1, one step per color
}));
```

`startText`/`endText` replace the numbers at the ends: with `startText: "Low"` the label says only "Low", whatever `startValue` is, and `""` leaves it blank. `{startValue}` in them is not filled in. Leave them out to show `startValue`/`endValue` as formatted numbers. On a map, give the legend a `background` so a zoomed map doesn't show through it (`references/map.md` → Choropleth). Docs: https://docs.amcharts.com/v5/concepts/legend/heat-legend/#startend-labels.

## Animations

```js
series.appear(1000);        // duration in ms
chart.appear(1000, 100);    // duration, delay
```

**`autoAppear`** — the same opening animation as a setting, so it works from a JSON config or `<am5-chart>`, where `appear()` cannot be called:

```js
chart.setAll({ autoAppear: true, appearDuration: 1000, appearDelay: 100 });
series.setAll({ autoAppear: true, appearDuration: 1000 });   // a series waits for its first data
// JSON: "settings": { "autoAppear": true, "appearDuration": 1000, "appearDelay": 100 } on the chart and each series
```

It plays once, when the element is first drawn (data updates do not replay it; turning the setting off and on does). `autoAppear` defaults to `false`; `appearDuration` falls back to the element's `stateAnimationDuration`; `appearDelay` (default `0`) keeps the element hidden until it starts. Elements made from a template play as each is made; an element that starts hidden stays hidden.

**Declared animations — the `animations` setting.** Every element can carry its own animations as data, so they live in a JSON config and survive `ChartSerializer` — prefer this over `sprite.animate({ loops: Infinity })` for endless effects:

```js
series.bullets.push(function (root, series, dataItem) {
  return am5.Bullet.new(root, {
    sprite: am5.Circle.new(root, {
      radius: 6,
      animations: [   // loops: 0 = forever; yoyo goes back to `from`; easing is a NAME
        { key: "scale", from: 1, to: 1.6, duration: 800, loops: 0, yoyo: true, easing: "sine" },
        { key: "opacity", from: 1, to: 0.3, duration: 800, loops: 0, yoyo: true }
      ]
    })
  });
});
```

Entry (`IDeclaredAnimation`) fields: `key`, `to`, `duration` (required; with `yoyo` one way), `from` (default: current value), `delay` (`0`), `loops` (`1`; `0` = forever), `yoyo` (`false`), `easing` (`"linear"` — one of the 8 names under "Easing functions"; unknown names silently become linear), `ease` (`"in"` default, `"out"`, `"inOut"`), `target` (`"self"` default, or `"dataItem"` for a data-item value such as a map point's `positionOnLine`). They start when set, restart when changed, stop on dispose. **Give `from` explicitly** unless the setting has a theme default (`scale`/`opacity`/`rotation` do; `dx`, `fill`, a data item's `positionOnLine` may not) — with `from` omitted and an `undefined` current value it jumps straight to `to`, no animation. In JSON, colors/percents in `from`/`to` are `{ "type": "Color", "value": "#f00" }` / `{ "type": "Percent", "value": 50 }`.

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

// Axis zoom/scroll — watch start/end settings, not events
xAxis.on("start", () => {
  console.log("Zoomed/scrolled");
});
xAxis.on("end", () => {
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

**Knowing when the chart has finished drawing.** There is no "rendered" event. `datavalidated` fires once the data is processed, and the next `frameended` can come long before drawing ends. With 5,000 bullets drawing ended about 5 times later than that `frameended`, and with 50,000 about 10 times later. amCharts draws only while something has changed or is animating, so two animation frames in a row without a `frameended` mean it is done. Call this right after the change (`data.setAll()`, a zoom, `selectPeriod()`):

```js
function whenRendered(root, callback) {
  var frames = 0, seen = 0, quiet = 0;
  var listener = root.events.on("frameended", function () { frames++; });
  (function check() {
    requestAnimationFrame(function () {
      if (frames === seen) quiet++; else { quiet = 0; seen = frames; }
      if (quiet >= 2) { listener.dispose(); callback(); } else check();
    });
  })();
}
series.data.setAll(data);
whenRendered(root, function () { /* everything is drawn */ });
```

With `root.fps` set, frames are spaced `1000 / root.fps` ms apart, so wait longer than that instead of two animation frames. Endless animations (`loops: Infinity` with `animate()`, `loops: 0` in declared `animations`) never settle. Without a license, the amCharts logo fades in 3 s after the root is created and draws for 1.5 s; a check during that time waits for it.

**Opening a link on click needs no handler:** `series.set("urlField", "url")` makes each item's bullet, column, slice, Venn slice, map polygon or map line open the URL in its data row (`linkTarget`, default `"_self"`) — see "Recent API changes". Hierarchy nodes, flow nodes/links and word-cloud labels are not linked; keep a `click` handler there.

**A tap fires no DOM `click` on a pannable chart.** On touch, a tap on an XYChart with `panX`/`panY`, under a cursor with a zoom or select `behavior`, or on a `draggable` sprite gives `pointerdown`/`pointerup` but no `mousedown`/`mouseup`/`click` on the page (Chrome touch emulation). The amCharts `click` event still fires: use `events.on("click", …)`, not a DOM listener.

## Settings API

```js
// Set after creation
sprite.set("fill", am5.color(0xff0000));
sprite.setAll({ fill: am5.color(0xff0000), strokeWidth: 2 });

// Read current value
const fill = sprite.get("fill");
const width = sprite.getPrivate("width"); // read-only internal values

// set() RETURNS the value — handy for capturing the created object inline:
const cursor = chart.set("cursor", am5xy.XYCursor.new(root, {}));
```

**Reading back a `Percent`:** a `Percent` exposes two numbers — `.percent` is the 0–100 value, `.value` is the normalized 0–1 fraction. `am5.percent(50).percent === 50` but `am5.percent(50).value === 0.5`. When you read a percent setting back (e.g. `sprite.get("x")` after setting `am5.percent(50)`), use `.percent` for a 0–100 number; `.value` gives `0.5`.

**Reading animated settings is unreliable mid-animation.** Right after `series.appear()` / `chart.appear()`, animated settings like `opacity` are still transitioning — `get("opacity")` can return `0` (the start value). Read such settings after the animation completes, or don't persist values read during appear (a common way to accidentally bake `opacity:0` into generated code).

## Settings that already equal the default — omit them

Generated code routinely spells out settings that equal the amCharts default (verified against the default themes, and for `Gantt` the code). They add noise and mislead readers into thinking they matter. Do not emit these unless changing them:

| Class | Setting(s) that are already the default |
|-------|------------------------------------------|
| All hierarchy series | `childDataField: "children"`, `downDepth: 1`, `initialDepth: 5` |
| `Sunburst`, `Partition`, `Treemap`, `VoronoiTreemap` | `singleBranchOnly: true` (`Partition`/`Treemap` also `upDepth: 0`; `VoronoiTreemap` also `shapeType: "polygon"`; `Sunburst` ignores the setting — always single-branch) |
| `Tree`, `ForceDirected` | `singleBranchOnly: false`, `upDepth: Infinity`, `topDepth: 0` (`Tree` also `orientation: "vertical"`) |
| `PieSeries`, `FunnelSeries`, `PyramidSeries`, `PictorialStackedSeries` | `alignLabels: true` (`PieSeries` also `startAngle: -90`, `endAngle: 270`; `FunnelSeries` also `orientation: "vertical"`, `startLocation: 0`, `endLocation: 1`, `bottomRatio: 0`) |
| `Chord` | `sort: "none"` (data order) |
| `Gantt` | `excludeWeekends: true` |
| `XYSeries` (all XY series) | `maskBullets: true` — note the default is **true**, so `maskBullets: false` is a real change |
| `LineSeries` (incl. smoothed/step) | `connect: true`; `StepLineSeries` `noRisers: false` |
| `XYChart` | `maxTooltipDistance` — leave **unset** (unset shows tooltips for all items in the category; `0` is *not* the same) |
| `HeatLegend` | `stepCount: 1` |

Runtime-mutated settings (`visible`, `x`/`y`, `opacity`, `scale`, cursor line `visible`, physics/random layouts, computed `AxisRendererCurve.points`) cannot be checked by "remove and re-`get()`" — amCharts rewrites them after render. User-set values live in `entity._userProperties` (what `ChartSerializer` reads), separate from the full `_settings` bag that includes theme and internal values.

## Dynamic data

```js
// Replace all data (full redraw, NO animation)
series.data.setAll(newData);

// Add items
series.data.push({ category: "New", value: 42 });

// Update item at index — this ANIMATES the change
series.data.setIndex(0, { category: "Updated", value: 99 });

// IMPORTANT: To update data WITH animation, use setIndex() per item.
// setAll() replaces everything at once — no transition animation.
// Loop through items for animated updates:
// newData.forEach(function(item, i) { series.data.setIndex(i, item); });

// Remove item at index
series.data.removeIndex(2);

// Insert at specific position
series.data.insertIndex(1, { category: "Inserted", value: 50 });

// Iterating dataItems — dataItems is a PLAIN ARRAY, not a List
// ❌ series.dataItems.each(fn)                              — will throw, .each() does not exist
// ✅ am5.array.each(series.dataItems, function(dataItem) {}) — amCharts array utility
// ✅ series.dataItems.forEach(function(dataItem) {})         — standard JS
```

**Changing values on existing data items.** Series draw from the **working** values, so `dataItem.set("valueY", v)` (or `"value"` on a pie) changes nothing on screen, and `markDirtyValues()` doesn't help. Change the data row with `series.data.setIndex(i, row)` (animates), or set the working key too (jumps; use it for per-frame updates):

```js
xySeries.dataItems[0].setAll({ valueX: 4, valueY: 7, valueXWorking: 4, valueYWorking: 7 });   // XY series
pieSeries.dataItems[0].setAll({ value: 80, valueWorking: 80 });                                // pie, funnel, pyramid
// per-frame updates through setIndex on XY: set interpolationDuration: 0 on the series, or the points lag behind
```

On maps it is worse: a `geometry` can't be animated, so with `setIndex` it switches only when `interpolationDuration` ends, and a line or polygon replaced every frame keeps its first shape. Set `interpolationDuration: 0`, or set the geometry on the element (`references/map.md` → "Live updates").

A data item's bullets are a plain array **property**: `dataItem.bullets?.[0]?.get("sprite")` is the sprite the bullet function made (`dataItem.get("bullets")` is `undefined`).

`dataItem.bullets` stays `undefined` until the series draws that item: right after `data.setAll()`, in `datavalidated` and in the first `frameended` (bullets appear on the second frame), and, on a zoomed `DateAxis`, for items out of view. Decide a bullet's look inside the bullet function from `dataItem` (or with `templateField`), not by reaching into `dataItem.bullets` after setting the data.

## Performance: big data, fast updates, no animation

- **Animations off:** leave out the Animated theme. To keep it but stop one element animating, set `interpolationDuration: 0` (value changes, axis zoom and rescale) or `stateAnimationDuration: 0` (hover and show/hide states) on it. Docs: https://docs.amcharts.com/v5/concepts/animations/#turning-off-animations.
- **Zoom without animation:** an axis zooms over its own `interpolationDuration` (600 ms with the Animated theme). There is no `zoomAnimation` setting. Set it to 0 on the X axis and on value axes, which also animate their rescale after a zoom. `zoom()`, `zoomToDates()`, `zoomToValues()` and `zoomToCategories()` take a duration as their third argument; `0` jumps. Docs: https://docs.amcharts.com/v5/charts/xy-chart/zoom-and-pan/#zoom-animation.
- **Frame rate:** `root.fps = 30` redraws about 20 times a second instead of 60, which frees the main thread for incoming data.
- **High-rate streams** (a point every 10 ms, 3 series):
  - The Live Data pattern (`push` + `removeIndex(0)` per point, Animated theme) kept up with 30–60% of the points.
  - `interpolationDuration: 0` on the series and axes, plus `root.fps = 30`, kept up with 80–90%.
  - Collecting points and adding them once per frame with `data.pushAll()` adds little.
  - A timer-driven feed drops the ticks a busy page skips; WebSocket messages queue instead.
- **Large line data:** `references/xy.md`, "DateAxis". **Large scatter:** `references/xy.md`, "Scatter / Bubble chart". **Knowing when drawing has finished:** "Events".

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

// Built-in state names: "default", "hover", "active", "disabled", "hidden"
// State animation: stateAnimationDuration, stateAnimationEasing
```

**A state keeps the value it first replaced.** Once a state (e.g. `hover`) has changed a setting, the sprite's `"default"` state holds the value from that moment and restores it on every unhover. So a setting changed later (painting a polygon on click, a new base color) snaps back on the next unhover unless you change it in both places:

```js
polygon.set("fill", color);
polygon.states.lookup("default")?.set("fill", color);
```

**`sprite.hover()` also shows the sprite's tooltip.** To highlight other elements from an event (related links or columns), apply the state instead: `sprite.states.applyAnimate("hover")`, and `"default"` to undo it. Several `hover()` calls move the shared tooltip to the last sprite.

## Parameterized themes — Monochrome & Adaptive

Two themes generate their palette from colors you pass in (OKLCH-based, so the result stays perceptually balanced). Because a theme cannot take constructor parameters, these ship as **factory functions** — call them with `(root, settings)`; `.new(root, settings)` also works.

```js
import am5themes_Monochrome from "@amcharts/amcharts5/themes/Monochrome";
import am5themes_Adaptive from "@amcharts/amcharts5/themes/Adaptive";

// Monochrome — single-hue lightness ramp
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_ResponsiveMax.new(root),
  am5themes_Monochrome(root, {
    color: 0x2c6e91,   // base hue (default: blue #2e7c9e)
    accent: 0xff7f0e,  // optional — applied to the FIRST series only, so it pops
    count: 7,          // steps in the ramp (default 7)
    dark: false        // true = dark background + light-to-mid ramp
  })
]);

// Adaptive — full palette generated from one or two base colors
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_ResponsiveMax.new(root),
  am5themes_Adaptive(root, {
    baseColor: 0x2c6e91,   // first generated color IS this color (good for brand colors), unless keepContrast has to move it
    baseColor2: 0xffdd00,  // optional — palette spans the two colors
    count: 10,             // number of series colors (default 10)
    dark: false
  })
]);
```

Both are additive (they do not modify built-in themes) and both handle dark mode themselves via `dark: true` — do not also add the `Dark` theme.

**More settings (both themes, all off by default):** `interleave: true` takes the colors in the order 1, 4, 2, 5, 3, 6, so series next to each other differ more (Monochrome keeps `accent` first); `mapShade: true` fills map land with a mid shade of the base color, as light as default map land, instead of the color itself; `keepContrast: true` keeps the series colors and primary buttons at least 3:1 against the background (or give a ratio, e.g. `4.5`). In Adaptive it moves `baseColor` and `baseColor2` themselves, so the first color is then not exactly the brand color; in Monochrome it moves the end of the shades nearest the background. A branded palette:

```js
am5themes_Adaptive(root, { baseColor: 0x2c6e91, baseColor2: 0xffdd00, count: 10, interleave: true, mapShade: true, keepContrast: true })
```

## Custom themes

```js
const myTheme = am5.Theme.new(root);
myTheme.rule("Label").setAll({ fontSize: 12, fill: am5.color(0x555555) });
myTheme.rule("Grid").setAll({ stroke: am5.color(0xe0e0e0) });
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root), myTheme]);
// Theme order matters: later themes override earlier ones
```

## Dark theme

When the page has a dark background, add the Dark theme so labels, grid, and tooltips are readable:

```js
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_ResponsiveMax.new(root),
  am5themes_Dark.new(root)        // must come after Animated and ResponsiveMax
]);
```

Import: `import am5themes_Dark from "@amcharts/amcharts5/themes/Dark"` or CDN `themes/Dark.js`.

**Rule:** Only add Dark theme when the background is clearly dark. Default to white/light background with no Dark theme.

## ColorSet — using default colors

amCharts assigns colors automatically via a built-in ColorSet. Do not invent custom palettes unless the user asks.

```js
// Get the chart's default color set
var colors = chart.get("colors");       // XY and Radar charts only (pie and other percent charts: series.get("colors"))
var colors = series.get("colors");      // pie/percent charts use series-level colors

// MapChart has NO built-in ColorSet — create your own:
var colors = am5.ColorSet.new(root, {});

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

**Swapping a `ColorSet`.** The theme's `baseColor` resolves lazily, so a **synchronous** re-color right after swapping a `ColorSet` can read a transient value (sprites momentarily black). Re-set the series data and let amCharts' own data pass recolor the sprites. An empty `colors` list starts from `baseColor`.

**`step` with a theme that reuses its colors** (`reuse: true`: Adaptive, Monochrome, Nord, Pastel, Kelly, …): every color is used before any repeats, so `step: count / 2` interleaves the palette.

**Repeats are lighter.** In themes that reuse their list, once every color has been used, the next round is 10% lighter. This covers Adaptive, Monochrome, Ember, Frozen, Kelly, Material, Midnight, Moonrise, Nord, Pastel, Petroleum, Savanna and Spirited, with their Dark variants, but not Colorblind, Patterns or Dataviz. The setting is `repeatLightness` on the `ColorSet`: HSL lightness per round, `0.1` is 10%, and a negative value makes it darker. It only acts with `reuse: true`. The color sets of one root share the theme's list, and the repeats are added to that list, so whichever color set on the root first needs a new round decides that round for every chart on it. To repeat the colors as they are, set it for the whole root:

```js
// Every ColorSet on the root, through a theme rule
var noLighter = am5.Theme.new(root);
noLighter.rule("ColorSet").set("repeatLightness", 0);
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root), am5themes_Nord.new(root), noLighter]);

// Adaptive and Monochrome: a theme setting (true = 0.1, or a number)
am5themes_Adaptive.new(root, { repeatLightness: false });
```

With one chart on the root, `chart.get("colors").set("repeatLightness", 0)` before the data is set works too.

**Three or more series.** Smallest color difference (CIEDE2000) among the first 4 / 6 series colors; higher is easier to tell apart:

| Choice | First 4 / 6 | Note |
|---|---|---|
| default | 6.3 / 6.3 | blue to violet, 18° apart |
| `chart.get("colors").set("step", 3)` | 19.1 / 19.1 | keeps the default look |
| `Colorblind` | 21.7 / 21.7 | Okabe-Ito; the 4th color is a pale yellow |
| `Kelly` | 22.8 / 17.0 | the 1st color is yellow, the weakest on white |
| `Nord` | 22.1 / 14.2 | |

Avoid these for many series: `Dataviz` (series 1 and 5 are the same navy: 4.0), `Frozen` (6.9), `Moonrise` (9.4), `Spirited` (10.2), `Material` (10.3). For lines, also set `strokeWidths: [2]` on the XYChart (the legend line follows) and `legend.markers.template.setAll({ width: 24, height: 14 })`.

## Data processor

```js
// Auto-convert fields when loading external / API data
series.data.processor = am5.DataProcessor.new(root, {
  dateFields: ["date"],
  dateFormat: "yyyy-MM-dd",  // read as local time (UTC with root.utc); see below for leaving it out
  numericFields: ["value", "count"],
  colorFields: ["color"],
  emptyAs: 0  // replace null, undefined and "" with 0 (0 and false stay)
});
// Configure processor BEFORE setting data
```

Without `dateFormat`, the browser's `Date` parses the strings, so ISO strings work and a date-only `"2024-03-15"` is midnight **UTC**.

**CSV:** `am5.CSVParser.parse(text, options)` turns CSV text into rows. An empty string gives `[]` with `useColumnNames: true`, but `[{}]` (one empty row) with the default options, so check for that before `data.setAll()`. Empty lines are skipped unless `skipEmpty: false`.

## Serializing to JSON (ChartSerializer / JsonParser)

**To write a config by hand** (a "JSON config", `<am5-chart>`, a framework package's `config` prop, a Chart Maker link), read `references/json.md`. It covers the exact format, with a complete config per chart family. A config is not `{ "chart": …, "series": … }`; every object has `"type"`, its options go in `"settings"`, and its parts and data go in `"properties"`.

`am5plugins_json.ChartSerializer` turns a live chart into a JSON config and `am5plugins_json.JsonParser` rebuilds it (`await parser.parse(config, { parent: root.container })`). Rules that keep round-trips working:

- **Serialize the chart's top-level container child:** `serializer.serializeAll(root.container.children.getIndex(0))` — the chart, or on chart-less types (hierarchy, flow, word cloud, Venn) the series itself. A series placed straight in `root.container`, or one whose `selectedDataItem` points back at itself, serializes too. A drilled-down hierarchy's `selectedDataItem` is not restored (it is written as `"@self.dataItems.-1"`).
- **Adapters do not round-trip.** `includeAdapters` (default `true`) writes each adapter as `{ key, callback: "function (…) {…}" }`; the parser does not turn that string back into a function (and the closure's `chart`/`series`/`root` would not exist anyway). On parse such adapters are skipped, so the effect is lost — replace palette-coloring adapters with declarative equivalents: `colorByDataItem: true` on column series, a per-item `fill` data field + `templateField`, or `heatRules`.
- **Only user-set settings are serialized** — theme and library defaults are left out, so a value you never set will not appear in it. Add `includeRoot: true` to also write a top-level `root` section (Root settings/properties, `interfaceColors`, formatters). A parsed `root` section sets only those properties and a named locale, and never turns `sanitizeHTML` off, unless parsed with `trusted: true` (`references/json.md` rule 9).
- **Re-parsing into an existing chart:** `parser.parse(config, { updateTargets: "soft" })` applies settings onto an existing object of the same `type` instead of replacing it. The default `"strict"` replaces.
- **Maps and locales with ES module imports:** a geodata pack or locale that isn't a global is copied into the config whole, about 250 KB for `worldLow`, unless the serializer gets it in `resources`. `am5plugins_json.ChartSerializer.new(root, { resources: { am5geodata_worldLow } })` writes `"geoJSON": "am5geodata_worldLow"` instead; parse it back with the same `resources` (`references/json.md`, rule 8). With script tags the packs are globals and are written by name without it.
- **Also saved:** `themeTags` you pass to `.new()` (e.g. a `Button` with `themeTags: ["switch"]` keeps its look; tags the library adds are left out); a legend inside an `axisHeader`; declared `animations` (see "Animations"); and animations started in code that **loop forever** (`loops: Infinity`) with a nameable `am5.ease` easing — written as `animations` entries. `runningAnimations` (default `true`) controls the latter: `am5plugins_json.ChartSerializer.new(root, { runningAnimations: false })` turns it off. Never saved: finite animations (`appear()`, states, zoom) and loops with a custom-function or `pow` easing — for the opening animation use `autoAppear` (see "Animations").
- **Data-item loops:** a loop started on a **data item** in code (`dataItem.animate({ key: "positionOnLine", loops: Infinity, … })`) is saved as an `animations` entry with `target: "dataItem"`. When every data item plays the same loop, it goes on the bullet sprite. When they differ (e.g. points each moving at their own speed), each one goes in its own data row, under the sprite's `templateField` (`"bulletSettings"` if none is set).
- **Also saved:** a series hidden with `hide()` or a legend click; elements you add to library-built containers (`chart.plotContainer`, `seriesContainer`, axes containers, an axis) — no `themeTags: ["serialize"]` needed; a `ZoomableContainer`'s `contents`; `Date` values (as timestamps); settings on a series' `bulletsContainer` and on a pie/funnel series' `labelsContainer`/`ticksContainer`; the `axisFills`, `rectangles` (`Treemap`, `Partition`) and `polygons` (`VoronoiTreemap`) templates. A color taken from `root.interfaceColors` is written as a reference, `"@root.interfaceColors.get('background')"`, so a config parsed under another theme (e.g. Dark) uses that theme's color — write the same string in hand-made configs. A color taken from the theme's palette with `getIndex()` is written as `"@colors.getIndex(3)"` the same way, and hand-made configs can use it too in settings (e.g. `"fill": "@colors.getIndex(0)"`). Colors a color set makes past the theme's list are written as references too. In a data row (e.g. a column `fill` from a `templateField`) the serializer marks the row, and the object in it that holds the reference, with `"__parse": true`; the parser reads references only in marked objects (and in arrays), and elsewhere in data a string starting with `@` or `#` is plain text. A hand-written data row needs both markers: `{ "c": "A", "v": 10, "cs": { "fill": "@colors.getIndex(2)", "__parse": true }, "__parse": true }` (see `references/json.md`, rule 3). A setting such as `step` on a chart's colors is written without the theme's palette.
- **Not captured by `ChartSerializer`** — re-apply these in code after `parse()`: a `ZoomTools` you added to a hand-made `ZoomableContainer` (its `target` is written as a copy, so the parsed buttons drive a different container — re-point `target`, or use `am5.SerialChartContainer`, which wires its own `zoomTools`); Venn `hoverGraphics` (Venn slice-template `states` do round-trip); data items added with `pushDataItem()` (only `series.data` rows are written — map points on lines can be data rows, see `references/map.md`).
- **Bullets** are captured by calling the factory with a sample data item — write factories as `function(root, series, dataItem)` (pitfall #36).

**`<am5-chart>` — a chart as an HTML element.** Builds a chart from a JSON config with no root or chart code. With script tags, load `index.js`, `plugins/json.js`, `element.js`, then every chart package the config uses; in a bundled app, `import { defineChartElement } from "@amcharts/amcharts5/element"` and call `defineChartElement()`, which does nothing if the element already exists. A bare `import "@amcharts/amcharts5/element"` also works. Chart packages are loaded on demand. Then set `element.themes = [am5themes_Animated, am5themes_ResponsiveMax]`, for a map `element.resources = { am5geodata_worldLow }`, and `element.config = {…}`, as properties:

```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/plugins/json.js"></script>
<script src="https://cdn.amcharts.com/lib/5/element.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>

<am5-chart themes="Animated ResponsiveMax" renderer="svg" style="height: 400px" config='{
  "type": "PieChart",
  "properties": { "series": [{ "type": "PieSeries",
    "settings": { "valueField": "value", "categoryField": "category", "autoAppear": true },
    "properties": { "data": [{ "category": "A", "value": 10 }, { "category": "B", "value": 6 }] } }] }
}'></am5-chart>

<script>
document.querySelector("am5-chart").whenReady().then(function (r) {
  // r.root, r.chart — or listen for the element's "ready" / "error" events
});
</script>
```

Attributes: `config` (JSON) or `src` (URL of a JSON file), `themes`, `renderer="svg"` (default canvas). The `themes` attribute takes names, space- or comma-separated, or JSON with a theme's settings: `themes='["Animated", "ResponsiveMax", ["Adaptive", { "baseColor": "#4150a0" }]]'`. It works with script tags only. In a bundled app, the `themes` property takes theme classes, e.g. `[am5themes_Animated, am5themes_ResponsiveMax]`, or a class with its settings: `[am5themes_Animated, am5themes_ResponsiveMax, [am5themes_Adaptive, { baseColor: "#4150a0" }]]`. Without a height from CSS or its parent it is 400 px high. Themes are not part of a config. Changing `config`/`themes` rebuilds the chart; removing the element disposes it.

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

**High contrast:** in a forced-colors mode such as Windows High Contrast, labels, grid, axes and buttons take the system colors so they stay visible; series colors are kept. It is on by default — `am5.Root.new("chartdiv", { forcedColors: false })` keeps the chart's own interface colors.

Keyboard/screen-reader behavior: a `focusableGroup` is one TAB stop; arrow keys move between its members even when they are draggable (flow and hierarchy nodes) and SHIFT + arrows drag them; ESC hides a focused element's tooltip and ends a keyboard drag; SPACE clicks a focused element with a button-like `role`. The `ariaExpanded` setting is set by the library on Gantt tasks with subtasks and on hierarchy nodes with children; a `role: "button"` with `toggleKey` exposes `aria-pressed`. `am5.Modal` is a dialog to screen readers, named by its `ariaLabel` setting (else its first heading).

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

**Layout options:**

| Layout | Access | Behavior |
|--------|--------|----------|
| Vertical | `root.verticalLayout` | Children stacked top-to-bottom |
| Horizontal | `root.horizontalLayout` | Children in a row left-to-right |
| Grid | `root.gridLayout` | Multi-column grid |
| Custom grid | `am5.GridLayout.new(root, { maxColumns: 3 })` | Grid with custom column count |
| None | omit `layout` | Children placed at x/y coordinates |

Use layouts to arrange charts, legends, controls, and labels within containers.

**Hidden children take no space.** An element hidden with `visible: false`, `forceHidden` or `hide()` takes up no room in a container sized to its content, e.g. a hidden XY scrollbar leaves no empty band above the plot.

**Right to left: `direction: "rtl"`.** On a container with a horizontal or grid layout, it places the children from right to left, as a mirror image: a child's `marginLeft` ends up on its right. Rows of a grid still go top to bottom. In a vertical layout, children with no `x` of their own line up on the right. For a right-to-left language, set the Root's `direction` instead, which also turns the text and lays out the legends (see "Legend"). `reverseChildren: true` only reverses the order, so a grid starts at the bottom row; don't use it for RTL.

## UI Elements

amCharts 5 provides built-in interactive UI widgets. **Prefer these over HTML elements** when building controls, unless instructed otherwise. Read `references/ui-elements.md` for full API and examples.

| Element | Class | Description |
|---------|-------|-------------|
| Button | `am5.Button` | Clickable button with label, icon, background, hover/down/active states |
| Slider | `am5.Slider` | Single-value slider (0-1 range), fires `rangechanged` event |
| Scrollbar | `am5.Scrollbar` | Two-grip range selector, `orientation: "horizontal"\|"vertical"` |
| NumericStepper | `am5.NumericStepper` | Number input with up/down arrows |
| ProgressPie | `am5.ProgressPie` | Circular progress indicator with `value`, `radius`, `innerRadius` |
| SpriteResizer | `am5.SpriteResizer` | Drag-to-resize handles on any sprite |
| EditableLabel | `am5.EditableLabel` | Label that becomes editable text field on click |
| Modal | `am5.Modal` | HTML overlay dialog with `open()`/`close()` |
| ColorPicker | `am5plugins_colorPicker.ColorPicker` | Color picker (requires `plugins/colorPicker.js`) |

**Quick example — slider + button controlling a chart:**

```js
// Slider to control a value
var slider = container.children.push(am5.Slider.new(root, {
  orientation: "horizontal",
  start: 0.5,
  width: am5.percent(80),
  centerX: am5.percent(50),
  x: am5.percent(50)
}));
slider.events.on("rangechanged", function(ev) {
  var value = ev.start;  // 0-1 range
  // update chart based on slider position
});

// Button
var button = container.children.push(am5.Button.new(root, {
  label: am5.Label.new(root, { text: "Reset" }),
  centerX: am5.percent(50),
  x: am5.percent(50)
}));
button.events.on("click", function() {
  slider.set("start", 0.5);
});
```

## License and the amCharts logo

Bring licensing up only when the user asks about the logo, licensing, pricing or removing branding, or says the chart is for a commercial product. Keep it to what was asked:
- Commercial use, or the logo: amCharts 5 is free in any project, commercial included; each chart shows a small amCharts logo, and a commercial license removes it.
- Which license, only when asked: Basic fits a public website without logins, SaaS a site or app with user logins, OEM an app distributed to others, and Extended OEM group companies, white-label software or tools that build other software. Charts, Maps, Stock Chart and Gantt are licensed separately.
- How to choose: https://www.amcharts.com/pricing/licenses-explained/

Don't quote prices or link to a store.

**The key:** `am5.addLicense("YOUR-AMCHARTS-LICENSE-KEY")` before the first chart is made (in a framework app, see "Frameworks"). Never write, guess or invent a key; leave the placeholder for the user's own.

**Hiding the logo:** the logo is the attribution the free license requires. If asked to remove or hide it any other way (through code, CSS, cropping or editing the library), don't write that code. Say briefly that it breaks the free license and that a license key removes the logo, give the link above, and carry on with the rest of the request.

## Frameworks (React, Vue, Angular, Svelte, React Native)

In a framework app, use amCharts' package for that framework instead of writing the mount and dispose code yourself. The current ones are react 5.0.1, vue 5.0.2, angular 5.0.1, svelte 5.0.3 and react-native 5.0.4, which loads amCharts 5.23.0. Each package makes the chart once its element is on the page, disposes it with the component, and loads the library in the browser only, so pages rendered on a server work (Next.js, Nuxt, SvelteKit, Angular SSR). Each takes a chart written in code, or a chart from a JSON config (it wraps the `<am5-chart>` element, see "Serializing to JSON").

| Framework | Package | A chart in code | A chart from a JSON config |
|---|---|---|---|
| React 18+ (Next.js, React Router, Astro) | `@amcharts/amcharts5-react` | `const { ref, root, result } = useAmChart((root) => { … }, [data])`, `<div ref={ref} style={{ height: "500px" }} />` | `<AmChart config={config} themes={[am5themes_Animated, am5themes_ResponsiveMax]} onReady={({ root, chart }) => …} />` |
| Vue 3.5.2+ (Nuxt) | `@amcharts/amcharts5-vue` | `const { root, result } = useAmChart(useTemplateRef("chart"), (root) => { … }, { watch: () => props.data })`, `<div ref="chart" style="height: 500px">`; `root`/`result` are shallow refs | `<AmChart :config="config" :themes="themes" @ready="onReady" />` |
| Angular 19+ | `@amcharts/amcharts5-angular` | `AmChartDirective`: `<div [amChart]="buildChart" [amChartDeps]="[data()]" (amChartReady)="…" style="height: 500px">`, with `buildChart = (root: Root) => { … }` as an arrow-function property so `this` is the component | `AmChartComponent`: `<am5-chart [config]="config" [themes]="themes" (amChartReady)="…">` |
| Svelte 4+ (SvelteKit) | `@amcharts/amcharts5-svelte` | Svelte 5.29+: `<div {@attach attachAmChart(buildChart)} style="height: 500px">`; Svelte 4 and older 5: `<div use:amChart={{ setup: buildChart, deps: [data] }}>` | `<am5-chart {@attach attachAmChartConfig({ config, themes })}>`, or `use:amChartConfig={{ config, themes }}`; events `onready`/`onerror` (`on:ready` in Svelte 4) |
| React Native 0.72+ | `@amcharts/amcharts5-react-native` (+ `react-native-webview` 13+) | `<AmChart code={code} data={data} />`, where `code` is a **string** (below) | `<AmChart config={config} themes={["Animated", "ResponsiveMax"]} />` |

Install `@amcharts/amcharts5` together with the framework's package, e.g. `@amcharts/amcharts5-react`. The current web packages need amCharts 5.21.4 or later (peer dependency `^5.21.4`). React Native needs only `@amcharts/amcharts5-react-native` and `react-native-webview`: the WebView loads the library from the amCharts CDN.

```jsx
// React. Vue, Angular and Svelte work the same way: your function gets the root.
import { useEffect } from "react";
import { useAmChart } from "@amcharts/amcharts5-react";
import * as am5xy from "@amcharts/amcharts5/xy";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
import am5themes_ResponsiveMax from "@amcharts/amcharts5/themes/ResponsiveMax";

function Chart({ data }) {
  const { ref, result } = useAmChart((root) => {
    root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);
    const chart = root.container.children.push(am5xy.XYChart.new(root, {}));
    const xAxis = chart.xAxes.push(am5xy.CategoryAxis.new(root, {
      categoryField: "category",
      renderer: am5xy.AxisRendererX.new(root, {})
    }));
    const yAxis = chart.yAxes.push(am5xy.ValueAxis.new(root, {
      renderer: am5xy.AxisRendererY.new(root, {})
    }));
    const series = chart.series.push(am5xy.ColumnSeries.new(root, {
      xAxis: xAxis, yAxis: yAxis, categoryXField: "category", valueYField: "value"
    }));
    return { xAxis, series };
  }, []); // made once: [data] here would make the chart again on every change

  // New data goes to the chart that is there, with an animation
  useEffect(() => {
    if (result) {
      result.xAxis.data.setAll(data);
      result.series.data.setAll(data);
    }
  }, [result, data]);

  return <div ref={ref} style={{ width: "100%", height: "500px" }}></div>;
}
```

- **Your function gets the root:** never call `am5.Root.new()` or `root.dispose()` in it, because the package does both. Put the code that follows `Root.new()` in any example inside it.
- **The chart's element needs a height.** The JSON-config components are 400 px high without one.
- **Deps make the chart again from scratch:** `[data]` in React, `watch` in Vue (what Vue's `watch()` takes; add `deep: true` to also catch `push()`), `[amChartDeps]` in Angular, `deps` in Svelte actions. A Svelte attachment runs again when state read in its own expression changes. To change data without rebuilding, return what you need and use it from `result` (React, Vue), from `(amChartReady)`'s `{ root, result }` (Angular), or from a `$state.raw` variable set in the function (Svelte).
- **SVG:** `useAmChart(setup, deps, { renderer: "svg" })` in React, `{ renderer: "svg" }` in Vue's options, `[amChartRenderer]="'svg'"` in Angular, and `attachAmChart(setup, { renderer: "svg" })` or `renderer: "svg"` in Svelte's action parameter. The JSON-config components take a `renderer` prop or input.
- **Themes for the JSON-config components are classes** (`[am5themes_Animated, am5themes_ResponsiveMax]`, not `.new(root)`): themes are not part of a config. A theme with its settings is a pair: `[am5themes_Animated, am5themes_ResponsiveMax, [am5themes_Adaptive, { baseColor: "#4150a0" }]]`. React Native takes theme **names** (`["Animated", "ResponsiveMax", "Dark"]`), and the pair as `["Adaptive", { baseColor: "#4150a0" }]`.
- **Maps and locales in a JSON config**: the config names its geodata (`"geoJSON": "am5geodata_worldLow"`) and locale (`"am5locales_de_DE"`), and with ES module imports those aren't globals, so pass them in the `resources` prop: `<AmChart config={config} resources={{ am5geodata_worldLow }} />` in React (import it from `@amcharts/amcharts5-geodata/worldLow`), `:resources` in Vue, `[resources]` in Angular, and `resources` in Svelte's `attachAmChartConfig({ config, resources })` / `use:amChartConfig`. Without it the map is blank, with a console warning that names the pack. React Native loads the geodata a config names by itself, but not a locale: give it `modules={["locales/de_DE"]}`.
- **Give amCharts plain data.** In Vue, keep amCharts objects out of reactive state: use `shallowRef()` for data, or pass `toRaw(data)`. In Svelte 5, use `$state.snapshot(data)`.
- **License:** call `am5.addLicense("YOUR-AMCHARTS-LICENSE-KEY")` once when the app starts, before any chart is made, never in the setup function, because the logo is decided when the root is made. Put it in `main.ts`/`main.jsx`, before `createApp()` or `bootstrapApplication()`. In Next.js, put it at the top of a client component's file; in Nuxt, in a client plugin (`plugins/amcharts.client.ts`); in SvelteKit, in `src/hooks.client.ts`. React Native takes `licenses={["YOUR-AMCHARTS-LICENSE-KEY"]}` on each chart. See "License and the amCharts logo".
- **Errors** thrown in your function go to React's nearest error boundary, Vue's `app.config.errorHandler`, Angular's `ErrorHandler` and `(amChartError)`, and Svelte's `onError` option (an uncaught error without it).
- **Server rendering:**
  - Next.js, Nuxt (no `<ClientOnly>` needed), SvelteKit and Angular SSR need no configuration.
  - In the Next.js app router, `useAmChart` and theme classes need a client component (`"use client"`). A server component can render `<AmChart config={config}>` itself, because a config is plain data.
  - React Router (Remix 2) and Astro: add `ssr: { noExternal: ["@amcharts/amcharts5"] }` to the Vite config (under `vite` in `astro.config.mjs`).
  - Remix 3 has no package. Either load the script files and render `<am5-chart config={JSON.stringify(config)} data-rmx-preserve-dom>`, or use a `clientEntry()` component that imports amCharts inside Remix's `ref` mixin and disposes the root when its signal aborts.
- **Unit tests in jsdom** (no canvas): mock the package, e.g. `vi.mock("@amcharts/amcharts5-react", () => ({ useAmChart: () => ({ ref: () => {}, root: undefined, result: undefined }), AmChart: () => null }))`.

**React Native** draws the real library in a WebView, so every chart type works, and it runs in Expo Go.
- The chart code is a **string** written like a script-tag example: `am5xy.XYChart`, globals rather than imports. `root`, `data` (the `data` prop) and `post(name, value)` are in scope, and the files the code uses are loaded for it.
- If the code returns a function, new `data` (compared by content) goes to that function instead of making the chart again.
- `post()` calls the `onMessage(name, value)` prop.
- Everything between the app and the chart is JSON, so give dates as timestamps.
- Never build `code` from text a user typed or a server sent: pass such values in `data`.
- Other props: `renderer`, `modules` (e.g. `["locales/de_DE"]`), `libraryUrl`, `geodataUrl`, `onReady`, `onError`, `webViewProps`. A ref gives `run(code)`.

```jsx
import { AmChart } from "@amcharts/amcharts5-react-native";

const code = `
const chart = root.container.children.push(am5xy.XYChart.new(root, {}));
// ... axes and series, as in a script-tag example
series.columns.template.events.on("click", (ev) => post("country", ev.target.dataItem.dataContext.country));
xAxis.data.setAll(data);
series.data.setAll(data);
return (newData) => { xAxis.data.setAll(newData); series.data.setAll(newData); };
`;

<AmChart code={code} data={visits} themes={["Animated", "ResponsiveMax"]} style={{ height: 300 }}
  onMessage={(name, value) => console.log(name, value)} />
```

**Without a package** (an older framework version, or when the user asks for it), create the root once the element exists, and dispose it when the component goes away:

```js
// React
useLayoutEffect(() => {
  const root = am5.Root.new(chartdiv.current); // const chartdiv = useRef(null)
  // ... build chart
  return () => { root.dispose(); };
}, []);

// Angular: in ngAfterViewInit, only if isPlatformBrowser(), inside zone.runOutsideAngular()
ngOnDestroy() { this.root?.dispose(); }

// Vue: root = am5.Root.new(chartdiv.value) in onMounted, kept in a plain variable, not reactive state
onBeforeUnmount(() => { root?.dispose(); });

// Svelte: onMount runs in the browser only
onMount(() => { const root = am5.Root.new(chartdiv); /* ... */ return () => root.dispose(); });
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
9. **Using CSS to style chart elements** — amCharts draws on canvas by default; even with `am5.SVGRenderer` its shapes have no per-element classes, are rewritten on redraw, and exports ignore page CSS. Style through settings, templates and themes.
10. **Wrong package import** — check the package map table above.
11. **Calling `.each()` on `dataItems`** — `series.dataItems` is a plain array, not an amCharts `List`. Use `am5.array.each(series.dataItems, fn)` or standard `forEach`/`for` loops. Only `series.data` (the `ListData` object) has `.each()`.
12. **CDN script load order** — `index.js` must load first, then `xy.js`, then any package that depends on it (`radar.js`, `timeline.js`, `gantt.js`). Wrong order causes runtime errors. Correct order: `index.js`, `xy.js`, then `radar.js` / `timeline.js` / `gantt.js`, then `themes/*.js`. Gantt also needs `plugins/colorPicker.js` before `gantt.js`, or `am5gantt` is undefined. From an npm CDN the order and file names are the same under `…/@amcharts/amcharts5@<version>/dist/`; load every file from one pinned version.
13. **No `minorGrid` / `minorTicks` / `minorLabels` objects** — These do not exist on axes. Minor grid is enabled via boolean flags on the **renderer**: `minorGridEnabled: true` and optionally `minorLabelsEnabled: true`. Styling is done through theme rules targeting the `"minor"` tag, not through separate object properties.
14. **Flow chart animated bullets go on `series.bullets`, NOT `series.links.template.bullets`** — To animate labels/circles flowing along Sankey or Chord links, use `series.bullets.push(function(...) { ... })`. Animate `bullet.locationX` (Sankey) or `bullet.locationY` (Chord) from 0→1 with `loops: Infinity`. Use an adapter on opacity for fade effect. See `references/flow.md` → "Animated bullets along links".
15. **`MapPointSeries` needs `latitudeField`/`longitudeField` when your data fields are named anything other than `latitude`/`longitude`** — The series defaults to `latitudeField: "latitude"`, `longitudeField: "longitude"`, so data using exactly those names works with no declaration. Any other naming (`lat`/`lng`, `y`/`x`, …) must be declared or the points silently won't appear: `am5map.MapPointSeries.new(root, { latitudeField: "lat", longitudeField: "lng" })`. Neither is needed with `pushDataItem({ latitude: ..., longitude: ... })`, which passes coordinates directly.
16. **`data.setAll()` does NOT animate — use `data.setIndex()` for animated updates** — When the user asks to update/refresh data with animation, do NOT use `series.data.setAll(newData)` — it replaces everything instantly with no transition. Instead, update each item with `series.data.setIndex(i, newItem)` which triggers smooth value animation. For full replacement with animation, loop: `newData.forEach(function(item, i) { series.data.setIndex(i, item); })`.
17. **`color.lighten()` / `color.darken()` are NOT instance methods** — `am5.color(0xff0000).lighten(0.3)` does NOT work. Use static methods: `am5.Color.lighten(color, 0.3)` to lighten, `am5.Color.lighten(color, -0.3)` to darken. There is NO `darken()` method — use negative lighten. Also available: `am5.Color.brighten()`, `am5.Color.saturate()`.
18. **Venn diagram has no `VennDiagram` class** — `am5venn.Venn` is pushed directly into a `Container`, NOT into a chart's `series`. See `references/venn.md`.
19. **Timeline `AxisRendererCurveX` requires `yRenderer`** — Always create the Y renderer first, then pass it: `am5timeline.AxisRendererCurveX.new(root, { yRenderer: yRenderer })`. Without this, crashes with `Cannot read properties of undefined (reading 'axis')`.
20. **Gantt data uses TWO separate calls** — Do NOT use `chart.data.setAll()`. Set categories on `chart.yAxis.data.setAll([{id, name, parentId, color}])` and tasks on `chart.series.data.setAll([{id, start, duration, progress, linkTo}])`. Use flat `parentId` for hierarchy, NOT nested `children` arrays. CDN order: `index.js` → `xy.js` → `plugins/colorPicker.js` → `gantt.js` → `themes/Animated.js` (gantt.js webpack-depends on colorPicker chunk). See `references/gantt.md`.
21. **No continent-level geodata at top-level CDN** — `geodata/europeLow.js` does NOT exist. Use `geodata/region/world/europeLow.js` (global: `am5geodata_region_world_europeLow`) or filter `worldLow` with `include: [...]`. On jsDelivr and unpkg the same path is under the geodata package's `dist/`: `https://cdn.jsdelivr.net/npm/@amcharts/amcharts5-geodata@<version>/dist/region/world/europeLow.js`.
22. **Do not add chart titles as HTML** — HTML titles are outside the chart and won't appear in exports. Use `am5.Label` pushed into the container BEFORE the chart, with `verticalLayout` on the container. See "Chart title" section above.
23. **`XYChartScrollbar` has no `xAxis`/`yAxis` settings** — its preview is a chart of its own, `scrollbar.chart`: push the preview's axes into `scrollbar.chart.xAxes`/`yAxes`, its series into `scrollbar.chart.series`, and give that series the data. Axes passed to `XYChartScrollbar.new()` are never added to the preview (its series then throws), and `get("xAxis")` only hands back whatever you passed. See `references/xy.md` → Scrollbar.
24. **`am5.color()` only accepts hex integers or CSS strings** — `am5.color(0xff0000)`, `am5.color("#ff0000")`, `am5.color("rgb(255,0,0)")` are valid. `am5.color({ r: 255, g: 0, b: 0 })` is NOT — it throws.
25. **`MapChart` has no default `colors`** — it inherits the `colors` setting from `SerialChart`, but no theme sets it on a map, so `chart.get("colors")` returns `undefined`, and map series don't use it anyway. XY and Radar charts (and percent series, via `series.get("colors")`) get a ColorSet from the theme. For maps, make your own: `am5.ColorSet.new(root, {})`.
26. **`VoronoiTreemap` has NO `.rectangles` property** — Unlike `Treemap` (which has `series.rectangles.template`), `VoronoiTreemap` renders organic polygon cells. Style via `series.nodes.template` and its children, not `.rectangles`.
27. **Labels with data placeholders need `populateText: true`** — `Label` defaults to `populateText: false`; labels the library makes for you (axis, legend, tooltip, pie/funnel slice labels, hierarchy and flow node labels…) get `true` from their own theme rules, but a `Label` you create does not. Any Label you create whose `text` has `{placeholders}` (a bullet sprite, a child of a bullet's container, a map point label) needs `populateText: true`, or it shows the braces literally. Example: `am5.Label.new(root, { text: "{name}", populateText: true, ... })`. A `tooltipText` is filled in regardless.
28. **Easing: `am5.ease.in()` does NOT exist** — amCharts 5 provides base easing functions (`am5.ease.cubic`, `am5.ease.bounce`, `am5.ease.elastic`, `am5.ease.linear`, `am5.ease.quad`, `am5.ease.sine`, `am5.ease.circle`, `am5.ease.exp`; `am5.ease.pow(t, e)` takes an exponent, so it is only usable wrapped: `(t) => am5.ease.pow(t, 3)`) and three modifiers: `am5.ease.out()`, `am5.ease.inOut()`, `am5.ease.yoyo()`. Usage: `am5.ease.cubic` (ease-in by default), `am5.ease.out(am5.ease.cubic)` (ease-out), `am5.ease.inOut(am5.ease.cubic)` (ease in+out). There is NO `am5.ease.in()` — using it throws a runtime error. The base functions already ease-in by default.
29. **`forceHidden` vs `visible`** — `forceHidden: true` ALWAYS hides an element, immune to states, themes/library and even `show()`. `visible` is weaker: amCharts re-manages it on many elements (notably cursor lines, but also labels, ticks, tooltips, grid) in response to interaction or data changes, so a `visible: false` you set may be flipped back to `true`. Rules of thumb: for a **persistent hide**, use `forceHidden: true` (`cursor.lineX.set("forceHidden", true)`, `yRenderer.labels.template.set("forceHidden", true)`, `xRenderer.grid.template.set("forceHidden", true)`). To **reveal** an element that defaults hidden — notably **axis ticks** (`ticks.template` defaults to `visible:false`) — you must raise `visible: true`; flipping `forceHidden` alone won't show it. For cursor lines specifically, always access `cursor.lineX`/`cursor.lineY` after creation — do NOT pass them as constructor options.
30. **`snapTooltip: true` on series for cursor tooltip snapping** — When using a cursor and you want tooltips to snap to data points, set `snapTooltip: true` on the series in addition to (or instead of) `snapToSeries` on the cursor. This is especially useful for timeline/curve charts.
31. **Globe rotation uses negative coordinates** — To center the globe (`geoOrthographic`) on a geographic point, set `rotationX` to **-longitude** and `rotationY` to **-latitude**. E.g., to center on Paris (48.86°N, 2.35°E): `chart.animate({ key: "rotationX", to: -2.35 }); chart.animate({ key: "rotationY", to: -48.86 });`. Using positive values rotates the globe the wrong way.
32. **`positionOnLine` with multi-segment lines limits per-segment control** — `MapPointSeries` data items can animate along a line via `positionOnLine` (0→1). If the line has 3+ points (multi-segment), position 0.5 is the midpoint of the *entire* path, making per-segment effects (scaling at each segment midpoint, pausing between segments, etc.) difficult. For advanced per-segment animations, use **single-segment lines** (2 points each) and animate the bullet across them sequentially. E.g., instead of one line [A,B,C,D], create [A,B], [B,C], [C,D].
33. **Do NOT exclude Antarctica (`exclude: ["AQ"]`) by default** — Many amCharts demos exclude Antarctica because they use Mercator projection where it appears disproportionately large. This is a demo-specific choice, not a best practice. Unless the user explicitly asks to exclude Antarctica, or references a demo that does so, keep Antarctica in the map. With non-Mercator projections (`geoNaturalEarth1`, `geoEqualEarth`, `geoOrthographic`, `geoEquirectangular`), Antarctica renders at a reasonable size.
34. **Custom GeoJSON for `MapPolygonSeries` needs CLOCKWISE exterior rings** — amCharts projects on a sphere (d3-geo), where ring winding decides which side is "inside". The RFC 7946 default is counter-clockwise, which d3-geo reads as *the whole globe minus the polygon*, so every polygon floods the entire map and the real shape shows only as a sliver. Programmatically generated GeoJSON (grids, cells, buffers) is usually CCW. amCharts does **not** auto-rewind — reverse only the polygons that come out inside out: if `am5map.getGeoArea({ type: "Polygon", coordinates: poly }) > 2 * Math.PI`, then `poly.forEach(ring => ring.reverse())`. That is safe on clockwise data and also catches small rings flipped by simplifying or rounding. See `references/map.md`.
35. **`axis.dispose()` does NOT remove the axis from `chart.xAxes`/`chart.yAxes`** — unlike `series.dispose()`, which self-removes from `chart.series`. A disposed axis stays in the list, keeps showing up in anything built from `chart.yAxes`, and makes `ChartSerializer` throw `Template is disposed`. Remove axes with `chart.yAxes.removeValue(axis)` (or `removeIndex(i)`) — the list auto-disposes the removed axis, no separate `dispose()` needed.
36. **Declare bullet callbacks as `function(root, series, dataItem)`** — amCharts passes all three; closing over an outer `root` works at runtime but breaks JSON round-trips (`ChartSerializer` captures the function source, and the outer `root` dangles on re-bind). Every `series.bullets.push(...)` in these references uses the parameter form — copy it.
37. **Several colors in one `am5.Graphics` `draw`** — `endFill()` fills everything since the last `beginPath()`, so start each color with `display.beginPath()`, or the last color paints over all shapes. Don't give such a Graphics its own `fill` either: after `draw` runs it fills the current path once more, so the last shape takes the Graphics' own `fill` (the default theme sets none). E.g. `draw: function (display) { display.beginPath(); display.beginFill(am5.color(0xff0000)); display.drawRect(0, 0, 10, 10); display.endFill(); display.beginPath(); display.beginFill(am5.color(0x0000ff)); display.drawRect(10, 0, 10, 10); display.endFill(); }`. An own `stroke` re-strokes the last path the same way.
38. **Changing a value on a data item moves nothing** — series draw from the working values: set `valueYWorking` (XY) or `valueWorking` (pie) along with the value, or use `series.data.setIndex()`. See "Dynamic data".
39. **A setting changed after a hover snaps back on unhover** — the `"default"` state keeps the value from before the first hover. Change it there too: `sprite.states.lookup("default")?.set("fill", c)`. See "States".
40. **`templateField` goes on the template, not on the series** — per-item settings from data rows are read by the elements a template makes: `series.columns.template.set("templateField", "columnSettings")`, likewise `slices.template`, `mapPolygons.template` or a bullet sprite. Set on the series, `templateField` does nothing and every column stays the same. For a pattern per column, see `references/xy.md` → "Different pattern per column".
41. **A JSON-config map that draws nothing with ES module imports** — the config names its geodata (`"geoJSON": "am5geodata_worldLow"`), and with ES module imports there is no global of that name. Pass the pack in `resources`: `JsonParser.parse(config, { parent, resources: { am5geodata_worldLow } })`, `element.resources`, or the framework packages' `resources` prop. The same goes for a locale (`am5locales_de_DE`). A console warning names the missing pack. A config serialized from such a chart copies the whole pack unless `ChartSerializer` gets the same `resources`.
42. **An `am5.Graphics` redraws only when `draw` changes** — `graphics.set("draw", fn)` with the function it already has does nothing. Pass a new closure that captures the new points: `graphics.set("draw", (display) => paint(display, points))`.
43. **A legend value without a cursor needs `calculateAggregates`** — `legendValueText` is used only under a cursor; otherwise the value label shows `legendRangeValueText`, which has no data item: `{valueY}` is empty there, and `{valueYClose}` (the latest value), `{valueYSum}`, `{valueYHigh}` and the other range values are empty unless the series has `calculateAggregates: true` (no warning). See `references/xy.md` → Legend.
44. **`#,###` does not round** — a number format without a `.` keeps every decimal (`6,708.044`); end it with `.` for whole numbers (`'#,###.'`) or give the decimals (`'#,###.00'`). Every number placeholder also gets a thousands separator, so `{year}` shows `2,024`: use `{year.formatNumber('#')}`. Sections are split by `|`, not `;`. See "Number formats".

## Easing functions

amCharts 5 easing functions live under `am5.ease`:

```js
// Base functions (ease-in by default) — these 8 are also the names `animations` / byName() accept:
am5.ease.linear    am5.ease.quad     am5.ease.cubic
am5.ease.sine      am5.ease.circle   am5.ease.exp
am5.ease.bounce    am5.ease.elastic

// pow(t, e) needs an exponent — passing am5.ease.pow directly gives NaN. Wrap it:
(t) => am5.ease.pow(t, 3)

// Modifiers — wrap a base function:
am5.ease.out(am5.ease.cubic)      // ease-out
am5.ease.inOut(am5.ease.cubic)    // ease in + out
am5.ease.yoyo(am5.ease.cubic)     // animate to end, then back to start

// Usage in .animate():
sprite.animate({
  key: "y", to: 100, duration: 400,
  easing: am5.ease.out(am5.ease.cubic)
});

// Name <-> function
am5.ease.byName("cubic", "out");                    // same as am5.ease.out(am5.ease.cubic); unknown name -> linear
am5.ease.easingInfo(am5.ease.yoyo(am5.ease.sine));  // { easing: "sine", yoyo: true }
```

**There is NO `am5.ease.in()`** — the base functions already ease-in by default. `easingInfo()` returns `undefined` for custom functions, `pow`, and combinations it cannot name (e.g. `out(yoyo(x))`) — such easings cannot go into an `animations` entry, and a code-started endless loop using one is not serialized.

## Validate generated code (if you can execute commands)

If you have the ability to run shell commands, validate your generated chart code before delivering it to the user:

1. Save the complete HTML+JS to a temp `.html` file
2. Open it in a headless browser (Puppeteer, Playwright, or similar) and collect console errors for a few seconds
3. If JavaScript errors are found, fix them and re-validate

Skip this step entirely if you cannot execute code (e.g., chat-only context with no tool access).

## Recent API changes

Latest release covered here: **5.23.0** (2026-11-19), with geodata 5.2.0, and the framework packages react 5.0.1, vue 5.0.2, angular 5.0.1, svelte 5.0.3 and react-native 5.0.4. These are the renames, new settings and behaviors that code written from older knowledge most often gets wrong — prefer these names/settings. For exact types and defaults, use the typings (`*.d.ts`) or the amCharts MCP server's `get_api_reference`, which is generated from them; for anything newer, verify against the live docs (see next section).

**Renamed settings (old name still works but is deprecated — use the new one):**

| Class(es) | Old | New |
|-----------|-----|-----|
| `MovingAverage`, `MovingAverageDeviation`, `MovingAverageEnvelope`, `BollingerBands` | `type` | `maType` |
| `VoronoiTreemap` | `type` | `shapeType` |

```js
// Moving-average indicators: use maType, not type
am5stock.MovingAverage.new(root, { maType: "exponential", period: 20 });
am5hierarchy.VoronoiTreemap.new(root, { shapeType: "rectangle" }); // not type
```

**WordCloud labels:** the layout is computed synchronously in one pass. There is no per-data-item `ghostLabel`, and labels live in an internal container, so reach them with `series.labels` / `dataItem.get("label")`, not by walking `series.children`.

**Framework packages:** `@amcharts/amcharts5-react`, `@amcharts/amcharts5-vue`, `@amcharts/amcharts5-angular`, `@amcharts/amcharts5-svelte` and `@amcharts/amcharts5-react-native`. They make and dispose the chart with the component and work with server rendering. Prefer them over hand-written `Root.new()`/`dispose()` code in those frameworks. See "Frameworks".

**New in 5.23.0 (2026-11-19):**
- **Touch:** `root.touchGestures` is `"chart"` (default), `"tap"` or `"auto"`. With `"auto"`, which ResponsiveMax sets, a vertical swipe over the chart scrolls the page and a sideways or two-finger swipe works the chart, so phones need no tap first. With ResponsiveMax, don't set `root.tapToActivate`. See "Responsive rules".
- **Value axis decimals:** `preferredDecimals` lists the decimal counts labels may show. With `[0, 2]`, labels read `48.50` and `49.00` instead of `48.5` and `49.0`, and whole prices stay whole. The axis tooltip shows the step's decimals plus `extraTooltipPrecision`, rounded up to the list. Not used with the axis's own `numberFormat`, nor on logarithmic axes. See `references/stock.md`.
- **Stock toolbar:** `autoFit` folds the controls that don't fit in one row. ResponsiveMax turns it on.
- **ResponsiveMax** on charts 400 px wide or less: horizontal chart scrollbars hide too, and pinching zooms instead; stock charts get less padding.
- **Number formats** without decimals drop floating-point noise: `#,###` shows 6708.044000000005 as `6,708.044`.
- react-native 5.0.4 loads 5.23.0.

**Settings and methods worth knowing** (newer than most training data):

*Declared animations, links, map points on lines*
- `animations` setting on **every** element: an array of `IDeclaredAnimation` entries `{ key, from?, to, duration, delay?, loops?, yoyo?, easing?, ease?, target? }` — animations described as data (`loops: 0` = forever, `easing` by **name**, `target: "dataItem"` for data-item values). Round-trips through JSON. `IDeclaredAnimation` is not exported from the package index. See "Animations" above for the snippet, defaults and the `from` caveat.
- `am5.ease.byName(name, mode?)` / `am5.ease.easingInfo(fn)` — easing function ↔ name; only the 8 named base easings (and `out`/`inOut`/`yoyo` of them) have names. See "Easing functions" above.
- `ChartSerializer`: `runningAnimations` (default `true`) — saves endless code-started loops as `animations` entries. See "Serializing to JSON" above.
- `Series` links: `urlField` (no default — links are off until set) names the data field holding a URL; the data item's `url` field gets it. Clicking the item's bullet (any series), column (`ColumnSeries`, candlestick, OHLC, Gantt…), slice (pie, funnel, pyramid, pictorial, Venn), `MapPolygon` or `MapLine` opens it in `linkTarget` (default `"_self"`; `"_blank"` opens with `noopener`; a chart inside an iframe usually wants `"_blank"` or `"_top"`). `cursorOverStyle: "pointer"` is set automatically unless you set one. `javascript:`/`data:`/`vbscript:` URLs are never opened. Override `series.openUrl(dataItem)` to intercept clicks. `urlField` can be set after the data, and a URL added later (`data.setIndex()`) links too. **Not linked:** hierarchy nodes, flow nodes/links, word-cloud labels, `MapSankeySeries` nodes.
- `MapPointSeries`: `lineIdField` (default `"lineId"`), `positionOnLineField` (`"positionOnLine"`), `autoRotateField` (`"autoRotate"`), `autoRotateAngleField` (`"autoRotateAngle"`) — a point on a line can be a plain data row `{ lineId: "jfk-lhr", positionOnLine: 0.5, autoRotate: true }` instead of `pushDataItem({ lineDataItem, positionOnLine })`, so it is saved by `ChartSerializer`. Data values of `autoRotate`/`autoRotateAngle` win over the bullet's. See `references/map.md`.
- `MapPointSeries`: an auto-rotating point turns round to face its travel direction on the way back — **only** while a bullet-sprite `animations` entry (`target: "dataItem"`, `key: "positionOnLine"`, e.g. with `yoyo: true`) moves it toward the line start. A point moved by `dataItem.animate()` in code is not flipped; that code must turn it.

*Themes & styling*
- Palette themes: `Midnight`, `Ember`, `Nord`, `Pastel`, `Colorblind`, `Patterns`, `Petroleum`, `Savanna`, plus `*Dark` variants of most palettes. Two parameterized factory themes, `Monochrome` and `Adaptive` — see "Parameterized themes" above.
- Fill patterns `am5.StarPattern` (`radius`, `innerRadius`, `spikes`, `gap`, `checkered`) and `am5.TrianglePattern` (`maxWidth`, `maxHeight`, `gap`, `checkered`).
- `rotateShapes` on `RectanglePattern`/`StarPattern`/`TrianglePattern`: `rotation` then spins each shape around its own center instead of the whole grid — tiles seamlessly and is much faster. Prefer it over a whole-pattern `rotation` on large tiles.
- `Series`: `fillGradient` / `strokeGradient` settings, handed to bullets (the series' own fills, lines and columns don't use them: set gradients on `fills.template` / `columns.template` / `strokes.template`). A bullet `Graphics` with no paint of its own (`fill`, `fillGradient`, `fillPattern`, `stroke`, `strokeGradient`) inherits all of them from its series — and on pie, funnel, flow and hierarchy charts, from its own slice/node.

*XY charts*
- `XYChart`: `strokeWidths` (array of pixel widths) and `strokeDasharrays` (array of dash arrays) — cycled across line series as they are added, exactly like `colors`. Lets series be told apart without relying on color, e.g. with the `Patterns` theme.
- `XYCursor`: `clickTolerance` (default `0`) — how many pixels outside the plot area a press may start and still begin a zoom/selection. The selection itself still starts at the plot edge.
- `XYSeries`: a value field (`valueYField`, `openValueYField`, …) can be changed after creation — re-set the series data afterwards for it to take effect. A series can also be reassigned to a different `xAxis`/`yAxis` after creation.
- Column series (`ColumnSeries`, `CandlestickSeries`, `OHLCSeries`, …): `colorByDataItem: true` (default `false`) gives each column its own color from the series' `colors` (`ColorSet`) setting — the series makes its own `ColorSet` if none is given. The color lands on the data item's `fill` field. Prefer this over an adapter on `columns.template` `fill`: it is declarative and survives JSON serialization.
- `Scrollbar`: `opposite: true` (default `false`) puts a chart's `scrollbarX` **below** the plot and `scrollbarY` to the **left**, instead of above / right. Only affects scrollbars set via `chart.set("scrollbarX"/"scrollbarY", …)`. This is a **different** setting from `opposite` on `AxisRendererX`/`AxisRendererY`.

*WordCloud*
- `svgPath` arranges words into a shape (experimental), with `maskByShape` to clip them to the outline and `shapeTolerance` to control spill (negative = padding inside). See `references/wordcloud.md`.
- `randomizeAngles: false` cycles `angles` in order for a reproducible layout; `allowNesting: false` packs words as non-overlapping bounding boxes (use it when labels have opaque backgrounds).
- `angles` accepts **any** angle (e.g. `[0, -30, -45]`).

*Core / events*
- `globalpointerdown` event on all elements — fires on a press anywhere on the chart surface, mirroring `globalpointermove`/`globalpointerup`.
- `Root`: `sanitizeHTML` (default `true`) — dynamically-set HTML (`html`/`labelHTML`, HTML tooltips, modal content) is sanitized; set `false` in code to opt out. A JSON config's `root` section can't turn it off unless parsed with `trusted: true`. Exports have `escapeFormulas` (default `true`) to guard CSV/XLSX against formula injection.
- `Root`: `ariaLabel` is applied to the `<div>` holding the chart's focusable elements.
- `Label`: `oversizedBehavior: "truncate"` ignores `maxHeight` (there is no way to truncate text vertically).
- A `Container`'s `Rectangle`/`RoundedRectangle` background defaults `crisp` to `true` unless set explicitly.
- A `Container`'s `background` with its own `templateField` reads the container's data item, so a per-bullet label background works: `am5.Label.new(root, { text: "{valueY}", populateText: true, background: am5.RoundedRectangle.new(root, { templateField: "labelBg" }) })` with `labelBg: { fill: am5.color(0xff0000) }` in the data row.
- `Root`: `fontFamily`, `fontSize` (number or string) and `fontWeight` (`"normal" | "bold" | "bolder" | "lighter"`, or `"100"` … `"900"`) settings — defaults for **all** text elements unless overridden on an element. Set them in `am5.Root.new("chartdiv", { fontFamily: "Inter" })` or `root.setAll({...})` instead of a custom theme rule on `Label`.
- `am5.SerialChartContainer` — a directly usable `SerialChart` whose `seriesContainer` sits inside a `ZoomableContainer` (exposed as `chart.zoomableContainer`), so series such as `ForceDirected` can be zoomed and panned. Exported from the **`am5` root module**, not a chart package. Optional `zoomTools` setting takes a `ZoomTools` instance whose `target` is pointed at the series container automatically. Bullets on its series are drawn, flow-chart bullets included. See `references/hierarchy.md`.

*JSON config / serialization*
- Gantt charts serialize and parse. Cross-references are written as references (`"@series.get('fill')"`) instead of copies; large values (a map's `geoJSON`, a `Root`'s locale) are written as the name of the pack they came from and rebuilt on parse.
- `JsonParser` resolves **forward references**: a setting/property naming a ref defined further down the config is resolved after the rest instead of throwing "Could not find ref" — a legend inside an `axisHeader` naming a series round-trips. Axis-range `axis`/`series` lookups are immediate, so keep those ordered.
- Map data rows written by `ChartSerializer` don't carry the geodata's own feature properties (`name`, `CNTRY`, `TYPE`); a row left with only its id is dropped.

*Bullets on non-XY charts*
- `ArcDiagram`: bullets on nodes are placed on the node circle, `locationX`/`locationY` being fractions of it. Flow charts (`Sankey`/`Chord`): node bullets are children of the node, drawn above its shape and below its label, positioned relative to the node. `FunnelSeries`/`PyramidSeries`: bullet `locationX`/`locationY` follow the slice's sloping edges instead of its bounding box.
- A bullet `Graphics` with no paint of its own takes the color of **its own slice or node** on pie, funnel, flow and hierarchy charts — so it is the same color as what it sits on and **invisible** unless offset, stroked, or given a contrasting `fill` deliberately. Setting `fill` on the bullet sprite turns the inheritance off.

*Maps*
- `MapChart`: `doubleClickZoom` (default **`true`**: a double click/tap zooms in, shift + double click zooms out; set `false` to opt out). On maps panned by rotating (`panX: "rotateX"` / `panY: "rotateY"`) the clicked point rotates to the center, otherwise it stays under the pointer. `boxZoom: "none" | "drag" | "shift" | "ctrl" | "alt"` (default `"none"`) is **not a boolean** — it names the key held while dragging a rectangle to zoom into; `"drag"` needs no key, so pair it with `panX`/`panY: "none"`. The rectangle is the readonly `chart.boxZoomSelection` element (a `Rectangle`). There are no keyboard-navigation settings on `MapChart`.
- `MapLineSeries`: `pointIds` data field (`{ pointIds: ["JFK", "LAX"] }`) names the `MapPointSeries` points a line connects, looked up in the series given by the `pointSeries` setting; `pointIdsField` (default `"pointIds"`) renames the field. `pointsToConnect` wins if both are set. See `references/map.md`.
- `MapChart`: `projectionName` — string alternative to `projection`, e.g. `"geoOrthographic"`, mainly for JSON config. Register extra ones with `am5map.registerProjection()`. Unused bundled projections tree-shake away.
- `MapSankeySeries` — Sankey overlaid on a map; it resolves `sourceId`/`targetId` once the `polygonSeries` geoJSON loads, so no `datavalidated` wrapper is needed. See `references/map.md`.

*Hierarchy & axes*
- `Tree`: `fitNodes` (exclude hidden nodes from layout), `nodeSeparation` (custom node-spacing fn), `clustered` (dendrogram layout — leaves at same depth).
- `ValueAxis`: `syncZeros` (align zero across synced axes — needs `syncWithAxis`).
- `Hierarchy`: `parentIdField` setting + `setFlatData(data)` method — feed flat `{id, parentId}` data instead of nested `children`.

*Stock*
- Indicator numeric settings enforce min/max limits. `IIndicatorEditableSetting` has `scale`, `maxValue` and `step`.
- Volume Profile spreads each bar's volume across its full high/low range. See `references/stock.md`.

*All entities*
- `onDebounced(key, cb, delay)` / `offDebounced(key, cb?)` and `onPrivateDebounced` / `offDebouncedPrivate` — fire once after rapid changes settle.
- `once(key, cb)` and `onceDebounced(key, cb, delay)` — like `on()`/`onDebounced()` for a **settings** key, but the callback fires only the first time that setting changes, then removes itself. Both return an `IDisposer`. (Distinct from `events.once("eventName", …)` for events.)
- `Label`: `fontFamily: "inherit"` uses the chart container's computed font. Text markup takes font names with spaces and font lists: `[fontFamily: 'Source Code Pro', monospace]`.

## Verify unfamiliar API before using it

If you are unsure whether a method, property, or setting exists on an amCharts 5 class, **verify it before using it**. Check the class reference (class name in lowercase, e.g. `LineSeries` → `lineseries`):

1. **Primary:** `https://docs.amcharts.com/v5/reference/{classname}/` — e.g. `https://docs.amcharts.com/v5/reference/lineseries/`
2. **Fallback (if primary is unavailable):** Check the TypeScript source on GitHub — `https://raw.githubusercontent.com/amcharts/amcharts5/master/src/.internal/charts/` and navigate to the relevant file. Settings interfaces are named `I{ClassName}Settings`.

Common source file paths:
- XY series: `charts/xy/series/{ClassName}.ts`
- XY axes: `charts/xy/axes/{ClassName}.ts`
- Pie: `charts/percent/pie/{ClassName}.ts`
- Radar: `charts/radar/{ClassName}.ts`
- Map: `charts/map/{ClassName}.ts`
- Hierarchy: `charts/hierarchy/{ClassName}.ts`
- Flow: `charts/flow/{ClassName}.ts`
- Core sprites: `core/render/{ClassName}.ts`
