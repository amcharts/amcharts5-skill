# Root Element Reference — amCharts 5
<!-- https://www.amcharts.com/docs/v5/getting-started/root-element/ -->

The `Root` object is the foundation of every amCharts 5 chart. It must be created before any chart, series, or other amCharts objects. All charts types share the same `Root` object as their base element regardless of chart type.

## Loading Core Modules
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Loading_required_modules -->

Every chart requires at minimum the core `am5` module. Chart-type-specific modules (e.g. `percent`, `xy`) are loaded separately.

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
```

### Vanilla JavaScript (script tags)

```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

When using script tags, all amCharts objects are available globally under the `am5` and `am5themes_Animated` namespaces — the same variable names used in ES module imports.

## Creating a Root Element
<!-- https://www.amcharts.com/docs/v5/getting-started/root-element/ -->

The root element wraps everything — charts, legends, labels — and is the repository for chart-wide configuration (locale, formatters, themes).

```javascript
// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
let root = am5.Root.new("chartdiv");
```

The string `"chartdiv"` must match the `id` of an HTML container element:

```html
<div id="chartdiv" style="width: 100%; height: 500px;"></div>
```

The container **must have explicit dimensions** (width and height) set via CSS or inline styles, otherwise the chart will not render.

## Applying Themes
<!-- https://www.amcharts.com/docs/v5/concepts/themes/ -->

Themes control default appearance and behavior. The `Animated` theme enables entrance and transition animations.

```javascript
// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([
  am5themes_Animated.new(root)
]);
```

Multiple themes can be applied simultaneously:

```javascript
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_Dark.new(root)
]);
```

### Available Built-in Themes

| Theme | Import (ES module) | Script (CDN) | Purpose |
|---|---|---|---|
| Animated | `@amcharts/amcharts5/themes/Animated` | `themes/Animated.js` | Enables animations |
| Dark | `@amcharts/amcharts5/themes/Dark` | `themes/Dark.js` | Dark color scheme |
| Frozen | `@amcharts/amcharts5/themes/Frozen` | `themes/Frozen.js` | Cool blue tones |
| Dataviz | `@amcharts/amcharts5/themes/Dataviz` | `themes/Dataviz.js` | Data visualization palette |
| Kelly | `@amcharts/amcharts5/themes/Kelly` | `themes/Kelly.js` | Kelly's 22 colors of max contrast |
| Material | `@amcharts/amcharts5/themes/Material` | `themes/Material.js` | Material Design colors |
| Micro | `@amcharts/amcharts5/themes/Micro` | `themes/Micro.js` | Sparkline-sized charts |
| Moonrise | `@amcharts/amcharts5/themes/Moonrise` | `themes/Moonrise.js` | Dark purple tones |
| Spirited | `@amcharts/amcharts5/themes/Spirited` | `themes/Spirited.js` | Vivid colors |

## Animations
<!-- https://www.amcharts.com/docs/v5/concepts/animations/ -->

Call `appear()` on charts and series to trigger entrance animations. This only works when the `Animated` theme is applied.

```javascript
// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
```

Parameters: `appear(duration, delay)` — both in milliseconds.

## Disposing a Root Element

When removing a chart (e.g. in a single-page app or component unmount), dispose the root to free resources:

```javascript
root.dispose();
```
