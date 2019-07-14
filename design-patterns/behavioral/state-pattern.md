# State Design Pattern

State is a behavioral design pattern that allows an object to change the behavior when its internal state changes.

The main idea of State pattern is to allow the object for changing its behavior without changing its class. Also, by implementing it, the code should remain cleaner without many if/else statements.

Imagine we have a package which is sent to a post office, the package itself can be ordered, then delivered to a post office and finally received by a client. Now, depending on the actual state, we want to print its delivery status.

The simplest approach would be to add some boolean flags and apply simple if/else statements within each of our methods in the class. That won’t complicate it much in a simple scenario. However, it might complicate and pollute our code when we’ll get more states to process which will result in even more if/else statements.

## Example

```java
public class Package {
 
    private PackageState state = new OrderedState();
 
    // getter, setter
 
    public void previousState() {
        state.prev(this);
    }
 
    public void nextState() {
        state.next(this);
    }
 
    public void printStatus() {
        state.printStatus();
    }
}
```

```java
public interface PackageState {
 
    void next(Package pkg);
    void prev(Package pkg);
    void printStatus();
}
```

```java
public class OrderedState implements PackageState {
 
    @Override
    public void next(Package pkg) {
        pkg.setState(new DeliveredState());
    }
 
    @Override
    public void prev(Package pkg) {
        System.out.println("The package is in its root state.");
    }
 
    @Override
    public void printStatus() {
        System.out.println("Package ordered, not delivered to the office yet.");
    }
}
```

```java
public class DeliveredState implements PackageState {
 
    @Override
    public void next(Package pkg) {
        pkg.setState(new ReceivedState());
    }
 
    @Override
    public void prev(Package pkg) {
        pkg.setState(new OrderedState());
    }
 
    @Override
    public void printStatus() {
        System.out.println("Package delivered to post office, not received yet.");
    }
}
```

```java
public class ReceivedState implements PackageState {
 
    @Override
    public void next(Package pkg) {
        System.out.println("This package is already received by a client.");
    }
 
    @Override
    public void prev(Package pkg) {
        pkg.setState(new DeliveredState());
    }
}
```

```java
@Test
public void givenNewPackage_whenPackageReceived_thenStateReceived() {
    Package pkg = new Package();
 
    assertThat(pkg.getState(), instanceOf(OrderedState.class));
    pkg.nextState();
 
    assertThat(pkg.getState(), instanceOf(DeliveredState.class));
    pkg.nextState();
 
    assertThat(pkg.getState(), instanceOf(ReceivedState.class));
}
```