# Decorator Design Pattern

The decorator design pattern allows us to dynamically add functionality and behavior to an object without affecting the behavior of other existing objects in the same class. 

We use inheritance to extend the behavior of the class. This takes place at compile time, and all of the instances of that class get the extended behavior.

Decorator design patterns allow us to add functionality to an object (not the class) at runtime, and we can apply this customized functionality to an individual object based on our requirement and choice.

Decorator patterns allow a user to add new functionality to an existing object without altering its structure. So, there is no change to the original class.

## Example

```java
public interface Pizza {
    public String bakePizza();
    public float getCost();
}
```

```java
public class BasePizza implements Pizza{
 
    public String bakePizza() {
        return "Basic Pizza";
    }
    public float getCost(){
        return 100;
    }
}
```

```java
public class PizzaDecorator implements Pizza {
    Pizza pizza;
    public PizzaDecorator(Pizza newPizza) {
        this.pizza = newPizza;
    }
 
    public String bakePizza() {
        return pizza.bakePizza();
    }
 
    @Override
    public float getCost() {
        return pizza.getCost();
    }
}
```

```java
public class Mushroom extends PizzaDecorator {
 
    public Mushroom(Pizza newPizza) {
        super(newPizza);
    }
 
    @Override
    public String bakePizza() {
        return super.bakePizza() + &amp;quot; with Mashroom Topings&amp;quot;;
    }
 
    @Override
    public float getCost() {
        return super.getCost()+80;
    }
}
```

```java
public class Pepperoni extends PizzaDecorator {
 
    public Pepperoni(Pizza newPizza) {
        super(newPizza);
    }
 
    @Override
    public String bakePizza() {
        return super.bakePizza() + &amp;quot; with Pepperoni Toppings&amp;quot;;
    }
 
    @Override
    public float getCost() {
        return super.getCost()+110;
    }
}
```

```java
 @Test
    public void shouldMakePepperoniPizza(){
        Pizza pizza = new Pepperoni(new BasePizza());
        assertEquals(&amp;quot;Basic Pizza with Pepperoni Toppings&amp;quot;,pizza.bakePizza());
        assertEquals(210.0,pizza.getCost(),0);
    }
```