# Heat Rules — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/settings/heat-rules/ -->

Heat rules apply value-dependent setting values (e.g. color, size) to series elements based on their data values.

## Prerequisites

Series must calculate aggregate (high/low) values. Enable with `calculateAggregates: true`:

```javascript
let series = chart.series.push(
  am5xy.ColumnSeries.new(root, {
    xAxis: xAxis,
    yAxis: yAxis,
    valueYField: "value",
    valueXField: "date",
    calculateAggregates: true
  })
);
```

> Not needed if you set `minValue`/`maxValue` explicitly (see below).

## Creating Heat Rules

Set `heatRules` on the series as an array of rule objects. Each rule has:

| Property | Required | Description |
|---|---|---|
| `target` | Yes | Template for the element to apply rules to (e.g. `series.columns.template`) |
| `key` | Yes | The setting to apply the interpolated value to (e.g. `"fill"`, `"radius"`) |
| `min` | Yes | Value applied to the lowest data value |
| `max` | Yes | Value applied to the highest data value |
| `dataField` | Yes | Data field to read the element's value from |
| `minValue` | No | Override the minimum of the value range |
| `maxValue` | No | Override the maximum of the value range |
| `customFunction` | No | Custom function for advanced logic (see below) |

## Basic Example — Column Colors by Value

```javascript
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  min: am5.color(0xff621f),
  max: am5.color(0x661F00),
  key: "fill"
}]);
```

## Arbitrary Value Field

Use the generic `valueField` / `"value"` data field to drive heat rules independently from the plotted value:

```javascript
let series = chart.series.push(
  am5xy.ColumnSeries.new(root, {
    xAxis: xAxis,
    yAxis: yAxis,
    valueField: "heatValue",  // drives heat rules
    valueYField: "value",     // drives column height
    valueXField: "date",
    calculateAggregates: true
  })
);

series.set("heatRules", [{
  target: series.columns.template,
  dataField: "value",
  min: am5.color(0xff621f),
  max: am5.color(0x661F00),
  key: "fill"
}]);
```

## Bullets

Bullets have no built-in template, so create one explicitly and pass it as the second argument to `new()`:

```javascript
let circleTemplate = am5.Template.new({});

series.bullets.push(function() {
  const bulletCircle = am5.Circle.new(root, {
    radius: 5,
    fill: series.get("fill"),
    fillOpacity: 0.8
  }, circleTemplate);

  return am5.Bullet.new(root, {
    sprite: bulletCircle
  });
});

series.set("heatRules", [{
  target: circleTemplate,
  dataField: "value",
  min: 3,
  max: 60,
  key: "radius"
}]);
```

## Custom Value Range

Override the auto-calculated min/max with explicit values. When set, `calculateAggregates` is not required:

```javascript
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  min: am5.color(0xff621f),
  max: am5.color(0x661F00),
  minValue: 0,
  maxValue: 1000,
  key: "fill"
}]);
```

## Custom Function

For advanced logic, use `customFunction` instead of `key`. The function receives:
1. Target element (live element, not template)
2. Min value
3. Max value
4. Current element value

```javascript
series.set("heatRules", [{
  target: series.columns.template,
  dataField: "valueY",
  minValue: 0,
  maxValue: 1000,
  customFunction: function(target, min, max, value) {
    // apply any settings on target as needed
  }
}]);
```
