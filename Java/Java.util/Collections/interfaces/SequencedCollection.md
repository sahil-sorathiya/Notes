## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Collection"
Iterator<E> iterator();
default Spliterator<T> spliterator();

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();
int size();
boolean isEmpty();
boolean contains(Object o);
Object[] toArray();
<T> T[] toArray(T[] a);
boolean add(E e);
boolean remove(Object o);
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);
void clear();
boolean equals(Object o);
int hashCode();
```

## Declared Directly in `SequencedCollection`

```java
SequencedCollection<E> reversed();

default void addFirst(E e)

default void addLast(E e)

default E getFirst()

default E getLast()

default E removeFirst()

default E removeLast()
```