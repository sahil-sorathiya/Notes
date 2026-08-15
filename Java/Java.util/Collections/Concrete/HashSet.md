## Inherited from Parent (not redeclared here)

```java
// Declared in "Object" & "Collection", Inheriting from "AbstractSet"
boolean equals(Object o);
int hashCode();

// Declared in "Object", Inheriting from "AbstractCollection"
String toString();

// Declared in "Iterable", Inheriting from "Iterable"
default void forEach(Consumer<? super T> action);

// Declared in "Collection", Inheriting from "Collection"
default <T> T[] toArray(IntFunction<T[]> generator);
default boolean removeIf(Predicate<? super E> filter);
default Stream<E> stream();
default Stream<E> parallelStream();

// Declared in "Collection", Inheriting from "AbstractCollection"
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean retainAll(Collection<?> c);

// Declared in "Collection", Inheriting from "AbstractSet"
boolean removeAll(Collection<?> c);

// Declared in "Set", Inheriting from "Set"
static <E> Set<E> of(E... elements);
static <E> Set<E> copyOf(Collection<? extends E> coll);

```

## Constructors

```java
HashSet();

HashSet(Collection<? extends E> c);

HashSet(int initialCapacity, float loadFactor);

HashSet(int initialCapacity);
```

## `Object` Methods

```java
Object clone();
```

## `Iterable` Methods

```java
Iterator<E> iterator();  // Iterable

Spliterator<E> spliterator(); // Iterable
```

## `Collection` Methods

### Query Operations

```java
int size();

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
void clear();
```

### Declared Directly in `HashSet`

```java
static <T> HashSet<T> newHashSet(int numElements);
```
           
