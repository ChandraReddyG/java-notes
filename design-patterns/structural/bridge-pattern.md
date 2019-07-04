# Bridge Design Pattern

The Bridge design pattern allows you to separate the abstraction from the implementation.

* Bridge patterns decouple the abstract class and its implementation classes by providing a bridge structure between them.

* The bridge pattern is useful when both the class and what it does vary, often because changes in the class can be made easily with minimal prior knowledge about the program. 

* The bridge pattern allows us to avoid compile-time binding between an abstraction and its implementation. This is so that an implementation can be selected at run-time.

## Example

Suppose we have formatter of different types and it can be used to write on console or file to write some list. So formatter and Writer can be changed independently and there will be bridge beteween them (**Writer** abstract class).

```java
// Formatter interface
public interface Formatter {
	String format(Map<String, String> items);
}
```

```java
public class JSONFormatter implements Formatter {

    @Override
    public String format(Map<String, String> items) {
        StringBuilder builder = new StringBuilder("{");

        for (Map.Entry<String, String> item: items.entrySet()) {
            builder.append(item.getKey() + ":" + item.getValue() + ",");
        }

        builder.deleteCharAt(builder.lastIndexOf(","));

        builder.append("}");

        return builder.toString();
    }
}
```

```java
public class XMLFormatter implements Formatter {
    @Override
    public String format(Map<String, String> items) {
        StringBuilder builder = new StringBuilder("<object>");

        for (Map.Entry<String, String> item: items.entrySet()) {
            builder.append("<"+ item.getKey() + ">" + item.getValue() + "</" + item.getKey() + ">");
        }

        builder.append("/<object>");

        return builder.toString();
    }
}

```

```java
// This class will act as bridge between Formatter and Writers
public abstract class Writer {

    public String write(Formatter formatter) {
        return formatter.format(getItems());
    }

    public abstract Map<String, String> getItems();
}
```

```java
public class ConsoleWriter extends Writer {

    private Map<String, String> items;

    public ConsoleWriter(Map<String, String> items) {
        this.items = items;
    }

    @Override
    public Map<String, String> getItems() {
        return items;
    }
}
```

```java
public static void main(String[] args) {
    Map<String, String> items = new HashMap<>();
    items.put("Name","Pradeep");
    items.put("Age", "35");
    items.put("Gender" , "Male");

    Formatter jsonFormatter = new JSONFormatter();
    ConsoleWriter writer = new ConsoleWriter(items);

    String result = writer.write(jsonFormatter);
    System.out.println(result);

    XMLFormatter xmlFormatter = new XMLFormatter();

    result = writer.write(xmlFormatter);
    System.out.println(result);

}
```