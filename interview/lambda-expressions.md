# Lambda Expressions

Java lambda expressions are new in Java 8. Java lambda expressions are Java's first step into functional programming. A Java lambda expression is thus a function which can be created without belonging to any class. A Java lambda expression can be passed around as if it was an object and executed on demand.

## FunctionalInterface

The term Java functional interface was introduced in Java 8. A functional interface in Java is an interface that contains only a single abstract (unimplemented) method. A functional interface can contain default and static methods which do have an implementation, in addition to the single unimplemented method.

So the interface should have only one abstract method (without default implementation) to become functional interface.

Examples:

```java
@FunctionalInterface
public interface ShortToByteFunction {

    String hello(String greet);

}
```

```java
@FunctionalInterface
interface SwissKnife {
    default int compare(int i1, int i2) {
        return i1 - i2;
    }

    static void run() {
        System.out.println("Running !");
    }

    String welcomify(String name);
}
```

This is functional interface because it has one unimplemented function 'welcomify' and run and compare has the implementation.

The lambda expression is matched against the parameter type of the welcomify() method's parameter. If the lambda expression matches the parameter type (in this case the SwissKnife interface) , then the lambda expression is turned into a function that implements the same interface as that parameter.

Java lambda expressions can only be used where the type they are matched against is a single method interface

A Java functional interface can be implemented by a Java Lambda Expression. Here is an example that implements the functional interface MyFunctionalInterface defined in the beginning of this Java functional interface tutorial:

A Java lambda expression implements a single method from a Java interface. In order to know what method the lambda expression implements, the interface can only contain a single unimplemented method. In other words, the interface must be a Java functional interface.

### Matching Lambdas to Interfaces

A single method interface is also sometimes referred to as a functional interface. Matching a Java lambda expression against a functional interface is divided into these steps:

* Does the interface have only one abstract (unimplemented) method?
* Does the parameters of the lambda expression match the parameters of the single method?
* Does the return type of the lambda expression match the return type of the single method?

If the answer is yes to these three questions, then the given lambda expression is matched successfully against the interface.

### Interfaces With Default and Static Methods

From Java 8 a Java interface can contain both default methods and static methods. Both default methods and static methods have an implementation defined directly in the interface declaration. This means, that a Java lambda expression can implement interfaces with more than one method - as long as the interface only has a single unimplemented (AKA abstract) method.

In other words, an interface is still a functional interface even if it contains default and static methods, as long as the interface only contains a single unimplemented (abstract) method.

## Lambda Expressions vs. Anonymous Interface Implementations

Even though lambda expressions are close to anonymous interface implementations, there are a few differences that are worth noting.

The major difference is, that an anonymous interface implementation can have state (member variables) whereas a lambda expression cannot. Look at this interface:

```java
interface Increment {
    int inc();
}
```

This interface can be implemented using an anonymous interface implementation, like this:

```java
Increment inc = new Increment() {
    int counter = 0;
    // we can maintaine the state of object with counter var

    @Override
    public int inc() {
        return ++counter;
    }
};
```

Full example:

```java
interface Increment {
    int inc();
}

Increment inc = new Increment() {
    int counter = 0;
    @Override
    public int inc() {
        return ++counter;
    }
};

System.out.println(inc.inc());
System.out.println(inc.inc());
// output 1
// output 2

Increment linc = () -> {
    // we cannot maintain the state of object (because if we call this method two time the count will increase but if we define var in functional interface and call 
    // the function twice it will be reinitialize as to 0 and then increment)
    int counter = 0;
    return ++counter;
};

System.out.println(linc.inc());
System.out.println(linc.inc());
// output 1
// output 1
```

## Lambda Parameters

Since Java lambda expressions are effectively just methods, lambda expressions can take parameters just like methods.

### Zero Parameters

If the method you are matching your lambda expression against takes no parameters, then you can write your lambda expression like this:

> () -> System.out.println("Zero parameter lambda");

### One Parameter

> (param) -> System.out.println("One parameter: " + param);

When a lambda expression takes a single parameter, you can also omit the parentheses, like this:

 > param -> System.out.println("One parameter: " + param);

### Multiple Parameters

If the method you match your Java lambda expression against takes multiple parameters, the parameters need to be listed inside parentheses. Here is how that looks in Java code:

> (p1, p2) -> System.out.println("Multiple parameters: " + p1 + ", " + p2);

## Parameter Types

Specifying parameter types for a lambda expression may sometimes be necessary if the compiler cannot infer the parameter types from the functional interface method the lambda is matching. Don't worry, the compiler will tell you when that is the case. Here is a Java lambda parameter type example:

> (Car car) -> System.out.println("The car is: " + car.getName());

Ref: http://tutorials.jenkov.com/java/lambda-expressions.html

## Built-in Functional Interfaces in Java

### Function

The Java Function interface (java.util.function.Function) interface is one of the most central functional interfaces in Java. The Function interface represents a function (method) that takes a single parameter and returns a single value. Here is how the Function interface definition looks:

```java
public interface Function<T,R> {
    public <R> apply(T parameter);
}
```

Sample implementation

```java 
Function<Integer, Integer> dbl = (v) -> v + v;
dbl.apply(5); // Will return 10
```

