# Settings Reference — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/settings/ -->

Settings are key-value pairs that every amCharts 5 element has, used to configure appearance and behavior.

## Setting Values

There are three equivalent ways to set settings on an element — all produce identical output.

### 1. Via `new()` (preferred)

Pass settings as the second argument when creating an element:

```javascript
const series = chart.series.push(
  am5percent.PieSeries.new(root, {
    valueField: "value",
    categoryField: "category"
  })
);
```

### 2. Via `set()` — single key

```javascript
series.set("valueField", "value");
series.set("categoryField", "category");
```

### 3. Via `setAll()` — multiple keys at once

```javascript
series.setAll({
  valueField: "value",
  categoryField: "category"
});
```

## Getting Values

Use `get()` to read the current value of a setting:

```javascript
series.get("valueField");
```
