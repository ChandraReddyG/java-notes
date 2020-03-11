# Java 9 New Features

## Java 9 Module System

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
