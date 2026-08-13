`SequencedCollection<E>` declares **7 public methods** of its own.

### Abstract Method

```java
SequencedCollection<E> reversed();
```

---

### Default Methods

```java
default void addFirst(E e)

default void addLast(E e)

default E getFirst()

default E getLast()

default E removeFirst()

default E removeLast()
```

---

### Complete List

| Method          | Return Type              | Type     |
| --------------- | ------------------------ | -------- |
| `reversed()`    | `SequencedCollection<E>` | Abstract |
| `addFirst(E e)` | `void`                   | Default  |
| `addLast(E e)`  | `void`                   | Default  |
| `getFirst()`    | `E`                      | Default  |
| `getLast()`     | `E`                      | Default  |
| `removeFirst()` | `E`                      | Default  |
| `removeLast()`  | `E`                      | Default  |

### Count

* Abstract methods: **1**
* Default methods: **6**
* Total methods declared in `SequencedCollection`: **7**

Since `SequencedCollection<E>` extends `Collection<E>`, it also **inherits all 20 public methods from `Collection`**, making **27 accessible public methods in total** (20 inherited + 7 declared).
