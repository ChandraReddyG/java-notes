# Java Generics

Java Generics were introduced in JDK 5.0 with the aim of reducing bugs and adding an extra layer of abstraction over types.

## Generic Methods

Generic methods are those methods that are written with a single method declaration and can be called with arguments of different types. The compiler will ensure the correctness of whichever type is used. These are some properties of generic methods:

* Generic methods have a type parameter (the diamond operator enclosing the type) before the return type of the method declaration
* Type parameters can be bounded (bounds are explained later in the article)
* Generic methods can have different type parameters separated by commas in the method signature
* Method body for a generic method is just like a normal method

An example of defining a generic method to convert an array to a list:

```java
public <T> List<T> fromArrayToList(T[] a) {   
    return Arrays.stream(a).collect(Collectors.toList());
}
```

In the previous example, the <T> in the method signature implies that the method will be dealing with generic type T. This is needed even if the method is returning void.

As mentioned above, the method can deal with more than one generic type, where this is the case, all generic types must be added to the method signature, for example, if we want to modify the above method to deal with type T and type G, it should be written like this:

```java
public static <T, G> List<G> fromArrayToList(T[] a, Function<T, G> mapperFunction) {
    return Arrays.stream(a)
      .map(mapperFunction)
      .collect(Collectors.toList());
}
```

We’re passing a function that converts an array with the elements of type T to list with elements of type G. An example would be to convert Integer to its String representation:

```java
@Test
public void givenArrayOfIntegers_thanListOfStringReturnedOK() {
    Integer[] intArray = {1, 2, 3, 4, 5};
    List<String> stringList
      = Generics.fromArrayToList(intArray, Object::toString);
  
    assertThat(stringList, hasItems("1", "2", "3", "4", "5"));
}
```

It is worth noting that Oracle recommendation is to use an uppercase letter to represent a generic type and to choose a more descriptive letter to represent formal types, for example in Java Collections T is used for type, K for key, V for value.

## Bounded Generics

As mentioned before, type parameters can be bounded. Bounded means “restricted“, we can restrict types that can be accepted by a method.

For example, we can specify that a method accepts a type and all its subclasses (upper bound) or a type all its superclasses (lower bound).

To declare an upper bounded type we use the keyword extends after the type followed by the upper bound that we want to use. For example:

```java
public <T extends Number> List<T> fromArrayToList(T[] a) {
    ...
}
```
Note that type variable T is declared immediately before return type, but it does not relate to return type in any way.

### Unbounded Wildcards
Type arguments without bounds are useful, but have limitations. If you declare a List of unbounded type, as in A List with an unbounded wildcard, you can read from it but not write to it.

```java
// A List with an unbounded wildcard
List<?> stuff = new ArrayList<>();
// stuff.add("abc");
// stuff.add(new Object());
// stuff.add(3);
int numElements = stuff.size();
```

That feels pretty useless, since there’s apparently no way to get anything into it. One use for them is that any method that takes a List<?> as an argument will accept any list at all when invoked .

```java
// Unbounded List as a method arg
private static void printList(List<?> list) {
    System.out.println(list);
}

public static void main(String[] args) {
    // ... create lists as before ...

    printList(ints);
    printList(strings);
    printList(stuff);
}
```

**boolean containsAll(Collection<?> c)**

That method returns true only if all the elements of the collection appear in the current list. Since the argument uses an unbounded wildcard, the implementation is restricted to only:

* Methods from Collection itself that don’t need the contained type, or
* Methods from Object

### Upper Bounded Wildcards

```java
List<? extends Number> foo3 = new ArrayList<Number>();  // Number "extends" Number (in this context)
List<? extends Number> foo3 = new ArrayList<Integer>(); // Integer extends Number
List<? extends Number> foo3 = new ArrayList<Double>();  // Double extends Number
```

An upper bounded wildcard uses the extends keyword to set a superclass limit. For example, to define a list of numbers that will allow ints, longs, doubles, and even BigDecimal instances to be added to it

```java
List<? extends Number> numbers = new ArrayList<>();
//        numbers.add(3);
//        numbers.add(3.14159);
//        numbers.add(new BigDecimal("3"));
```
Well, that seemed like a good idea at the time. Unfortunately, while you can define the list with the upper bounded wildcard, again you can’t add to it. The problem is that when you retrieve the value, the compiler has no idea what type it is, only that it extends Number.

Still, you can define a method argument that takes List<? extends Number> and then invoke the method with the different types of lists. See Using an upper bound.

```java
private static double sumList(List<? extends Number> list) {
    return list.stream()
            .mapToDouble(Number::doubleValue) // returns DoubleStream
            .sum();
}

public static void main(String[] args) {
    List<Integer> ints = Arrays.asList(1, 2, 3, 4, 5);
    List<Double> doubles = Arrays.asList(1.0, 2.0, 3.0, 4.0, 5.0);
    List<BigDecimal> bigDecimals = Arrays.asList(
        new BigDecimal("1.0"),
        new BigDecimal("2.0"),
        new BigDecimal("3.0"),
        new BigDecimal("4.0"),
        new BigDecimal("5.0")
    );

    System.out.printf("ints sum is        %s%n", sumList(ints));
    System.out.printf("doubles sum is     %s%n", sumList(doubles));
    System.out.printf("bigdecimals sum is %s%n", sumList(bigDecimals));
}
```

### Lower Bounded Wildcards

```java
List<? super Integer> foo3 = new ArrayList<Integer>();  // Integer is a "superclass" of Integer (in this context)
List<? super Integer> foo3 = new ArrayList<Number>();   // Number is a superclass of Integer
List<? super Integer> foo3 = new ArrayList<Object>();   // Object is a superclass of Integer
```

A lower bounded wildcard means any ancestor to your class is acceptable. You use the super keyword with the wildcard to specify a lower bound. In the case of a List<? super Number>, the implication is that the reference could represent List<Number> or List<Object>.

With the upper bound, we were specifying the type that the variables must conform to in order for the implementation of the method to work. To add up the numbers, we needed to be sure that the variables had a doubleValue method, which is defined in Number. All of the Number subclasses have that method as well, either directly or through an override. That’s why we specified List<? extends Number> for the input type.

Here, however, we’re taking the items from the list and adding them to a different collection. That destination collection could be a List<Number>, but it could also be a List<Object> because the individual Object references would be satisfied by a Number.

```java
public void numsUpTo(Integer num, List<? super Integer> output) {
    IntStream.rangeClosed(1, num)
        .forEach(output::add);
}
```
The reason this isn’t idiomatic Java 8 is that it’s using the supplied list as an output variable. That’s essentially a side-effect, and therefore frowned upon. Still, by making the second argument of type List<? super Integer>, the supplied list can be of type List<Integer>, List<Number>, or even List<Object>

```java

ArrayList<Integer> integerList = new ArrayList<>();
ArrayList<Number> numberList = new ArrayList<>();
ArrayList<Object> objectList = new ArrayList<>();

numsUpTo(5, integerList);
numsUpTo(5, numberList);
numsUpTo(5, objectList);
```

### PECS
The term PECS stands for "Producer Extends, Consumer Super", which is an odd acronym coined by Joshua Block in his Effective Java book but provides a mnemonic on what to do. It means that if a parameterized type represents a producer, use extends. If it represents a consumer, use super. If the parameter is both, don’t use wildcards at all — the only type that satisfies both requirements is the explicit type itself.

The advice boils down to:

* Use extends when you only get values out of a data structure
* Use super when you only put values into a data structure
* Use an explicit type when you plan to do both

As long as we’re on the subject of terminology, there are formal terms for these concepts, which are frequently used in languages like Scala[2].

The term covariant preserves the ordering of types from more specific to more general. In Java, arrays are covariant because String[] is a subtype of Object[]. As we’ve seen, collections in Java are not covariant unless we use the extends keyword with a wildcard.

The term contravariant goes the other direction. In Java, that’s where we use the super keyword with a wildcard.

An invariant means that the type must be exactly as specified. All parameterized types in Java are invariant unless we use extends or super, meaning that if a method expects a List<Employee> then that’s what you have to supply. Neither a List<Object> nor a List<Salaried> will do.

The PECS rule is a restatement of the formal rule that a type constructor is contravariant in the input type and covariant in the output type. The idea is sometimes stated as "be liberal in what you accept and conservative in what you produce".

```java
import java.util.List;

public class PecsExample
{
    public static void exampleShowingOneSubtleDifference()
    {
        List<? super Number> superNumbers = null;
        List<Number> numbers = null;

        PecsExample.<Number>copyA(superNumbers, numbers); // Works
        //PecsExample.<Number>copyB(superNumbers, numbers); // Does not work
    }

    public static void exampleShowingThatTheyAreBasicallyEquivalent()
    {
        List<? super Object> superObjects = null;
        List<? super Number> superNumbers = null;
        List<? super Integer> superIntegers = null;

        List<Object> objects = null;
        List<Number> numbers = null;
        List<Integer> integers = null;

        List<? extends Object> extendsObjects = null;
        List<? extends Number> extendsNumbers = null;
        List<? extends Integer> extendsIntegers = null;

        copyA(objects, objects);
        copyA(objects, numbers);
        copyA(objects, integers);
        copyA(numbers, numbers);
        copyA(numbers, integers);
        copyA(integers, integers);

        copyA(superObjects, objects);
        copyA(superObjects, numbers);
        copyA(superObjects, integers);
        copyA(superNumbers, numbers);
        copyA(superNumbers, integers);
        copyA(superIntegers, integers);

        copyA(objects, extendsObjects);
        copyA(objects, extendsNumbers);
        copyA(objects, extendsIntegers);
        copyA(numbers, extendsNumbers);
        copyA(numbers, extendsIntegers);
        copyA(integers, extendsIntegers);

        copyB(objects, objects);
        copyB(objects, numbers);
        copyB(objects, integers);
        copyB(numbers, numbers);
        copyB(numbers, integers);
        copyB(integers, integers);

        copyB(superObjects, objects);
        copyB(superObjects, numbers);
        copyB(superObjects, integers);
        copyB(superNumbers, numbers);
        copyB(superNumbers, integers);
        copyB(superIntegers, integers);

        copyB(objects, extendsObjects);
        copyB(objects, extendsNumbers);
        copyB(objects, extendsIntegers);
        copyB(numbers, extendsNumbers);
        copyB(numbers, extendsIntegers);
        copyB(integers, extendsIntegers);
    }

    public static <T> void copyA(List<? super T> dest, List<? extends T> src)
    {
        for (int i = 0; i < src.size(); i++)
        {
            dest.set(i, src.get(i));
        }
    }

    public static <T> void copyB(List<T> dest, List<? extends T> src)
    {
        for (int i = 0; i < src.size(); i++)
        {
            dest.set(i, src.get(i));
        }
    }
}
```

## Multiple Bounds

A type can also have multiple upper bounds as follows:

```java
<T extends Number & Comparable>
```

If one of the types that are extended by T is a class (i.e Number), it must be put first in the list of bounds. Otherwise, it will cause a compile-time error.

## Using Wildcards with Generics

Wildcards are represented by the question mark in Java “?” and they are used to refer to an unknown type. Wildcards are particularly useful when using generics and can be used as a parameter type but first, there is an important note to consider.

It is known that Object is the supertype of all Java classes, however, a collection of Object is not the supertype of any collection.

For example, a List<Object> is not the supertype of List<String> and assigning a variable of type List<Object> to a variable of type List<String> will cause a compiler error. This is to prevent possible conflicts that can happen if we add heterogeneous types to the same collection.

The Same rule applies to any collection of a type and its subtypes. Consider this example:

```java
public static void paintAllBuildings(List<Building> buildings) {
    buildings.forEach(Building::paint);
}
```

if we imagine a subtype of Building, for example, a House, we can’t use this method with a list of House, even though House is a subtype of Building. If we need to use this method with type Building and all its subtypes, then the bounded wildcard can do the magic:

```java
public static void paintAllBuildings(List<? extends Building> buildings) {
    ...
}
```

Now, this method will work with type Building and all its subtypes. This is called an upper bounded wildcard where type Building is the upper bound.

Wildcards can also be specified with a lower bound, where the unknown type has to be a supertype of the specified type. Lower bounds can be specified using the super keyword followed by the specific type, for example, <? super T> means unknown type that is a superclass of T (= T and all its parents).

## Type Erasure

Generics were added to Java to ensure type safety and to ensure that generics wouldn’t cause overhead at runtime, the compiler applies a process called type erasure on generics at compile time.

Type erasure removes all type parameters and replaces it with their bounds or with Object if the type parameter is unbounded. Thus the bytecode after compilation contains only normal classes, interfaces and methods thus ensuring that no new types are produced. Proper casting is applied as well to the Object type at compile time.

This is an example of type erasure:

```java
public <T> List<T> genericMethod(List<T> list) {
    return list.stream().collect(Collectors.toList());
}
```

With type erasure, the unbounded type T is replaced with Object as follows:

```java
// for illustration
public List<Object> withErasure(List<Object> list) {
    return list.stream().collect(Collectors.toList());
}

// which in practice results in
public List withErasure(List list) {
    return list.stream().collect(Collectors.toList());
}
```