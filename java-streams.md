# Java Streams

One of the best feature in Java SE 8 is lambda expressions. It is like anonymous method. Streams has simple building blocks (filtering, mapping, sorting, aggregation) It makes code more clear and easy to read.Code that's more readable is also less error prone, because maintainers are more likely to be able to correctly discern at first glance what the code does.

# How it works

All stream computations share a common structure: They have a stream source, zero or more intermediate operations, and a single terminal operation. The elements of a stream can be object references (Stream<String>) or they can be primitive integers (IntStream), longs (LongStream), or doubles (DoubleStream).

Intermediate operations — such as filter() (selecting elements matching a criterion), map() (transforming elements according to a function), distinct() (removing duplicates), limit() (truncating a stream at a specific size), and sorted()— transform a stream into another stream. Some operations, such as mapToInt(), take a stream of one type and return a stream of a different type

Intermediate operations are always lazy: Invoking an intermediate operation merely sets up the next stage in the stream pipeline but doesn't initiate any work. Intermediate operations are further divided into stateless and stateful operations. Stateless operations (such as filter() or map()) can operate on each element independently, whereas stateful operations (such as sorted() or distinct()) can incorporate state from previously seen elements that affects the processing of other elements.

The processing of the data set begins when a terminal operation is executed, such as a reduction (sum() or max()), application (forEach()), or search (findFirst()) operation. Terminal operations produce a result or a side effect. When a terminal operation is executed, the stream pipeline is terminated, and if you want to traverse the same data set again, you can set up a new stream pipeline. Table 3 shows some of the terminal stream operations.

**A stateful lambda expression is one whose result depends on any state that might change during the execution of a pipeline. On the other hand, a stateless lambda expression is one whose result does not depend on any state that might change during the execution of a pipeline.**

# How Parallel Streams Works

It has three stages (Split, Apply & Combine)
> Split : Split the datsource in independent chunks. It will done using Spliterators
> 

# Streams versus collections

A collection is a data structure; its main concern is the organization of data in memory, and a collection persists over a period of time. A collection might often be used as the source or target for a stream pipeline, but a stream's focus is on computation, not data. The data comes from elsewhere (a collection, array, generator function, or I/O channel) and is processed through a pipeline of computational steps to produce a result or side effect, at which point the stream is finished. Streams provide no storage for the elements that they process

Collections and streams also differ in the way that their operations are executed. Operations on collections are eager and mutative; when the remove() method is called on a List, after the call returns, you know that the list state was modified to reflect the removal of the specified element. For streams, only the terminal operation is eager; the others are lazy.

Expressing a stream pipeline as a sequence of functional transformations enables several useful execution strategies, such as laziness, short circuiting, and operation fusion. Short-circuiting enables a pipeline to terminate successfully without examining all the data; queries such as "find the first transaction over $1,000" needn't examine any more transactions after a match is found. Operation fusion means that multiple operations can be executed in a single pass on the data.

Stream pipelines, in contrast, fuse their operations into as few passes on the data as possible, often a single pass. (Stateful intermediate operations, such as sorting, can introduce barrier points that necessitate multipass execution.) Each stage of a stream pipeline produces its elements lazily, computing elements only as needed, and feeds them directly to the next stage. You don't need a collection to hold the intermediate result of filtering or mapping, so you save the effort of populating (and garbage-collecting) the intermediate collections. Also, following a "depth first" rather than "breadth first" execution strategy (tracing the path of a single data element through the entire pipeline) causes the data being operated upon to more often be "hot" in cache, so you can spend more time computing and less time waiting for data.

In addition to using streams for computation, you might want to consider using streams to return aggregates from API methods, where previously you might have returned an array or collection. Returning a stream is often more efficient, since you don't have to copy all the data into a new array or collection. Returning a stream is also often more flexible; the form of collection the library chooses to return might not be what the caller needs, and it's easy to convert a stream into any collection type. (The main situation in which returning a stream is inappropriate, and falling back to returning a materialized collection is better, is when the caller would need to see a consistent snapshot of the state at a point in time.)

# Parallelism

A beneficial consequence of structuring computations as functional transformations is that you can easily switch between sequential and parallel execution with minimal changes to the code. The sequential expression of a stream computation and the parallel expression of the same computation are almost identical. Listing 4 shows how to execute the query from below in parallel.

```
int totalSalesFromNY
    = txns.parallelStream()
          .filter(t -> t.getSeller().getAddr().getState().equals("NY"))
          .mapToInt(t -> t.getAmount())
          .sum();
```
All stream operations can be executed either sequentially or in parallel, but bear in mind that parallelism isn't magic performance dust. A parallel execution might be faster than, the same speed as, or slower than a sequential one. It's best to start out with sequential streams and apply parallelism when you know that you will get — and benefit from — a speedup. A later installment in this series returns to analyzing a stream pipeline for parallel performance.

Most stream operations require that the lambdas passed to them be non-interfering and stateless. Non-interfering means that they won't modify the stream source; stateless means that they won't access (read or write) any state that might change during the lifetime of the stream operation. For reduction operations (for example, computing summary data such as sum, min, or max) the lambdas passed to these operations must be associative (or conform to similar requirements).

The root of all concurrency risks is shared mutable state. One possible source of shared mutable state is the stream source. If the source is a traditional collection like ArrayList, the Streams library assumes that it remains unmodified during the course of a stream operation. (Collections explicitly designed for concurrent access, such as ConcurrentHashMap, are exempt from this assumption.) Not only does the noninterference requirement exclude the source being mutated by other threads during a stream operation, but the lambdas passed to stream operations themselves should also refrain from mutating the source.

In addition to not modifying the stream source, lambdas passed to stream operations should be stateless. For example, the code, which tries to eliminate any element that's twice some preceding element, violates this rule.

```
HashSet<Integer> twiceSeen = new HashSet<>();
int[] result
    = elements.stream()
              .filter(e -> {
                  twiceSeen.add(e * 2);
                  return twiceSeen.contains(e);
              })
              .toArray();
```

If executed in parallel, this pipeline would produce incorrect results, for two reasons. First, access to the twiceSeen set is done from multiple threads without any coordination and therefore isn't thread safe. Second, because the data is partitioned, there's no guarantee that when a given element is processed, all elements preceding that element were already processed.

# Stream pipelines
A stream pipeline is composed of a stream source, zero or more intermediate operations, and a terminal operation. Stream sources can be collections, arrays, generator functions, or any other data source that can suitably provide access to its elements. Intermediate operations transform streams into other streams — by filtering the elements (filter()), transforming the elements (map()), sorting the elements (sorted()), truncating the stream to a certain size (limit()), and so on. Terminal operations include aggregations (reduce(), collect()), searching (findFirst()), and iteration (forEach()).

Stream pipelines are constructed lazily. Constructing a stream source doesn't compute the elements of the stream, but instead captures how to find the elements when necessary. Similarly, invoking an intermediate operation doesn't perform any computation on the elements; it merely adds another operation to the end of the stream description. Only when the terminal operation is invoked does the pipeline actually perform the work — compute the elements, apply the intermediate operations, and apply the terminal operation. This approach to execution makes several interesting optimizations possible.

# Stream sources
A stream source is described by an abstraction called Spliterator. As its name suggests, Spliterator combines two behaviors: accessing the elements of the source (iterating), and possibly decomposing the input source for parallel execution (splitting).

Although Spliterator includes the same basic behaviors as Iterator, it doesn't extend Iterator, instead taking a different approach to element access. An Iterator has two methods, hasNext() and next(); accessing the next element can involve (but doesn't require) calling both of these methods. As a result, coding an Iterator correctly requires a certain amount of defensive and duplicative coding. (What if the client doesn't call hasNext() before next()? What if it calls hasNext() twice?) Additionally, the two-method protocol generally requires a fair amount of statefulness, such as peeking ahead one element (and keeping track of whether you've already peeked ahead). Together, these requirements add up to a fair degree of per-element access overhead.

Having lambdas in the language enables Spliterator to take an approach to element access that's generally more efficient — and easier to code correctly. Spliterator has two methods for accessing elements:
```
boolean tryAdvance(Consumer<? super T> action);
void forEachRemaining(Consumer<? super T> action);
```
The tryAdvance() method tries to process a single element. If no elements remain, tryAdvance() merely returns false; otherwise, it advances the cursor and passes the current element to the provided handler and returns true. The forEachRemaining() method processes all the remaining elements, passing them one at a time to the provided handler.

To split the source, so that two threads can work separately on different sections of the input, Spliterator provides a trySplit() method:
```
Spliterator<T> trySplit();
```
# trySplit()
trySplit() splits off a part of the sequence this spliterator was supposed to handle into a new spliterator. In our case this must always be the strict prefix of this spliterator's sequence, because the I/O resource behind it stays with this and the returned spliterator contains the starting batch in materialized form. A useful, reusable implementation of this method is the goal of our support class.

# tryAdvance(action), forEachRemaining(action)
tryAdvance() is where the actual stream processing takes place: the spliterator advances by one item and invokes the passed-in action on it. This is the method where the user must supply behavior appropriate to his I/O source.

It has a cousin called forEachRemaining(), which is there purely for optimization purposes: when the execution framework decides to consume a spliterator in full, it can make a single call to forEachRemaining() instead of calling tryAdvance() for each item individually. This method has a default implementation which repeatedly calls tryAdvance(), so there is no rush in implementing it ourselves. Often, though, it is quite easy to provide an optimized implementation.

# characteristics(), hasCharacteristics(characteristics)
Next we have the int characteristics() method, which is again devoted to various aspects of optimization. Spliterator reports its characteristics in a single int, treated as a bitfield. Normally this is considered an anti-pattern because an EnumSet is the recommended typesafe alternative, but once again optimization concerns trump everything else in this API. For our usage only a few spliterator characteristics are relevant:

> Spliterator.SUBSIZED guarantees that each spliterator returned by trySplit() will itself be both SIZED (its item count will be precisely known) and SUBSIZED. This characteristic will be guaranteed by our general class because we materialize the items in advance when splitting and thus know their exact number.

> Spliterator.ORDERED signals that the spliterator will respect a well-defined encounter order: items will be visited by it in this order and trySplit() will always return a strict prefix with respect to it. Most of the time, we will want to enable this characteristic since the nature of the stream source forces us to respect it in any case. Not specifying ORDERED may allow more optimization inside some operations, but it should be handled with a lot of care as it invites very confusing bugs: non-commutative operations will not be required to respect the encounter order in that case.

> Spliterator.NONNULL indicates that no item will ever be null. Many kinds of processing get much more involved in the face of possible null values, so setting (and respecting) this characteristic makes life easier for everyone.

> Spliterator.IMMUTABLE guarantees that the source cannot be structurally modified (items added, removed, or replaced). It is hard to imagine how this could be violated for a lazy stream, where items don't even exist before they are used. There is a closely related method — hasCharacteristics(int characteristics) — but it has a default implementation and there is little reason to ever override it.

# Implementation Spliterator
Spliterator implementations can range considerably in quality, making trade-offs between the effort of implementation and the performance of stream pipelines that use spliterators as a source. The Spliterator interface has several methods that are essentially optional, such as trySplit(). If you don't want to implement splitting, you can always return null from trySplit()— but this means that streams using this Spliterator as a source will be unable to exploit parallelism to speed up the computation.

Considerations that affect the quality of a spliterator include:

> Does the spliterator report an accurate size?
> Can the spliterator split the input at all?
> Can it split the input into roughly equal sections?
> Are the sizes of the splits predictable (reflected through the SUBSIZED characteristics)?
> Does the spliterator report all relevant characteristics?

The easiest way to make a spliterator, but which results in the worst-quality result, is to pass an Iterator to Spliterators.spliteratorUnknownSize(). You can obtain a slightly better spliterator by passing an Iterator and a size to Spliterators.spliterator. But if stream performance is important — especially, parallel performance — implement the full Spliterator interface, including all applicable characteristics. The JDK sources for collection classes such as ArrayList, TreeSet, and HashMap provide examples of high-quality spliterators that you can emulate for your own data structures.

