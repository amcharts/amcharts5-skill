# amcharts5-skill

An AI skill for building [amCharts 5](https://docs.amcharts.com/v5/) charts and data visualizations. Works with Claude, Cursor, GitHub Copilot, and any AI assistant that supports custom context or skill files.

Supports XY (line, bar, area, scatter), pie/donut, maps, hierarchy (treemap, sunburst), flow (Sankey, chord), radar, stock/financial, Gantt, word cloud, Venn, and timeline charts.

AI docs: https://docs.amcharts.com/v5/ai/

## Installation

### Claude Code

Copy the skill folder, `amcharts5-skill/` in this repo, into your project's `.claude/skills/` as `amcharts5` (Claude Code looks for `.claude/skills/<name>/SKILL.md`):

```sh
git clone --depth 1 https://github.com/amcharts/amcharts5-skill /tmp/amcharts5-skill
cp -r /tmp/amcharts5-skill/amcharts5-skill .claude/skills/amcharts5
```

Or add the [amCharts MCP server](https://github.com/amcharts/amcharts5-mcp), which serves this skill's content and the full docs through its tools: `claude mcp add --transport http amcharts5 https://mcp.amcharts.com/mcp`.

### Claude.ai (web)

1. Download this repo as a ZIP and unpack it
2. Rename the `amcharts5-skill` folder inside to `amcharts5`, and zip that folder alone (the ZIP's root must be the skill folder)
3. Go to **Claude.ai → Settings → Skills**
4. Click **Upload skill** and select the ZIP
5. Toggle the skill on

### Cursor

Add the hosted [amCharts MCP server](https://github.com/amcharts/amcharts5-mcp). It has 12 read-only tools for the amCharts 5 docs, 350+ code examples, the API reference and the map files, and needs no sign-in. One click:

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=amcharts5&config=eyJ1cmwiOiJodHRwczovL21jcC5hbWNoYXJ0cy5jb20vbWNwIn0%3D)

Or add it to Cursor's MCP settings (`.cursor/mcp.json`) yourself:

```json
{
  "mcpServers": {
    "amcharts5": {
      "url": "https://mcp.amcharts.com/mcp"
    }
  }
}
```

To have the skill too, copy `amcharts5-skill/` into your project and reference its files with `@file` in chat.

### Windsurf / other editors

Copy or symlink the skill files into your project, then add them to your AI context, or reference them in chat. For editors that take one rules file, `cursorrules` in this repo has the essentials condensed into a single file.

### Any AI assistant

The skill is plain Markdown. You can paste the contents of `amcharts5-skill/SKILL.md` and the relevant `amcharts5-skill/references/*.md` files directly into any AI chat as context before asking your question.

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

## License

MIT. amCharts 5 itself is licensed separately ([licenses explained](https://www.amcharts.com/online-store/licenses-explained/)): without a license, each chart shows a small amCharts logo, which the paid licenses remove.
