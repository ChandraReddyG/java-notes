# Builder Design Pattern

The builder pattern provides a build object which is used to construct a complex object called the product. It encapsulates the logic of constructing the different pieces of the product.

Typically the builder pattern is implemented by an class which has several methods to configure the product. These methods typically return the builder object. This allows to use the builder via a fluent API, e.g, by calling methods directly after each other. Once the product is completely configured a build() method is called to construct the object.

## Example

Assume you have a data model like the following.


```java
public class Task {
    private final long id;
    private String summary = "";
    private String description = "";
    private boolean done = false;
    private Date dueDate;
}
```

Your builder would looks like the following.

```java
import java.util.Date;

public class TaskBuilder {

    private final long id;
    private String summary = "";
    private String description = "";
    private boolean done = false;
    private Date dueDate;

    public TaskBuilder(long id, String summary, String description, boolean done,
            Date dueDate) {
        this.id = id;
        this.summary = summary;
        this.description = description;
        this.done = done;
        this.dueDate = dueDate;
    }


    public TaskBuilder setSummary(String summary) {
        this.summary = summary;
        return this;
    }

    public TaskBuilder setDescription(String description) {
        this.description = description;
        return this;
    }

    public TaskBuilder setDone(boolean done) {
        this.done = done;
        return this;
    }

    public TaskBuilder setDueDate(Date dueDate) {
        this.dueDate = new Date(dueDate.getTime());
        return this;
    }
    public Task build() {
        return new Task(id,summary, description,done, dueDate);
    }
}
```