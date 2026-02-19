# Pie / Donut Chart Reference — amCharts 5

<!-- TODO: Fill in with actual amCharts 5 API details -->

## Key Classes

| Class | Import | Purpose |
|---|---|---|
| `PieChart` | `am5percent` | The chart container for pie/donut charts |
| `PieSeries` | `am5percent` | The data series that renders slices |
| `Percent` | `am5percent` | Utility for percentage-based values |
| `Legend` | `am5` | Optional legend component |

## Prerequisites

Before creating a pie chart, you must create a `Root` element and apply themes. See [root.md](root.md) for full details on root element creation, theme application, and animations.

## Loading Required Modules
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Loading_required_modules -->

Pie charts require the `percent` module in addition to the core `am5` module loaded via `Root`.

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
```

### Vanilla JavaScript (script tags)

```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

When using script tags, all amCharts objects are available globally under the `am5`, `am5percent`, and `am5themes_Animated` namespaces — the same variable names used in ES module imports. The chart code itself is identical in both environments; only the loading method differs.

## Creating a Pie Chart
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/ -->

```javascript
// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);
```

## Creating a Series
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series -->

```javascript
// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
let series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);
```

## Making it a Donut
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Donut -->

Set `innerRadius` to create a donut chart:

```javascript
// Create chart (donut)
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Donut
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    innerRadius: am5.percent(50)
  })
);
```

## Key Series Properties

<!-- TODO: Add comprehensive property list -->

| Property | Type | Description |
|---|---|---|
| `valueField` | string | Data field for slice values (required field) |
| `categoryField` | string | Data field for slice labels (required field) |
| `alignLabels` | boolean | Align labels in columns |
| `legendValueText` | string | Text shown in legend values |
| `legendLabelText` | string | Text shown in legend labels |

## Key Slice Properties

<!-- TODO: Add slice template properties -->

| Property | Type | Description |
|---|---|---|
| `strokeWidth` | number | Border width of slices |
| `strokeOpacity` | number | Border opacity |
| `cornerRadius` | number | Rounded corners on slices |
| `tooltipText` | string | Tooltip format string |

## Adding a Legend
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/legend-percent-series/ -->

```javascript
// Add legend
// https://www.amcharts.com/docs/v5/charts/percent-charts/legend-percent-series/
let legend = chart.children.push(
  am5.Legend.new(root, {
    centerX: am5.percent(50),
    x: am5.percent(50),
    layout: root.horizontalLayout
  })
);
legend.data.setAll(series.dataItems);
```

## Labels and Ticks

<!-- TODO: Add label/tick configuration reference -->

```javascript
series.labels.template.setAll({
  text: "{category}: {valuePercentTotal.formatNumber('0.0')}%",
  fontSize: 12
});

series.ticks.template.setAll({
  strokeOpacity: 0.5
});
```

## Setting Colors
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Colors -->

Color section is optional. The chart will use color list from the loaded theme. If user wants to override colors applied to each new slice, they can override with a custom color list.

```javascript
// Set colors
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Colors
series.get("colors").set("colors", [
  am5.color(0x095256),
  am5.color(0x087f8c),
  am5.color(0x5aaa95),
  am5.color(0x86a873),
  am5.color(0xbb9f06)
]);
```

## Data Format
<!-- https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data -->

Data should be an array of objects with at minimum a value field and a category field:

```javascript
// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Category A", value: 40 },
  { category: "Category B", value: 30 },
  { category: "Category C", value: 20 },
  { category: "Category D", value: 10 }
]);
```
