# Abstract Factory Design Pattern

By contrast, the Abstract Factory Design Pattern is used to create families of related or dependent objects. It’s also sometimes called a factory of factories.

Abstract Factory pattern is almost similar to Factory Pattern is considered as another layer of abstraction over factory pattern. Abstract Factory patterns work around a super-factory which creates other factories.

Abstract Factory **provides an interface for creating families of related or dependent objects without specifying their concrete classes**. In other words, this model allows us to create objects that follow a general pattern.

## Example

```java
public interface GeometricShape {
    void draw();
}
```

```java

public class Line implements GeometricShape {
    @Override
    public void draw() {
        System.out.println("Line Drawn.");
    }
}
```

```java
public class Circle implements GeometricShape {
    @Override
    public void draw() {
        System.out.println("Circle is drawn.");
    }
}
```

```java
public enum FactoryType {
  TWO_D_SHAPE_FACTORY,
  THREE_D_SHAPE_FACTORY
}
public enum  ShapeType {
    LINE,
    CIRCLE,
    SPHERE
}
```

```java
public abstract class AbstractFactory {
    abstract GeometricShape getShape(ShapeType name);
}
```

```java

public class TwoDShapeFactory extends AbstractFactory {
    @Override
    GeometricShape getShape(ShapeType name) {
        if (ShapeType.LINE == name) {
            return new Line();
        } else if (ShapeType.CIRCLE == name) {
            return new Circle();
        }
        return null;
    }
}
```

```java
public class ThreeDShapeFactory extends AbstractFactory {
    @Override
    GeometricShape getShape(ShapeType name) {
        if (ShapeType.SPHERE == name) {
            return new Sphere();
        }
        return null;
    }
}
```

```java

public class FactoryProvider {
    public static AbstractFactory getFactory(FactoryType factoryType) {
        if (FactoryType.TWO_D_SHAPE_FACTORY == factoryType) {
            return new TwoDShapeFactory();
        } else if (FactoryType.THREE_D_SHAPE_FACTORY == factoryType) {
            return new ThreeDShapeFactory();
        }
        return null;
    }
}
```

```java

public class Application {
    public static void main(String[] args) {
        //drawing 2D shape
        AbstractFactory factory = FactoryProvider.getFactory(TWO_D_SHAPE_FACTORY);
        if (factory == null) {
            System.out.println("Factory for given name doesn't exist.");
            System.exit(1);
        }
        //getting shape using factory obtained
        GeometricShape shape = factory.getShape(ShapeType.LINE);
        if (shape != null) {
            shape.draw();
        } else {
            System.out.println("Shape with given name doesn't exist.");
        }
        //drawing 3D shape
        factory = FactoryProvider.getFactory(THREE_D_SHAPE_FACTORY);
        if (factory == null) {
            System.out.println("Factory for given name doesn't exist.");
            System.exit(1);
        }
        //getting shape using factory obtained
        shape = factory.getShape(ShapeType.SPHERE);
        if (shape != null) {
            shape.draw();
        } else {
            System.out.println("Shape with given name doesn't exist.");
        }
    }
}
```