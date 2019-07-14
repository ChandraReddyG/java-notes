# Factory Design Pattern

Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

Defining a "virtual" constructor.

The new operator considered harmful.

## Example

In this example, we’ll create a Polygon interface which will be implemented by several concrete classes. A PolygonFactory will be used to fetch objects from this family:

Let’s first create the Polygon interface:

```java
public interface Polygon {
    String getType();
}
```

Next, we’ll create a few implementations like Square, Triangle, etc. that implement this interface and return an object of Polygon type.

Now we can create a factory that takes the number of sides as an argument and returns the appropriate implementation of this interface:

```java
public class PolygonFactory {
    public Polygon getPolygon(int numberOfSides) {
        if(numberOfSides == 3) {
            return new Triangle();
        }
        if(numberOfSides == 4) {
            return new Square();
        }
        if(numberOfSides == 5) {
            return new Pentagon();
        }
        if(numberOfSides == 7) {
            return new Heptagon();
        }
        else if(numberOfSides == 8) {
            return new Octagon();
        }
        return null;
    }
}
```

## When to Use Factory Method Design Pattern

* When the implementation of an interface or an abstract class is expected to change frequently
* When the current implementation cannot comfortably accommodate new change
* When the initialization process is relatively simple, and the constructor only requires a handful of parameters
