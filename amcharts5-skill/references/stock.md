# Stock & Financial Charts
Build financial stock charts with panels, indicators, and toolbar.


## Required imports

### ES modules
```ts
import * as am5 from "@amcharts/amcharts5";
import * as am5xy from "@amcharts/amcharts5/xy";
import * as am5stock from "@amcharts/amcharts5/stock";
import am5themes_Animated from "@amcharts/amcharts5/themes/Animated";
```

### CDN
```html
<script src="https://cdn.amcharts.com/lib/5/index.js"></script>
<script src="https://cdn.amcharts.com/lib/5/xy.js"></script>
<script src="https://cdn.amcharts.com/lib/5/stock.js"></script>
<script src="https://cdn.amcharts.com/lib/5/themes/Animated.js"></script>
```

## Core setup

```html
<div id="chartcontrols"></div>  <!-- toolbar container -->
<div id="chartdiv"></div>       <!-- chart container -->
```

```js
const root = am5.Root.new("chartdiv");
root.setThemes([am5themes_Animated.new(root)]);

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
  renderer: am5xy.AxisRendererY.new(root, {})
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

// 7. Cursor
mainPanel.set("cursor", am5xy.XYCursor.new(root, {
  yAxis: valueAxis,
  xAxis: dateAxis,
  behavior: "none",
  snapToSeries: [valueSeries]
}));

// 8. Scrollbar
const scrollbar = mainPanel.set("scrollbarX",
  am5xy.XYChartScrollbar.new(root, {
    orientation: "horizontal",
    height: 50
  })
);

// 9. Data — LAST
valueSeries.data.setAll(data);

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

## Data format

```js
var data = [
  { Date: new Date(2024, 0, 2).getTime(), Open: 150, High: 155, Low: 148, Close: 153, Volume: 1200000 },
  { Date: new Date(2024, 0, 3).getTime(), Open: 153, High: 158, Low: 151, Close: 156, Volume: 1350000 },
  // ...
];
```

**All date values must be timestamps (milliseconds).**

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

## Disposal

```js
root.dispose();
```

