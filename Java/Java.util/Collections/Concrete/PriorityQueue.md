## Inherited from parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "Object"
boolean equals(Object o);
int hashCode();

// Declared in "Collection" , Inherited from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
boolean isEmpty();
boolean containsAll(Collection<?> c);

// Declared in "Collection", Inheriting from "AbstractQueue"
boolean addAll(Collection<? extends E> c);

// Declared in "Queue", Inheriting from "AbstractQueue"
E element();
E remove();
```

### Constructors

```java
PriorityQueue();

PriorityQueue(int initialCapacity);

PriorityQueue(Comparator<? super E> comparator);

PriorityQueue(int initialCapacity,
                     Comparator<? super E> comparator);

PriorityQueue(Collection<? extends E> c);

PriorityQueue(PriorityQueue<? extends E> c);

PriorityQueue(SortedSet<? extends E> c);
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable
final Spliterator<E> spliterator();  // Iterable
default void forEach(Consumer<? super E> action);  // Iterable
```

## `Collection` Methods

### Query Operations

```java
int size();

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

---

## `Queue` Methods

```java
boolean offer(E e);

E peek();

E poll();
```

## Declared Directly in `PriorityQueue`

```java
Comparator<? super E> comparator();
```


