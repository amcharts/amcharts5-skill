---
name: amcharts
description: This skill generates amCharts 5 chart code. It should be used when the user asks to 'create a chart', 'build a pie chart', 'make a donut chart', 'generate a visualization', or mentions 'amCharts'.
metadata:
	author: amCharts
	version: 1.0.0-beta.1
	category: code
	tags: [charts, maps]
	documentation: https://amcharts.com/docs/v5
	support: contact@amcharts.com
---

# amCharts 5 Chart Builder

To generate amCharts 5 chart code, follow these steps:

## Step 1: Determine Chart Type

Identify which chart type is needed based on the request:

| User mentions | Chart type |
|---|---|
| pie, pie chart, donut, donut chart, slices, proportions, percentage breakdown | **pie** |

If the chart type is not listed above, use your own knowledge to build the requested chart.

## Step 2: Load Reference Material

Always load the root element reference first (applies to all chart types):
- Root element, animations: [references/root.md](references/root.md)
- Themes (built-in list, ordering, custom themes, default themes): [references/themes.md](references/themes.md)
- Settings API (set, setAll, get): [references/settings.md](references/settings.md)
- Template fields (per-item setting overrides from data): [references/template-fields.md](references/template-fields.md)
- Adapters (dynamic setting values via functions): [references/adapters.md](references/adapters.md)
- Heat rules (value-dependent settings on series elements): [references/heat-rules.md](references/heat-rules.md)
- Events (click/hover handlers, settings watchers, debounced events): [references/events.md](references/events.md)
- Responsive (breakpoint-based setting overrides): [references/responsive.md](references/responsive.md)

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

Using the reference material and examples as a guide:

1. Use the correct loading method for the detected environment (see reference files for both formats)
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

- Always create a root element first: `am5.Root.new("chartdiv")`
- Apply themes using `root.setThemes([am5themes_Animated.new(root)])`
- Use `am5.percent()` for percentage-based sizing
- Use `.set()` / `.setAll()` for setting properties
- Data is set on series via `series.data.setAll([...])`
- Animate on load with `series.appear()` and `chart.appear()`
