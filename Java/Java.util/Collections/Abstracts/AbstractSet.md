### Equality & Hashing

```java
public boolean equals(Object o);

public int hashCode();
```

---

### Bulk Operations

```java
public boolean removeAll(Collection<?> c);
```

---

### Summary

`AbstractSet<E>` declares only these three public methods:

```java
public boolean equals(Object o);

public int hashCode();

public boolean removeAll(Collection<?> c);
```

All other public methods (`size`, `iterator`, `add`, `contains`, `clear`, `toArray`, etc.) are inherited from `AbstractCollection` and/or implemented by concrete subclasses. The constructor is:

```java
protected AbstractSet();
```

which is **not public**.
