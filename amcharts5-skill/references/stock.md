# Stock & Financial Charts

> **As a JSON config:** see `references/json.md`. The settings below go into each object's `"settings"` there.

Build financial stock charts with panels, indicators, and toolbar.


## Required imports

### ES modules
```ts
import * as am5 from "@amcharts/amcharts5";
import * as am5xy from "@amcharts/amcharts5/xy";
import * as am5stock from "@amcharts/amcharts5/stock";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
import am5themes_ResponsiveMax from "@amcharts/amcharts5/themes/ResponsiveMax";
```

### CDN
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
<script src="https://cdn.amcharts.com/lib/5/stock.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
```

From an npm CDN (AI sandboxes, strict CSPs), the same files are under `https://cdn.jsdelivr.net/npm/@amcharts/amcharts5@5.23.0/dist/`, one pinned version for all: see SKILL.md "Plain HTML, no bundler".

## Core setup

```html
<div id="chartcontrols"></div>  <!-- toolbar container -->
<div id="chartdiv"></div>       <!-- chart container -->
```

```js
const root = am5.Root.new("chartdiv");
root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);
root.numberFormatter.set("numberFormat", "#,###.00##"); // prices: cents, and up to 4 decimals for small ones

// 1. Stock chart (wrapper)
const stockChart = root.container.children.push(
  am5stock.StockChart.new(root, {})
);

// 2. Main panel (is an XYChart)
const mainPanel = stockChart.panels.push(
  am5stock.StockPanel.new(root, {
    wheelY: "zoomX",
    panX: true,
    panY: true
  })
);

// 3. Axes on the panel
const dateAxis = mainPanel.xAxes.push(am5xy.DateAxis.new(root, {
  baseInterval: { timeUnit: "day", count: 1 },
  renderer: am5xy.AxisRendererX.new(root, {}),
  tooltip: am5.Tooltip.new(root, {})
}));

const valueAxis = mainPanel.yAxes.push(am5xy.ValueAxis.new(root, {
  renderer: am5xy.AxisRendererY.new(root, {}),
  tooltip: am5.Tooltip.new(root, {}), // shows the cursor's price on the axis...
  extraTooltipPrecision: 2,           // ...with two more decimals than the grid step
  preferredDecimals: [0, 2]           // labels: whole numbers, or cents
}));

// 4. Main series (candlestick)
const valueSeries = mainPanel.series.push(am5xy.CandlestickSeries.new(root, {
  name: "STCK",
  xAxis: dateAxis,
  yAxis: valueAxis,
  valueXField: "Date",
  valueYField: "Close",
  highValueYField: "High",
  lowValueYField: "Low",
  openValueYField: "Open",
  calculateAggregates: true,
  legendValueText: "{valueY}"
}));

// 5. Register as main stock series (CRITICAL)
stockChart.set("stockSeries", valueSeries);

// 6. Stock legend
const legend = mainPanel.plotContainer.children.push(
  am5stock.StockLegend.new(root, { stockChart: stockChart })
);
legend.data.setAll([valueSeries]);

// 7. Cursor
mainPanel.set("cursor", am5xy.XYCursor.new(root, {
  yAxis: valueAxis,
  xAxis: dateAxis,
  behavior: "none",
  snapToSeries: [valueSeries]
}));

// 8. Scrollbar with preview series
const scrollbar = mainPanel.set("scrollbarX",
  am5xy.XYChartScrollbar.new(root, {
    orientation: "horizontal",
    height: 50
  })
);

var sbDateAxis = scrollbar.chart.xAxes.push(am5xy.DateAxis.new(root, {
  baseInterval: { timeUnit: "day", count: 1 },
  renderer: am5xy.AxisRendererX.new(root, {})
}));
var sbValueAxis = scrollbar.chart.yAxes.push(am5xy.ValueAxis.new(root, {
  renderer: am5xy.AxisRendererY.new(root, {})
}));
var sbSeries = scrollbar.chart.series.push(am5xy.LineSeries.new(root, {
  xAxis: sbDateAxis,
  yAxis: sbValueAxis,
  valueYField: "Close",
  valueXField: "Date"
}));

// 9. Data — LAST
valueSeries.data.setAll(data);
sbSeries.data.setAll(data);

// 10. Toolbar (optional)
const toolbar = am5stock.StockToolbar.new(root, {
  container: document.getElementById("chartcontrols"),
  stockChart: stockChart,
  controls: [
    am5stock.IndicatorControl.new(root, { stockChart: stockChart }),
    am5stock.DrawingControl.new(root, { stockChart: stockChart }),
    am5stock.PeriodSelector.new(root, {
      stockChart: stockChart,
      periods: [
        { timeUnit: "day", count: 1, name: "1D" },
        { timeUnit: "month", count: 1, name: "1M" },
        { timeUnit: "month", count: 3, name: "3M" },
        { timeUnit: "year", count: 1, name: "1Y" },
        { timeUnit: "max", name: "Max" }
      ]
    }),
    am5stock.SeriesTypeControl.new(root, { stockChart: stockChart }),
    am5stock.SettingsControl.new(root, { stockChart: stockChart }),
    am5stock.ResetControl.new(root, { stockChart: stockChart })
  ]
});
```

## Adding a volume panel

```js
const volumePanel = stockChart.panels.push(
  am5stock.StockPanel.new(root, {
    wheelY: "zoomX",
    panX: true,
    panY: true,
    height: am5.percent(30)
  })
);

const volDateAxis = volumePanel.xAxes.push(am5xy.DateAxis.new(root, {
  baseInterval: { timeUnit: "day", count: 1 },
  renderer: am5xy.AxisRendererX.new(root, {})
}));

const volValueAxis = volumePanel.yAxes.push(am5xy.ValueAxis.new(root, {
  renderer: am5xy.AxisRendererY.new(root, {})
}));

const volumeSeries = volumePanel.series.push(am5xy.ColumnSeries.new(root, {
  name: "Volume",
  xAxis: volDateAxis,
  yAxis: volValueAxis,
  valueXField: "Date",
  valueYField: "Volume"
}));

stockChart.set("volumeSeries", volumeSeries);
volumeSeries.data.setAll(data);
```

## Styling: candle colors, panel buttons, navigator position

Candles take the interface colors `positive` (#50b300) and `negative` (#b30000) unless the stock chart gets its own. Give them when the stock chart is made (step 1 of "Core setup"); the rest uses the names from "Core setup" and the volume panel above:

```js
const stockChart = root.container.children.push(am5stock.StockChart.new(root, {
  stockPositiveColor: am5.color(0x089981),   // candles closing at or above their open
  stockNegativeColor: am5.color(0xf23645),   // candles closing below it
  volumePositiveColor: am5.color(0x089981),  // what getVolumeColor() returns
  volumeNegativeColor: am5.color(0xf23645),
  autoHidePanelControls: true                // a panel's move, expand and close buttons show only while the pointer is over it
}));

// Volume columns colored by the price move
volumeSeries.columns.template.adapters.add("fill", function (fill, target) {
  return target.dataItem ? stockChart.getVolumeColor(target.dataItem) : fill;
});
// No gear or close button on the legend items (the gear opens the series settings dialog)
legend.settingsButtons.template.set("forceHidden", true);
legend.closeButtons.template.set("forceHidden", true);
// Navigator below the panels: put it in the tools container, then move that container to the end
stockChart.toolsContainer.children.push(scrollbar);
stockChart.children.moveValue(stockChart.toolsContainer);
stockChart.toolsContainer.set("paddingTop", 10);
// Hide date labels cut off at the plot edges ("an 2020")
dateAxis.get("renderer").labels.template.setAll({ minPosition: 0.01, maxPosition: 0.99 });
```

The colors are an example: any pair works. Panel buttons on hover: https://docs.amcharts.com/v5/charts/stock/panels/#showing-controls-on-hover.

## Adding indicators programmatically

```js
// Bollinger Bands
stockChart.indicators.push(am5stock.BollingerBands.new(root, {
  stockChart: stockChart,
  stockSeries: valueSeries,
  legend: legend
}));

// Moving Average (SMA)
stockChart.indicators.push(am5stock.MovingAverage.new(root, {
  stockChart: stockChart,
  stockSeries: valueSeries,
  legend: legend,
  period: 20,
  maType: "simple"  // "simple", "weighted", "exponential", "dema", "tema"
                    // NOTE: use `maType`; the old `type` still
                    // works but is deprecated. Same for MovingAverageDeviation,
                    // MovingAverageEnvelope and BollingerBands.
}));

// RSI (creates its own panel)
stockChart.indicators.push(am5stock.RelativeStrengthIndex.new(root, {
  stockChart: stockChart,
  stockSeries: valueSeries,
  legend: legend
}));

// MACD (creates its own panel)
stockChart.indicators.push(am5stock.MACD.new(root, {
  stockChart: stockChart,
  stockSeries: valueSeries,
  legend: legend
}));

// Volume (requires volumeSeries)
stockChart.indicators.push(am5stock.Volume.new(root, {
  stockChart: stockChart,
  stockSeries: valueSeries,
  volumeSeries: volumeSeries,
  legend: legend
}));
```

### Available indicators

| Indicator | Class | Needs volumeSeries |
|-----------|-------|--------------------|
| Moving Average | `MovingAverage` | No |
| Moving Average Cross | `MACross` | No |
| Moving Average Envelope | `MovingAverageEnvelope` | No |
| Moving Average Deviation | `MovingAverageDeviation` | No |
| Bollinger Bands | `BollingerBands` | No |
| MACD | `MACD` | No |
| RSI | `RelativeStrengthIndex` | No |
| Stochastic Oscillator | `StochasticOscillator` | No |
| Williams %R | `WilliamsR` | No |
| Aroon | `Aroon` | No |
| Awesome Oscillator | `AwesomeOscillator` | No |
| Commodity Channel Index | `CommodityChannelIndex` | No |
| Standard Deviation | `StandardDeviation` | No |
| Disparity Index | `DisparityIndex` | No |
| Trix | `Trix` | No |
| ZigZag | `ZigZag` | No |
| Typical Price | `TypicalPrice` | No |
| Median Price | `MedianPrice` | No |
| On Balance Volume | `OnBalanceVolume` | Yes |
| Accumulation Distribution | `AccumulationDistribution` | Yes |
| Chaikin Money Flow | `ChaikinMoneyFlow` | Yes |
| Chaikin Oscillator | `ChaikinOscillator` | Yes |
| Volume Profile | `VolumeProfile` | Yes |
| VWAP | `VWAP` | Yes |
| PVT | `PVT` | Yes |
| Volume | `Volume` | Yes |

All classes are under `am5stock.*` (e.g., `am5stock.BollingerBands`).

### Indicator notes

- `VolumeProfile` spreads each bar's volume across the full high/low range it traded.
- `MACross` defaults: `fastPeriod: 9`, `period: 21`.
- Numeric indicator settings enforce min/max limits, so the settings modal cannot set extreme values (e.g. a Volume Profile row count of 24,000).

Changing column colors after creation recolors the columns already drawn — `increasingColor`/`decreasingColor` on `Volume`, `MACD`, `AwesomeOscillator`, `MovingAverageDeviation` and `HeikinAshi`, `upColor`/`downColor` on `VolumeProfile` (e.g. `indicator.set("increasingColor", am5.color(0x00aa00))`). An indicator drawn in its own panel has its `legend` and `cursor` properties set.

### Custom indicator editable settings

`IIndicatorEditableSetting` has three more properties for the settings modal:

```js
_editableSettings: [{
  key: "period",
  name: "Period",
  type: "number",
  minValue: 1,
  maxValue: 200,   // upper bound enforced by the input
  step: 1          // step; a step of 1 also marks the value as an integer
}, {
  key: "factor",
  name: "Factor",
  type: "number",
  scale: 1000      // display multiplier for the modal ONLY:
                   // shown value = stored × scale, divided back on save
}]
```

## Data format

```js
var data = [
  { Date: new Date(2024, 0, 2).getTime(), Open: 150.0, High: 155.2, Low: 148.5, Close: 153.1, Volume: 1200000 },
  { Date: new Date(2024, 0, 3).getTime(), Open: 153.1, High: 158.0, Low: 151.3, Close: 156.4, Volume: 1350000 },
  { Date: new Date(2024, 0, 4).getTime(), Open: 156.4, High: 160.1, Low: 154.8, Close: 159.7, Volume: 1100000 },
  { Date: new Date(2024, 0, 5).getTime(), Open: 159.7, High: 162.5, Low: 157.2, Close: 158.3, Volume: 980000 },
  { Date: new Date(2024, 0, 8).getTime(), Open: 158.3, High: 163.0, Low: 156.9, Close: 161.8, Volume: 1420000 },
  { Date: new Date(2024, 0, 9).getTime(), Open: 161.8, High: 165.4, Low: 160.1, Close: 164.2, Volume: 1560000 },
  { Date: new Date(2024, 0, 10).getTime(), Open: 164.2, High: 167.8, Low: 162.0, Close: 163.5, Volume: 1180000 },
  { Date: new Date(2024, 0, 11).getTime(), Open: 163.5, High: 166.3, Low: 161.7, Close: 165.9, Volume: 1050000 },
  { Date: new Date(2024, 0, 12).getTime(), Open: 165.9, High: 170.2, Low: 164.5, Close: 169.1, Volume: 1630000 },
  { Date: new Date(2024, 0, 16).getTime(), Open: 169.1, High: 172.0, Low: 167.3, Close: 171.4, Volume: 1470000 },
  // ... more data points
];
```

**All date values must be timestamps (milliseconds).** Data must be sorted ascending by date.

## Toolbar controls

| Control | Class | Purpose |
|---------|-------|---------|
| Period selector | `am5stock.PeriodSelector` | 1D, 1M, 1Y, Max buttons |
| Date range | `am5stock.DateRangeSelector` | Date picker for custom range |
| Series type | `am5stock.SeriesTypeControl` | Switch line/candlestick/OHLC/area |
| Indicators | `am5stock.IndicatorControl` | Add SMA, EMA, Bollinger, RSI, etc. |
| Drawing | `am5stock.DrawingControl` | Trend lines, Fibonacci, annotations |
| Comparison | `am5stock.ComparisonControl` | Compare multiple symbols |
| Settings | `am5stock.SettingsControl` | Theme, fills, auto-save |
| Reset | `am5stock.ResetControl` | Reset chart to defaults |
| Interval | `am5stock.IntervalControl` | Switch time interval |

**Fitting:** with ResponsiveMax, the toolbar's `autoFit` is on: controls that don't fit in one row fold, first to their icons, then the period buttons into one button that lists them. Set `autoFit: false` on the `StockToolbar` to keep them in full.

**Keyboard:** `StockToolbar` `focusable` defaults to `true` — toolbar buttons are TAB stops and lists work with arrow keys; set `focusable: false` on the `StockToolbar` to turn that off. The settings dialog (`am5.Modal`) traps focus while open and gives it back on close; panel and legend icon buttons and overbought/oversold grips have screen-reader names.

**`PeriodSelector` labels:** a period without a `name` is labeled from its unit and count — `{ timeUnit: "month" }` shows `MONTH1`, `{ timeUnit: "ytd" }` shows `YTD`. Give each period a `name` for nicer labels.

**Time zones:** `PeriodSelector` and `DateRangeSelector` count periods and dates in `root.timezone` (or UTC with `root.utc`), like the date axis. With `root.timezone`, grid, labels and data groups stay on the hour across daylight saving changes.

## Events

```js
// Period changed — the `periodselected` event fires on the PeriodSelector
// instance, NOT on stockChart. Attach the handler to the selector you created.
periodSelector.events.on("periodselected", (ev) => {
  console.log("Period:", ev.period);
});

// Series type changed
valueSeries.events.on("datavalidated", () => {
  console.log("Data updated");
});
```

## Disposal

In a React, Vue, Angular, Svelte or React Native app, use amCharts' framework package, which disposes the root with the component (SKILL.md, "Frameworks"). Otherwise dispose the root when the component goes away:

```js
root.dispose();
```

---

## Example 1: Stock chart with candlesticks and toolbar

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Stock Chart</title>
  <style>
    body { font-family: -apple-system, sans-serif; margin: 0; padding: 20px; }
    #chartcontrols { height: auto; padding: 5px 0; }
    #chartdiv { width: 100%; height: 500px; }
  </style>
  <script src="https://cdn.amcharts.com/lib/5/index.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/stock.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
  <script src="https://cdn.amcharts.com/lib/5/themes/ResponsiveMax.js"></script>
</head>
<body>
  <div id="chartcontrols"></div>
  <div id="chartdiv"></div>
  <script>
    var root = am5.Root.new("chartdiv");
    root.setThemes([am5themes_Animated.new(root), am5themes_ResponsiveMax.new(root)]);
    root.numberFormatter.set("numberFormat", "#,###.00##"); // prices: cents, and up to 4 decimals for small ones

    // Stock chart wrapper
    var stockChart = root.container.children.push(
      am5stock.StockChart.new(root, {})
    );

    // Main panel
    var mainPanel = stockChart.panels.push(
      am5stock.StockPanel.new(root, {
        wheelY: "zoomX",
        panX: true,
        panY: true
      })
    );

    // Axes
    var dateAxis = mainPanel.xAxes.push(am5xy.DateAxis.new(root, {
      baseInterval: { timeUnit: "day", count: 1 },
      renderer: am5xy.AxisRendererX.new(root, {}),
      tooltip: am5.Tooltip.new(root, {})
    }));

    var valueAxis = mainPanel.yAxes.push(am5xy.ValueAxis.new(root, {
      renderer: am5xy.AxisRendererY.new(root, {}),
      tooltip: am5.Tooltip.new(root, {}),
      extraTooltipPrecision: 2,
      preferredDecimals: [0, 2] // labels: whole numbers, or cents
    }));

    // Candlestick series
    var valueSeries = mainPanel.series.push(am5xy.CandlestickSeries.new(root, {
      name: "ACME",
      xAxis: dateAxis,
      yAxis: valueAxis,
      valueXField: "Date",
      valueYField: "Close",
      highValueYField: "High",
      lowValueYField: "Low",
      openValueYField: "Open",
      calculateAggregates: true,
      legendValueText: "O:{openValueY} H:{highValueY} L:{lowValueY} C:{valueY}"
    }));

    // CRITICAL: register as stock series
    stockChart.set("stockSeries", valueSeries);

    // Legend
    var legend = mainPanel.plotContainer.children.push(
      am5stock.StockLegend.new(root, { stockChart: stockChart })
    );
    legend.data.setAll([valueSeries]);

    // Cursor
    mainPanel.set("cursor", am5xy.XYCursor.new(root, {
      yAxis: valueAxis,
      xAxis: dateAxis,
      behavior: "none",
      snapToSeries: [valueSeries]
    }));

    // Scrollbar
    var scrollbar = mainPanel.set("scrollbarX",
      am5xy.XYChartScrollbar.new(root, {
        orientation: "horizontal",
        height: 50
      })
    );

    // Scrollbar preview series
    var sbDateAxis = scrollbar.chart.xAxes.push(am5xy.DateAxis.new(root, {
      baseInterval: { timeUnit: "day", count: 1 },
      renderer: am5xy.AxisRendererX.new(root, {})
    }));
    var sbValueAxis = scrollbar.chart.yAxes.push(am5xy.ValueAxis.new(root, {
      renderer: am5xy.AxisRendererY.new(root, {})
    }));
    var sbSeries = scrollbar.chart.series.push(am5xy.LineSeries.new(root, {
      xAxis: sbDateAxis,
      yAxis: sbValueAxis,
      valueYField: "Close",
      valueXField: "Date"
    }));

    // Toolbar
    am5stock.StockToolbar.new(root, {
      container: document.getElementById("chartcontrols"),
      stockChart: stockChart,
      controls: [
        am5stock.IndicatorControl.new(root, { stockChart: stockChart }),
        am5stock.DrawingControl.new(root, { stockChart: stockChart }),
        am5stock.PeriodSelector.new(root, {
          stockChart: stockChart,
          periods: [
            { timeUnit: "month", count: 1, name: "1M" },
            { timeUnit: "month", count: 3, name: "3M" },
            { timeUnit: "month", count: 6, name: "6M" },
            { timeUnit: "year", count: 1, name: "1Y" },
            { timeUnit: "max", name: "Max" }
          ]
        }),
        am5stock.SeriesTypeControl.new(root, { stockChart: stockChart }),
        am5stock.ResetControl.new(root, { stockChart: stockChart })
      ]
    });

    // Generate sample OHLCV data
    var data = [];
    var open = 150;
    var date = new Date(2024, 0, 2);
    for (var i = 0; i < 120; i++) {
      // Skip weekends
      if (date.getDay() === 0) date.setDate(date.getDate() + 1);
      if (date.getDay() === 6) date.setDate(date.getDate() + 2);

      var change = (Math.random() - 0.48) * 4;
      var high = open + Math.random() * 3 + 1;
      var low = open - Math.random() * 3 - 1;
      var close = open + change;
      close = Math.max(low, Math.min(high, close));

      data.push({
        Date: date.getTime(),
        Open: Math.round(open * 100) / 100,
        High: Math.round(high * 100) / 100,
        Low: Math.round(low * 100) / 100,
        Close: Math.round(close * 100) / 100,
        Volume: Math.round(800000 + Math.random() * 1200000)
      });

      open = close;
      date = new Date(date);
      date.setDate(date.getDate() + 1);
    }

    // Set data LAST
    valueSeries.data.setAll(data);
    sbSeries.data.setAll(data);

    stockChart.appear(1000, 100);
  </script>
</body>
</html>
```

## More examples

Longer complete examples are in the amCharts MCP server's examples (https://mcp.amcharts.com/mcp; the same files are in the amcharts/amcharts5-mcp repository, under `extended/examples/`):
- Stock chart with volume panel and Bollinger Bands indicator: `get_example(path: "examples/stock/stock-chart-with-volume-and-bollinger-bands")`
