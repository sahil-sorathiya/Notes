### Inherits

All Methods of Vector

### Constructors

```java
public Stack();
```

---

### Stack Operations

```java
public E push(E item);

public synchronized E pop();

public synchronized E peek();

public boolean empty();

public synchronized int search(Object o);
```

---

### Complete Public Method List Declared in `Stack`

```java
public Stack();

public E push(E item);

public synchronized E pop();

public synchronized E peek();

public boolean empty();

public synchronized int search(Object o);
```

**Note:** `Stack<E>` extends `Vector<E>`, so it also inherits all public methods of `Vector`, `AbstractList`, `AbstractCollection`, `Collection`, `Iterable`, etc. The list above contains only the methods declared directly in `java.util.Stack`.
