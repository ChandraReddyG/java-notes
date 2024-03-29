# Java 9 New Features

## Java 9 Module System
One of the big changes or java 9 feature is the Module System. Oracle Corp is going to introduce the following features as part of Jigsaw Project.

* Modular JDK
* Modular Java Source Code
* Modular Run-time Images
* Encapsulate Java Internal APIs
* Java Platform Module System

Before Java SE 9 versions, we are using Monolithic Jars to develop Java-Based applications. This architecture has a lot of limitations and drawbacks. To avoid all these shortcomings, Java SE 9 is coming with the Module System.

JDK 9 is coming with 92 modules (may change in final release).
A modular system provides capabilities similar to OSGi framework's system. Modules have a concept of dependencies, can export a public API and keep implementation details hidden/private.

One of the main motivations here is to provide modular JVM, which can run on devices with a lot less available memory. The JVM could run with only those modules and APIs which are required by the application. 

Also, JVM internal (implementation) APIs like com.sun.* are no longer accessible from application code.

Simply put, the modules are going to be described in a file called module-info.java located in the top of java code hierarchy:

```
module com.java9.modules.car {
    requires com.java9.modules.engines;
    exports com.java9.modules.car.handling;
}
```
Ref: 
https://openjdk.java.net/projects/jigsaw/quick-start

## jlink tool in Java 9
When you have modules with explicit dependencies, and a modularized JDK, new possibilities arise. Your application modules now state their dependencies on other application modules and on the modules it uses from the JDK. Why not use that information to create a minimal runtime environment, containing just those modules necessary to run your application? That's made possible with the new jlink tool in Java 9. Instead of shipping your app with a fully loaded JDK installation, you can create a minimal runtime image optimized for your application

## Private interface methods
With Java 9, you can add private helper methods to interfaces

## HTTP/2
A new way of performing HTTP calls arrives with Java 9. This much overdue replacement for the old `HttpURLConnection` API also supports WebSockets and HTTP/2 out of the box. One caveat: The new HttpClient API is delivered as a so-called _incubator module_ in Java 9. 

```
HttpClient client = HttpClient.newHttpClient();

HttpRequest req =
   HttpRequest.newBuilder(URI.create("http://www.google.com"))
              .header("User-Agent","Java")
              .GET()
              .build();

HttpResponse<String> resp = client.send(req, HttpResponse.BodyHandler.asString());
```

## Factory Methods for Immutable List, Set, Map and Map.Entry
```
List immutableList = List.of();
List immutableList = List.of("one","two","three");

Map emptyImmutableMap = Map.of()
```

## Java 9 Try-With-Resources Improvements
In Java 7, the try-with-resources syntax requires a fresh variable to be declared for each resource being managed by the statement.

In Java 9 there is an additional refinement: if the resource is referenced by a final or effectively final variable, a try-with-resources statement can manage a resource without a new variable being declared:

```
MyAutoCloseable mac = new MyAutoCloseable();
try (mac) {
    // do some stuff with mac
}
  
try (new MyAutoCloseable() { }.finalWrapper.finalCloseable) {
   // do some stuff with finalCloseable
} catch (Exception ex) { }
```
## Java Compact Strings
Strings in Java are internally represented by a char[] containing the characters of the String. And, every char is made up of 2 bytes because Java internally uses UTF-16.

For instance, if a String contains a word in the English language, the leading 8 bits will all be 0 for every char, as an ASCII character can be represented using a single byte.

Many characters require 16 bits to represent them but statistically most require only 8 bits — LATIN-1 character representation. So, there is a scope to improve the memory consumption and performance.

What's also important is that Strings typically usually occupy a large proportion of the JVM heap space. And, because of the way they're stored by the JVM, in most cases, a String instance can take up double space it actually needs.

Java 9 has brought the concept of compact Strings back.

This means that whenever we create a String if all the characters of the String can be represented using a byte — LATIN-1 representation, a byte array will be used internally, such that one byte is given for one character.

In other cases, if any character requires more than 8-bits to represent it, all the characters are stored using two bytes for each — UTF-16 representation.

So basically, whenever possible, it’ll just use a single byte for each character.

Ref: https://www.baeldung.com/java-9-compact-string
