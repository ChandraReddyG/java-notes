# Java 8 New Features

## Lambda Expression
Java programming language, a Lambda expression (or function) is just an anonymous function, i.e., a function with no name and without being bounded to an identifier. They are written exactly in the place where it’s needed, typically as a parameter to some other function.

They enable you to treat functionality as a method argument, or code as data. Lambda expressions let you express instances of single-method interfaces (referred to as functional interfaces) more compactly.

## Java 8 Streams
Simply put, streams are wrappers around a data source, allowing us to operate with that data source and making bulk processing convenient and fast.

A stream does not store data and, in that sense, is not a data structure. It also never modifies the underlying data source.

This new functionality – java.util.stream – supports functional-style operations on streams of elements, such as map-reduce transformations on collections.


## Functional Interface
Functional interfaces are also called Single Abstract Method interfaces (SAM Interfaces). As name suggest, they permit exactly one abstract method inside them. Java 8 introduces an annotation i.e. @FunctionalInterface which can be used for compiler level errors when the interface you have annotated violates the contracts of Functional Interface.

```
@FunctionalInterface
public interface FirstFunctionalInterface {
    public void fun();
}
```
Please note that a functional interface is valid even if the @FunctionalInterface annotation would be omitted. It is only for informing the compiler to enforce single abstract method inside interface.

Also, since default methods are not abstract you’re free to add default methods to your functional interface as many as you like.

Another important point to remember is that if an interface declares an abstract method overriding one of the public methods of java.lang.Object, that also does not count toward the interface’s abstract method count since any implementation of the interface will have an implementation from java.lang.Object or elsewhere. for example, below is perfectly valid functional interface.

```
@FunctionalInterface
public interface MyFirstFunctionalInterface 
{
    public void firstWork();
 
    @Override
    public String toString();                //Overridden from Object class
 
    @Override
    public boolean equals(Object obj);        //Overridden from Object class
}
```

## Default Methods
Java 8 allows you to add non-abstract methods in interfaces. These methods must be declared default methods. Default methods were introduces in java 8 to enable the functionality of lambda expression.

Default methods enable you to add new functionality to the interfaces of your libraries and ensure binary compatibility with code written for older versions of those interfaces.

