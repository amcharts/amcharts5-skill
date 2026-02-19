# Legend Reference — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/legend/ -->

`Legend` is a universal component available in all chart types except `MapChart` and `StockChart`. It is part of the core `am5` module — no additional imports required.

## Creating a Legend

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {})
);
```

After creating the legend, populate it by passing the series' data items:

```javascript
legend.data.setAll(series.dataItems);
```

---

## 1. Placement
<!-- https://www.amcharts.com/docs/v5/concepts/legend/#Adding_to_chart -->

Legend placement depends on two things:

- **Where in `chart.children` it is inserted** — controls whether it appears before or after the plot area.
- **The chart's `layout` setting** — controls whether children are stacked vertically or horizontally.

| Desired position | Insert method | Chart `layout` |
|---|---|---|
| Top | `chart.children.unshift(legend)` | `root.verticalLayout` |
| Bottom | `chart.children.push(legend)` | `root.verticalLayout` |
| Left | `chart.children.unshift(legend)` | `root.horizontalLayout` |
| Right | `chart.children.push(legend)` | `root.horizontalLayout` |

### Legend on top

```javascript
// Chart must use vertical layout so children stack top-to-bottom
let chart = root.container.children.push(
  am5xy.XYChart.new(root, {
    layout: root.verticalLayout
  })
);

// unshift places the legend before the plot area (top)
let legend = chart.children.unshift(
  am5.Legend.new(root, {})
);
```

### Legend on bottom (default / most common)

```javascript
let chart = root.container.children.push(
  am5xy.XYChart.new(root, {
    layout: root.verticalLayout
  })
);

// push places the legend after the plot area (bottom)
let legend = chart.children.push(
  am5.Legend.new(root, {})
);
```

### Legend on the right

```javascript
let chart = root.container.children.push(
  am5xy.XYChart.new(root, {
    layout: root.horizontalLayout
  })
);

// push places the legend after the plot area (right)
let legend = chart.children.push(
  am5.Legend.new(root, {})
);
```

### Legend on the left

```javascript
let chart = root.container.children.push(
  am5xy.XYChart.new(root, {
    layout: root.horizontalLayout
  })
);

// unshift places the legend before the plot area (left)
let legend = chart.children.unshift(
  am5.Legend.new(root, {})
);
```

---

## 2. Orientation
<!-- https://www.amcharts.com/docs/v5/concepts/legend/#Layout -->

The legend's own `layout` setting controls how its **items** are arranged internally (independent of chart-level layout).

| `layout` value | Behavior |
|---|---|
| `root.horizontalLayout` | All items in a single horizontal row |
| `root.verticalLayout` | All items in a single vertical column |
| `root.gridLayout` | Items wrapped into a multi-column grid |

### Horizontal items (single row)

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    layout: root.horizontalLayout
  })
);
```

### Vertical items (single column)

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    layout: root.verticalLayout
  })
);
```

### Grid layout (multi-column)

Use `root.gridLayout` when you have many series and want items to wrap into multiple columns.

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    layout: root.gridLayout
  })
);
```

Additional settings relevant to grid layout:

| Setting | Type | Description |
|---|---|---|
| `maxColumns` | number | Maximum number of columns before wrapping to a new row |
| `fixedWidthGrid` | boolean | When `true`, all columns are equal width (based on the widest item) |

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    layout: root.gridLayout,
    maxColumns: 3,
    fixedWidthGrid: true
  })
);
```

---

## 3. Positioning and Centering
<!-- https://www.amcharts.com/docs/v5/concepts/legend/#Position -->

Use `x`, `y`, `centerX`, and `centerY` to fine-tune the legend's position within its container.

| Setting | Description |
|---|---|
| `x` | Horizontal position (from left edge of container) |
| `y` | Vertical position (from top edge of container) |
| `centerX` | Horizontal anchor point of the legend itself |
| `centerY` | Vertical anchor point of the legend itself |

Values can be absolute pixels or `am5.percent()` values.

### Horizontally centered (e.g. below chart)

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    x: am5.percent(50),
    centerX: am5.percent(50)
  })
);
```

Setting `x: am5.percent(50)` moves the legend's left edge to the container's midpoint; `centerX: am5.percent(50)` then shifts the legend left by half its own width, resulting in true centering.

### Aligned to the right

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    x: am5.percent(100),
    centerX: am5.percent(100)
  })
);
```

### Aligned to the left

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    x: 0,
    centerX: 0
  })
);
```

### Vertically centered (e.g. beside chart)

```javascript
let legend = chart.children.push(
  am5.Legend.new(root, {
    y: am5.percent(50),
    centerY: am5.percent(50)
  })
);
```
