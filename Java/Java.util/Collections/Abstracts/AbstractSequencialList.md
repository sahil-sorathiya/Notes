### Inherited from parent (not redeclared here)

```java
void forEach(Consumer<? super T> action);  // Declared in "Iterable", Inheriting from "Iterable"
Spliterator<T> spliterator();  // Declared in "Iterable", Inheriting from "Collection"
```

---

### Methods Implemented by `AbstractSequentialList`

```java
public E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);

public boolean addAll(int index, Collection<? extends E> c);

public Iterator<E> iterator();  // Iterable
```

---

### Abstract Method (Must Be Implemented by Subclasses)

```java
public abstract ListIterator<E> listIterator(int index);
```

This class introduces only **one new abstract method** (`listIterator(int)`); the rest are concrete implementations built on top of that iterator. 
