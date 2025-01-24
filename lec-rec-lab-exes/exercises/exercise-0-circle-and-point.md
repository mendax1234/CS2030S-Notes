# Exercise 0 - Circle and Point

## Tips

### `this` and `CLASS`

When we encapsulate a class, it is recommended to use `this` before any instance field in this class and `CLASS.` before any static/class field. This is to differentiate them from the local variables.

### The practice of "Tell, Don't Ask"

When we design the `.contains()` method attached to a circle, we should keep [the principle of "Tell, don't ask"](../lab/lab-01-logistics-introduction-to-oop-exercise-0.md#tell-dont-ask) in mind. So, our implementation will be:

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
  double x = this.x - p.x;
  double y = this.y - p.y;
  return Math.sqrt(x * x + y * y);
}
```
{% endcode %}
