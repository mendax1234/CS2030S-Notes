# Lec 01 - Compiler, Types, Classes, Objects

## [Program and Compiler](https://nus-cs2030s.github.io/2425-s1/01-compiler.html)

### Software Program

A software program is [a collection of data variables and instructions on how to modify these variables](#user-content-fn-1)[^1]. To dictate these instructions to the computer, programmers usually write down the instructions using a programming language.

### Compiled vs. Intepreted Programs

* Compiler: The compiler reads in the **entire program** written in a higher-level programming language and translates it into machine code. The machine code is then saved into an **executable file**, which can be executed later. e.g., C/C++
* Interpreter: The interpreter reads in the program **one statement at a time** interprets what the statement means, and executes its directly. e.g., Python, JavaScript

**Java** programs, on the other hand, can be executed in two ways:

1. The Java program can first be compiled into bytecode. During execution, the bytecode is interpreted and compiled on-the-fly by the _Java Virtual Machine_ (_JVM_) into machine code. See [#compiling-and-running-java-programs](lec-01-compiler-types-classes-objects.md#compiling-and-running-java-programs "mention")
2. The Java program can be interpreted by the Java interpreter. See [#interpreting-a-java-program](lec-01-compiler-types-classes-objects.md#interpreting-a-java-program "mention")

#### Compiling and Running Java Programs

Suppose we have a Java program called `Hello.java`.

{% stepper %}
{% step %}
### Compile the Java Program into bytecode

To compile the program, we type

{% code lineNumbers="true" %}
```bash
javac Hello.java
```
{% endcode %}

into the command line. `javac` is the Java compiler. This step will either lead to the bytecode called `Hello.class` being created or spew out some errors. In this step, the `Hello.java` program is compiled from Java to the **JVM language** (_bytecode_).
{% endstep %}

{% step %}
### Interpret/Execute the compiled bytecode

Assuming that there is no error in compilation, we can now run

{% code lineNumbers="true" %}
```bash
java Hello
```
{% endcode %}

to invoke the JVM `java` and execute the bytecode contained in `Hello.class`.

{% hint style="info" %}
**JVM** is an interpreter.
{% endhint %}
{% endstep %}
{% endstepper %}

#### Interpreting a Java Program

Java (_version 8 or later_) comes with an interpreter called `jshell` that can read in Java statements, evaluate them, and print the results. Its usage is as follows

{% code lineNumbers="true" %}
```bash
jshell Hello.jsh
```
{% endcode %}

{% hint style="info" %}
Files intended to be run on jshell typically uses `.jsh` extension while files intended to be compiled and run uses `.java` extension. However, this difference is merely a convention. You can still interpret `.java` program on jshell.
{% endhint %}

### Compiler

The compiler does more than just translating source code into machine code or bytecode. The compiler also needs to parse the source code written and check if it follows the precise specification of the programming language (_called grammar_) used, and produces a _syntax error_ if the grammar is violated. It therefore can detect any syntax error before the program is run. This kind of error is called the **Compilation Error**.

{% hint style="info" %}
For the difference between **Compilation Error** and **Runtime Error**, please see my CS1010 Notes [here](https://app.gitbook.com/s/KipySCGxC8NC1UpA24DS/lec-tut-lab-exes/lecture/lec-03-basic-c-programming#compilation-error-vs.-run-time-error).
{% endhint %}

### Workflow

A typical workflow in a compiled language is the **edit, compile, execute**, loop. This is shown as follows

<figure><img src="../../.gitbook/assets/lec01-java-workflow.png" alt=""><figcaption></figcaption></figure>

[^1]: I believe this needs the computer organization knowledge. Basically, you can recap the behavior of fetching variables and instructions from the computer memory.
