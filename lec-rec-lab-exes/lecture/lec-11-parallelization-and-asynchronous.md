# Lec 11 - Parallelization and Asynchronous

## Threads

## Asynchronous Programming

We have seen much above `Thread` from above. However, `Thread` may have the following limitations:

1. **no return value**: there are no method in `Thread` that returns a value.
2. **hard to specify the execution order**: there is no method is specify which thread starts after another thread completes.
3. **overhead**: the creation and deletion of `Thread` in Java takes up "some"[^1] resources.

So, to overcome all these limitations, luckily, we have `CompletableFuture` in Java. In CS2030S, the use of `CompletableFuture` is to save us from the trouble of dealing with `Thread`.

### `CompletableFuture<T>`

Basically, `ComputableFuture<T>` is a [**monad**](lec-10-monad-and-parallel-stream.md#monad) that encapsulates a value that is either there or [not there _yet_](#user-content-fn-2)[^2]. Such an abstraction is also known as a **promise** in other languages — it encapsulates the **promise** to produce a value.

{% hint style="info" %}
A key property of `CompletableFuture` is whether the value it promises is ready — i.e., the tasks that it encapsulates have been _completed_ or not.
{% endhint %}

#### Create a `CompletableFuture`

In the lecture, the following **four** methods are introduced to create a `CompletedFuture`.

{% stepper %}
{% step %}
**Use the** `completedFuture` **method**

Creates a `CompletableFuture` that is already completed with the given value.

**Example**

{% code overflow="wrap" %}
```java
CompletableFuture<String> future = CompletableFuture.completedFuture("Result is ready immediately");
String result = future.get(); // No waiting, result is available right away
```
{% endcode %}

This is useful when you need to return a CompletableFuture but already have the result
{% endstep %}

{% step %}
**Use the** `runAsync` **method that takes in a** `Runnable` **lambda expression**

Creates a `CompletableFuture` that runs the given `Runnable` asynchronously and completes when the `Runnable` finishes. Since `Runnable` doesn't return any value, the `CompletableFuture` completes with `null`.

**Example**

{% code overflow="wrap" %}
```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    System.out.println("Async task running on thread: " + Thread.currentThread().getName());
    try {
        Thread.sleep(1000); // Simulating work
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    System.out.println("Async task completed");
});

future.join(); // Wait for completion
// The task runs in a separate thread from the ForkJoinPool.commonPool(). 
```
{% endcode %}

Use this when you need to perform an operation asynchronously but don't need a result
{% endstep %}

{% step %}
**Use the** `supplyAsync` **method that takes in a** `Supplier<T>` **lambda expression**

Creates a `CompletableFuture` that computes a result asynchronously using the given Supplier and completes with that **result**.

**Example**

{% code overflow="wrap" %}
```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    System.out.println("Computing value on thread: " + Thread.currentThread().getName());
    try {
        Thread.sleep(1000); // Simulating computation
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    return 42; // This value will be returned when future completes
});
// The key difference from runAsync is that this returns a value
// Perfect for when you need to calculate something in the background and use the result later

Integer result = future.get(); // Will wait for computation to complete
// The key difference from runAsync is that this returns a value.
```
{% endcode %}

&#x20;Use this when you need to calculate something in the background and use the result later.
{% endstep %}

{% step %}
**Rely on other** `CompletableFuture` **instances**

The example given in CS2030s is `anyOf/allOf`. Basically, it creates a `CompletableFuture` that completes when **all** or **any of** the given futures complete.

**Example**

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
    sleep(2000); // This takes longer
    return "First result";
});

CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    sleep(1000); // This completes faster
    return "Second result";
});
```

The example of calling `anyOf()` and `allOf()` is shown as follows

{% tabs %}
{% tab title="allOf()" %}
{% code overflow="wrap" %}
```java
CompletableFuture<Void> allFuture = CompletableFuture.allOf(future1, future2);
allFuture.thenRun(() -> System.out.println("All futures completed"));
// Completes when both future1 and future2 complete
```
{% endcode %}

Use this when you need to wait for multiple independent operations to finish
{% endtab %}

{% tab title="anyOf()" %}
{% code overflow="wrap" %}
```java
// Completes when either future1 or future2 completes (whichever is first)
CompletableFuture<Object> anyFuture = CompletableFuture.anyOf(future1, future2);
anyFuture.thenAccept(result -> System.out.println("First completed result: " + result));
// In this case, "Second result" would print first because future2 completes faster
```
{% endcode %}

Use this when implementing timeouts or getting the fastest result from multiple sources.
{% endtab %}
{% endtabs %}
{% endstep %}
{% endstepper %}

After creating a `CompletableFuture`, we often chain them together.

#### Chain `CompletableFuture`

The usefulness of `CompletableFuture` comes from the ability to chain them up and specify a sequence of computations to be run. We have the following methods:

{% stepper %}
{% step %}
`thenApply`, which is analogous to `map`

Transforms the result of a `CompletableFuture` using the provided function.

**Example**

```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<String> result = future.thenApply(i -> "Result: " + i);
```

This is similar to `map` in FP[^3]. The `thenApply` method takes the result of the original future (10) and applies a function to transform it into a new value ("Result: 10"). The transformation happens in the same thread that completed the original future.
{% endstep %}

{% step %}
`thenCompose`, which is analogous to `flatMap`

Chains two `CompletableFuture` operations where the second operation depends on the result of the first.

**Example**

```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<String> result = future.thenCompose(i -> 
    CompletableFuture.supplyAsync(() -> "Result: " + i));
```

This is similar to `flatMap` in FP. Unlike `thenApply`, which returns a simple value wrapped in a `CompletableFuture`, `thenCompose` expects a function that returns another `CompletableFuture`. This is useful when you have operations that depend on previous results and also need to be performed asynchronously.
{% endstep %}

{% step %}
`thenCombine`, which is analogous to `combine`

Combines the results of two independent `CompletableFuture` operations.

**Example**

```java
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> 20);
CompletableFuture<String> combined = future1.thenCombine(future2, 
    (result1, result2) -> "Sum: " + (result1 + result2));
```

This waits for both futures to complete and then applies a function to their results. The function receives the results of both futures as parameters. In this example, it calculates the sum of the two results (10 + 20) and creates a string with the result ("Sum: 30").
{% endstep %}

{% step %}
`thenRun`

Executes an action after the CompletableFuture completes, without using its result.

**Example**

```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    System.out.println("Calculating...");
    return 42;
});
CompletableFuture<Void> result = future.thenRun(() -> 
    System.out.println("Computation finished!"));
```

The `thenRun` method executes a Runnable after the current CompletableFuture completes, ignoring its result. This is useful for performing side effects like logging or notifications after an operation completes. In this example, it prints "Computation finished!" after the original future completes.
{% endstep %}

{% step %}
`runAfterBoth`

Executes an action after both the current CompletableFuture and another specified CompletableFuture complete.

**Example**

```java
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
    sleep(1000);
    System.out.println("First task completed");
    return 10;
});
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    sleep(2000);
    System.out.println("Second task completed");
    return "Result";
});
CompletableFuture<Void> result = future1.runAfterBoth(future2, () -> 
    System.out.println("Both tasks finished!"));
```

This method waits for both the original future and another future to complete before running a specified action. The action doesn't use the results of either future. In this example, "Both tasks finished!" is printed only after both future1 and future2 have completed.
{% endstep %}

{% step %}
`runAfterEither`

Executes an action after either the current CompletableFuture or another specified CompletableFuture completes.

**Example**

```java
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
    sleep(2000);
    System.out.println("Slow task completed");
    return 10;
});
CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> {
    sleep(1000);
    System.out.println("Fast task completed");
    return 20;
});
CompletableFuture<Void> result = future1.runAfterEither(future2, () -> 
    System.out.println("At least one task is done!"));
```

This method runs an action as soon as either the original future or another specified future completes. In this example, since future2 completes faster (1 second vs 2 seconds), "At least one task is done!" will print after "Fast task completed" and before "Slow task completed".
{% endstep %}
{% endstepper %}

{% hint style="info" %}
All these methods have asynchronous versions (`thenApplyAsync`, `thenComposeAsync`, `thenCombineAsync`, `thenRunAsync`, `runAfterBothAsync`, `runAfterEitherAsync`) that execute the provided functions or actions in a different thread, potentially increasing concurrency.
{% endhint %}

After we chain our `CompletableFuture`, we are more interested in getting the result right! Till now, the process is also called **set-up** the tasks to run asynchronously.

#### Get the result

There are two methods to get the result,

{% stepper %}
{% step %}
`get()`

It may throw a couple of **checked exceptions**, which we need to catch and handle.
{% endstep %}

{% step %}
`join()`

It won't throw any **checked exception**.
{% endstep %}
{% endstepper %}

`get()`/`join()` is a **sychronous call**, i.e., it blocks until the `CompletableFuture` completes. So, to maximize concurrency, we should only call them **as the final step in our code**.

### Example

1. Create one instance of `CompletableFuture` is equal to create a thread.

#### Handle Exceptions

## Fork and Join

Recall that creating and destroying threads is not cheap, and as much as possible we should reuse existing threads to perform different tasks. This goal can be achieved by using a _thread pool_.

### Thread Pool

A **thread pool** is a system that:

1. **Has some threads already created and waiting**.
2. **Holds tasks (units of work) in a queue**.
3. **Lets threads pick tasks from the queue and execute them**.
4. **Reuses threads** instead of creating a new one every time.

This avoids the overhead of constantly creating and destroying threads, which is expensive.

***

To understand the concept better, let's use a small example,

{% stepper %}
{% step %}
**Create a simple Thread Pool**

{% code lineNumbers="true" %}
```java
Queue<Runnable> queue;
new Thread(() -> {
  while (true) {
    if (!queue.isEmpty()) {
      Runnable r = queue.dequeue();
      r.run();
    }
  }
}).start();
```
{% endcode %}

In this simple **thread pool**, we have the following:

* `queue`: A **shared task queue** where all tasks are added.
* `Runnable`: Each **task** is a `Runnable`, which means **it can be run by a thread**.
* `new Thread(...)`: This **creates a single worker thread** (the thread pool only has 1 thread in this simple example).
* `while (true) {...}`: This thread runs **forever**, constantly checking the queue.
* `queue.dequeue() + r.run()`: If there’s a task, it **takes it out** of the queue and **executes** it.

More vividely speaking, you can think it of as

* One person (thread) standing by.
* There's a basket (queue) where you keep dropping small notes (tasks).
* The person picks up a note when available, and does what it says.
{% endstep %}

{% step %}
**Add tasks to the Thread Pool**

```java
for (int i = 0; i < 100; i++) {
  int count = i;
  queue.add(() -> System.out.println(count));
}
```

This loop is creating **100 tasks**, where each task is:

```java
() -> System.out.println(count)
```

These are **lambdas** that implement `Runnable` (because they have a `run()` method internally). They just print a number.

***

So what happens is:

* You enqueue 100 print tasks.
* The single worker thread goes through them one by one, running each.
{% endstep %}
{% endstepper %}

To summarize, the following is the **general working mechanism** of **Thread Pool** in Java

1. **You create a thread pool** — this includes:
   * One or more **worker threads** (which will run in the background),
   * A **task queue** where jobs (tasks) are temporarily stored.
2. **You submit tasks** to the thread pool — these tasks get **queued** first.
3. **The threads in the pool pick up tasks from the queue** and run them **one by one** (or in parallel, depending on how many threads there are).

[^1]: For the sake of this course, just treat it as "a lot of" LOL

[^2]: This means that the value is **not yet available**, but it **will be computed and available in the future**—once some asynchronous computation completes.

[^3]: "FP" stands for **F**unctional **P**rogramming.
