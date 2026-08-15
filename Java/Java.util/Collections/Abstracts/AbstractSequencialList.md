### Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractList"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
String toString();

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
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);

// Declared in "SequencedCollection", Inheriting from "List"
default void addFirst(E e);
default void addLast(E e);
default E getFirst();
default E getLast();
default E removeFirst();
default E removeLast();
default List<E> reversed();

// Declared in "List", Inheriting from "List"
default void replaceAll(UnaryOperator<E> operator);
default void sort(Comparator<? super E> c);
static <E> List<E> of(E... elements);
static <E> List<E> copyOf(Collection<? extends E> coll);

// Declared in "List", Inheriting from "AbstractList"
int indexOf(Object o);
int lastIndexOf(Object o);
ListIterator<E> listIterator();
List<E> subList(int fromIndex, int toIndex);
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable
```

## `List` Methods

### Positional Access

```java
abstract E get(int index);

E set(int index, E element);

void add(int index, E element);

E remove(int index);
```

### Bulk Operations

```java
boolean addAll(int index, Collection<? extends E> c);
```

### Iterators

```java
ListIterator<E> listIterator(int index);
```

## `AbstractSequencialList` class do not have its own methods.