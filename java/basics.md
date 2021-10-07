# Java Basics

## What special guarantees does the JVM hold for final fields of a class?

JVM basically guarantees that final fields of a class will be initialized before any thread gets hold of the object. Without this guarantee, a reference to an object may be published, i.e. become visible, to another thread before all the fields of this object are initialized, due to reorderings or other optimizations. This could cause racy access to these fields.

This is why, when creating an immutable object, you should always make all its fields final, even if they are not accessible via getter methods.

## Static vs Non Static Initialization Blocks
```java
public class StaticNonStaticBlocks {

    private final static String FINAL_STRING = "FINAL_STRING";
    private static String STATIC_STRING = "STATIC_STRING";

    static {
        System.out.println("In static block");
    }

    {
        System.out.println("In non static block");
        beforeConstructor();
    }

    public StaticNonStaticBlocks() {
        System.out.println("In constructor");
    }

    public void beforeConstructor() {
        System.out.println("In beforeConstructor");
    }

    public static void main(String[] args) {
        StaticNonStaticBlocks si = new StaticNonStaticBlocks();
    }
}

// Output
// In static block
// In non static block
// In beforeConstructor
// In constructor
```
Static block will be called first, then the non static block then the beforeConstructor method and then the constructor.

The code in the "static" section(s) will be executed at class load time, before any instances of the class are constructed (and before any static methods are called from elsewhere). That way you can make sure that the class resources are all ready to use.

It's also possible to have non-static initializer blocks. Those act like extensions to the set of constructor methods defined for the class. They look just like static initializer blocks, except the keyword "static" is left off.

The need for a non-static block is to execute any logic whenever an object is created irrespective of the constructor.



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
