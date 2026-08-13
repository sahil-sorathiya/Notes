### Queue Operations

```java
boolean add(E e);

boolean offer(E e);

E remove();

E poll();

E element();

E peek();
```

---

### Inherited from `Collection<E>` (not redeclared here)

```java
int size();

boolean isEmpty();

boolean contains(Object o);

Iterator<E> iterator();

Object[] toArray();

<T> T[] toArray(T[] a);

boolean remove(Object o);

boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean removeAll(Collection<?> c);

boolean retainAll(Collection<?> c);

void clear();

boolean equals(Object o);

int hashCode();

default <T> T[] toArray(IntFunction<T[]> generator);

default boolean removeIf(Predicate<? super E> filter);

default Spliterator<E> spliterator();

default Stream<E> stream();

default Stream<E> parallelStream();
```

---

### Complete Public Methods Available on a `Queue<E>`

```java
boolean add(E e);
boolean offer(E e);

E remove();
boolean remove(Object o);

E poll();

E element();
E peek();

int size();
boolean isEmpty();
boolean contains(Object o);

Iterator<E> iterator();

Object[] toArray();
<T> T[] toArray(T[] a);
default <T> T[] toArray(IntFunction<T[]> generator);

boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);

void clear();

boolean equals(Object o);
int hashCode();

default boolean removeIf(Predicate<? super E> filter);
default Spliterator<E> spliterator();
default Stream<E> stream();
default Stream<E> parallelStream();
```

The methods **introduced by `Queue` itself** are only:

```java
boolean offer(E e);

E remove();

E poll();

E element();

E peek();
```

`add(E e)` is redeclared from `Collection<E>`.
