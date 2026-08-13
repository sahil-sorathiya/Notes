## Inherited from Parent (not redeclared here)

```java
// Declared in "Iterable", Inheriting from "Iterable"
void forEach(Consumer<? super T> action);

// Declared in "Iterable", Inheriting from "Set"
Iterator<E> iterator();
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

// Declared in "SequencedCollection", Inheriting from "SequencedCollection"
void addFirst(E e)
void addLast(E e)
E getFirst()
E getLast()
E removeFirst()
E removeLast()
```
## `SequencedCollection` Methods

```java
SequencedSet<E> reversed();
```

## `SequencedSet` class do not have its own methods.
