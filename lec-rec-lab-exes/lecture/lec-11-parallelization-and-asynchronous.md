# Lec 11 - Parallelization and Asynchronous

## Threads

## Asynchronous Programming

We have seen much above `Thread` from above. However, `Thread` may have the following limitations:

1. **no return value**: there are no method in `Thread` that returns a value.
2. **hard to specify the execution order**: there is no method is specify which thread starts after another thread completes.
3. **overhead**: the creation and deletion of `Thread` in Java takes up "some"[^1] resources.

So, to overcome all these limitations, luckily, we have `CompletableFuture` in Java.

### `CompletableFuture<T>`

Basically, `ComputableFuture<T>` is a [**monad**](lec-10-monad-and-parallel-stream.md#monad) that encapsulates a value that is either there or [not there _yet_](#user-content-fn-2)[^2]. Such an abstraction is also known as a **promise** in other languages — it encapsulates the **promise** to produce a value.

{% hint style="info" %}
A key property of `CompletableFuture` is whether the value it promises is ready — i.e., the tasks that it encapsulates have been _completed_ or not.
{% endhint %}

#### Create a `CompletableFuture`

In the lecture, the following **four** methods are introduced to create a `CompletedFuture`.

{% stepper %}
{% step %}
Use the `completedFuture` method.


{% endstep %}

{% step %}
Use the `runAsync` method that takes in a `Runnable` lambda expression.


{% endstep %}

{% step %}
Use the `supplyAsync` method that takes in a `Supplier<T>` lambda expression.


{% endstep %}

{% step %}
Rely on other `CompletableFuture` instances.


{% endstep %}
{% endstepper %}

After creating a `CompletableFuture`, we often chain them together.

#### Chain `CompletableFuture`

The usefulness of `CompletableFuture` comes from the ability to chain them up and specify a sequence of computations to be run. We have the following methods:

{% stepper %}
{% step %}
`thenApply` which analogous to `map`


{% endstep %}

{% step %}
`thenCompose`, which is analogous to `flatMap`


{% endstep %}

{% step %}
`thenCombine`, which is analogous to `combine`

The above three methods have their asynchronous version also.
{% endstep %}

{% step %}
`thenRun`


{% endstep %}

{% step %}
`runAfterBoth`


{% endstep %}

{% step %}
`runAfterEither`


{% endstep %}
{% endstepper %}

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
