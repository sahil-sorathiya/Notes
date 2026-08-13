### Public Methods in `AbstractSequentialList<E>`

Source: `java.util.AbstractSequentialList` 

```java
public E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);

public boolean addAll(int index, Collection<? extends E> c);

public Iterator<E> iterator();

public abstract ListIterator<E> listIterator(int index);
```

---

### Methods Implemented by `AbstractSequentialList`

```java
public E get(int index);

public E set(int index, E element);

public void add(int index, E element);

public E remove(int index);

public boolean addAll(int index, Collection<? extends E> c);

public Iterator<E> iterator();
```

---

### Abstract Method (Must Be Implemented by Subclasses)

```java
public abstract ListIterator<E> listIterator(int index);
```

This class introduces only **one new abstract method** (`listIterator(int)`); the rest are concrete implementations built on top of that iterator. 
