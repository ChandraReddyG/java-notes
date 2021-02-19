# Java Multithreading & Concurrency

## What's a monitor in Java

A monitor is mechanism to control concurrent access to an object.
This allows you to do:

Thread-1
```java
public void a()
{
    synchronized(someObject) {
        // do something (1)
    }
}
```

Thread-2
```java
public void b()
{
    synchronized(someObject) {
        // do something else (2)
    }
}
```

This prevents Threads 1 and 2 accessing the monitored (synchronized) section at the same time. One will start, and monitor will prevent the other from accessing the region before the first one finishes.

## Locks in Java

A lock is a thread synchronization mechanism like synchronized blocks except locks can be more sophisticated than Java's synchronized blocks. Locks (and other more advanced synchronization mechanisms) are created using synchronized blocks, so it is not like we can get totally rid of the synchronized keyword.

From Java 5 the package **java.util.concurrent.locks** contains several lock implementations, so you may not have to implement your own locks.

### Intrinsic Lock

Intrinsic Or Monitor Lock Java provide built-in locking mechanism to make any operation atomic by synchronization in concurrent environment. In Java programming language ‘synchronized' keyword is used for taking lock or monitor on object. There are other locking utility also available in Java but we will concentrate on basic locking mechanism (synchronized) only. synchronized keyword can be use with method signature or with particular code block to make that synchronize. These built-in locks known as intrinsic or monitor lock.

```java
public synchronized void objectLevelLock() {
 // Code to make synchronized or atomic
}

public void objectLevelLock () {
 synchronized(lockObject) {
  // Code to make synchronized or atomic
 }
}

public static synchronized void classLevelLock () {
 // Static members and Code to make synchronized or atomic
}

public void classLevelLock () {
 synchronized(ClassName.class) {
  // Static members and Code to make synchronized or atomic
 }
}
```

### ReentrantLock

A reentrant lock is one where a process can claim the lock multiple times without blocking on itself. It's useful in situations where it's not easy to keep track of whether you've already grabbed a lock. If a lock is non re-entrant you could grab the lock, then block when you go to grab it again, effectively deadlocking your own process.

ReentrantLock allow threads to enter into lock on a resource more than once. When the thread first enters into lock, a hold count is set to one. Before unlocking the thread can re-enter into lock again and every time hold count is incremented by one. For every unlock request, hold count is decremented by one and when hold count is 0, the resource is unlocked.

Reentrant Locks also offer a fairness parameter, by which the lock would abide by the order of the lock request i.e. after a thread unlocks the resource, the lock would go to the thread which has been waiting for the longest time. This fairness mode is set up by passing true to the constructor of the lock.

#### A use case for re-entrant locking

A (somewhat generic and contrived) example of an application for a re-entrant lock might be:

* You have some computation involving an algorithm that traverses a graph (perhaps with cycles in it). A traversal may visit the same node more than once due to the cycles or due to multiple paths to the same node.

* The data structure is subject to concurrent access and could be updated for some reason, perhaps by another thread. You need to be able to lock individual nodes to deal with potential data corruption due to race conditions. For some reason (perhaps performance) you don't want to globally lock the whole data structure.

* You computation can't retain complete information on what nodes you've visited, or you're using a data structure that doesn't allow 'have I been here before' questions to be answered quickly.

```java
public void some_method() { 
        reentrantlock.lock(); 
        try { 
            //Do some work 
        } 
        catch(Exception e)  { 
            e.printStackTrace(); 
        } 
        finally { 
            reentrantlock.unlock(); 
        } 
          
} 
```

Other Example : lock and unlock has to be called in same thread (can be in different methods (setlock)).

```java
    private int count = 0;
    private Lock lock = new ReentrantLock();

    private void increment() {
        for (int i = 0; i < 10000; i++) {
            count++;
        }
    }

    public void setLock() {
        lock.lock();
    }

    public void firstThread() throws InterruptedException {
        setLock();
        try {
            increment();
        } finally {
            lock.unlock();
        }
    }

    public void secondThread() throws InterruptedException {
        Thread.sleep(1000);
        lock.lock();
        try {
            increment();
        } finally {
            //should be written to unlock Thread whenever signal() is called
            lock.unlock();
        }
    }

    public void finished() {
        System.out.println("Count is: " + count);
    }

    // Main Method
    final Runner runner = new Runner();
        Thread t1 = new Thread(new Runnable() {
            public void run() {
                try {
                    runner.firstThread();
                } catch (InterruptedException ignored) {
                }
            }
        });

    Thread t2 = new Thread(new Runnable() {
        public void run() {
            try {
                runner.secondThread();
            } catch (InterruptedException ignored) {
            }
        }
    });

    t1.start();
    t2.start();
    t1.join();
    t2.join();
    runner.finished();
```

The unlock statement is always called in the finally block to ensure that the lock is released even if an exception is thrown in the method body(try block).

#### ReentrantLock Methods

* lock(): Call to the lock() method increments the hold count by 1 and gives the lock to the thread if the shared resource is initially free.
* unlock(): Call to the unlock() method decrements the hold count by 1. When this count reaches zero, the resource is released.
* tryLock(): If the resource is not held by any other thread, then call to tryLock() returns true and the hold count is incremented by one. If the resource is not free then the method returns false and the thread is not blocked but it exits.
* tryLock(long timeout, TimeUnit unit): As per the method, the thread waits for a certain time period as defined by arguments of the method to acquire the lock on the resource before exiting.
* lockInterruptibly(): This method acquires the lock if the resource is free while allowing for the thread to be interrupted by some other thread while acquiring the resource. It means that if the current thread is waiting for lock but some other thread requests the lock, then the current thread will be interrupted and return immediately without acquiring lock.
* getHoldCount(): This method returns the count of the number of locks held on the resource.
* isHeldByCurrentThread(): This method returns true if the lock on the resource is held by the current thread.

### Condition

Like threads communicate using wait(), notify() and notifyAll() methods of object when intrinsic lock is used to restrict access to a shared objects, Condition allows inter threads communication when external locks are used. Condition defines mechanism to suspend a thread until another thread notifies it. Using condition, deadlock issue can be avoided with external locks.

Condition object is obtained by calling newCondition() methods on lock object, ReentrantLock or ReentrantReadWriteLock. Condition defines methods such as await(), signal() and signalAll() for waiting and notifying. Overloaded await() methods allows you to specify the duration of the wait. Method siganlAll() notifies all waiting threads.


```java
private int count = 0;
    private Lock lock = new ReentrantLock();
    private Condition cond = lock.newCondition();

    private void increment() {
        for (int i = 0; i < 10000; i++) {
            count++;
        }
    }

    public void setLock() {
        lock.lock();
    }

    public void firstThread() throws InterruptedException {
        setLock();

        System.out.println("Waiting ....");
        cond.await();
        System.out.println("Woken up!");
        try {
            increment();
        } finally {
            lock.unlock();
        }
    }

    public void secondThread() throws InterruptedException {
        Thread.sleep(1000);
        lock.lock();
        System.out.println("Press the return key!");
        new Scanner(System.in).nextLine();
        System.out.println("Got return key!");
        cond.signal();
        try {
            increment();
        } finally {
            //should be written to unlock Thread whenever signal() is called
            lock.unlock();
        }
    }

    public void finished() {
        System.out.println("Count is: " + count);
    }
```

### ReadWrite Lock - > ReentrantReadWriteLock

Threads can obtain read lock and write lock using ReadWriteLock. Read lock is for read only operations and write lock is for writes. Thread which acquires read lock can see updates made by the thread which previously released the write lock.

ReadWriteLock is implemented by ReentrantReadWriteLock Class in java.util.concurrent.locks package.Multiple Threads can acquire multiple read Locks, but only a single Thread can acquire mutually-exclusive write Lock .Other threads requesting readLocks have to wait till the write Lock is released. 

```java
public class SynchronizedHashMapWithReadWriteLock {
 
    Map<String,String> syncHashMap = new HashMap<>();
    ReadWriteLock lock = new ReentrantReadWriteLock();
    // ...
    Lock writeLock = lock.writeLock();
 
    public void put(String key, String value) {
        try {
            writeLock.lock();
            syncHashMap.put(key, value);
        } finally {
            writeLock.unlock();
        }
    }
    ...
    public String get(String key){
        try {
            readLock.lock();
            return syncHashMap.get(key);
        } finally {
            readLock.unlock();
        }
    }
    //...
}
```

### StampedLock

StampedLock was introduced in Java 8 and has been one of the most important features of the concurrent family.

Sometimes, we need better control over synchronization and to achieve this, we need a separate lock for read and write access. Thankfully, Java introduced ReentrantReadWriteLock under the java.util.concurrent.locks package, which provides an explicit locking mechanism.

In ReadWrite Locking policy, it allows the read lock to be held simultaneously by multiple reader threads, as long as there are no writers, and the write lock is exclusive.

But it was later discovered that ReentrantReadWriteLock has some severe issues with starvation if not handled properly (using fairness may help, but it may be an overhead and compromise throughput). For example, a number of reads but very few writes can cause the writer thread to fall into starvation. Make sure to analyze your setup properly to know how many reads/writes are present before choosing ReadWriteLock.

StampedLock is made of a stamp and mode, where your lock acquisition method returns a stamp, which is a long value used for unlocking within the finally block. If the stamp is ever zero, that means there's been a failure to acquire access. StampedLock is all about giving us a possibility to perform optimistic reads.

Keep one thing in mind: StampedLock is not reentrant, so each call to acquire the lock always returns a new stamp and blocks if there's no lock available, even if the same thread already holds a lock, which may lead to deadlock.

StampedLock. Not only is this lock faster, but it also offers a powerful API for optimistic locking, where you can obtain a reader lock at a very low cost, hoping that no write operation occurs during the critical section. At the end of the section you query the lock to see whether a write has occurred during that time, in which case you can decide whether to retry, escalate the lock or give up.

* StampedLocks allow optimistic locking for read operations
* ReentrantLocks are reentrant (StampedLocks are not)

So if you have a scenario where you have contention (otherwise you may as well use synchronized or a simple Lock) and more readers than writers, using a StampedLock can significantly improve performance.

**Optimistic locking.** The most important piece in terms of new capabilities for this lock is the new Optimistic locking mode. Research and practical experience show that read operations are for the most part not contended with write operations. Asa result, acquiring a full-blown read lock may prove to be overkill. A better approach may be to go ahead and perform the read, and at the end of it see whether the value has been actually modified in the meanwhile. If that was the case you can retry the read, or upgrade to a heavier lock.

```java
long stamp = lock.tryOptimisticRead(); // non blocking. assume there is not write is being done

read();

if (!lock.validate(stamp)) { // if a write occurred, try again with a read lock

long stamp = lock.readLock();

try {

read();
} finally {

lock.unlock(stamp);
}
}
```

### Semaphore Lock

A semaphore controls access to a shared resource through the use of a counter. If the counter is greater than zero, then access is allowed. If it is zero, then access is denied. What the counter is counting are permits that allow access to the shared resource. Thus, to access the resource, a thread must be granted a permit from the semaphore.

Semaphore plays with a set of permits. Permit is taken and returned back by using two methods of Semaphore class. 

**acquire():** When a thread needs to access a resource, it acquires the permit from the Semaphore using acquire() method. If the permit is not available, it holds until one is available. 

**release():** Once the thread is finished using resource, it needs to return the permit. Using release() method of Semaphore, thread releases the permit back to Semaphore. 

* Synchronized allows only one thread ∞of execution to access the resource at the same time. 
* Semaphore allows up to n (you get to choose n) threads of execution to access the resource at the same time.
* Semaphore locks can be acquire and released in different threads.

```java
    private Semaphore sem = new Semaphore(10, true);
    private int connections = 0;

    private Connection() {
    }

    public static Connection getInstance() {
        return instance;
    }

    public void connect() {
        try {

            // get permit decrease the sem value, if 0 wait for release
            sem.acquire();

            //if doConnect throws and exception is still releases the permit
            //so we use a separate method here to increase the connections count
            doConnect();

        } catch (InterruptedException ignored) {
        } finally {
            //release permit, increase the sem value and activate waiting thread
            sem.release();
        }
    }

    public void doConnect() {
        synchronized (this) { //atomic
            connections++;
        }
        try {
            //do your job
            System.out.println("Current connections (max 10 allowed): " + connections);
            Thread.sleep(2000);
        } catch (InterruptedException ignored) {}
        //when exit doConnect method decrement number of connections
        synchronized (this) {//atomic
            connections--;
            System.out.println("I'm done " + Thread.currentThread().getName() + " Connection is released , connection count: " + connections);
        }
    }

    //  Main class

    public static void main(String[] args) {

        ExecutorService executorService = Executors.newCachedThreadPool();

        for (int i = 0; i < 200; i++) {
            executorService.submit(new Runnable() {
                @Override
                public void run() {
                    Connection.getInstance().connect();
                }
            });
        }
    }
```

## LockSupport

LockSupport is a class located in java.util.concurrent.locks package. Its neighbours are the classes and interfaces as: Lock,ReentrantLock or ReentrantWriteLock. Thanks to this neighbourhood it can be easily deduced that LockSupports is a kind of lock mechanism.

LockSupport provides an alternative for some of Thread's deprecated methods: suspend() and resume(). It uses a concept of permit and parking to detect if given thread should block or not. Permit is associated to each class using LockSupport and is manipulated through park-like methods as:

**park()** - blocks the execution of the current thread.

**time-based park**- a thread can also be "parked" within specified delay: during some time (parkNanos(long)) or until some time (parkUntil(long))

**park with blocker** - park methods allow to pass an object called blocker: (park(Object), parkNanos(Object, long), parkUntil(Object, long)). The blocker is assigned to blocked thread. But beware, it's not a kind of exclusive lock. More than that, the blocker is assigned to thread even before stopping it:

**unpark(Thread)**- unblocks given Thread, i.e. the permit is made available again.

```java
    public static void main(String[] args) throws InterruptedException {
        final Thread thread1   thread invoking this method
            LockSupport.park();
            try {
                Thread.sleep(1_000L);
                System.out.println("After Park");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        thread1.start();

        Thread thread2 = new Thread(() -> {
            System.out.println("In Thread-2 before Park");
            try {
                Thread.sleep(2_600L);
            } catch (InterruptedException e) {
                e.printStackTrace();
                Thread.currentThread().interrupt();
            }
            // unpark(Thread) releases thread specified
            // in the parameter
            LockSupport.unpark(thread1); // UnPark Thread
            System.out.println("In Thread-2 after UnPark");
        });
        thread2.start();

        thread1.join();
        thread2.join();

        // -------------------

        System.out.println("============================");
        Object lock = new Object();

        Thread thread3 = new Thread(() -> {
            // park() blocks thread invoking this method
            LockSupport.park(lock);
            try {
                Thread.sleep(1_000L);
                System.out.println("After Park with params - 1");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        thread3.start();

        Thread thread4 = new Thread(() -> {
            // park() blocks thread invoking this method
            LockSupport.park(lock);
            try {
                Thread.sleep(1_000L);
                System.out.println("After Park with params - 2");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        thread4.start();

        LockSupport.unpark(thread3);
        LockSupport.unpark(thread4);

    }
```

## lock vs Monitor vs Mutex vs Semaphore

### Lock

is a CLR construct that for thread synchronization. lock ensures that only one thread can take ownership of the object’s lock & enter the locked block of code. Other threads must wait till the current owner relinquishes the lock by exiting the block of code.

### Moniter

lock(obj) is implemented internally using a Monitor. You should prefer lock(obj) because it prevents you from goofing up like forgetting the cleanup procedure. It ‘idiot-proof’s the Monitor construct if you will. Using Monitor is generally preferred over mutexes, because monitors were designed specifically for the .NET Framework and therefore make better use of resources. Monitor is limited to Current Application Domain.

### Mutex

a mutex can be used to synchronize threads across processes. When used for inter-process synchronization, a mutex is called a named mutex because it is to be used in another application, and therefore it cannot be shared by means of a global or static variable. It must be given a name so that both applications can access the same mutex object. In contrast, the Mutex class is a wrapper to a Win32 construct. While it is more powerful than a monitor, a mutex requires interop transitions that are more computationally expensive than those required by the Monitor class.

### Semaphore

Let’s say you have a method that is really CPU intensive, and also makes use of resources that you need to control access to (using Mutexes :)). You’ve also determined that a maximum of five calls to the method is about all your machine can hanlde without making it unresponsive. Your best solution here is to make use of the Semaphore class which allows you to limit a certain number of threads’ access to a resource

## What is the difference between a process and a thread?

Both processes and threads are units of concurrency, but they have a fundamental difference: processes do not share a common memory, while threads do.

From the operating system’s point of view, a process is an independent piece of software that runs in its own virtual memory space. Any multitasking operating system (which means almost any modern operating system) has to separate processes in memory so that one failing process wouldn’t drag all other processes down by scrambling common memory.

The processes are thus usually isolated, and they cooperate by the means of inter-process communication which is defined by the operating system as a kind of intermediate API.

On the contrary, a thread is a part of an application that shares a common memory with other threads of the same application. Using common memory allows to shave off lots of overhead, design the threads to cooperate and exchange data between them much faster.

## How can you create a Thread instance and run it?

To create an instance of a thread, you have two options. First, pass a Runnable instance to its constructor and call start(). Runnable is a functional interface, so it can be passed as a lambda expression:

```java
Thread thread1 = new Thread(() ->
  System.out.println("Hello World from Runnable!"));
thread1.start();
```

Thread also implements Runnable, so another way of starting a thread is to create an anonymous subclass, override its run() method, and then call start():

```java
Thread thread2 = new Thread() {
    @Override
    public void run() {
        System.out.println("Hello World from subclass!");
    }
};
thread2.start();
```

## Describe the different states of a Thread and when do the state transitions occur.

The state of a Thread can be checked using the Thread.getState() method. Different states of a Thread are described in the Thread.State enum. They are:

* NEW — a new Thread instance that was not yet started via Thread.start()
* RUNNABLE — a running thread. It is called runnable because at any given time it could be either running or waiting for the next quantum of time from the thread scheduler. A NEW thread enters the RUNNABLE state when you call Thread.start() on it
* BLOCKED — a running thread becomes blocked if it needs to enter a synchronized section but cannot do that due to another thread holding the monitor of this section
* WAITING — a thread enters this state if it waits for another thread to perform a particular action. For instance, a thread enters this state upon calling the Object.wait() method on a monitor it holds, or the Thread.join() method on another thread
* TIMED_WAITING — same as the above, but a thread enters this state after calling timed versions of Thread.sleep(), Object.wait(), Thread.join() and some other methods
* TERMINATED — a thread has completed the execution of its Runnable.run() method and terminated

![java-thread-states.png](images/java-thread-states.png)

### Code samples for different Thread States

> NEW - Thread is not yet started. It's only created with New keyword.

```java
Runnable runnable = () -> {
    System.out.println("Hello");
};

Thread t1 = new Thread(runnable);

// Thread is created and is in state NEW (It is not yet started or running)
System.out.println("Thread State : " + t1.getState());
t1.start(); // Now the thread is started and in different state.
```

> RUNNABLE - Thread is started or running. This is the state after *.start()* method is called on Thread class.

```java
Runnable runnable = () -> {

    System.out.println("Hello World!");
};

Thread t1 = new Thread(runnable);

System.out.println("Thread State : " + t1.getState()); //Thread is in NEW state.
t1.start();
System.out.println("Thread State : " + t1.getState()); // Thread is in RUNNABLE state.
```

> TIMED_WAITING - Goes into this state when timed versions of Thread.sleep(), Object.wait(), Thread.join() is called

```java
Runnable runnable = () -> {
    try {
        Thread.sleep(2000); // Timed versions of Thread.sleep()
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("Hello");
};

// Thread will in TIMED_WAITING state until the Thread.sleep() period mentioned in Runnable block is over

Thread t1 = new Thread(runnable);
System.out.println("Thread State : " + t1.getState()); // NEW State
t1.start();
System.out.println("Thread State : " + t1.getState()); // RUNNABLE State
while (t1.isAlive()) {
    Thread.sleep(500);
    System.out.println("Thread State : " + t1.getState()); // TIMED_WAITING state
}
```

> WAITING - A thread enters this state if it waits for another thread to perform a particular action

```java
Object monitor = new Object();

Runnable runnable = () -> {
    synchronized (monitor) {
        System.out.println("Runnable() Start");
        try {
            // wait(), notify(), notifyAll() always must be in synchronized block
            monitor.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Runnable() End");
    }
};

Thread t1 = new Thread(runnable);
System.out.println("Thread State : " + t1.getState());  // NEW State
t1.start();
System.out.println("Thread State : " + t1.getState());  // RUNNABLE State
while (t1.isAlive()) {
    Thread.sleep(500);
    System.out.println("Thread State : " + t1.getState()); // WAITING state

    synchronized (monitor) {
        // wait(), notify(), notifyAll() always must be in synchronized block
        monitor.notify(); // This block will be in different thread in real scenario.
    }
}
```

> BLOCKED - One thread is holding the monitor required by another thread (Also example of DeadLock)

```java
public class ThreadDeadLock {

    public static void main(String[] args) throws InterruptedException {
        Object obj1 = new Object();
        Object obj2 = new Object();

        // synchronized on object 1 & Object 2
        Thread thread1 = new Thread(new SynchronizeThread(obj1, obj2), "Thread 1");

        // synchronized on object 2 & Object 1
        Thread thread2 = new Thread(new SynchronizeThread(obj2, obj1), "Thread 2");

        thread1.start();
        Thread.sleep(3000);
        thread2.start();
        Thread.sleep(3000);

        while (true) {
            // One of the thread will go in BLOCKED state as the object2 needed by thread 2 is already locked by thread 1.
            System.out.println("Thread 1 State : " + thread1.getState());
            System.out.println("Thread 2 State : " + thread2.getState());
        }
    }
}

class SynchronizeThread implements Runnable {
    private Object obj1;
    private Object obj2;

    public SynchronizeThread(Object obj1, Object obj2) {
        this.obj1 = obj1;
        this.obj2 = obj2;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        synchronized (obj1) {
            System.out.println(name + " acquired lock on Object1: " + obj1);
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            synchronized (obj2) {
                System.out.println(name + " acquired lock on Object2: " + obj2);
            }
            System.out.println(name + " released lock on Object2: " + obj2);
        }
        System.out.println(name + " released lock on Object1: " + obj1);
        System.out.println(name + " Finished Crunchify Deadlock Test.");
    }
}
```

## Why Enclose wait() in a while Loop

Since notify() and notifyAll() randomly wakes up threads that are waiting on this object’s monitor, it’s not always important that the condition is met. Sometimes it can happen that the thread is woken up, but the condition isn’t actually satisfied yet.

We can also define a check to save us from spurious wakeups – where a thread can wake up from waiting without ever having received a notification.

A thread can also wake up without being notified, interrupted, or timing out, a so-called spurious wakeup. While this will rarely occur in practice, applications must guard against it by testing for the condition that should have caused the thread to be awakened, and continuing to wait if the condition is not satisfied. In other words, waits should always occur in loops, like this one:

```java
synchronized (obj) {
     while (<condition does not hold>)
         obj.wait(timeout);
     ... // Perform action appropriate to condition
 }
```

Ref:
https://wiki.sei.cmu.edu/confluence/display/java/THI03-J.+Always+invoke+wait%28%29+and+await%28%29+methods+inside+a+loop

## What is ThreadGroup and what is it's use.

ThreadGroup is a convenient class that groups some related threads as a single unit and allows you to perform some operations on a group as a whole, rather than with each separate thread.

We need to specify the name of the group upon creation like this:

```java
ThreadGroup groupA = new ThreadGroup("Group A");
ThreadGroup groupB = new ThreadGroup("Group B");
```

For example, suppose Task is a thread, you can create 4 threads and group them in one group like this:

```java
ThreadGroup group = new ThreadGroup("GroupA");
 
new Task(group, "A").start();
new Task(group, "B").start();
new Task(group, "C").start();
new Task(group, "D").start();
```

Using ThreadGroup can be a useful diagnostic technique in big application servers with thousands of threads. If your threads are logically grouped together, then when you get a stack trace you can see which group the offending thread was part of (e.g. "Tomcat threads", "MDB threads", "thread pool X", etc), which can be a big help in tracking down and fixing the problem.

## What is the difference between the Runnable and Callable interfaces? How are they used

The Runnable interface has a single run method. It represents a unit of computation that has to be run in a separate thread. The Runnable interface does not allow this method to return value or to throw unchecked exceptions.

```java
public class RunnableTask implements Runnable {

    @Override
    public void run() {
        System.out.println("Task done");
    }
}

public class RunnableTaskExecute {

    public static void main(String[] args) {
        Thread runnable = new Thread(new RunnableTask());
        runnable.start();
    }
}
```

The Callable interface has a single call method and represents a task that has a value. That’s why the call method returns a value. It can also throw exceptions. Callable is generally used in ExecutorService instances to start an asynchronous task and then call the returned Future instance to get its value.

```java
import java.util.concurrent.Callable;

public class CallableTask implements Callable<String> {

    @Override
    public String call() {
        return "Task done";
    }
}
```

```java
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class CallableTaskExecution {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executer = Executors.newSingleThreadExecutor();
        Future<String> holder = executer.submit(new CallableTask());
        String result = holder.get();
        System.out.println(result);
    }
}s
```

![callabe-vs-runnable.jpg](images/callabe-vs-runnable.jpg)

## What is a daemon thread, what are its use cases? How can you create a daemon thread?

A daemon thread is a thread that does not prevent JVM from exiting. When all non-daemon threads are terminated, the JVM simply abandons all remaining daemon threads. Daemon threads are usually used to carry out some supportive or service tasks for other threads, but you should take into account that they may be abandoned at any time.

To start a thread as a daemon, you should use the setDaemon() method before calling start():

```java
Runnable runnable = () -> {
    try {
        Thread.sleep(100);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("May or May Not Print this Message");
};

Thread t1 = new Thread(runnable);
t1.setDaemon(true);
t1.start();
```
Curiously, if you run this as a part of the main() method, the message might not get printed. This could happen if the main() thread would terminate before the daemon would get to the point of printing the message. You generally should not do any I/O in daemon threads, as they won’t even be able to execute their finally blocks and close the resources if abandoned.

## What is the Thread’s interrupt flag? How can you set and check it? How does it relate to the InterruptedException?

The interrupt flag, or interrupt status, is an internal Thread flag that is set when the thread is interrupted. To set it, simply call thread.interrupt() on the thread object.

If a thread is currently inside one of the methods that throw InterruptedException (wait, join, sleep etc.), then this method immediately throws InterruptedException. The thread is free to process this exception according to its own logic.

If a thread is not inside such method and thread.interrupt() is called, nothing special happens. It is thread’s responsibility to periodically check the interrupt status using static Thread.interrupted() or instance isInterrupted() method. The difference between these methods is that the static Thread.interrupt() clears the interrupt flag, while isInterrupted() does not.

```java
Runnable runnable1 = () -> {
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        System.out.println("Error - Thread In Interrupted");
    }

    System.out.println("Thread will continue execution...");
};

Runnable runnable2 = () -> {
    for(int i=0;i<5;i++)
    {
        System.out.println(i);

        // Checking interrupt status and also clears the interrupted stats flag so next time it
        // will return false unless thread is again interrupted
        if (Thread.interrupted()) {
            System.out.println("Thread was interrupted");
        }

    }
};

Thread t1 = new Thread(runnable1);
t1.start();
t1.interrupt();

t1 = new Thread(runnable2);
t1.start();
t1.interrupt();
```

## What are Executor and ExecutorService? What are the differences between these interfaces?

Executor and ExecutorService are two related interfaces of java.util.concurrent framework. Executor is a very simple interface with a single execute method accepting Runnable instances for execution. In most cases, this is the interface that your task-executing code should depend on.

ExecutorService extends the Executor interface with multiple methods for handling and checking the lifecycle of a concurrent task execution service (termination of tasks in case of shutdown) and methods for more complex asynchronous task handling including Futures.

![executor-vs-executorservice.png](images/executor-vs-executorservice.png)

## What is a volatile field and what guarantees does the JMM hold for such field?

A volatile field has special properties according to the Java Memory Model (see Q9). The reads and writes of a volatile variable are synchronization actions, meaning that they have a total ordering (all threads will observe a consistent order of these actions). A read of a volatile variable is guaranteed to observe the last write to this variable, according to this order.

If you have a field that is accessed from multiple threads, with at least one thread writing to it, then you should consider making it volatile, or else there is a little guarantee to what a certain thread would read from this field.

Another guarantee for volatile is atomicity of writing and reading 64-bit values (long and double). Without a volatile modifier, a read of such field could observe a value partly written by another thread.

## Which of the following operations are atomic?

> Atomic means that the update operations done on that type are ensured to be done atomically (in one step, in one goal).

* writing to a non-volatile int;
* writing to a volatile int;
* writing to a non-volatile long;
* writing to a volatile long;
* incrementing a volatile long?

A write to an int (32-bit) variable is guaranteed to be atomic, whether it is volatile or not. A long (64-bit) variable could be written in two separate steps, for example, on 32-bit architectures, so by default, there is no atomicity guarantee. However, if you specify the volatile modifier, a long variable is guaranteed to be accessed atomically.

The increment operation is usually done in multiple steps (retrieving a value, changing it and writing back), so it is never guaranteed to be atomic, wether the variable is volatile or not. If you need to implement atomic increment of a value, you should use classes AtomicInteger, AtomicLong etc.

## What is the meaning of a synchronized keyword in the definition of a method? Of a static method? Before a block?

The synchronized keyword before a block means that any thread entering this block has to acquire the monitor (the object in brackets). If the monitor is already acquired by another thread, the former thread will enter the BLOCKED state and wait until the monitor is released.

```java
synchronized(object) {
    // ...
}
```

A synchronized instance method has the same semantics, but the instance itself acts as a monitor.

```java
synchronized void instanceMethod() {
    // ...
}
```

For a static synchronized method, the monitor is the Class object representing the declaring class.

```java
static synchronized void staticMethod() {
    // ...
}
```

## If two threads call a synchronized method on different object instances simultaneously, could one of these threads block? What if the method is static?

If the method is an instance method, then the instance acts as a monitor for the method. Two threads calling the method on different instances acquire different monitors, so none of them gets blocked.

If the method is static, then the monitor is the Class object. For both threads, the monitor is the same, so one of them will probably block and wait for another to exit the synchronized method.

## What is the purpose of the wait, notify and notifyAll methods of the Object class?

A thread that owns the object’s monitor (for instance, a thread that has entered a synchronized section guarded by the object) may call object.wait() to temporarily release the monitor and give other threads a chance to acquire the monitor. This may be done, for instance, to wait for a certain condition.

When another thread that acquired the monitor fulfills the condition, it may call object.notify() or object.notifyAll() and release the monitor. The notify method awakes a single thread in the waiting state, and the notifyAll method awakes all threads that wait for this monitor, and they all compete for re-acquiring the lock.

The following BlockingQueue implementation shows how multiple threads work together via the wait-notify pattern. If we put an element into an empty queue, all threads that were waiting in the take method wake up and try to receive the value. If we put an element into a full queue, the put method waits for the call to the get method. The get method removes an element and notifies the threads waiting in the put method that the queue has an empty place for a new item.

```java
public class BlockingQueue<T> {
 
    private List<T> queue = new LinkedList<T>();
 
    private int limit = 10;
 
    public synchronized void put(T item) {
        while (queue.size() == limit) {
            try {
                wait();
            } catch (InterruptedException e) {}
        }
        if (queue.isEmpty()) {
            notifyAll();
        }
        queue.add(item);
    }
 
    public synchronized T take() throws InterruptedException {
        while (queue.isEmpty()) {
            try {
                wait();
            } catch (InterruptedException e) {}
        }
        if (queue.size() == limit) {
            notifyAll();
        }
        return queue.remove(0);
    }
}
```


```java
public class WaitAntNotify {

    public void produce() throws InterruptedException {
        synchronized (this) {
            System.out.println("Starting producer");
            wait();
            System.out.println("Resumed");
        }
    }


    public void consume() throws InterruptedException {
        synchronized (this) {
            System.out.println("Started Consumer");
            Scanner scanner = new Scanner(System.in);

            System.out.println("Press enter to notify");
            scanner.nextLine();
            notify();

// Even though notify is called the thread will wait till the sync block is completed as the lock is on current object (this)
            Thread.sleep(3000);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        final WaitAntNotify processor = new WaitAntNotify();
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    processor.produce();
                } catch (InterruptedException ignored) {}
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    processor.consume();
                } catch (InterruptedException ignored) {}
            }
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();
    }
}
```

## Describe the conditions of deadlock, livelock, and starvation. Describe the possible causes of these conditions.

**Livelock** is a case of multiple threads reacting to conditions, or events, generated by themselves. An event occurs in one thread and has to be processed by another thread. During this processing, a new event occurs which has to be processed in the first thread, and so on. Such threads are alive and not blocked, but still, do not make any progress because they overwhelm each other with useless work.

**Starvation** is a case of a thread unable to acquire resource because other thread (or threads) occupy it for too long or have higher priority. A thread cannot make progress and thus is unable to fulfill useful work.

## Describe the purpose and use-cases of the fork/join framework.

The fork/join framework allows parallelizing recursive algorithms. The main problem with parallelizing recursion using something like ThreadPoolExecutor is that you may quickly run out of threads because each recursive step would require its own thread, while the threads up the stack would be idle and waiting.

The fork/join framework entry point is the ForkJoinPool class which is an implementation of ExecutorService. It implements the work-stealing algorithm, where idle threads try to “steal” work from busy threads. This allows to spread the calculations between different threads and make progress while using fewer threads than it would require with a usual thread pool.
