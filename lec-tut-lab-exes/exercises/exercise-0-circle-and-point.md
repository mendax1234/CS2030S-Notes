# Exercise 0 - Circle and Point

## Tips

### `this` and `CLASS`

When we encapsulate a class, it is recommended to use `this` before any instance field in this class. And `CLASS.` before any static/class field. This is to differentiate them from the local variables.

### The practice of "Tell, Don't Ask"

When we design the `.contains()` method attached to a circle, we should keep the rule following in mind:

> We never ask an object to spit out its own **raw** **data.** Instead, we let the object know what we want and ask for the **processed data**.

So, our implement will be

{% code title="Circle.java" lineNumbers="true" %}
```java
public boolean contains(Point p) {
  return this.c.distanceTo(p) <= this.r;
}
```
{% endcode %}

{% code title="Point.java" overflow="wrap" lineNumbers="true" %}
```java
import java.lang.Math;

public double distanceTo(Point p) {
  return Math.sqrt((this.x - p.x) * (this.x - p.x) + (this.y - p.y) * (this.y - p.y));
}
```
{% endcode %}
