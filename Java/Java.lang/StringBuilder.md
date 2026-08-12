`StringBuilder` in Java is a class used to **create and modify strings efficiently**.

The key reason it exists is that **`String` is immutable**.

### Why do we need `StringBuilder`?

Suppose you do:

```java
String s = "";

for (int i = 0; i < 5; i++) {
    s += i;
}
```

Every `s += i` creates a **new String object**, because the original String cannot be modified.

Conceptually:

```text
"" → "0" → "01" → "012" → "0123" → "01234"
```

This can become expensive when you're doing many modifications.

With `StringBuilder`:

```java
StringBuilder sb = new StringBuilder();

for (int i = 0; i < 5; i++) {
    sb.append(i);
}

String s = sb.toString();
```

The same `StringBuilder` object is modified repeatedly.

## Constructors

### 1. No argument

```java
StringBuilder sb = new StringBuilder();
```

Creates an empty builder with a default capacity of **16**.

---

### 2. With initial capacity

```java
StringBuilder sb = new StringBuilder(100);
```

Creates an empty builder with capacity **100**.

```java
sb.length();    // 0
sb.capacity();  // 100
```

---

### 3. With a `String`

```java
StringBuilder sb = new StringBuilder("Hello");
```

Creates a builder containing `"Hello"`.

```java
sb.length();    // 5
```

Its initial capacity is:

```text
16 + length of the String
```

So for `"Hello"`:

```text
capacity = 21
```

---

### 4. With a `CharSequence`

```java
CharSequence cs = "Hello";

StringBuilder sb = new StringBuilder(cs);
```

This constructor accepts any object implementing `CharSequence`, such as `String`, `StringBuffer`, etc.

## Common operations

### `toString()`

Converts the `StringBuilder` into a `String`.

```java
StringBuilder sb = new StringBuilder("Hello");

String s = sb.toString();
```

---

### `charAt()`

Gets the character at an index.

```java
StringBuilder sb = new StringBuilder("Hello");

char c = sb.charAt(1);

System.out.println(c);
```

Output:

```text
e
```

---

### `append()`

```java
StringBuilder sb = new StringBuilder();

sb.append("Hello");
sb.append(" ");
sb.append("World");

System.out.println(sb);
```

Output:

```text
Hello World
```

You can append almost anything:

```java
sb.append(10);
sb.append(3.14);
sb.append(true);
sb.append('A');
```

---

### `setCharAt()`

You can directly change a character:

```java
StringBuilder sb = new StringBuilder("Hello");

sb.setCharAt(0, 'Y');

System.out.println(sb);
```

Output:

```text
Yello
```

This is something you **cannot do with String**:

```java
String s = "Hello";

// s.charAt(0) = 'Y';  // ❌
```

---

### `insert()`

```java
StringBuilder sb = new StringBuilder("Hello World");

sb.insert(6, "Java ");

System.out.println(sb);
```

Output:

```text
Hello Java World
```

The index is where the insertion happens.

---

### `delete()`

```java
StringBuilder sb = new StringBuilder("Hello World");

sb.delete(5, 11);

System.out.println(sb);
```

Output:

```text
Hello
```

`delete(start, end)` uses an **exclusive `end` index**, just like `substring()`.

---

### `deleteCharAt()`

```java
StringBuilder sb = new StringBuilder("Hello");

sb.deleteCharAt(1);

System.out.println(sb);
```

Output:

```text
Helo
```

---

### `reverse()`

Very useful in DSA:

```java
StringBuilder sb = new StringBuilder("hello");

sb.reverse();

System.out.println(sb);
```

Output:

```text
olleh
```

---

### `length()`

```java
StringBuilder sb = new StringBuilder("Hello");

System.out.println(sb.length());
```

Output:

```text
5
```

---

### `replace()`

Replaces characters from `start` to `end - 1`.

```java
StringBuilder sb = new StringBuilder("Hello World");

sb.replace(6, 11, "Java");

System.out.println(sb);
// Hello Java
```

---

### `capacity()`

Returns the current internal capacity.

```java
StringBuilder sb = new StringBuilder();

System.out.println(sb.capacity());
// 16
```

By default, a new `StringBuilder` has capacity **16**.

If you exceed the capacity, it automatically grows.

You can also specify it:

```java
StringBuilder sb = new StringBuilder(100);

System.out.println(sb.capacity());
// 100
```

---

### `ensureCapacity()`

Ensures that the builder has at least the specified capacity.

```java
StringBuilder sb = new StringBuilder();

sb.ensureCapacity(100);

System.out.println(sb.capacity());
```

The capacity will be **at least 100**.

---

### `substring()`

Returns a **String**, not a `StringBuilder`.

```java
StringBuilder sb = new StringBuilder("Hello World");

String s = sb.substring(0, 5);

System.out.println(s);
// Hello
```

You can also use:

```java
String s = sb.substring(6);
```

---

### `indexOf()`

Finds the first occurrence of a string.

```java
StringBuilder sb = new StringBuilder("Hello World World");

System.out.println(sb.indexOf("World"));
// 6
```

---

### `lastIndexOf()`

Finds the last occurrence.

```java
StringBuilder sb = new StringBuilder("Hello World World");

System.out.println(sb.lastIndexOf("World"));
// 12
```

---

### `setLength()`

Changes the length of the builder.

```java
StringBuilder sb = new StringBuilder("Hello World");

sb.setLength(5);

System.out.println(sb);
// Hello
```

It can also **increase** the length:

```java
StringBuilder sb = new StringBuilder("Hello");

sb.setLength(10);

System.out.println(sb);
```

The extra positions contain `'\0'` characters.

---

### `getChars()`

Copies characters into a `char[]`.

```java
StringBuilder sb = new StringBuilder("Hello");

char[] arr = new char[5];

sb.getChars(0, 5, arr, 0);
```

Now `arr` contains:

```text
['H', 'e', 'l', 'l', 'o']
```

---

## The ones you should memorize for DSA

I'd prioritize these:

```text
append()
insert()
delete()
deleteCharAt()
replace()
setCharAt()
charAt()
length()
reverse()
substring()
indexOf()
lastIndexOf()
toString()
```

And remember this important distinction:

```java
sb.length();     // number of actual characters
sb.capacity();   // allocated internal capacity
```

For example:

```java
StringBuilder sb = new StringBuilder();

sb.append("Hello");

System.out.println(sb.length());   // 5
System.out.println(sb.capacity()); // 16
```

So **length ≠ capacity**.

---

### Convert to String

Eventually, you often need an actual `String`:

```java
StringBuilder sb = new StringBuilder("Hello");

String s = sb.toString();
```

---

## `String` vs `StringBuilder`

|                                 | `String`           | `StringBuilder`           |
| ------------------------------- | ------------------ | ------------------------- |
| Mutable?                        | ❌ No               | ✅ Yes                     |
| `append()`                      | Creates new String | Modifies existing builder |
| `reverse()`                     | No direct method   | Yes                       |
| `setCharAt()`                   | ❌                  | ✅                         |
| Good for repeated modifications | ❌                  | ✅                         |
| Thread-safe                     | Immutable          | ❌ Not synchronized        |

### One DSA rule to remember

If you're building a result character-by-character:

```java
StringBuilder sb = new StringBuilder();

for (char c : s.toCharArray()) {
    if (...) {
        sb.append(c);
    }
}

return sb.toString();
```

This is extremely common in LeetCode/DSA Java solutions.

Also, **`StringBuilder` has a capacity**, which is why you'll sometimes see:

```java
new StringBuilder(s.length())
```

instead of just:

```java
new StringBuilder()
```

That capacity concept is worth understanding because it explains how `StringBuilder` remains efficient internally.

Sure. Here are the **important `StringBuilder` methods** you'll commonly use in Java/DSA.
