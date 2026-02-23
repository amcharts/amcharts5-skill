# Events — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/events/ -->

## User Interaction Events

### Adding a Handler

Use `events.on(eventName, handler)` on any element or its template:

```javascript
columnSeries.columns.template.events.on("click", function(ev) {
  console.log("Clicked on a column", ev.target);
});
```

To fire only once, use `events.once()`:

```javascript
columnSeries.columns.template.events.once("click", function(ev) {
  console.log("Clicked on a column", ev.target);
});
```

### Removing a Handler

Use `events.off()` — requires a named function reference (anonymous functions cannot be removed):

```javascript
function handleColumnClick(ev) {
  console.log("Clicked on a column", ev.target);
}

columnSeries.columns.template.events.on("click", handleColumnClick);
columnSeries.columns.template.events.off("click", handleColumnClick);
```

### Disabling / Enabling Events

Temporarily disable a specific event type:

```javascript
columnSeries.columns.template.events.disableType("click");
columnSeries.columns.template.events.enableType("click");
```

Disable / enable all event handlers on an element:

```javascript
columnSeries.columns.template.events.disable();
columnSeries.columns.template.events.enable();
```

## Behavioral Events

Some elements emit events on internal state changes, not user interaction. For example, `Scrollbar` emits `rangechanged` when its selection range changes (whether by dragging or programmatically). Check the element's class reference for its full event list.

## Debounced Events

Available since v5.14. Use `events.onDebounced(eventName, handler, timeout)` to fire the handler only after the event has stopped for the specified number of milliseconds:

```javascript
series.events.onDebounced("valueschanged", function(ev) {
  // runs only after 500ms of inactivity since the last "valueschanged" event
}, 500);
```

## Settings Value Change Events

### Watching a Setting

Use `element.on(settingKey, handler)` to react when a specific setting value changes:

```javascript
series.on("visible", function(visible, target) {
  if (visible) {
    console.log("Series shown", target);
  } else {
    console.log("Series hidden", target);
  }
});
```

For private settings, use `element.onPrivate(settingKey, handler)`:

```javascript
xAxis.onPrivate("selectionMin", function(value, target) {
  console.log("Start date changed:", new Date(value));
});

xAxis.onPrivate("selectionMax", function(value, target) {
  console.log("End date changed:", new Date(value));
});
```

### Removing Setting Watchers

```javascript
// Remove specific callback for a setting
series.off("visible", seriesVisibilityChange);

// Remove all handlers for a private setting key
xAxis.offPrivate("selectionMin");
xAxis.offPrivate("selectionMax");
```

If the callback parameter is omitted from `off()` / `offPrivate()`, all handlers for that key are removed.
