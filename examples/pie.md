# Pie / Donut Chart Examples — amCharts 5

<!-- TODO: Replace placeholder examples with verified, working amCharts 5 code -->

Each example is shown in two formats: **ES modules / TypeScript** and **Vanilla JavaScript**. The chart logic is identical — only the loading method differs. Default to ES modules if the user does not specify.

---

## Basic Pie Chart

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";

// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
let root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
let series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Research", value: 1000 },
  { category: "Marketing", value: 1200 },
  { category: "Sales", value: 850 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
```

### Vanilla JavaScript

```html
<div id="chartdiv" style="width: 100%; height: 500px;"></div>

<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script>
// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
var root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
var chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
var series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Research", value: 1000 },
  { category: "Marketing", value: 1200 },
  { category: "Sales", value: 850 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
</script>
```

---

## Donut Chart

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";

// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
let root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart (donut)
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Donut
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    innerRadius: am5.percent(50),
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
let series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Product A", value: 501 },
  { category: "Product B", value: 301 },
  { category: "Product C", value: 201 },
  { category: "Product D", value: 165 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
```

### Vanilla JavaScript

```html
<div id="chartdiv" style="width: 100%; height: 500px;"></div>

<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script>
// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
var root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart (donut)
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Donut
var chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    innerRadius: am5.percent(50),
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
var series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Product A", value: 501 },
  { category: "Product B", value: 301 },
  { category: "Product C", value: 201 },
  { category: "Product D", value: 165 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
</script>
```

---

## Pie Chart with Legend

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";

// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
let root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
let series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category",
    legendValueText: "{value}",
    legendLabelText: "{category}"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Rent", value: 1500 },
  { category: "Food", value: 600 },
  { category: "Transport", value: 300 },
  { category: "Utilities", value: 200 },
  { category: "Entertainment", value: 150 }
]);

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

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
```

### Vanilla JavaScript

```html
<div id="chartdiv" style="width: 100%; height: 500px;"></div>

<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script>
// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
var root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
var chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
var series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category",
    legendValueText: "{value}",
    legendLabelText: "{category}"
  })
);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Rent", value: 1500 },
  { category: "Food", value: 600 },
  { category: "Transport", value: 300 },
  { category: "Utilities", value: 200 },
  { category: "Entertainment", value: 150 }
]);

// Add legend
// https://www.amcharts.com/docs/v5/charts/percent-charts/legend-percent-series/
var legend = chart.children.push(
  am5.Legend.new(root, {
    centerX: am5.percent(50),
    x: am5.percent(50),
    layout: root.horizontalLayout
  })
);
legend.data.setAll(series.dataItems);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
</script>
```

---

## Pie Chart with Custom Colors

<!-- TODO: Add verified custom color example -->

### ES modules / TypeScript

```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";

// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
let root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
let chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
let series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set colors
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Colors
series.get("colors").set("colors", [
  am5.color(0x095256),
  am5.color(0x087f8c),
  am5.color(0x5aaa95),
  am5.color(0x86a873),
  am5.color(0xbb9f06)
]);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Asia", value: 4641 },
  { category: "Africa", value: 1340 },
  { category: "Europe", value: 747 },
  { category: "Americas", value: 1028 },
  { category: "Oceania", value: 43 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
```

### Vanilla JavaScript

```html
<div id="chartdiv" style="width: 100%; height: 500px;"></div>

<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script>
// Create root element
// https://www.amcharts.com/docs/v5/getting-started/root-element/
var root = am5.Root.new("chartdiv");

// Set themes
// https://www.amcharts.com/docs/v5/concepts/themes/
root.setThemes([am5themes_Animated.new(root)]);

// Create chart
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/
var chart = root.container.children.push(
  am5percent.PieChart.new(root, {
    layout: root.verticalLayout
  })
);

// Add series
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series
var series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);

// Set colors
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Colors
series.get("colors").set("colors", [
  am5.color(0x095256),
  am5.color(0x087f8c),
  am5.color(0x5aaa95),
  am5.color(0x86a873),
  am5.color(0xbb9f06)
]);

// Set data
// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data
series.data.setAll([
  { category: "Asia", value: 4641 },
  { category: "Africa", value: 1340 },
  { category: "Europe", value: 747 },
  { category: "Americas", value: 1028 },
  { category: "Oceania", value: 43 }
]);

// Animate chart
// https://www.amcharts.com/docs/v5/concepts/animations/
series.appear(1000, 100);
chart.appear(1000, 100);
</script>
```
