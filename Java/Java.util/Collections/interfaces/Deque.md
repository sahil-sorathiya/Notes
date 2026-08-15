## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
default Spliterator<T> spliterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();
boolean isEmpty();
Object[] toArray();
<T> T[] toArray(T[] a);
boolean containsAll(Collection<?> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);
void clear();
boolean equals(Object o);
int hashCode();
boolean equals(Object o);
int hashCode();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable
```

## `Collection` Methods
### Query Operations

```java
int size();

boolean contains(Object o);
```

### Modification Operations

```java
boolean add(E e);

boolean remove(Object o);
```

### Bulk Operations

```java
boolean addAll(Collection<? extends E> c);
```


## `SequencedCollection` Methods

```java
default Deque<E> reversed();

void addFirst(E e);

void addLast(E e);

E getFirst();

E getLast();

E removeFirst();

E removeLast();
```

## `Queue` Methods

```java
boolean offer(E e);

E remove();

E poll();

E element();

E peek();
```

### Declared Directly in `Deque`

```java
boolean offerFirst(E e);

boolean offerLast(E e);

E pollFirst();

E pollLast();

E peekFirst();

E peekLast();

boolean removeFirstOccurrence(Object o);

boolean removeLastOccurrence(Object o);

void push(E e);

E pop();

Iterator<E> descendingIterator();
```

`add`, `offer`, `remove`, `poll`, `element`, `peek`, `addAll`, `remove(Object)`, `contains`, `size`, `iterator`, and `reversed()` are redeclared or inherited from parent interfaces.  
