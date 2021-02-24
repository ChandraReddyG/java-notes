# Interview Questions

## How many ways we can create singleton object?

### Early Initialization

The easiest way to achieve thread safety is to inline the object creation or to use an equivalent static block. This takes advantage of the fact that static fields and blocks are initialized one after another

```java
public class EarlyInitSingleton {
    private static final EarlyInitSingleton INSTANCE = new EarlyInitSingleton();
    public static EarlyInitSingleton getInstance() {
        return INSTANCE;
    }
    
     // private constructor and other methods...
}
```

### Initialization on Demand

Additionally, since we know from the Java Language Specification reference in the previous paragraph that a class initialization occurs the first time we use one of its methods or fields, we can use a nested static class to implement lazy initialization:

```java
public class InitOnDemandSingleton {
    private static class InstanceHolder {
        private static final InitOnDemandSingleton INSTANCE = new InitOnDemandSingleton();
    }
    public static InitOnDemandSingleton getInstance() {
        return InstanceHolder.INSTANCE;
    }

     // private constructor and other methods...
}
```

In this case, the InstanceHolder class will assign the field the first time we access it by invoking getInstance.

### Enum Singleton

The last solution comes from the Effective Java book (Item 3) by Joshua Block and uses an enum instead of a class. At the time of writing, this is considered to be the most concise and safe way to write a singleton:

```java
public enum EnumSingleton {
    INSTANCE;

    // other methods...
}
```

### Double-Checked Locking

```java
public class DclSingleton {
    private static volatile DclSingleton instance; // Should be volatile to avoid cache incoherence issues (fully initialize objected being used)
    public static DclSingleton getInstance() {
        if (instance == null) {
            synchronized (DclSingleton.class) {
                if (instance == null) {
                    instance = new DclSingleton();
                }
            }
        }
        return instance;
    }

    // private constructor and other methods...
}
```

Ref: https://www.youtube.com/watch?v=Z5TRputhzHs

## Using volatile vs AtomicInteger in Java concurrency

Ref : https://www.youtube.com/watch?v=WH5UvQJizH0

### Volatile keyword in Java

It provides a visibility guarantee. Write to volatile variable happens before every subsequent read of the same variable.
It also prevents Compiler from doing smart things, which can create problems in a multi-threading environment, like caching variables, re-ordering of code, etc.

a read of a volatile variable always returns the most recent write by any thread.

http://tutorials.jenkov.com/java-concurrency/volatile.html

### Atomic Variables in Java

The most commonly used atomic variable classes in Java are AtomicInteger, AtomicLong, AtomicBoolean, and AtomicReference. These classes represent an int, long, boolean, and object reference respectively which can be atomically updated. The main methods exposed by these classes are:

* get() – gets the value from the memory, so that changes made by other threads are visible; equivalent to reading a volatile variable
* set() – writes the value to memory, so that the change is visible to other threads; equivalent to writing a volatile variable
* lazySet() – eventually writes the value to memory, maybe reordered with subsequent relevant memory operations. One use case is nullifying references, for the sake of garbage collection, which is never going to be accessed again. In this case, better performance is achieved by delaying the null volatile write
* compareAndSet() – same as described in section 3, returns true when it succeeds, else false
* weakCompareAndSet() – same as described in section 3, but weaker in the sense, that it does not create happens-before orderings. This means that it may not necessarily see updates made to other variables. As of Java 9, this method has been deprecated in all atomic implementations in favor of weakCompareAndSetPlain(). The memory effects of weakCompareAndSet() were plain but its names implied volatile memory effects. To avoid this confusion, they deprecated this method and added four methods with different memory effects such as weakCompareAndSetPlain() or weakCompareAndSetVolatile()

## Differences between Externalizable vs Serializable

|Serializable|Externalizable|
|:--- | :--- |
|Serializable is a marker interface i.e. does not contain any method. | Externalizable interface contains two methods writeExternal() and readExternal() which implementing classes MUST override.|
|Serializable interface pass the responsibility of serialization to JVM and it’s default algorithm.	|Externalizable provides control of serialization logic to programmer – to write custom logic.|
|Mostly, default serialization is easy to implement, but has higher performance cost.	|Serialization done using Externalizable, add more responsibility to programmer but often result in better performance.|
|It’s hard to analyze and modify class structure because any change may break the serialization.|It’s more easy to analyze and modify class structure because of complete control over serialization logic.|