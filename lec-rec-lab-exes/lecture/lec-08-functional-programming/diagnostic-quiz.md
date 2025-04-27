# Diagnostic Quiz

## Problems

### 01. Pure Functions

> Pure functions can take in **no** input, but every time they are given the same input, we must get the same output!

For example, the following is not a **pure function** because it doesn't give us the same **output** every time we call the function.

```java
public long foo() {
  // Returns the current time in milliseconds. 
  return System.currentTimeMillis(); 
}
```

### 02. Functional Interface

> 1. A functional interface must have **exactly one abstract method**, it can have any number of helpers (constants, [static/default methods](#user-content-fn-1)[^1]). It can extend from another class, but if the parent class has multiple **abstract method**, then the interface is **no longer** a **functional interface**.
> 2. All the **fields** in an **interface** are treated as `public static final` by default.

### 04. Variable Capture in Lambda Expression

> The lambda can only use variables that are either its own parameters (e.g., `x` in `x -> ...`) or variables in scope (like `y` in this case, this is done by variable capture).

<figure><img src="../../../.gitbook/assets/lec08-quiz-04.png" alt="" width="563"><figcaption></figcaption></figure>

In Java, lambda expressions can "capture" variables from their enclosing scope, meaning they can use variables that are not defined as parameters of the lambda itself. However, there are strict rules for how this works:

1. **Instance or Static Variables**: Lambda expressions can freely capture instance or static variables of the enclosing class without restrictions.
2. **Local Variables**: If a lambda captures a local variable (a variable declared in the enclosing method), that variable must be **effectively final**. This means the variable must either be explicitly marked as final or must not be modified after its initialization.
3. **Parameters of the Lambda**: Variables declared as parameters of the lambda (e.g., `x` in `x -> ...`) are local to the lambda and can be used freely within its body.
4. **Shadowing**: If a lambda parameter has the same name as a variable in the enclosing scope, the lambda parameter "shadows" the outer variable, meaning the lambda uses its own parameter instead of the outer variable.

An example code is shown as follows:

{% code overflow="wrap" lineNumbers="true" %}
```java
import java.util.function.Function;
import java.util.function.Supplier;
import java.util.function.BiFunction;

public class LambdaCaptureExample {
    // Instance variable (like the `int y;` in the question)
    int y = 10;

    public void demonstrateCapture() {
        // Local variable that is final
        final int localZ = 5;

        // Local variable that is effectively final (not modified after initialization)
        int localW = 20;

        // Local variable that is NOT effectively final (will cause an error if captured)
        int localX = 30;
        localX++; // Modifying localX makes it not effectively final

        // 1. Lambda capturing instance variable `y` (valid)
        // This is similar to `x -> y + 1` from the question
        Function<Integer, Integer> lambda1 = x -> y + 1;
        System.out.println("Lambda1 (x -> y + 1) with x = 5: " + lambda1.apply(5)); // y = 10, so 10 + 1 = 11

        // 2. Lambda capturing instance variable `y` with no parameters
        // This is similar to `() -> y;` from the question
        Supplier<Integer> lambda2 = () -> y;
        System.out.println("Lambda2 (() -> y): " + lambda2.get()); // y = 10, so returns 10

        // 3. Lambda using its own parameters, shadowing `y`
        // This is similar to `(x, y) -> x + y;` from the question
        BiFunction<Integer, Integer, Integer> lambda3 = (x, y) -> x + y;
        System.out.println("Lambda3 ((x, y) -> x + y) with x = 3, y = 4: " + lambda3.apply(3, 4)); // 3 + 4 = 7
        // Note: The `y` in the lambda parameters shadows the instance variable `y`

        // 4. Lambda capturing a local variable (valid because localZ is effectively final)
        Function<Integer, Integer> lambda4 = a -> a + localZ;
        System.out.println("Lambda4 (a -> a + localZ) with a = 2: " + lambda4.apply(2)); // localZ = 5, so 2 + 5 = 7

        // 5. Lambda capturing a local variable (valid because localW is effectively final)
        Function<Integer, Integer> lambda5 = b -> b + localW;
        System.out.println("Lambda5 (b -> b + localW) with b = 3: " + lambda5.apply(3)); // localW = 20, so 3 + 20 = 23

        // 6. Lambda capturing a local variable (INVALID because localX is not effectively final)
        // This will cause a compilation error if uncommented
        // Function<Integer, Integer> lambda6 = c -> c + localX; // Error: "local variables referenced from a lambda expression must be final or effectively final"
    }

    public static void main(String[] args) {
        LambdaCaptureExample example = new LambdaCaptureExample();
        example.demonstrateCapture();
    }
}
```
{% endcode %}

### \*09. Function Interface Implementation with Method Reference

> 1. In Method Referencing, if the thing before `::` is an **instance**, it **does not** need to be **effectively final**.
> 2. In `A::foo`, if `foo` is an **instance method**, it will use the **first** input as the **instance**, and pass the remaining **inputs** as arguments. Otherwise, it will call the **class** method and pass **all inputs** as arguments.
> 3. The **number of inputs** is decided in the **functional interface**'s abstract metho&#x64;**.** It is the same as the number of **parameters** in that abstract method.
> 4. Include the steps to solve this kind of question into Cheatsheet.

This is a very tricky question, but use what we have summarized from lectures above, we should be able to solve it.

<figure><img src="../../../.gitbook/assets/lec08-quiz-09.png" alt="" width="563"><figcaption></figcaption></figure>

{% stepper %}
{% step %}
**Determine the number of inputs according to the functional interface**

For example, for interface `F<T>`, the number of input is 1. Similarly, for `G<S, T>`, the number of input is 2. Thus, their corresponding lambda will be `x -> ...` and `(x, y) -> ...`.
{% endstep %}

{% step %}
**Use the rule to rewrite the normal lambda to see if it compiles**

For example, the logic for the third option is

1. `(x, y) -> x.foo(y)`, since `foo` is an **instance method** and it will use the first input `x` as the **instance**.
{% endstep %}
{% endstepper %}

## Tips

1. Pure functions can take in **no** input, but every time they are given the same input, we must get the same output!
2. **Functional Interface Requirements**
   1. A functional interface must have **exactly one abstract method**, it can have any number of helpers (constants, [static/default methods](#user-content-fn-1)[^1]). It can extend from another class, but if the parent class has multiple **abstract method**, then the interface is **no longer** a **functional interface**.
   2. All the **fields** in an **interface** are treated as `public static final` by default.
3. The lambda can only use variables that are either its own parameters (e.g., `x` in `x -> ...`) or variables in scope (like `y` in this case, this is done by variable capture).
4. **Method Referencing Rule of Thumb**
   1. In Method Referencing, if the thing before `::` is an **instance**, it **does not** need to be **effectively final**.
   2. In `A::foo`, if `foo` is an **instance method**, it will use the **first** input as the **instance**, and pass the remaining **inputs** as arguments. Otherwise, it will call the **class** method and pass **all inputs** as arguments.
   3. The **number of inputs** is decided in the **functional interface**'s abstract metho&#x64;**.** It is the same as the number of **parameters** in that abstract method.
5. **Method Referencing Solving Steps**
   1. Determine the **number of inputs** according to the functional interface, then your lambda will be like `(x, y, ..) -> ...`, the L.H.S is the **number of inputs**
   2. Use the method referencing rule of thumb to rewrite the normal lambda to see if 1) number of parameters matches 2) type matches

[^1]: This is called [**impure interface**](https://nus-cs2030s.github.io/2425-s2/18-interface.html#impure-interfaces), and it is avoided in CS2030S.
