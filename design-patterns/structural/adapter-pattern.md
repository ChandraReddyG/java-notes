# Adapter Design Pattern

An Adapter pattern acts as a connector between two incompatible interfaces that otherwise cannot be connected directly. An Adapter wraps an existing class with a new interface so that it becomes compatible with the client’s interface.

The main motive behind using this pattern is to convert an existing interface into another interface that the client expects. It’s usually implemented once the application is designed.

* The adapter design pattern is a structural design pattern that allows two unrelated/uncommon interfaces to work together. In other words, the adapter pattern makes two incompatible interfaces compatible without changing their existing code.

* The adapter pattern is often used to make existing classes work with others without modifying their source code.

* This pattern converts the (incompatible) interface of a class (the adaptee) into another interface (the target) that clients require.

* The adapter pattern also lets classes work together, which, otherwise, couldn't have worked, because of the incompatible interfaces.

## Example

Consider a scenario in which there is an app that’s developed in the US which returns the top speed of luxury cars in miles per hour (MPH). Now we need to use the same app for our client in the UK that wants the same results but in kilometers per hour (km/h).

```java
// New interface
public interface EmployeeNew {
    int getId();
    String getFirstName();
    String getLastName();
}
```

```java
// This is old class which we cannot change
public class EmployeeOld {
    private String id;
    private String givenName;
    private String surname;

    //...
}
```

```java
/**
 * Adapter to make old employee compatible with new one
 *
 */
public class EmployeeOldAdapter implements EmployeeNew {

    private EmployeeOld employeeOld;

    public EmployeeOldAdapter(EmployeeOld employeeOld) {
        this.employeeOld = employeeOld;
    }

    @Override
    public int getId() {
        return Integer.valueOf(employeeOld.getId());
    }

    @Override
    public String getFirstName() {
        return employeeOld.getGivenName();
    }

    @Override
    public String getLastName() {
        return employeeOld.getSurname();
    }
}
```

```java
public class MovableAdapterImpl implements MovableAdapter {
    private Movable luxuryCars;
    // standard constructors

    @Override
    public double getSpeed() {
        return convertMPHtoKMPH(luxuryCars.getSpeed());
    }

    private double convertMPHtoKMPH(double mph) {
        return mph * 1.60934;
    }
}
```

```java
public static void main(String[] args) {

    List<EmployeeNew> employeeNewList = new ArrayList<>();

    employeeNewList.add(new EmployeeNewImpl(1, "Adand", "Kumar"));

    // This object we cannot directly add to employee list as it doesn't implement the EmployeeNew Interface
    EmployeeOld employeeOld = new EmployeeOld("123", "Ram", "Kumar");

    EmployeeOldAdapter employeeOldAdapter = new EmployeeOldAdapter(employeeOld);

    employeeNewList.add(employeeOldAdapter);

    System.out.println(employeeNewList);

}
```
