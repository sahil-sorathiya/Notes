### Inherited from parent (not redeclared here)

```java
Iterator<E> iterator(); // Declared in "Iterable", Inheriting from "AbstractCollection"
void forEach(Consumer<? super T> action);  // Declared in "Iterable", Inheriting from "Iterable"
Spliterator<T> spliterator();  // Declared in "Iterable", Inheriting from "Collection"
```

### Public Methods

```java
public boolean add(E e);

public E remove();

public E element();

public void clear();

public boolean addAll(Collection<? extends E> c);
```

---

### Constructors

```java
protected AbstractQueue();
```

---

### Notes

`AbstractQueue<E>` introduces only **5 public methods** of its own:

* `add(E e)`
* `remove()`
* `element()`
* `clear()`
* `addAll(Collection<? extends E> c)`

The following `Queue` methods are **not implemented here** and remain abstract/inherited from the `Queue` interface:

```java
boolean offer(E e);

E poll();

E peek();
```

Subclasses (such as `PriorityQueue`, `ArrayDeque`, etc.) must provide implementations for those methods, along with `size()` and `iterator()`.
