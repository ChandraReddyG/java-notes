# Iterator Design Pattern

Iterator Pattern is a relatively simple and frequently used design pattern. There are a lot of data structures/collections available in every language. Each collection must provide an iterator that lets it iterate through its objects. However while doing so it should make sure that it does not expose its implementation.

## When to use iterator design pattern

Every programming language support some data structures like list or maps, which are used to store a group of related objects. In Java, we have List, Map and Set interfaces and their implementations such as ArrayList and HashMap.

The iterator pattern allow us to design a collection iterator in such a way that –

* we are able to access elements of a collection without exposing the internal structure of elements or collection itself.
* iterator supports multiple simultaneous traversals of a collection from start to end in forward, backward or both directions.
* iterator provide a uniform interface for traversing different collection types transparently.

## Examples

Implement Iterable iterface in our repository. Which exposes the Iterator to loop though all the items.

```java
import java.util.Iterator;

public class MyList implements Iterable<String>{

	private static final int MAX_SIZE = 6;
	private int length = 0;
	private String list[];

	public MyList(){
		list = new String[MAX_SIZE];
	}
	
	public void addItem(String str){
		if(length >= MAX_SIZE){
			System.out.println("The list is full, You can't add anymore");
		}else{
			list[length++] = str;
		}
	}
	
	@Override
	public Iterator<String> iterator() {
		return new ListIterator(list, length);
	}
}
```

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class ListIterator implements Iterator<String> {

	private String list[];
	private int size = 0;
	private int curIndex = 0;
	
	public ListIterator(String[] list, int size) {
		this.list = list;
		this.size = size;
	}
	
	@Override
	public boolean hasNext() {
		return size > curIndex;
	}

	@Override
	public String next() {
		if (!hasNext()) {
			throw new NoSuchElementException();
		}
		return list[curIndex++];
	}
	
	public void remove(){
		throw new UnsupportedOperationException(); 
	}
}
```

Main Class

```java
public static void main(String[] args) {
    
    // create list object(list of strings)
    MyList list = new MyList();
    
    // add strings 
    list.addItem("Prague");
    list.addItem("California");
    list.addItem("Amsterdam");
    
    // get iterator object 
    Iterator<String> it = list.iterator();
    
    // iterate through list items
    while(it.hasNext()){
        System.out.println(it.next());
    }
    
}
```