## Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractSet"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
public String toString();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);        
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
Object[] toArray();
<T> T[] toArray(T[] a);
boolean containsAll(Collection<?> c);
boolean retainAll(Collection<?> c);

// Declared in "Collection", Inheriting from "AbstractSet"
boolean removeAll(Collection<?> c);

// Declared in "Set", Inheriting from "Set"
static <E> Set<E> of(E... elements);
static <E> Set<E> copyOf(Collection<? extends E> coll);

// Declared in "SequencedCollection", Inheriting from "NavigableSet"
default NavigableSet<E> reversed();
default E getFirst()
default E getLast()
default E removeFirst()
default E removeLast()
```

## Constructors

```java
public TreeSet();

public TreeSet(Comparator<? super E> comparator);

public TreeSet(Collection<? extends E> c);

public TreeSet(SortedSet<E> s);
```

## `Object` Methods

```java
public Object clone();
```

## `Iterable` Methods

```java
public Iterator<E> iterator();  // Iterable

public Spliterator<E> spliterator();  // Iterable
```

## `Collection` Methods

### Query Operations

```java
public int size();

public boolean isEmpty();

public boolean contains(Object o);
```

### Modification Operations

```java
public boolean add(E e);

public boolean remove(Object o);
```

### Bulk Operations

```java
public boolean addAll(Collection<? extends E> c);

public void clear();
```

## `SequenceCollection` Methods
```java
default void addFirst(E e)

default void addLast(E e)
```

## `SortedSet` Methods
```java
Comparator<? super E> comparator();

SortedSet<E> subSet(E fromElement, E toElement);

SortedSet<E> headSet(E toElement);

SortedSet<E> tailSet(E fromElement);

E first();

E last();
```


## `NavigableSet` Methods

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