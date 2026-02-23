# Adapters — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/settings/adapters/ -->

Adapters are custom functions that dynamically alter the value of an element's setting at render time.

## Adding an Adapter

Use `adapters.add(key, function)` directly on an object or its template:

```javascript
series.columns.template.adapters.add("fill", function(fill, target) {
  if (target.dataItem.get("valueY") < 1000) {
    return am5.color(0xff621f);
  }
  return fill;
});
```

- First parameter: the setting key to intercept
- Second parameter: a function `(currentValue, targetElement) => newValue`
- The function runs every time that setting value is requested
- Return the original value unchanged to keep the default

## Removing an Adapter

### Via `dispose()` on the returned Disposer

`adapters.add()` returns a `Disposer` object:

```javascript
let myAdapter = series.columns.template.adapters.add("fill", function(fill, target) {
  if (target.dataItem.get("valueY") < 1000) {
    return am5.color(0xff621f);
  }
  return fill;
});

myAdapter.dispose(); // removes this adapter
```

### Via `remove()`

```javascript
series.columns.template.adapters.remove("fill");
```

Simpler, but removes **all** adapters for that key on the target.

## Disabling / Enabling

Temporarily disable an adapter without removing it:

```javascript
series.columns.template.adapters.disable("fill");
```

Re-enable it:

```javascript
series.columns.template.adapters.enable("fill");
```

## Mutating Other Settings Inside an Adapter

Adapters can also call `target.set()` to update other settings on the element as a side effect:

```javascript
series.columns.template.adapters.add("fill", function(fill, target) {
  if (target.dataItem.get("valueY") < 0) {
    target.set("tooltipY", am5.p100); // move tooltip to bottom for negative columns
    return am5.color(0xff0000);
  }
  return fill;
});
```
