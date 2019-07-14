# Memento design pattern

Memento pattern is a behavioral design pattern. Memento pattern is used to restore state of an object to a previous state. As your application is progressing, you may want to save checkpoints in your application and restore back to those checkpoints later.

* **originator** : the object for which the state is to be saved. It creates the memento and uses it in future to undo.
* **memento** : the object that is going to maintain the state of originator. Its just a POJO.
* **caretaker** : the object that keeps track of multiple memento. Like maintaining savepoints.

![memento.png](images/memento.png)

Memento pattern uses three actor classes. Memento contains state of an object to be restored. Originator creates and stores states in Memento objects and Caretaker object is responsible to restore object state from Memento. We have created classes Memento, Originator and CareTaker.

```java
public class Employee {

	private String name;
	private String address;
	private String phone;

	public EmployeeMemento save() {
		return new EmployeeMemento(name, phone);
	}

	public void revert(EmployeeMemento emp) {
		this.name = emp.getName();
		this.phone = emp.getPhone();
	}
    // getters and setters
}
```

```java
public class EmployeeMemento {

	private String name;
	private String phone;

	public EmployeeMemento(String name, String phone) {
		this.name = name;
		this.phone = phone;
	}

    // getters and setters
}
```

```java
public class CareTaker {

	private Stack<EmployeeMemento> empHistory = new Stack<>();
	
	public void save(Employee emp) {
		empHistory.push(emp.save());
	}
	
	public void revert(Employee emp) {
		EmployeeMemento reveringMem = empHistory.pop();
		emp.revert(reveringMem);
	}
}
```

```java
public class MementoClient {

    public static void main(String[] args) {
        CareTaker careTaker = new CareTaker();

        Employee emp = new Employee();
        emp.setName("Ashay Patil");
        emp.setPhone("21323123");
        emp.setAddress("London");

		// Put Into Memento
        System.out.println("Employee before save : " + emp);
		careTaker.save(emp);

		// Update and Put Into Memento
		emp.setPhone("9999999");
		careTaker.save(emp);
		System.out.println("Employee after phone change : " + emp);

		emp.setPhone("234870732"); // Not put into memento

		careTaker.revert(emp);
		System.out.println("Reverting to previous save : " + emp);

		careTaker.revert(emp);
		System.out.println("Reverting to orignal save : " + emp);

	}
}
```