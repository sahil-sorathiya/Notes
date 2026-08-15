### Inherited from Parent (not redeclared here)

```java
// Declared in "Object", Inheriting from "AbstractCollection"
public String toString();

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
boolean add(E e);
boolean remove(Object o);
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean retainAll(Collection<?> c);
void clear();

// Declared in "Set", Inheriting from "Set"
static <E> Set<E> of(E... elements);
static <E> Set<E> copyOf(Collection<? extends E> coll);
```

## `Collection` Methods

### 'Object' Methods

```java
boolean equals(Object o);

int hashCode();
```

### Bulk Operations

```java
boolean removeAll(Collection<?> c);
```
## `AbstractSet` class do not have its own methods.