# Composite Design pattern

The composite pattern is meant to allow treating individual objects and compositions of objects, or “composites” in the same way. 

It can be viewed as a tree structure made up of types that inherit a base type, and it can represent a single part or a whole hierarchy of objects.

We can break the pattern down into:

* component – is the base interface for all the objects in the composition. It should be either an interface or an abstract class with the common methods to manage the child composites.
* leaf – implements the default behavior of the base component. It doesn’t contain a reference to the other objects.
* composite – has leaf elements. It implements the base component methods and defines the child-related operations.
* client – has access to the composition elements by using the base component object.
  
## Example

```java
public interface Department {
    void printDepartmentName();
}
```

```java
public class FinancialDepartment implements Department {
 
    private Integer id;
    private String name;
 
    public void printDepartmentName() {
        System.out.println(getClass().getSimpleName());
    }
 
    // standard constructor, getters, setters
}
```

```java
public class SalesDepartment implements Department {
 
    private Integer id;
    private String name;
 
    public void printDepartmentName() {
        System.out.println(getClass().getSimpleName());
    }
 
    // standard constructor, getters, setters
}
```

```java
public class HeadDepartment implements Department {
    private Integer id;
    private String name;
 
    private List<Department> childDepartments;
 
    public HeadDepartment(Integer id, String name) {
        this.id = id;
        this.name = name;
        this.childDepartments = new ArrayList<>();
    }
 
    public void printDepartmentName() {
        childDepartments.forEach(Department::printDepartmentName);
    }
 
    public void addDepartment(Department department) {
        childDepartments.add(department);
    }
 
    public void removeDepartment(Department department) {
        childDepartments.remove(department);
    }
}
```

```java
public static void main(String args[]) {
    Department salesDepartment = new SalesDepartment(1, "Sales department");
    Department financialDepartment = new FinancialDepartment(2, "Financial department");

    HeadDepartment headDepartment = new HeadDepartment(3, "Head department");

    headDepartment.addDepartment(salesDepartment);
    headDepartment.addDepartment(financialDepartment);

    headDepartment.printDepartmentName();
}
```