# Lambda Expressions

Java lambda expressions are new in Java 8. Java lambda expressions are Java's first step into functional programming. A Java lambda expression is thus a function which can be created without belonging to any class. A Java lambda expression can be passed around as if it was an object and executed on demand.

Lambda expression is a functional expression. All functional interfaces are recommended to have an informative @FunctionalInterface annotation. This not only clearly communicates the purpose of this interface, but also allows a compiler to generate an error if the annotated interface does not satisfy the conditions.

Lambda Expression Examples:-
```java

() -> {}                     // No parameters; void result

() -> 42                     // No parameters, expression body
() -> null                   // No parameters, expression body
() -> { return 42; }         // No parameters, block body with return
() -> { System.gc(); }       // No parameters, void block body

// Complex block body with multiple returns
() -> {
  if (true) return 10;
  else {
    int result = 15;
    for (int i = 1; i < 10; i++)
      result *= i;
    return result;
  }
}                          

(int x) -> x+1             // Single declared-type argument
(int x) -> { return x+1; } // same as above
(x) -> x+1                 // Single inferred-type argument, same as below
x -> x+1                   // Parenthesis optional for single inferred-type case

(String s) -> s.length()   // Single declared-type argument
(Thread t) -> { t.start(); } // Single declared-type argument
s -> s.length()              // Single inferred-type argument
t -> { t.start(); }          // Single inferred-type argument

(int x, int y) -> x+y      // Multiple declared-type parameters
(x,y) -> x+y               // Multiple inferred-type parameters
(x, final y) -> x+y        // Illegal: can't modify inferred-type parameters
(x, int y) -> x+y          // Illegal: can't mix inferred and declared types

```

```java
(Employee e) -> { return e.yearsOfExpr >= 7 ? "Expert" : e.yearsOfExpr >= 3 ? "Intermediet" : "Fresher"; }
```
Basically Lambda has 3 parts.

* A list of parameters : In above example “Employee e”
* Function body : The behavior (right hand side of arrow)
* An arrow : Separator between parameter list and function body

Lambda syntax follows some of the below rules.

* Parameter types are optional.
* If you have single parameter then both parameter type and parenthesis are optional.
* If you have multiple parameters, then they should be enclosed with in parenthesis.
* For multiple statements in function body should be enclosed with in courly braces.
* If lambda body exnclosed inside courly braces then return keyward is required in case your behavior returns value.


Method and Constructor References :-
```java
System::getProperty
System.out::println
"abc"::length
ArrayList::new
int[]::new
```

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

## Accessing outer scope variables
Some of the rules applicable for anonymous classes are also applicable to Lambdas:

* Lambda has access to members of its enclosing scope. (see line-1)
* Like nested class or anonymous class, it can also shadows any other declarations in the enlosing scope that is of same name. (see line-2)

```java
public class LambdaFeatures {
    private int x = 10;

    public void example() {
        Consumer<String> funcInterface = str -> {
            System.out.println("x= " + x);  // Line-1

            int x = 50;                     // Line-2
            System.out.println("x= " + x);
        };
    }
}

Output: x= 10
        x= 50
```


## Why do variables in lambdas have to be final or effectively final?

In Java 8 with the addition of lambda expressions, a new concept effectively final variable has been added, which closely relates to lambda expressions.

## InvokeDynamic and Method Handles

In Java 6 the bytecode specification has four method invocation instructions which correspond directly to Java method calls:

* invokestatic which is used to invoke class methods
* invokespecial which is used to invoke instance initialisation methods, when invoking methods in a superclass, and for private methods;
* invokevirtual, the normal method invocation for an instance method
* invokeinterface for interface methods

Of these, the virtual and interface calls might serve the purposes of a dynamic language, but they are not flexible enough for the dynamic language runtime to bind the call site to a destination at runtime if the call site is not aware of the destination's interface prior to the bind. JSR 292 therefore adds a fifth method invocation, invokedynamic, which acts as a marker indicating that a dynamic language runtime specific call occurs at this point. At the JVM level, an invokedynamic instruction is used to call methods which have linkage and dispatch semantics defined by non-Java languages.

Like the other four invocation instructions invokedynamic is statically typed, however an invokedynamic instruction is dynamically linked under program control using a method handle.

Given any method M that I am able to invoke, the JVM provides me a way to produce a method handle H(M). I can use this handle later on, even after forgetting the name of M, to call M as often as I want. Moreover, if I provide this handle to other callers, they also can invoke M through the handle, even if they do not have access rights to call M by name. If the method is non-static, the method handle always takes the receiver as its first argument. If the method is virtual or interface, the method handle performs the dispatch on the receiver.


A method handle will confess its type reflectively, as a series of Class values, through the type operation.

## Restrictions in Lambdas
Lambda has some restrictions:

* You can’t declare any static or non-static initializers.
* It cann’t access local variables in its enclosing scope that are not defined final or effectively final. This restriction exists with anonymous class also. Let’s discuss why is this limitation with following code snippet.

```java
public class LambdaFeatures {
    int y = 50;

    public static void main(String[] args) throws Exception {
        int x = 50;

        Thread tt = new Thread() {
            public void run() {
                System.out.println("MyThread start.");

                Thread.sleep(1000L);

                System.out.println("MyThread end. x=" + x);
            }
        };

        t.start();

        x++;
        System.out.println("main end");
    }
}
```
Local variables stored in the stack where as instance variables stored in heap. In the above code snippet main thread declares variable “x” and also creates a Thread which is trying to use this x variable. As we know local variables will be stored in the local stack (here stack of main) and when thread “tt” will be created it will executed separate to main thread. There might be chances that main will be completed first and the stack will be released before thread tt trying to use it. So if variable is declared final, them lambda will a copy of it and use whenever require.

## Where to use Lambdas
We have discussed enough on lambdas and anonybmous classes. Let’s discuss the scenarios where should we use them.

* Anonymous class: Use it whenever you want to declare some additional fields or methods which lambda cann’t do.
* Lambda:
    * Use it if you want to encapsulate a single unit of behavior and pass to some other code. For example: performing certain operation on each element of collection.
    * Use it if you need a simple instance of a functional interface and none of the preceding criteria apply (for example, you do not need a constructor, a named type, fields, or additional methods).

