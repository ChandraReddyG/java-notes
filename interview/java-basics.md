# Java Basics

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

## Concurrency vs Parallelism

* **Concurrency** : is when two or more tasks can start, run, and complete in overlapping time periods. It doesn't necessarily mean they'll ever both be running at the same instant. For example, multitasking on a single-core machine.

    A condition that exists when at least two threads are making progress. A more generalized form of parallelism that can include time-slicing as a form of virtual parallelism.

* **Parallelism** : is when tasks literally run at the same time, e.g., on a multicore processor.
    
A condition that arises when at least two threads are executing simultaneously.

![concurrency-and-parallelism.jpg](images/concurrency-and-parallelism.jpg)

## AtomicLong

The AtomicLong class provides you with a long variable which can be read and written atomically, and which also contains advanced atomic operations like compareAndSet(). The AtomicLong class is located in the java.util.concurrent.atomic package, so the full class name is java.util.concurrent.atomic.AtomicLong .

The reasoning behind the AtomicLong design is explained in my Java Concurrency tutorial in the text about Compare and Swap.

**Compare and swap **is a technique used when designing concurrent algorithms. Basically, compare and swap compares an expected value to the concrete value of a variable, and if the concrete value of the variable is equals to the expected value, swaps the value of the variable for a new variable. Compare and swap may sound a bit complicated but it is actually reasonably simple once you understand it, so let me elaborate a bit further on the topic.

### Compare And Swap As Atomic Operation

Modern CPUs have built-in support for atomic compare and swap operations. From Java 5 you can get access to these functions in the CPU via some of the new atomic classes in the java.util.concurrent.atomic package.

Here is an example showing how to implement the lock() method shown earlier using the AtomicBoolean class:

```java
public static class MyLock {
    private AtomicBoolean locked = new AtomicBoolean(false);

    public boolean lock() {
        return locked.compareAndSet(false, true);
    }

}
```

Notice how the locked variable is no longer a boolean but an AtomicBoolean. This class has a compareAndSet() function which will compare the value of the AtomicBoolean instance to an expected value, and if has the expected value, it swaps the value with a new value. In this case it compares the value of locked to false and if it is false it sets the new value of the AtomicBoolean to true.

The compareAndSet() method returns true if the value was swapped, and false if not.

## AtomicReference

The AtomicReference class provides an object reference variable which can be read and written atomically. By atomic is meant that multiple threads attempting to change the same AtomicReference (e.g. with a compare-and-swap operation) will not make the AtomicReference end up in an inconsistent state. AtomicReference even has an advanced compareAndSet() method which lets you compare the reference to an expected value (reference) and if they are equal, set a new reference inside the AtomicReference object.

### Creating an AtomicReference

```java
AtomicReference atomicReference = new AtomicReference();

String initialReference = "the initially referenced string";
AtomicReference<String> atomicStringReference = new AtomicReference<String>(initialReference);
```

```java
import java.util.concurrent.atomic.AtomicReference;
public class AtomicRefEg {
    static AtomicReference<Person> p  = new AtomicReference<Person>(new Person(20));
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new Runnable(){
            @Override
            public void run() {
                for(int i=1 ; i<=3 ; i++){
                    p.set(new Person(p.get().age+10));
                    System.out.println("Atomic Check by first thread: "+Thread.currentThread().getName()+" is "+p.get().age);
                }
            }
        });
        Thread t2 = new Thread(new Runnable(){
            @Override
            public void run() {
                Person per = p.get();
                for(int i=1 ; i<=3 ; i++){
                    System.err.println(p.get().equals(per)+"_"+per.age+"_"+p.get().age);
                    p.compareAndSet(per, new Person(p.get().age+10));
                    System.out.println("Atomic Check by second thread : "+Thread.currentThread().getName()+" is "+p.get().age);
                }
            }
        });
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println("Final value: "+p.get().age);
    }
}
class Person {
    int age;
    public Person(int i) {
        age=i;
    }
}
```

### Comparing and Setting the AtomicReference Reference

The AtomicReference class contains a useful method named compareAndSet(). The compareAndSet() method can compare the reference stored in the AtomicReference instance with an expected reference, and if they two references are the same (not equal as in equals() but same as in ==), then a new reference can be set on the AtomicReference instance.

If compareAndSet() sets a new reference in the AtomicReference the compareAndSet() method returns true. Otherwise compareAndSet() returns false.

## AtomicStampedReference

The AtomicStampedReference class provides an object reference variable which can be read and written atomically. By atomic is meant that multiple threads attempting to change the same AtomicStampedReference will not make the AtomicStampedReference end up in an inconsistent state.

The AtomicStampedReference is different from the AtomicReference in that the AtomicStampedReference keeps both an object reference and a stamp internally. The reference and stamp can be swapped using a single atomic compare-and-swap operation, via the compareAndSet() method.

```java
import java.util.concurrent.atomic.AtomicStampedReference;
public class AtomicRefEg {
    static int stampVal = 1;
    static AtomicStampedReference<Person> s  = new AtomicStampedReference<Person>(new Person(20), stampVal);
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new Runnable(){
            @Override
            public void run() {
                for(int i=1 ; i<=3 ; i++) {
                    System.out.println("stamp value for first thread:"+stampVal);
                    s.compareAndSet(s.getReference(), new Person(s.getReference().age+10), stampVal, ++stampVal);
                    System.out.println("Atomic Check by first thread: "+Thread.currentThread().getName()+" is "+s.getReference().age);
                }
            }
        });
        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                for(int i=1 ; i<=3 ; i++){
                    System.out.println("stamp value for second thread:"+stampVal);
                    s.compareAndSet(s.getReference(), new Person(s.getReference().age+10), stampVal, ++stampVal);
                    System.out.println("Atomic Check by second thread : "+Thread.currentThread().getName()+" is "+s.getReference().age);
                }
            }
        });
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println("Final value: "+s.getReference().age);
    }
}
class Person {
    int age;
    public Person(int i) {
        age=i;
    }
}
```