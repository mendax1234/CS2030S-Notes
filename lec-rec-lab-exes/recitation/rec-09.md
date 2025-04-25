# Rec 09

## Problems

### 1. ForkJoinPool Example

> 1. When a current workder hits `task.join()`, the current worker will become **idle.**

### **2. Difference between `fork()`, `join()` and `compute()`**

> `task.fork()` will actually let `task` to be completed by another worker[^1].

{% tabs %}
{% tab title="Option 1" %}
{% code lineNumbers="true" %}
```java
f1.fork();
int a = f2.compute();
int b = f1.join();
return a + b;
```
{% endcode %}

The task forks `f1` for another worker to complete, while completing `f2` by itself in the meantime.
{% endtab %}

{% tab title="Option 2" %}
{% code lineNumbers="true" %}
```java
f1.fork();
int a = f1.join();
int b = f2.compute();
return a + b;
```
{% endcode %}

The task forks `f1` for another worker to complete, and waits for it to complete first, before completing `f2` by itself. There is no parallelism in this version.
{% endtab %}

{% tab title="Option 3" %}
{% code lineNumbers="true" %}
```java
int a = f1.compute();
int b = f2.compute();
return a + b;
```
{% endcode %}

This task completes both subtasks `f1` and `f2` by itself, sequentially.
{% endtab %}

{% tab title="Option 4" %}
{% code lineNumbers="true" %}
```java
f1.fork();
f2.fork();
int a = f2.join();
int b = f1.join();
return a + b;
```
{% endcode %}

This task forks both subtasks `f1` and `f2` to be run in parallel. Then waits for the subtasks to\
complete.
{% endtab %}

{% tab title="Option 5" %}
{% code lineNumbers="true" %}
```java
f1.fork();
f2.fork();
int a  int a = f1.join();
int b = f2.join();
return a + b;
```
{% endcode %}

Same as the Option 4. But there is a "crossing" and is less efficient.
{% endtab %}
{% endtabs %}

[^1]: another way of saying **thread**.
