`StringBuffer` is very similar to `StringBuilder`, but the **main difference is thread safety**.

### Basic idea

Like `StringBuilder`, `StringBuffer` represents a **mutable sequence of characters**.

```java
StringBuffer sb = new StringBuffer("Hello");

sb.append(" World");

System.out.println(sb);
// Hello World
```

Unlike `String`, the existing object is modified.

---

## String vs StringBuilder vs StringBuffer

|              | `String`    | `StringBuilder`            | `StringBuffer`              |
| ------------ | ----------- | -------------------------- | --------------------------- |
| Mutable      | ❌           | ✅                          | ✅                           |
| Thread-safe  | ✅ Immutable | ❌                          | ✅                           |
| Synchronized | N/A         | ❌                          | ✅                           |
| Performance  | —           | Fastest for modification   | Slower than StringBuilder   |
| Typical use  | Normal text | DSA / single-threaded code | Multi-threaded modification |

The key distinction is:

```text
String        → immutable
StringBuilder → mutable + not synchronized
StringBuffer  → mutable + synchronized
```

---

## Why is StringBuffer thread-safe?

Its modification methods are synchronized.

Conceptually:

```java
StringBuffer sb = new StringBuffer();

sb.append("Hello");
```

`append()` is synchronized, so if multiple threads try to modify the same `StringBuffer`, Java ensures that the operation is controlled between threads.

This makes it safer for shared mutable data, but synchronization adds some overhead.

---

## Methods

Most of the methods you're learning for `StringBuilder` are also available in `StringBuffer`:

```java
append()
insert()
delete()
deleteCharAt()
replace()
setCharAt()
charAt()
length()
capacity()
reverse()
substring()
indexOf()
lastIndexOf()
toString()
setLength()
```

For example:

```java
StringBuffer sb = new StringBuffer("Hello World");

sb.delete(5, 11);
sb.append(" Java");
sb.reverse();

System.out.println(sb);
```

---

## Constructors

`StringBuffer` has **4 commonly listed constructors**, similar to `StringBuilder`:

```java
StringBuffer()
StringBuffer(int capacity)
StringBuffer(String str)
StringBuffer(CharSequence seq)
```

For example:

```java
StringBuffer a = new StringBuffer();
StringBuffer b = new StringBuffer(100);
StringBuffer c = new StringBuffer("Hello");
StringBuffer d = new StringBuffer((CharSequence) "Hello");
```

---

## Which one should you use?

For most of your **DSA/LeetCode Java code**, use:

```java
StringBuilder sb = new StringBuilder();
```

You generally **don't need `StringBuffer`** unless you're specifically dealing with multiple threads sharing and modifying the same character sequence.

A simple rule:

> **Single-threaded → `StringBuilder`**
> **Shared mutable string across threads → consider `StringBuffer`**
> **Normal unchanging text → `String`**
