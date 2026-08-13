## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Set"
Iterator<E> iterator();

// Declared in "Collection", Inheriting from "Collection"
<T> T[] toArray(IntFunction<T[]> generator);
boolean removeIf(Predicate<? super E> filter);
Stream<E> stream();
Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "Set"
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

// Declared in "Set", Inheriting from "Set"
static <E> Set<E> of(E... elements);
static <E> Set<E> copyOf(Collection<? extends E> coll);
```

## `Iterable` Methods

```java
default Spliterator<E> spliterator();  // Iterable
```

## `SequenceCollection` Methods

```java
default SortedSet<E> reversed();

default void addFirst(E e)

default void addLast(E e)

default E getFirst()

default E getLast()

default E removeFirst()

default E removeLast()
```


## Declared Directly in `SortedSet`

```java
Comparator<? super E> comparator();

SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

E first();

E last();
```