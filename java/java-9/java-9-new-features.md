# Java 9 New Features

## Java 9 Module System
One of the big changes or java 9 feature is the Module System. Oracle Corp is going to introduce the following features as part of Jigsaw Project.

**Modular JDK
**Modular Java Source Code
**Modular Run-time Images
**Encapsulate Java Internal APIs
**Java Platform Module System

Before Java SE 9 versions, we are using Monolithic Jars to develop Java-Based applications. This architecture has a lot of limitations and drawbacks. To avoid all these shortcomings, Java SE 9 is coming with the Module System.

JDK 9 is coming with 92 modules (may change in final release). We can use JDK Modules and also we can create our own modules as shown below:

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
