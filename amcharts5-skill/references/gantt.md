# Gantt Chart

Docs: https://www.amcharts.com/docs/v5/charts/gantt/

Gantt is a standalone chart class (not just a simulated XY chart). It auto-creates axes, series, and toolbar — you only provide data. It supports editable tasks, task linking, progress tracking, collapsible groups, weekends/holidays, and a color picker.

**Note:** Gantt is a separate licensed product. You can simulate a basic Gantt with XY charts (CategoryAxis + DateAxis + ColumnSeries with `openValueXField`/`valueXField`), but the dedicated `am5gantt.Gantt` class provides far more features out of the box.

## Imports

### ES modules / TypeScript
```ts
import * as am5 from "@amcharts/amcharts5";
import * as am5xy from "@amcharts/amcharts5/xy";
import * as am5gantt from "@amcharts/amcharts5/gantt";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
```

### CDN / script tags
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
<script src="https://cdn.amcharts.com/lib/5/gantt.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

**Optional:** Color picker plugin for task color editing:
```html
<script src="https://cdn.amcharts.com/lib/5/plugins/colorPicker.js"></script>
```

## Core setup

```js
const root = am5.Root.new("chartdiv");
root.setThemes([am5themes_Animated.new(root)]);

const chart = root.container.children.push(
  am5gantt.Gantt.new(root, {
    editable: true,         // allows UI editing (default: true)
    durationUnit: "day",    // time unit for duration values
  })
);
```

**Important:** Gantt auto-creates axes and series — do NOT create them manually. Just set data on the chart.

## Chart settings

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `editable` | boolean | `true` | Allow user to add, edit, remove, drag tasks |
| `durationUnit` | string | `"day"` | `"year"`, `"month"`, `"week"`, `"day"`, `"hour"`, `"minute"`, `"second"` |
| `weekends` | number[] | `[0, 6]` | Non-working days (0=Sun, 1=Mon, ... 6=Sat) |
| `excludeWeekends` | boolean | `true` | Whether to exclude weekends from task durations |
| `holidays` | Date[] | `[]` | Specific non-working dates |
| `snapThreshold` | number | `0.5` | Drag snap position within a period (0–1) |

## Data structure

Data is set on the chart (not on a series):

```js
chart.data.setAll([
  {
    name: "Design Phase",           // task name (required)
    start: new Date(2024, 0, 1).getTime(),  // start timestamp (required)
    duration: 10,                   // number of durationUnits (required)
    progress: 100,                  // 0–100 completion (optional)
    color: am5.color(0x297373),     // task bar color (optional)
    children: [                     // nested sub-tasks (optional)
      {
        name: "Wireframes",
        start: new Date(2024, 0, 1).getTime(),
        duration: 4,
        progress: 100
      },
      {
        name: "Mockups",
        start: new Date(2024, 0, 7).getTime(),
        duration: 6,
        progress: 100
      }
    ]
  },
  {
    name: "Development",
    start: new Date(2024, 0, 15).getTime(),
    duration: 20,
    progress: 60,
    children: [
      { name: "Frontend", start: new Date(2024, 0, 15).getTime(), duration: 15, progress: 80 },
      { name: "Backend", start: new Date(2024, 0, 20).getTime(), duration: 15, progress: 40 }
    ]
  }
]);
```

### Data fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Task display name |
| `start` | number | Yes | Start timestamp (milliseconds) |
| `duration` | number | Yes | Duration in `durationUnit`s |
| `progress` | number | No | 0–100 percentage completion |
| `color` | Color | No | Task bar color (`am5.color(0x...)`) |
| `children` | array | No | Nested sub-tasks (unlimited depth) |
| `id` | string | No | Unique task identifier |

### Zero-duration tasks (milestones)

Set `duration: 0` to create event-type milestone markers.

## Weekend & holiday configuration

```js
const chart = root.container.children.push(
  am5gantt.Gantt.new(root, {
    durationUnit: "day",
    weekends: [0, 5, 6],           // Sunday, Friday, Saturday
    excludeWeekends: true,
    holidays: [
      new Date(2025, 0, 1),        // New Year's Day
      new Date(2025, 6, 4),        // Independence Day
      new Date(2025, 10, 27)       // Thanksgiving
    ]
  })
);
```

When weekends/holidays are excluded, a 3-day task starting Thursday will end on the following Monday (skipping Sat/Sun).

## Read-only mode

```js
am5gantt.Gantt.new(root, {
  editable: false   // view-only, no UI editing
})
```

## Today marker / current date line

```js
// Add a vertical line at today's date using axis ranges
var today = new Date().getTime();
var rangeDataItem = xAxis.makeDataItem({ value: today });
xAxis.createAxisRange(rangeDataItem);
rangeDataItem.get("grid").setAll({
  stroke: am5.color(0xff0000),
  strokeWidth: 2,
  strokeOpacity: 1
});
rangeDataItem.get("label").setAll({
  text: "Today",
  fill: am5.color(0xff0000),
  location: 0
});
```

## Simulated Gantt with XY (limited alternative)

If you don't have the Gantt license, you can simulate a basic Gantt using XY charts. This approach lacks editing, task linking, and hierarchy but works for simple display:

```js
// CategoryAxis on Y (tasks), DateAxis on X (time)
const yAxis = chart.yAxes.push(am5xy.CategoryAxis.new(root, {
  categoryField: "task",
  renderer: am5xy.AxisRendererY.new(root, { inversed: true })
}));

const xAxis = chart.xAxes.push(am5xy.DateAxis.new(root, {
  baseInterval: { timeUnit: "day", count: 1 },
  renderer: am5xy.AxisRendererX.new(root, {})
}));

const series = chart.series.push(am5xy.ColumnSeries.new(root, {
  xAxis: xAxis,
  yAxis: yAxis,
  openValueXField: "start",    // task start timestamp
  valueXField: "end",          // task end timestamp
  categoryYField: "task",
  tooltip: am5.Tooltip.new(root, { labelText: "{task}" })
}));

// Color by task status
series.columns.template.adapters.add("fill", function(fill, target) {
  var dc = target.dataItem.dataContext;
  if (dc.progress >= 1) return am5.color(0x4caf50);    // done
  if (dc.progress > 0) return am5.color(0xff9800);      // in progress
  return am5.color(0x2196f3);                             // not started
});

yAxis.data.setAll(data);
series.data.setAll(data);
```

## Events on Gantt elements

```js
// For the dedicated am5gantt.Gantt class, editing events are handled
// internally (drag, resize, add, delete). Access updated data via:
chart.events.on("datavalidated", function() {
  console.log("Data changed — tasks:", chart.data.values);
});

// For simulated XY Gantt, use standard XY events:
series.columns.template.events.on("click", function(ev) {
  var task = ev.target.dataItem.dataContext;
  console.log("Clicked:", task.task, "Progress:", task.progress);
});

series.columns.template.events.on("pointerover", function(ev) {
  ev.target.set("fillOpacity", 1);
});

series.columns.template.events.on("pointerout", function(ev) {
  ev.target.set("fillOpacity", 0.8);
});
```

---

## Example 1: Full Gantt chart

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Gantt Chart</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/gantt.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <style>
    #chartdiv { width: 100%; height: 500px; }
  </style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

    var chart = root.container.children.push(
      am5gantt.Gantt.new(root, {
        editable: true,
        durationUnit: "day",
        weekends: [0, 6],
        excludeWeekends: true
      })
    );

    chart.data.setAll([
      {
        name: "Planning",
        start: new Date(2024, 0, 1).getTime(),
        duration: 5,
        progress: 100,
        color: am5.color(0x297373),
        children: [
          { name: "Requirements", start: new Date(2024, 0, 1).getTime(), duration: 3, progress: 100 },
          { name: "Architecture", start: new Date(2024, 0, 4).getTime(), duration: 2, progress: 100 }
        ]
      },
      {
        name: "Design",
        start: new Date(2024, 0, 8).getTime(),
        duration: 10,
        progress: 80,
        color: am5.color(0xe9724c),
        children: [
          { name: "Wireframes", start: new Date(2024, 0, 8).getTime(), duration: 4, progress: 100 },
          { name: "Visual Design", start: new Date(2024, 0, 14).getTime(), duration: 6, progress: 60 }
        ]
      },
      {
        name: "Development",
        start: new Date(2024, 0, 22).getTime(),
        duration: 25,
        progress: 40,
        color: am5.color(0xffc857),
        children: [
          { name: "Frontend", start: new Date(2024, 0, 22).getTime(), duration: 20, progress: 50 },
          { name: "Backend API", start: new Date(2024, 0, 22).getTime(), duration: 18, progress: 45 },
          { name: "Integration", start: new Date(2024, 1, 15).getTime(), duration: 7, progress: 10 }
        ]
      },
      {
        name: "Launch",
        start: new Date(2024, 2, 1).getTime(),
        duration: 0,     // milestone
        progress: 0
      }
    ]);

    chart.appear(1000, 100);
  </script>
</body>
</html>
```

## Example 2: Simulated Gantt with XY chart (no Gantt license needed)

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Simulated Gantt (XY)</title>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <style>
    #chartdiv { width: 100%; height: 400px; }
  </style>
</head>
<body>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root)]);

    var chart = root.container.children.push(am5xy.XYChart.new(root, {
      panX: false,
      panY: false,
      layout: root.verticalLayout
    }));

    var data = [
      { task: "Research",     start: new Date(2024, 0, 1).getTime(),  end: new Date(2024, 0, 12).getTime(), progress: 1 },
      { task: "Design",       start: new Date(2024, 0, 8).getTime(),  end: new Date(2024, 0, 22).getTime(), progress: 1 },
      { task: "Frontend",     start: new Date(2024, 0, 15).getTime(), end: new Date(2024, 1, 10).getTime(), progress: 0.7 },
      { task: "Backend",      start: new Date(2024, 0, 20).getTime(), end: new Date(2024, 1, 15).getTime(), progress: 0.5 },
      { task: "Testing",      start: new Date(2024, 1, 5).getTime(),  end: new Date(2024, 1, 20).getTime(), progress: 0.2 },
      { task: "Deployment",   start: new Date(2024, 1, 18).getTime(), end: new Date(2024, 1, 25).getTime(), progress: 0 }
    ];

    // Category Y axis (tasks) — inversed so first task is at top
    var yAxis = chart.yAxes.push(am5xy.CategoryAxis.new(root, {
      categoryField: "task",
      renderer: am5xy.AxisRendererY.new(root, { inversed: true, minGridDistance: 20 })
    }));

    // Date X axis (time)
    var xAxis = chart.xAxes.push(am5xy.DateAxis.new(root, {
      baseInterval: { timeUnit: "day", count: 1 },
      renderer: am5xy.AxisRendererX.new(root, {})
    }));

    // Column series with open/close values for task bars
    var series = chart.series.push(am5xy.ColumnSeries.new(root, {
      xAxis: xAxis,
      yAxis: yAxis,
      openValueXField: "start",
      valueXField: "end",
      categoryYField: "task",
      tooltip: am5.Tooltip.new(root, {
        labelText: "{task}\n{openValueX.formatDate('MMM dd')} – {valueX.formatDate('MMM dd')}"
      })
    }));

    series.columns.template.setAll({
      height: am5.percent(60),
      cornerRadiusBR: 4,
      cornerRadiusTR: 4,
      cornerRadiusBL: 4,
      cornerRadiusTL: 4,
      strokeOpacity: 0
    });

    // Color by progress
    series.columns.template.adapters.add("fill", function(fill, target) {
      var p = target.dataItem.dataContext.progress;
      if (p >= 1) return am5.color(0x4caf50);
      if (p > 0)  return am5.color(0xff9800);
      return am5.color(0x2196f3);
    });
    series.columns.template.adapters.add("stroke", function(stroke, target) {
      var p = target.dataItem.dataContext.progress;
      if (p >= 1) return am5.color(0x4caf50);
      if (p > 0)  return am5.color(0xff9800);
      return am5.color(0x2196f3);
    });

    // Today marker
    var today = new Date().getTime();
    var rangeDataItem = xAxis.makeDataItem({ value: today });
    xAxis.createAxisRange(rangeDataItem);
    rangeDataItem.get("grid").setAll({
      stroke: am5.color(0xff0000),
      strokeWidth: 2,
      strokeOpacity: 1
    });

    yAxis.data.setAll(data);
    series.data.setAll(data);

    series.appear(1000);
    chart.appear(1000, 100);
  </script>
</body>
</html>
```
