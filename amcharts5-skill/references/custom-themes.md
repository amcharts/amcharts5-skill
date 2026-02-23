# Creating Custom Themes — amCharts 5
<!-- https://www.amcharts.com/docs/v5/concepts/themes/creating-themes/ -->

Custom themes are reusable sets of settings, events, and adapters applied automatically to targeted chart elements.

## Theme Structure

Extend `am5.Theme` and define all rules in `setupDefaultRules()`:

```javascript
class MyTheme extends am5.Theme {
  setupDefaultRules() {
    // rules go here
  }
}
```

Apply it like any built-in theme:

```javascript
root.setThemes([
  am5themes_Animated.new(root),
  MyTheme.new(root)
]);
```

## Rules

Rules target elements by class name and optionally by theme tags, then apply settings, events, adapters, or states to all matching elements.

### Creating a Rule

Use `this.rule(className)` — returns a template you can call `.setAll()`, `.set()`, `.events.on()`, `.adapters.add()`, or `.states.create()` on:

```javascript
class MyTheme extends am5.Theme {
  setupDefaultRules() {

    // Settings
    this.rule("Label").setAll({
      fontSize: 12,
      fill: am5.color(0x777777)
    });

    // Settings on a specific element type
    this.rule("AxisRenderer").setAll({
      minGridDistance: 30
    });

    // Events
    this.rule("Bullet").events.on("pointerover", function(event) {
      console.log("Pointer over bullet!", event.target);
    });

    // Adapters
    this.rule("LineSeries").adapters.add("stroke", function(stroke, target) {
      return am5.color(0x881133);
    });

    // States
    this.rule("AxisLabel").states.create("hover", {
      fontWeight: "bold"
    });

    // Setup function (runs when a new matching element is created)
    this.rule("AxisLabel").setup = function(target) {
      target.set("background", am5.Rectangle.new(this._root, {
        fill: am5.color(0xffffff),
        fillOpacity: 0.5
      }));
    };

  }
}
```

## Targeting Elements

### By Class Name

Pass the element's class name as the first argument to `rule()`. Each element supports its own class name and all ancestor class names, allowing broad or narrow targeting:

```javascript
this.rule("AxisRendererY").setAll({ ... }); // vertical axis renderers only
this.rule("AxisRenderer").setAll({ ... });  // all axis renderers
```

### By Theme Tags (Narrowing)

Pass an array of tags as the second argument to narrow the target:

```javascript
// All labels
this.rule("Label").setAll({ fontSize: 12 });

// Only axis labels
this.rule("Label", ["axis"]).setAll({ fontSize: 12 });

// Only horizontal axis labels
this.rule("Label", ["axis", "x"]).setAll({ fontSize: 12 });
```

When multiple tags are specified, only elements that have **all** listed tags are targeted.

> **Tags are inheritable.** An element inherits the theme tags of all its ancestors. For example, axis labels inherit the `"axis"` tag from their parent axis.

### Custom Tags

Add custom tags to any element via `themeTags`, then target them in a rule:

```javascript
// In the theme
this.rule("AxisLabel", ["my-axis-label"]).setAll({
  fontSize: 12,
  fill: am5.color(0x777777)
});

// On the element
xAxis.get("renderer").labels.template.setAll({
  themeTags: ["my-axis-label"]
});
```

### Non-Inheritable Tags

Use `themeTagsSelf` to set tags that are **not** passed down to child elements:

```javascript
am5.Container.new(root, {
  themeTagsSelf: ["non-inheritable-tag"]
});
```

This is useful when targeting a container but not its children of the same class (e.g. legend item containers).
