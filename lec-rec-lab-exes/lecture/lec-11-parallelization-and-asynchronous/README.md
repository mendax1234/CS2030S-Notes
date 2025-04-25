# Lec 11 - Parallelization and Asynchronous

## Threads

A **thread** is a single path of execution within a program. Normally, when we run a Java program, it's single-threaded, which means it does one thing at a time. But sometimes, we want a program to do **multiple things at once**, like downloading a file while keeping the UI responsive. That’s where **threads** come in.

{% hint style="info" %}
1. Each thread runs independently, and multiple threads can run **concurrently**, or even **in parallel** (especially on multi-core CPUs).&#x20;
2. Threads can be used to handle **asynchronous operations**, like network requests or file I/O, **without blocking the main flow**.
{% endhint %}

### Create a Thread

This can be done using the `Thread` class with a `Runnable`. For example,

{% code lineNumbers="true" %}
```java
// Thread 1 prints _
new Thread(() -> {
    for (int i = 0; i < 50; i++) {
        System.out.print("_");
    }
}).start();

// Thread 2 prints *
new Thread(() -> {
    for (int i = 0; i < 50; i++) {
        System.out.print("*");
    }
}).start();
```
{% endcode %}

{% hint style="info" %}
`.start()` triggers the thread to run. **Don’t use** `.run()`, or it will just execute in the current thread like a normal method call.
{% endhint %}

#### Runnable

`Runnable` is a **functional interface** (has only one method: `run()`). It is used to define a task to be executed by a thread. It can be implemented using a **lambda expression** (as shown above). For example,

{% code lineNumbers="true" %}
```java
Runnable task = () -> System.out.println("Running in a thread");
Thread thread = new Thread(task);
thread.start();
```
{% endcode %}

### Name of Thread

Every thread has a **name**, we can use `getName()` to get the name of a thread, which can help us debug or understand how threads are being used. For example,

{% code lineNumbers="true" %}
```java
Thread thread = new Thread(() -> {
    System.out.println("This is running on: " + Thread.currentThread().getName());
});
thread.start();

// Output
// This is running on: Thread-0
```
{% endcode %}

<details>

<summary>How to decide which thread I am in now?</summary>

**Rule of Thumb**

1. If you're **inside a lambda or Runnable** which is passed to `new Thread(...)`, you're in that **new thread**.
2. If you're **outside** the `new Thread(...)`, e.g. in the main method you're usually in the **main thread**.

{% hint style="info" %}
Here, "you're" means the position of code you call `Thread.(whatever)`.
{% endhint %}

> You can always find out what thread you're in by using:
>
> {% code lineNumbers="true" %}
> ```java
> System.out.println(Thread.currentThread().getName());
> ```
> {% endcode %}

For example,

{% code overflow="wrap" lineNumbers="true" %}
```java
Thread findPrime = new Thread(() -> {
    System.out.println("Inside findPrime: " + Thread.currentThread().getName());
});

System.out.println("Before starting thread: " + Thread.currentThread().getName());

findPrime.start();

System.out.println("After starting thread: " + Thread.currentThread().getName());

// Output
// Before starting thread: main
// After starting thread: main
// Inside findPrime: Thread-0
```
{% endcode %}

</details>

To have a deeper glimpse of `getName()`, we can use a parallel stream example

{% tabs %}
{% tab title="Sequential Stream" %}
Sequential Stream means **single-threaded**.

{% code lineNumbers="true" %}
```java
IntStream.range(0, 5).forEach(i ->
    System.out.println(i + " on " + Thread.currentThread().getName()));
    
// Output
// 0 on main
// 1 on main
// 2 on main
// 3 on main
// 4 on main
```
{% endcode %}
{% endtab %}

{% tab title="Parallel Stream" %}
Parallel stream means **multi-threaded**.

{% code lineNumbers="true" %}
```java
IntStream.range(0, 5).parallel().forEach(i ->
    System.out.println(i + " on " + Thread.currentThread().getName()));
    
// Output (One possibility)

// 3 on ForkJoinPool.commonPool-worker-4
// 0 on ForkJoinPool.commonPool-worker-2
// 4 on ForkJoinPool.commonPool-worker-3
// 2 on main
// 1 on ForkJoinPool.commonPool-worker-1
```
{% endcode %}

{% hint style="info" %}
Here, we are calling `Thread.(whatever)` after `.parallel()`, which means it is called inside each thread!
{% endhint %}
{% endtab %}
{% endtabs %}

### Pause a Thread

This can be done using `Thread.sleep(milliseconds)`, which is a **static method** that pauses the [**current thread**](#user-content-fn-1)[^1] for a specified number of milliseconds. Things to note down when we pause a thread,

1. The thread is **put to sleep**, meaning it **temporarily stops execution**.
2. During that time, the thread doesn’t do anything — it’s like it’s napping.
3. After the time is up, the thread **becomes eligible** to run again, **but doesn't necessarily start immediately**. The **OS scheduler** decides when exactly it gets CPU time again.
4. While a thread is sleeping, **other threads can keep running**.

For example,

{% code lineNumbers="true" %}
```java
// findPrime Thread
Thread findPrime = new Thread(() -> {
  System.out.println(
      Stream.iterate(2, i -> i + 1)
          .filter(i -> isPrime(i))
          .limit(1_000_000L)
          .reduce((x, y) -> y)
          .orElse(null));
});

findPrime.start();

// main Thread
while (findPrime.isAlive()) {
  try {
    Thread.sleep(1000);
    System.out.print(".");
  } catch (InterruptedException e) {
    System.out.print("interrupted");
  }
}

// Output (One possibility)
// ............32452843
```
{% endcode %}

After Line 10, the `findPrime` thread will start doing some heavy computation at the **background**. And, within the `main`  thread, which contains a `while` loop,

* The `main` thread checks if `findPrime` is **still running** using `.isAlive()`.
* If yes, the `main` thread **sleeps for 1 second**, then prints `"."`.
* Repeats until the `findPrime` thread finishes.

## Asynchronous Programming

We have seen much above `Thread` from above. However, `Thread` may have the following limitations:

1. **no return value**: there are no method in `Thread` that returns a value.
2. **hard to specify the execution order**: there is no method that specifies which thread starts after another thread completes.
3. **overhead**: the creation and deletion of `Thread` in Java takes up "some"[^2] resources.

So, to overcome all these limitations, luckily, we have `CompletableFuture` in Java. In CS2030S, the use of `CompletableFuture` is to save us from the trouble of dealing with `Thread`.

### `CompletableFuture<T>`

Basically, `ComputableFuture<T>` is a [**monad**](../lec-10-monad-and-parallel-stream/#monad) that **encapsulates a value that is either there** or [not there _yet_](#user-content-fn-3)[^3]. (Just another wrapper like `Maybe`, `Lazy` we have learned before) Such an abstraction is also known as a **promise** in other languages — it encapsulates the **promise** to produce a value.

{% hint style="info" %}
A key property of `CompletableFuture` is whether the value it promises is **ready** — i.e., the tasks that it encapsulates have been _completed_ or not.
{% endhint %}

#### CompletableFuture Rule of Thumb

> 1. `CF(f).then(g)` means: **start** `g` **only after** `f` **has been>    &#x20;completed**.
>    1. Examples: `thenRun`, `thenCombine`, `thenApply`, etc.
> 2. `static CF.async(g)` means: **start** `g` **on a new thread**
>    1. Examples: `supplyAsync`, `runAsync`, etc.
> 3. `CF(f).then...async(g)` means: **start** `g` **only after** `f` **has been>    &#x20;completed, but use a new thread.**
>    1. Examples: `thenRunAsync`, `thenApplyAsync`, etc.
>
> Difference between `run` and `supply`: `run` executes a void> &#x20;function while `supply` executes a function with a return> &#x20;value.

#### Create a `CompletableFuture`

In the lecture, the following **four** methods are introduced to create a `CompletedFuture`.

{% stepper %}
{% step %}
**Use the** `completedFuture` **method**

Creates a `CompletableFuture` that is **already completed with the given value**.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<String> future = CompletableFuture.completedFuture("Result is ready immediately");
String result = future.get(); // No waiting, result is available right away
```
{% endcode %}

This is useful when you need to return a CompletableFuture but **already have the result**.
{% endstep %}

{% step %}
**Use the** `runAsync` **method that takes in a** `Runnable` **lambda expression**

Creates a `CompletableFuture` that runs the given `Runnable` asynchronously and completes when the `Runnable` finishes. Since `Runnable` doesn't return any value, the `CompletableFuture` completes with `null`.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
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

{% code overflow="wrap" lineNumbers="true" %}
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

Use this when you need to calculate something in the background and use the result later.
{% endstep %}

{% step %}
**Rely on other** `CompletableFuture` **instances**

The example given in CS2030s is `anyOf/allOf`. Basically, it creates a `CompletableFuture` that completes when **all** or **any of** the given futures complete.

**Example**

{% code lineNumbers="true" %}
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
{% endcode %}

The example of calling `anyOf()` and `allOf()` is shown as follows

{% tabs %}
{% tab title="allOf()" %}
{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<Void> allFuture = CompletableFuture.allOf(future1, future2);
allFuture.thenRun(() -> System.out.println("All futures completed"));
// Completes when both future1 and future2 complete
```
{% endcode %}

Use this when you need to wait for multiple independent operations to finish
{% endtab %}

{% tab title="anyOf()" %}
{% code overflow="wrap" lineNumbers="true" %}
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
`thenApply`, **which is analogous to** `map`

Transforms the result of a `CompletableFuture` using the provided function.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<String> result = future.thenApply(i -> "Result: " + i);
```
{% endcode %}

This is similar to `map` in FP[^4]. The `thenApply` method takes the result of the original future (10) and applies a function to transform it into a new value ("Result: 10"). The transformation happens in the same thread that completed the original future.
{% endstep %}

{% step %}
`thenCompose`, **which is analogous to** `flatMap`

Chains two `CompletableFuture` operations where the second operation depends on the result of the first.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<String> result = future.thenCompose(i -> 
    CompletableFuture.supplyAsync(() -> "Result: " + i));
```
{% endcode %}

This is similar to `flatMap` in FP. Unlike `thenApply`, which returns a simple value wrapped in a `CompletableFuture`, `thenCompose` expects a function that returns another `CompletableFuture`. This is useful when you have operations that depend on previous results and also need to be performed asynchronously.
{% endstep %}

{% step %}
`thenCombine`, **which is analogous to** `combine`

Combines the results of two independent `CompletableFuture` operations.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> 20);
CompletableFuture<String> combined = future1.thenCombine(future2, 
    (result1, result2) -> "Sum: " + (result1 + result2));
```
{% endcode %}

This waits for both futures to complete and then applies a function to their results. The function receives the results of both futures as parameters. In this example, it calculates the sum of the two results (10 + 20) and creates a string with the result ("Sum: 30").
{% endstep %}

{% step %}
`thenRun`

Executes an action after the CompletableFuture completes, without using its result.

**Example**

{% code lineNumbers="true" %}
```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    System.out.println("Calculating...");
    return 42;
});
CompletableFuture<Void> result = future.thenRun(() -> 
    System.out.println("Computation finished!"));
```
{% endcode %}

The `thenRun` method executes a Runnable after the current CompletableFuture completes, ignoring its result. This is useful for performing side effects like logging or notifications after an operation completes. In this example, it prints "Computation finished!" after the original future completes.
{% endstep %}

{% step %}
`runAfterBoth`

Executes an action after both the current CompletableFuture and another specified CompletableFuture complete.

**Example**

{% code lineNumbers="true" %}
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
{% endcode %}

This method waits for both the original future and another future to complete before running a specified action. The action doesn't use the results of either future. In this example, "Both tasks finished!" is printed only after both future1 and future2 have completed.
{% endstep %}

{% step %}
`runAfterEither`

Executes an action after either the current CompletableFuture or another specified CompletableFuture completes.

**Example**

{% code lineNumbers="true" %}
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
{% endcode %}

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

It may throw a couple of **checked exceptions**, which we **must** catch and handle.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");

try {
    String result = future1.get(); // Requires handling checked exceptions
    System.out.println(result);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```
{% endcode %}

* We create a CompletableFuture that completes with the string "Hello"
* We call `get()` to retrieve the result, which will block until the `future1` completes
* Since `get()` can throw checked exceptions, we **must handle them**:
  * `InterruptedException`: thrown if the current thread is interrupted while waiting
  * `ExecutionException`: thrown if the computation completed exceptionally (wraps the actual exception)
* After we get the result, we print it out
{% endstep %}

{% step %}
`join()`

It won't throw any **checked exception**.

**Example**

{% code overflow="wrap" lineNumbers="true" %}
```java
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");

String result = future2.join(); // No need to handle checked exceptions
System.out.println(result);
```
{% endcode %}

* We create a CompletableFuture that completes with the string "World"
* We call `join()` to retrieve the result, which will also block until the future completes
* Unlike `get()`, `join()` doesn't throw checked exceptions, so we **don't need** a try-catch block
* If the computation fails, `join()` will throw an unchecked `CompletionException` instead
* After we get the result, we print it out

{% hint style="success" %}
For the sake of **cleaner code**, we may prefer `.join()` over `.get()`.
{% endhint %}
{% endstep %}
{% endstepper %}

`get()`/`join()` is a **sychronous call**, i.e., it blocks until the `CompletableFuture` completes. So, to maximize concurrency, we should only call them **as the final step in our code**.

### Example

Given two numbers i and j, we want to find the difference between the i-th prime number and the j-th prime number. We can do the following:

{% code overflow="wrap" lineNumbers="true" %}
```java
int findIthPrime(int i) {
  return Stream
          .iterate(2, x -> x + 1)
          .filter(x -> isPrime(x))
          .limit(i)
          .reduce((x, y) -> y)
          .orElse(0);
}

// Create two threads for findIthPrime()
CompletableFuture<Integer> ith = CompletableFuture.supplyAsync(() -> findIthPrime(i));
CompletableFuture<Integer> jth = CompletableFuture.supplyAsync(() -> findIthPrime(j));

// Combine the result
CompletableFuture<Integer> diff = ith.thenCombine(jth, (x, y) -> x - y);

// Join(Get) the result
diff.join();
```
{% endcode %}

#### Handle Exceptions

During the chaining operations for our `CompletableFuture<T>`, we may get some exceptions. For example,

{% code lineNumbers="true" %}
```java
CompletableFuture.<Integer>supplyAsync(() -> null)
                 .thenApply(x -> x + 1)
                 .join();
```
{% endcode %}

There will be a `NullPointerException` after Line 2 and if we execute the code, we will get a [`CompletionException`](#user-content-fn-5)[^5]. So, instead of using a `try-catch` block to deal with such an exception, we can use the **inline** `.handle()`, which takes in a **Combiner**, to deal with such an exception. For example,

{% code lineNumbers="true" %}
```java
cf.thenApply(x -> x + 1)
  .handle((t, e) -> (e == null) ? t : 0)
  .join();
```
{% endcode %}

In Line 2, especially inside the Combiner,

1. the first parameter of the `Combiner` is the **value**,
2. the second is the **exception**,
3. the third is the **return value**.

Only **one** of them will be non-null:

* If everything went fine: `e == null`, so we return `t`.
* If an exception happened: `t == null`, `e != null`, so we return `0` as a fallback/default.

So instead of the program crashing or needing to catch the exception manually, we catch it inside the chain and return a safe value (`0` in this case).

## Fork and Join

Recall that creating and destroying threads is not cheap, and as much as possible we should reuse existing threads to perform different tasks. This goal can be achieved by using a _thread pool_.

### Thread Pool

A **thread pool** is a system that:

1. **Has some threads already created and waiting**.
2. **Holds tasks (units of work) in a queue**. (Usually, "task" is a `Runnable`, or `Supplier`)
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

These are **lambdas** that implement `Runnable` (because they have a `run()` method internally). They just print a number. (See more from [functional-interface.md](../lec-08-functional-programming/functional-interface.md "mention") if you are unfamiliar with the lambda expression for `Runnable`)

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

### Real Fork and Join

Imagine you have an array, you want to get the sum of each element, (I know you have lots of methods to do that :joy:), but you could just use **one loop (sequentially)** to do that, like&#x20;

{% code lineNumbers="true" %}
```java
int sum = 0;
for (int i = 0; i < array.length; i++) {
    sum += array[i];
}
```
{% endcode %}

But in **Fork/Join**, you want to speed this up by using **multiple threads**. So you split the task into parts and let **different threads work on the parts at the same time**, then combine (join) their results. In simple words,

> * **Fork**: Break the task into smaller tasks and run them _in parallel_ (with multiple threads).
> * **Join**: Wait for the results from the smaller tasks and **combine** them to get the final answer.

In Java, we can create a task that we can fork and join as an instance of the abstract class `RecursiveTask<T>`. It has an abstract method `compute()`, which we, as the client, have to define to specify what computation we want to compute. For example,

{% code overflow="wrap" lineNumbers="true" %}
```java
class Summer extends RecursiveTask<Integer> {
  private static final int FORK_THRESHOLD = 2;
  private int low;
  private int high;
  private int[] array;

  public Summer(int low, int high, int[] array) {
    this.low = low;
    this.high = high;
    this.array = array;
  }

  @Override
  protected Integer compute() {
    // stop splitting into subtask if array is already small.
    // Base case (small enough to be solved directly)
    if (high - low < FORK_THRESHOLD) {
      int sum = 0;
      for (int i = low; i < high; i++) {
        sum += array[i];
      }
      return sum;
    }
    
    // Recursive case (not small enough, still need to divide)
    int middle = (low + high) / 2;
    Summer left = new Summer(low, middle, array);
    Summer right = new Summer(middle, high, array);
    left.fork();  // Run left half in another thread (parallel)
    int rightResult = right.compute();  // Compute right half in this thread (sequential)
    int leftResult = left.join();  // Wait for the left thread to finish and get its result
    return leftResult + rightResult;  // Combine the two halves
  }
}
```
{% endcode %}

You define a class like `Summer` that extends `RecursiveTask<Integer>`. This means:

* You’re writing a task that eventually returns an Integer.
* You’ll define the `compute()` method, which decides whether to:
  * Split the work into smaller subtasks (`fork()`).
  * Or solve it directly if it’s small enough (base case).

<details>

<summary>Why call <code>left.fork()</code> and then <code>right.compute()</code>?</summary>

It’s a trick to optimize:

* You **fork** the left half to let another thread work on it.
* While that’s running, you immediately **compute** the right half yourself.
* Then you **join** to wait for the left to finish.

This avoids wasting time just waiting around — both halves get done in parallel.

</details>

To run it, we can use

{% tabs %}
{% tab title="Understand compute()" %}
{% code lineNumbers="true" %}
```java
Summer task = new Summer(0, array.length, array);
int sum = task.compute();
```
{% endcode %}

The line `task.compute()` above is just like another method invocation.
{% endtab %}

{% tab title="Understand multi-threads" %}
{% code lineNumbers="true" %}
```java
ForkJoinPool pool = new ForkJoinPool(4);
Summer task = new Summer(0, array.length, array);
int result = pool.invoke(task);
```
{% endcode %}

In Line 1, it creates a pool with 4 **threads**.
{% endtab %}
{% endtabs %}

#### Behind the scene of `pool.invoke(task)`

> * Each thread has a deque[^6] of tasks.
> * When a thread is idle, it checks its deque of tasks.
>   * If the deque is **not empty**, it picks up a task at the head of the deque to execute (e.g., invoke its `compute()` method).
>   * Otherwise, if the deque is **empty**, it picks up a task from the _**tail**_ of the deque of another thread to run. The latter is a mechanism called _work stealing_.
> * When `fork()` is called, the caller adds itself to the _**head**_ of the deque of the executing thread. This is done so that the most recently forked task gets executed next, similar to how normal recursive calls.
> * When `join()` is called, several cases might happen.
>   * If the subtask to be joined **hasn't been executed**, this subtaks will be **popped out first**, and then its `compute()` method is called and the subtask is executed.
>   * If the subtask to be joined **has been completed** (some other thread has stolen this and completed it), then the result is read, and `join()` returns.
>   * If the subtask to be joined has been stolen and is being executed by another thread, then the current thread either finds some other tasks to work on from its **local deque**, or steals another task from **another deque**.

For more examples, please find it in:

1. [#id-11-15.-forkjoinpool](diagnostic-quiz.md#id-11-15.-forkjoinpool "mention")

#### Order of `fork()` and `join()`

TL;DR, your `fork()`, `compute()`, `join()` order should form a [_palindrome_](https://en.wikipedia.org/wiki/Palindrome) and there should be no crossing. This is because the most recently forked task is likely to be executed next, we should `join()` the most recent `fork()` task first.

{% tabs %}
{% tab title="No Crossing (Correct)" %}
**Example 1**

```java
left.fork();  // >-----------+
right.fork(); // >--------+  | should have
return right.join() // <--+  | no crossing
     + left.join(); // <-----+
```

**Example 2**

```java
left.fork();  // >-----------+
return right.compute() //    | compute in middle
     + left.join(); // <-----+
```
{% endtab %}

{% tab title="Crossing (Incorrect)" %}
```java
left.fork();  // >-------------+
right.fork(); // >----------+  | there is crossing
return left.join()   // <---|--+
     + right.join(); // <---+
```
{% endtab %}
{% endtabs %}

[^1]: To find this thread, go back to [#how-to-decide-which-thread-i-am-in-now](./#how-to-decide-which-thread-i-am-in-now "mention")

[^2]: For the sake of this course, just treat it as "a lot of" LOL

[^3]: This means that the value is **not yet available**, but it **will be computed and available in the future —** once some asynchronous computation completes.

[^4]: "FP" stands for **F**unctional **P**rogramming.

[^5]: It’s a **runtime exception** thrown by `CompletableFuture` methods like `.join()` when the computation failed.

[^6]: double-ended queue
