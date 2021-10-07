# Java Fundamentals

## Volatile keyword in Java

Volatile specifier is used to indicate that a variable’s value can be modified by multiple threads simultaneously.
The value of this variable will never be cached thread-locally: all reads and writes will go straight to “main memory” ( that is not right at all practically :), but this is what mostly written everywhere.)

Volatile comes with two major concepts : Visibility and Ordering.

Visibility: If one thread changes a value of a variable, the change will be visible immediately to other threads reading the variable. This is guaranteed by not allowing the compiler or the JVM to allocate those variables in the CPU registers. Any write to a volatile variable is flushed immediately to main memory and any read of it is fetched from main memory. That means there is a little bit of performance penalty, but that's far better from a concurrency point of view.

Ordering: Sometimes for performance optimization, the JVM reorders instructions. This is not allowed when accessing volatile variables. Access to volatile variables is not reordered with access to other volatile variables, nor with access to other normal fields around them. This makes writes to non-volatile fields around them visible immediately to other threads.

In a multithreaded application where the threads operate on non-volatile variables, each thread may copy variables from main memory into a CPU cache while working on them, for performance reasons. If your computer contains more than one CPU, each thread may run on a different CPU. That means, that each thread may copy the variables into the CPU cache of different CPUs. This is illustrated here:

![thread-visibility-non-volatile.png](images/thread-visibility-non-volatile.png)

With non-volatile variables there are no guarantees about when the Java Virtual Machine (JVM) reads data from main memory into CPU caches, or writes data from CPU caches to main memory.

Imagine too, that only Thread 1 increments the counter variable, but both Thread 1 and Thread 2 may read the counter variable from time to time.

If the counter variable is not declared volatile there is no guarantee about when the value of the counter variable is written from the CPU cache back to main memory. This means, that the counter variable value in the CPU cache may not be the same as in main memory. This situation is illustrated here:

![thread-visibility-non-volatile.png](images/thread-visibility-non-volatile-2.png)

The problem with threads not seeing the latest value of a variable because it has not yet been written back to main memory by another thread, is called a "visibility" problem. The updates of one thread are not visible to other threads.

### The Java volatile Visibility Guarantee

The Java volatile keyword is intended to address variable visibility problems. By declaring the counter variable volatile all writes to the counter variable will be written back to main memory immediately. Also, all reads of the counter variable will be read directly from main memory.

### Full volatile Visibility Guarantee

Actually, the visibility guarantee of Java volatile goes beyond the volatile variable itself. The visibility guarantee is as follows:

* If Thread A writes to a volatile variable and Thread B subsequently reads the same volatile variable, then all variables visible to Thread A before writing the volatile variable, will also be visible to Thread B after it has read the volatile variable.
* If Thread A reads a volatile variable, then all all variables visible to Thread A when reading the volatile variable will also be re-read from main memory.

Ref: http://tutorials.jenkov.com/java-concurrency/volatile.html