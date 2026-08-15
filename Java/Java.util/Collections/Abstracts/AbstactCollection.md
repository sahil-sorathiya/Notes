### Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "Object"
boolean equals(Object o);
int hashCode();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
default Spliterator<T> spliterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();
```

## `Object` Methods

```java
String toString();
```

## `Iterable` Methods

```java
abstract Iterator<E> iterator();  // Iterable
```

## `Collection` Methods

### Query Operations

```java
abstract int size();

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
boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean removeAll(Collection<?> c);

boolean retainAll(Collection<?> c);

void clear();
```

## `AbstractCollection` class do not have its own methods.