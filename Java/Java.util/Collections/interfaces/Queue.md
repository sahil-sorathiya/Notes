## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
Iterator<E> iterator();
Spliterator<T> spliterator();

//: All Methods Declared Directly in "Collection", except "boolean add(E e);"
// Declared in "Collection", Inheriting from "Collection"
int size();
boolean isEmpty();
boolean contains(Object o);
Object[] toArray();
<T> T[] toArray(T[] a);
default <T> T[] toArray(IntFunction<T[]> generator);
boolean remove(Object o);
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
default boolean removeIf(Predicate<? super E> filter);
boolean retainAll(Collection<?> c);
void clear();
boolean equals(Object o);
int hashCode();
default Stream<E> stream();
default Stream<E> parallelStream();
```

## `Collection` Methods

### Modification Operations
```java
boolean add(E e);
```

## Declared Directly in `Queue`
```java
boolean offer(E e);

E remove();

E poll();

E element();

E peek();
```