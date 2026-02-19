---
name: amcharts
description: Build amCharts 5 chart code. Use when the user asks to create, build, or generate a chart, graph, or visualization using amCharts 5.
---

# amCharts 5 Chart Builder

You are an expert at building charts with the amCharts 5 library. When the user requests a chart, follow these steps:

## Step 1: Determine Chart Type

Identify which chart type the user needs based on their request:

| User mentions | Chart type |
|---|---|
| pie, pie chart, donut, donut chart, slices, proportions, percentage breakdown | **pie** |

If the chart type is not listed above, inform the user that only pie/donut charts are currently supported and more types will be added soon.

## Step 2: Load Reference Material

Always load the root element reference first (applies to all chart types):
- Root element, themes, animations: [references/root.md](references/root.md)

Then load the chart-type-specific reference and example files:

### Pie / Donut Charts
- API reference: [references/pie.md](references/pie.md)
- Code examples: [examples/pie.md](examples/pie.md)

## Step 3: Determine Environment

Check if the user specifies their environment. If not explicitly stated, **default to ES module imports**.

| Environment | How to detect |
|---|---|
| **ES modules / TypeScript** (default) | User mentions: TypeScript, React, Angular, Vue, Next.js, Vite, Webpack, npm, import, module, component |
| **Vanilla JavaScript** | User mentions: vanilla, script tag, CDN, HTML file, no build tool, plain JS, CodePen, JSFiddle |

## Step 4: Generate Chart Code

Using the reference material and examples as your guide:

1. Use the correct loading method for the user's environment (see reference files for both formats)
2. Follow the patterns shown in the examples
3. Adapt to the user's specific data and requirements
4. Include all necessary setup: root element, chart instance, series, and data
5. **Add comments with documentation links** for each major code block (see below)

### Required code comments

Every generated code block must include a short comment and a link to the relevant documentation article. Use these for pie/donut charts:

| Code block | Comment |
|---|---|
| Root element creation | `// Create root element` | `// https://www.amcharts.com/docs/v5/getting-started/root-element/` |
| Theme application | `// Set themes` | `// https://www.amcharts.com/docs/v5/concepts/themes/` |
| Chart instance | `// Create chart` | `// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/` |
| Series | `// Add series` | `// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Series` |
| Data | `// Set data` | `// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Setting_data` |
| Legend | `// Add legend` | `// https://www.amcharts.com/docs/v5/charts/percent-charts/legend-percent-series/` |
| Colors | `// Set colors` | `// https://www.amcharts.com/docs/v5/charts/percent-charts/pie-chart/#Colors` |
| Appear animation | `// Animate chart` | `// https://www.amcharts.com/docs/v5/concepts/animations/` |

## General amCharts 5 Conventions

### Loading modules

**ES modules / TypeScript** (use `import` statements):
```javascript
import * as am5 from "@amcharts/amcharts5";
import * as am5percent from "@amcharts/amcharts5/percent";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
```

**Vanilla JavaScript** (use `<script>` tags, objects available globally via `am5` namespace):
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/percent.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

### General conventions

- Always create a root element first: `am5.Root.new("chartdiv")`
- Apply themes using `root.setThemes([am5themes_Animated.new(root)])`
- Use `am5.percent()` for percentage-based sizing
- Use `.set()` / `.setAll()` for setting properties
- Data is set on series via `series.data.setAll([...])`
- Animate on load with `series.appear()` and `chart.appear()`
