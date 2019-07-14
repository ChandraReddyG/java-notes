# Singleton Design Pattern

The purpose of the Singleton class is to control object creation, limiting the number of objects to only one. The singleton allows only one entry point to create the new instance of the class.

Since there is only one Singleton instance, any instance fields of a Singleton will occur only once per class, just like static fields. Singletons are often useful where you have to control the resources, such as database connections or sockets.

## Example

There are different ways how singleton pattern can be created.

### Eagerly Initialized Singleton

```java
public class EagerSingleton {

    /** private constructor to prevent others from instantiating this class */
    private EagerSingleton() {}

    /** Create an instance of the class at the time of class loading */
    private static final EagerSingleton instance = new EagerSingleton();

    /** Provide a global point of access to the instance */
    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

### Eagerly Initialized Static Block Singleton

```java
public class EagerStaticBlockSingleton {

    private static final EagerStaticBlockSingleton instance;

    /** Don't let anyone else instantiate this class */
    private EagerStaticBlockSingleton() {}

    /** Create the one-and-only instance in a static block */
    static {
        try {
            instance = new EagerStaticBlockSingleton();
        } catch (Exception ex) {
            throw ex;
        }
    }

    /** Provide a public method to get the instance that we created */
    public static EagerStaticBlockSingleton getInstance() {
        return instance;
    }
}
```

### Lazily Initialized Singleton

```java
public class LazySingleton {

    private static LazySingleton instance;

    /** Don't let anyone else instantiate this class */
    private LazySingleton() {}

    /** Lazily create the instance when it is accessed for the first time */
    public static synchronized LazySingleton getInstance() {
        if(instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}
```

### Lazily Initialized Double-Checked Locking Singleton

```java
public class LazyDoubleCheckedLockingSingleton {

    private static volatile LazyDoubleCheckedLockingSingleton instance;

    /** private constructor to prevent others from instantiating this class */
    private LazyDoubleCheckedLockingSingleton() {}

    /** Lazily initialize the singleton in a synchronized block */
    public static LazyDoubleCheckedLockingSingleton getInstance() {
        if(instance == null) {
            synchronized (LazyDoubleCheckedLockingSingleton.class) {
                // double-check
                if(instance == null) {
                    instance = new LazyDoubleCheckedLockingSingleton();
                }
            }
        }
        return instance;
    }
}
```

### Lazily Initialized Inner Class Singleton

```java
public class LazyInnerClassSingleton {

    /** private constructor to prevent others from instantiating this class */
    private LazyInnerClassSingleton() {}

    /** This inner class is loaded only after getInstance() is called for the first time. */
    private static class SingletonHelper {
        private static final LazyInnerClassSingleton INSTANCE = new LazyInnerClassSingleton();
    }

    public static LazyInnerClassSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

### Enum Singleton

```java
import java.util.Arrays;

/** An Enum value is initialized only once at the time of class loading.
    It is singleton by design and is also thread-safe.
 */
enum EnumSingleton {
    WEEKDAY("Monday", "Tuesday", "Wednesday", "Thursday", "Friday"),
    WEEKEND("Saturday", "Sunday");

    private String[] days;

    EnumSingleton(String ...days) {
        System.out.println("Initializing enum with " + Arrays.toString(days));
        this.days = days;
    }

    public String[] getDays() {
        return this.days;
    }

    @Override
    public String toString() {
        return "EnumSingleton{" +
                "days=" + Arrays.toString(days) +
                '}';
    }
}

public class EnumSingletonExample {
    public static void main(String[] args) {
        System.out.println(EnumSingleton.WEEKDAY);
        System.out.println(EnumSingleton.WEEKEND);
    }
}
```

### Protection against Reflection

```java
class MySingleton {
    private static final MySingleton instance = new MySingleton();

    private MySingleton() {
        // protect against instantiation via reflection
        if(instance != null) {
            throw new IllegalStateException("Singleton already initialized");
        }
    }

    public static MySingleton getInstance() {
        return instance;
    }
}
```

### Protection against Serialization

```java
class SerializableSingleton implements Serializable {
    private static final long serialVersionUID = 8806820726158932906L;

    private static SerializableSingleton instance;

    private SerializableSingleton() {}

    public static synchronized SerializableSingleton getInstance() {
        if(instance == null) {
            instance = new SerializableSingleton();
        }
        return instance;
    }

    // implement readResolve method to return the existing instance
    protected Object readResolve() {
        return instance;
    }
}
```