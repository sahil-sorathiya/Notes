Assuming the file is the source for `java.util.Collection<E>`, the **public methods declared in `Collection<E>`** are: 

### Query Operations

```java
int size();
boolean isEmpty();
boolean contains(Object o);
Iterator<E> iterator();
Object[] toArray();
<T> T[] toArray(T[] a);
default <T> T[] toArray(IntFunction<T[]> generator);
```

### Modification Operations

```java
boolean add(E e);
boolean remove(Object o);
```

### Bulk Operations

```java
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
default boolean removeIf(Predicate<? super E> filter);
boolean retainAll(Collection<?> c);
void clear();
```

### Comparison & Hashing

```java
boolean equals(Object o);
int hashCode();
```

### Traversal & Streams

```java
@Override
default Spliterator<E> spliterator();

default Stream<E> stream();

default Stream<E> parallelStream();
```

### Count

| Type                                          |  Count |
| --------------------------------------------- | -----: |
| Abstract methods                              |     15 |
| Default methods                               |      5 |
| Total public methods declared in `Collection` | **20** |

The **5 default methods** are:

```java
default <T> T[] toArray(IntFunction<T[]> generator)
default boolean removeIf(Predicate<? super E> filter)
default Spliterator<E> spliterator()
default Stream<E> stream()
default Stream<E> parallelStream()
```

All of these methods are implicitly `public` because every method declared in a Java interface is public.     
