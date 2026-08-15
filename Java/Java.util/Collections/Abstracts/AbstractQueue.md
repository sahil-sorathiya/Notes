### Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "Object"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
String toString();

// Declared in "Iterable", Inheriting from "AbstractCollection"
Iterator<E> iterator();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
default Spliterator<T> spliterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
abstract int size();
boolean isEmpty();
boolean contains(Object o);
Object[] toArray();
<T> T[] toArray(T[] a);
boolean remove(Object o);
boolean containsAll(Collection<?> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);

// Declared in "Queue", Not defined yet anywhere
boolean offer(E e);
E poll();
E peek();
```

## `Collection` Methods

### Modification Operations

```java
boolean add(E e);
```

### Bulk Operations

```java
boolean addAll(Collection<? extends E> c);

void clear();
```

## `Queue` Methods

```java
E remove();

E element();
```

## `AbstractQueue` class do not have its own methods.