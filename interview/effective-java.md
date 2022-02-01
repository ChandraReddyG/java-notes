# Effective Java

## Deprecate Object.finalize [Java 9]

The **java.lang.Object.finalize** method has been deprecated. The finalization mechanism is inherently problematic and can lead to performance issues, deadlocks, and hangs.

Finalizers get invoked when JVM figures out that this particular instance should be garbage collected. Such a finalizer may perform any operations, including bringing the object back to life.

The main purpose of a finalizer is, however, to release resources used by objects before they're removed from the memory. A finalizer can work as the primary mechanism for clean-up operations, or as a safety net when other methods fail.

### Demonstration of Finalizers' Effects

Let's define a new class with a non-empty finalizer:

```java
public class CrashedFinalizable {
    public static void main(String[] args) throws ReflectiveOperationException {
        for (int i = 0; ; i++) {
            new CrashedFinalizable();
            // other code
        }
    }

    @Override
    protected void finalize() {
        System.out.print("");
    }
}
```

Notice the finalize() method – it just prints an empty string to the console. If this method were completely empty, the JVM would treat the object as if it didn't have a finalizer. Therefore, we need to provide finalize() with an implementation, which does almost nothing in this case.

To understand why the garbage collector didn't discard objects as it should, we need to look at how the JVM works internally.

When creating an object, also called a referent, that has a finalizer, the JVM creates an accompanying reference object of type java.lang.ref.Finalizer. After the referent is ready for garbage collection, the JVM marks the reference object as ready for processing and puts it into a reference queue.

We can access this queue via the static field queue in the java.lang.ref.Finalizer class.

Meanwhile, a special daemon thread called Finalizer keeps running and looks for objects in the reference queue. When it finds one, it removes the reference object from the queue and calls the finalizer on the referent.

During the next garbage collection cycle, the referent will be discarded – when it's no longer referenced from a reference object.

If a thread keeps producing objects at a high speed, which is what happened in our example, the Finalizer thread cannot keep up. Eventually, the memory won't be able to store all the objects, and we end up with an OutOfMemoryError.

Notice a situation where objects are created at warp speed as shown in this section doesn't often happen in real life. However, it demonstrates an important point – finalizers are very expensive.

### Disadvantages of Finalizers

The first noticeable issue is the lack of promptness. We cannot know when a finalizer runs since garbage collection may occur anytime.

By itself, this isn't a problem because the finalizer still executes, sooner or later. However, system resources aren't unlimited. Thus, we may run out of resources before a clean-up happens, which may result in a system crash.

Finalizers also have an impact on the program's portability. Since the garbage collection algorithm is JVM implementation-dependent, a program may run very well on one system while behaving differently on another.

The performance cost is another significant issue that comes with finalizers. Specifically, JVM must perform many more operations when constructing and destroying objects containing a non-empty finalizer.

The last problem we'll be talking about is the lack of exception handling during finalization. If a finalizer throws an exception, the finalization process stops, leaving the object in a corrupted state without any notification.

### What to use instead of finalize() method
Let's explore a solution providing the same functionality but without the use of finalize() method. Notice that the example below isn't the only way to replace finalizers.

Instead, it's used to demonstrate an important point: there are always options that help us to avoid finalizers.

Here's the declaration of our new class:

```java
public class CloseableResource implements AutoCloseable {
    private BufferedReader reader;

    public CloseableResource() {
        InputStream input = this.getClass()
          .getClassLoader()
          .getResourceAsStream("file.txt");
        reader = new BufferedReader(new InputStreamReader(input));
    }

    public String readFirstLine() throws IOException {
        String firstLine = reader.readLine();
        return firstLine;
    }

    @Override
    public void close() {
        try {
            reader.close();
            System.out.println("Closed BufferedReader in the close method");
        } catch (IOException e) {
            // handle exception
        }
    }
}
```

## Difference Between String s1 = new String("Test"); & String s1 == "Test";

> String s = new String("bikini"); // DON'T DO THIS!

The statement creates a new String instance each time it is executed, and none of those object creations is necessary. The argument to the String constructor ("bikini") is itself a String instance, functionally identical to all of the objects created by the constructor. If this usage occurs in a loop or in a frequently invoked method, millions of String instances can be created needlessly.

The improved version is simply the following:

> String s = "bikini";

This version uses a single String instance, rather than creating a new one each time it is executed. Furthermore, it is guaranteed that the object will be reused by any other code running in the same virtual machine that happens to contain the same string literal [JLS, 3.10.5].

You can often avoid creating unnecessary objects by using static factory meth-ods (Item 1) in preference to constructors on immutable classes that provide both.

For example, the factory method Boolean.valueOf(String) is preferable to the constructor Boolean(String), which was deprecated in Java 9. The constructor must create a new object each time it’s called, while the factory method is never required to do so and won’t in practice. In addition to reusing immutable objects, you can also reuse mutable objects if you know they won’t be modified.

## Autoboxing problem

```java
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++)
        sum += i;
    return sum;
}
```

This program gets the right answer, but it is much slower than it should be, due to a one-character typographical error.

```java
private static long sum() {
    long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++)
        sum += i;
    return sum;
}
```

The variable sum is declared as a Long instead of a long, which means that the program constructs about 231 unnecessary Long instances (roughly one for each time the long i is added to the Long sum). 
Changing the declaration of sum from Long to long reduces the runtime from 6.3 seconds to 0.59 seconds on my machine. The lesson is clear: prefer primitives to boxed primitives, and watch out for unintentional autoboxing.