# Template Fields — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/settings/template-fields/ -->

Template fields bind an element's settings to data. Any element that has a data item can use a field in that data item to supply setting overrides for that specific element instance.

## How It Works

Set `templateField` on an element's template to name the data field that contains per-item setting overrides. When the element is rendered for a data item, any settings in that field are applied on top of the template defaults.

## Usage

Set `templateField` on the element template (e.g. `columns.template`, `slices.template`):

```javascript
series.slices.template.setAll({
  templateField: "sliceSettings"
});
```

Then include the named key in each data item with an object of setting overrides:

```javascript
series.data.setAll([
  {
    country: "France",
    sales: 100000,
    sliceSettings: {
      fill: am5.color(0xd6e681),
      stroke: am5.color(0xd6e681)
    }
  },
  {
    country: "Spain",
    sales: 160000,
    sliceSettings: {
      fill: am5.color(0xbabf95),
      stroke: am5.color(0xbabf95)
    }
  },
  {
    country: "United Kingdom",
    sales: 80000,
    sliceSettings: {
      fill: am5.color(0xc4ad83),
      stroke: am5.color(0xc4ad83)
    }
  }
]);
```

## Column Series Example

```javascript
series.columns.template.setAll({
  fillOpacity: 0.5,
  strokeWidth: 2,
  templateField: "columnSettings"
});

series.data.setAll([
  { date: new Date(2021, 0, 1).getTime(), value: 1000, columnSettings: { fill: am5.color(0xd6e681) } },
  { date: new Date(2021, 0, 2).getTime(), value: 800,  columnSettings: { fill: am5.color(0xbabf95) } },
  { date: new Date(2021, 0, 3).getTime(), value: 700,  columnSettings: { fill: am5.color(0xc4ad83) } },
  { date: new Date(2021, 0, 4).getTime(), value: 1200, columnSettings: { fill: am5.color(0xc6b677) } },
  { date: new Date(2021, 0, 5).getTime(), value: 740,  columnSettings: { fill: am5.color(0xdbb957) } }
]);
```

## Key Points

- `templateField` is set on the **template** (`slices.template`, `columns.template`), not on the series itself
- The data field value must be an **object** of valid settings for that element type
- Template field settings override template defaults for that specific data item only
- Most commonly used with series elements (slices, columns, bullets) since series are the primary consumers of data
