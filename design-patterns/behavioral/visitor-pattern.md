# Visitor Design Pattern

The purpose of a Visitor pattern is to define a new operation without introducing the modifications to an existing object structure.

Imagine that we have a composite object which consists of components. The object’s structure is fixed – we either can’t change it, or we don’t plan to add new types of elements to the structure.

Now, how could we add new functionality to our code without modification of existing classes?

The Visitor design pattern might be an answer. Simply put, we’ll have to do is to add a function which accepts the visitor class to each element of the structure.

That way our components will allow the visitor implementation to “visit” them and perform any required action on that element.

In other words, we’ll extract the algorithm which will be applied to the object structure from the classes.

Consequently, we’ll make good use of the Open/Closed principle as we won’t modify the code, but we’ll still be able to extend the functionality by providing a new Visitor implementation.

Shopping in the supermarket is another common example, where the shopping cart is your set of elements. When you get to the checkout, the cashier acts as a visitor, taking the disparate set of elements (your shopping), some with prices and others that need to be weighed, in order to provide you with a total. 

## Example

```java
//Element interface
public interface Visitable{
  public void accept(Visitor visitor);
}
```

```java
//concrete element
public class Book implements Visitable{
  private double price;
  private double weight;
  //accept the visitor
  public void accept(Visitor vistor) {
    visitor.visit(this);
  }
  public double getPrice() {
    return price;
  }
  public double getWeight() {
    return weight;
  }
}
```

Now we'll move on to the Visitor interface. For each different type of concrete element here, we'll need to add a visit method. As we'll just deal with Book for now, this is as simple as: 


```java
public interface Visitor{
  public void visit(Book book);
  
  //visit other concrete items
  public void visit(CD cd);
  public void visit(DVD dvd);
}
```

The implementation of the Vistor can then deal with the specifics of what to do when we visit a book. 

```java
public class PostageVisitor implements Visitor {
  private double totalPostageForCart;
  //collect data about the book
  public void visit(Book book) {
    //assume we have a calculation here related to weight and price
    //free postage for a book over 10 
    if(book.getPrice() < 10.0) {
      totalPostageForCart += book.getWeight() * 2;
    }
  }
  //add other visitors here
  public void visit(CD cd) {...}
  public void visit(DVD dvd) {...}
  //return the internal state
  public double getTotalPostage() {
    return totalPostageForCart;
  }
}
```

```java

public class ShoppingCart {
  //normal shopping cart stuff
  private ArrayList<Visitable> items;
  public double calculatePostage() {
    //create a visitor
    PostageVisitor visitor = new PostageVisitor();
    //iterate through all items
    for(Visitable item: items) {
      item.accept(visitor);
    }
    double postage = visitor.getTotalPostage();
    return postage;
  }
}
```

Note that if we had other types of item here, once the visitor implements a method to visit that item, we could easily calculate the total postage.

So, while the Visitor may seem a bit strange at first, you can see how much it helps to clean up your code. That's the whole point of this pattern - to allow you seperate out certain logic from the elements themselves, keeping your data classes simple.

