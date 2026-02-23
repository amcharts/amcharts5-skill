# Responsive — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/responsive/ -->

Responsive features adapt chart settings based on available width/height, activated via the `Responsive` theme.

## Loading

```javascript
// ES module
import am5themes_Responsive from "@amcharts/amcharts5/themes/Responsive";
```

```html
<!-- Script tag -->
<script src="https://cdn.amcharts.com/lib/5/themes/Responsive.js"></script>
```

## Applying

With default built-in rules:

```javascript
root.setThemes([
  am5themes_Responsive.new(root)
]);
```

Without default rules (when you want full control):

```javascript
root.setThemes([
  am5themes_Responsive.newEmpty(root)
]);
```

## Adding Custom Rules

Pre-create the theme object, add rules, then pass it to `setThemes()`:

```javascript
const responsive = am5themes_Responsive.new(root);

responsive.addRule({
  name: "AxisRendererY",
  relevant: function(width, height) {
    return width < 1000;
  },
  settings: {
    inside: true
  }
});

root.setThemes([
  am5themes_Animated.new(root),
  responsive
]);
```

Each rule has:

| Property | Description |
|---|---|
| `name` | Element class name to target (same as theme rules) |
| `tags` | Optional array of theme tags to narrow targeting |
| `relevant` | Function `(width, height) => boolean` — returns `true` when rule applies |
| `settings` | Key-value setting overrides to apply when condition is met |
| `applying` | Optional function called when condition becomes true |
| `removing` | Optional function called when condition is no longer true |

## Breakpoints

```javascript
am5themes_Responsive.XXS  // 100
am5themes_Responsive.XS   // 200
am5themes_Responsive.S    // 300
am5themes_Responsive.M    // 400
am5themes_Responsive.L    // 600
am5themes_Responsive.XL   // 800
am5themes_Responsive.XXL  // 1000
```

## Built-in Condition Functions

Pass directly as the `relevant` value instead of writing a custom function:

| Function | Condition |
|---|---|
| `widthXXS` … `widthXXL` | `width <= breakpoint` |
| `heightXXS` … `heightXXL` | `height <= breakpoint` |
| `maybeXXS` … `maybeXXL` | `width <= bp \|\| height <= bp` |
| `isXXS` … `isXXL` | `width <= bp && height <= bp` |

Example using a built-in condition:

```javascript
responsive.addRule({
  name: "AxisRendererY",
  relevant: am5themes_Responsive.widthXXL,
  settings: {
    inside: true
  }
});
```

## Important: Rules Cannot Override User-Set Settings

Responsive rules use templates and will **not** override settings set directly on an element. If a setting is already set on an object, the responsive rule for that setting will be ignored.

**Wrong approach** (won't work — `layout` is set directly on chart):

```javascript
let chart = root.container.children.push(am5xy.XYChart.new(root, {
  layout: root.verticalLayout  // this blocks the responsive rule
}));

responsive.addRule({
  name: "XYChart",
  relevant: function(width, height) { return width < 1000; },
  settings: { layout: root.horizontalLayout }  // will NOT apply
});
```

**Correct approach** — remove the direct setting, use two complementary rules:

```javascript
// No layout set on chart directly
let chart = root.container.children.push(am5xy.XYChart.new(root, {}));

responsive.addRule({
  name: "XYChart",
  relevant: function(width, height) { return width >= 1000; },
  settings: { layout: root.verticalLayout }
});

responsive.addRule({
  name: "XYChart",
  relevant: function(width, height) { return width < 1000; },
  settings: { layout: root.horizontalLayout }
});
```

## Custom Apply/Remove Functions

For complex changes beyond single setting overrides, use `applying` and `removing`:

```javascript
responsive.addRule({
  relevant: am5themes_Responsive.widthM,
  applying: function() {
    chart.set("layout", root.verticalLayout);
    legend.setAll({
      y: null,
      centerY: null,
      x: am5.p0,
      centerX: am5.p0
    });
  },
  removing: function() {
    chart.set("layout", root.horizontalLayout);
    legend.setAll({
      y: am5.p50,
      centerY: am5.p50,
      x: null,
      centerX: null
    });
  }
});
```
