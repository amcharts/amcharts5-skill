# Themes — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/themes/ -->

Themes apply a collection of default settings to chart elements. They do **not** override values set directly in user code — they only supply defaults.

## Applying Themes

Use `root.setThemes()` with an array of theme instances:

```javascript
root.setThemes([
  am5themes_Animated.new(root),
  am5themes_Micro.new(root)
]);
```

> **Order matters.** If two themes set the same setting, the last theme wins.

## Built-in Themes

| Theme | Import path | Purpose |
|---|---|---|
| `Animated` | `@amcharts/amcharts5/themes/Animated` | Enables animations (zoom, fade, color cross-fade) |
| `Dark` | `@amcharts/amcharts5/themes/Dark` | Suitable for dark backgrounds |
| `Dataviz` | `@amcharts/amcharts5/themes/Dataviz` | Alternative default color palette |
| `Frozen` | `@amcharts/amcharts5/themes/Frozen` | Cold/blue color palette |
| `Kelly` | `@amcharts/amcharts5/themes/Kelly` | High-contrast color palette |
| `Material` | `@amcharts/amcharts5/themes/Material` | Material Design colors |
| `Micro` | `@amcharts/amcharts5/themes/Micro` | Bare-minimum charts (no labels, axes) for small spaces |
| `Moonrise` | `@amcharts/amcharts5/themes/Moonrise` | Alternative default colors |
| `Responsive` | `@amcharts/amcharts5/themes/Responsive` | Adapts settings based on available space |
| `Spirited` | `@amcharts/amcharts5/themes/Spirited` | Vivid alternative color palette |

## Quick Custom Theme

To override defaults without creating a full reusable theme, instantiate `am5.Theme` and add rules. For full reusable themes with class-based rules and tag targeting, see [custom-themes.md](custom-themes.md).

```javascript
const myTheme = am5.Theme.new(root);

myTheme.rule("Label").setAll({
  fill: am5.color(0xFF0000),
  fontSize: "1.5em"
});

root.setThemes([
  am5themes_Animated.new(root),
  myTheme  // applied last, so its rules win
]);
```

`rule("Label")` targets all `Label` elements in the chart. Rules can target any element class name.

## Default Themes per Chart Type

Each chart type (XY, Percent, etc.) has its own default theme that is applied automatically when the chart is created. This matters when rendering chart elements (e.g. series legend markers) in a **separate root element** — the chart-specific default theme must be applied to that root too:

```javascript
let root = am5.Root.new("chartdiv");
let legendRoot = am5.Root.new("legenddiv");

root.setThemes([
  am5themes_Animated.new(root)
]);

legendRoot.setThemes([
  am5themes_Animated.new(legendRoot),
  am5xy.DefaultTheme.new(legendRoot)  // required for XY-specific element defaults
]);
```
