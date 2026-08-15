## Inherited from parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "Object"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
String toString();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);

//seqcol
default Deque<E> reversed();
```

## Constructors

```java
ArrayDeque();

ArrayDeque(int numElements);

ArrayDeque(Collection<? extends E> c);
```

## `Object` Methods

```java
ArrayDeque<E> clone();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable

Spliterator<E> spliterator(); // Iterable
```

## `Collection` Methods

### Query Operations

```java
int size();

boolean isEmpty();

boolean contains(Object o);

Object[] toArray();

<T> T[] toArray(T[] a);
```

### Modification Operations

```java
boolean add(E e);

boolean remove(Object o);
```

### Bulk Operations

```java
boolean removeAll(Collection<?> c);

default boolean removeIf(Predicate<? super E> filter);

boolean retainAll(Collection<?> c);

void clear();
```

## `SequencedCollection` Methods

```java
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


## `Deque` Methods

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
