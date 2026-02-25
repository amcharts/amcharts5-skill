# amcharts5-skill

An AI skill for building [amCharts 5](https://www.amcharts.com/docs/v5/) charts and data visualizations. Works with Claude, Cursor, GitHub Copilot, and any AI assistant that supports custom context or skill files.

Supports XY (line, bar, area, scatter), pie/donut, maps, hierarchy (treemap, sunburst), flow (Sankey, chord), radar, stock/financial, Gantt, word cloud, Venn, and timeline charts.

AI docs: https://www.amcharts.com/docs/v5/ai/

## Installation

### Claude Code

Add the skill to your project's `.claude/skills/` directory:

```sh
git clone https://github.com/amcharts/amcharts5-skill .claude/skills/amcharts5-skill
```

### Claude.ai (web)

1. Download this repo as a ZIP
2. Go to **Claude.ai → Settings → Skills**
3. Click **Upload skill** and select the ZIP
4. Toggle the skill on

### Cursor / Windsurf / other editors

Copy or symlink the skill files into your project, then add them to your AI context. For example, in Cursor add the files to `.cursorrules` or reference them via `@file` in chat.

### Any AI assistant

The skill is plain Markdown. You can paste the contents of `SKILL.md` and the relevant `references/*.md` files directly into any AI chat as context before asking your question.

## Usage

Once installed, just ask your AI assistant to build a chart:

```
Create a bar chart showing monthly sales data using amCharts 5
```

```
Build a world map choropleth with country GDP data
```

```
Add a legend and animated transitions to my amCharts pie chart
```

The skill automatically loads the right reference documentation for the chart type you're building.
