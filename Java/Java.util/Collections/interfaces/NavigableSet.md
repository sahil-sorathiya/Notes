## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "SortedSet"
Spliterator<T> spliterator();                       

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

// Declared in "SortedSet", Inheriting from "SortedSet"
Comparator<? super E> comparator();  
E first();
E last();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable
```

## `SequenceCollection` Methods

```java
default NavigableSet<E> reversed();

default void addFirst(E e)

default void addLast(E e)

default E getFirst()

default E getLast()

default E removeFirst()

default E removeLast()
```

## `SortedSet` Methods

```java
SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);
```

## Declared Directly in `NavigableSet`
### Navigation Operations

```java
E lower(E e);

E floor(E e);

E ceiling(E e);

E higher(E e);
```

### Endpoint Operations

```java
E pollFirst();

E pollLast();
```

### Iteration & Views

```java
NavigableSet<E> descendingSet();

Iterator<E> descendingIterator();
```

### Range View Operations

```java
NavigableSet<E> subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive);

NavigableSet<E> headSet(E toElement, boolean inclusive);

NavigableSet<E> tailSet(E fromElemen boolean inclusive);
```