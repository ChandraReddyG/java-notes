# Lamda Expression

## What is lambda expression
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

