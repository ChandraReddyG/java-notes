# Facade Design Pattern

Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use.

Wrap a complicated subsystem with a simpler interface.

In Java, the interface JDBC can be called a facade because, we as users or clients create connection using the “java.sql.Connection” interface, the implementation of which we are not concerned about. The implementation is left to the vendor of driver.

Another good example can be the startup of a computer. When a computer starts up, it involves the work of cpu, memory, hard drive, etc. To make it easy to use for users, we can add a facade which wrap the complexity of the task, and provide one simple interface instead.

## Example

```java
public interface Hotel 
{ 
    public Menus getMenus(); 
}
```

```java
public class NonVegRestaurant implements Hotel 
{ 
    public Menus getMenus() 
    { 
        NonVegMenu nv = new NonVegMenu(); 
        return nv; 
    } 
} 
```

```java
public class VegRestaurant implements Hotel 
{ 
    public Menus getMenus() 
    { 
        VegMenu v = new VegMenu(); 
        return v; 
    } 
}
```

```java
public class VegNonVegBothRestaurant implements Hotel 
{ 
    public Menus getMenus() 
    { 
        Both b = new Both(); 
        return b; 
    } 
} 
```

```java
public class HotelFacade
{ 
    public VegMenu getVegMenu() 
    { 
        VegRestaurant v = new VegRestaurant(); 
        VegMenu vegMenu = (VegMenu)v.getMenus(); 
        return vegMenu; 
    } 
      
    public NonVegMenu getNonVegMenu() 
    { 
        NonVegRestaurant v = new NonVegRestaurant(); 
        NonVegMenu NonvegMenu = (NonVegMenu)v.getMenus(); 
        return NonvegMenu; 
    } 
      
    public Both getVegNonMenu() 
    { 
        VegNonBothRestaurant v = new VegNonBothRestaurant(); 
        Both bothMenu = (Both)v.getMenus(); 
        return bothMenu; 
    }     
}
```

```java
public static void main (String[] args)  {
    HotelKeeper keeper = new HotelKeeper();

    VegMenu v = keeper.getVegMenu(); 
    NonVegMenu nv = keeper.getNonVegMenu();
    Both = keeper.getVegNonMenu();
}
```
