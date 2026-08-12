In Java, **`String`** is a class used to represent a sequence of characters.

```java
String s = "Hello";
```

## Important points

### 1. String is immutable

Once created, its contents cannot be changed.

```java
String s = "Hello";
s = s + " World";
```

This creates a **new String** `"Hello World"`; the original `"Hello"` is unchanged.

### 2. Creating Strings

```java
String s1 = "Hello";              // String literal
String s2 = new String("Hello");  // String object
```

Usually prefer the first form.


Now there are **two String objects**:

```text
String Pool
+---------+
| "hello" | ← a
+---------+

Heap
+---------+
| "hello" | ← b
+---------+
```

Therefore:

```java
a == b       // false
a.equals(b)  // true
```

`equals()` compares the **actual characters**, while `==` compares the **references**.

### 3. Comparing Strings

❌ Don't use `==` for content comparison:

```java
s1 == s2
```

Use:

```java
s1.equals(s2)
```

For case-insensitive comparison:

```java
s1.equalsIgnoreCase(s2)
```

`String` has quite a lot of methods. For Java/DSA, you don't need to memorize all of them. Here's a useful grouping.

## String Methods

### 1. Basic information

```java
String s = "Hello World";

s.length();          // 11
s.isEmpty();         // false
s.isBlank();         // false  (Java 11+)
```

* `length()` → number of characters
* `isEmpty()` → `length() == 0`
* `isBlank()` → empty or contains only whitespace

---

### 2. Access characters

```java
s.charAt(0);         // 'H'
```

```java
s.toCharArray();     // ['H', 'e', 'l', 'l', 'o', ...]
```

Also:

```java
s.codePointAt(0);
s.codePointCount(0, s.length());
```

For normal DSA, `charAt()` and `toCharArray()` are the important ones.

---

### 3. Comparing Strings

```java
s.equals("Hello World");
```

```java
s.equalsIgnoreCase("hello world");
```

```java
s.compareTo("Hello");
```

```java
s.compareToIgnoreCase("hello");
```

`compareTo()` and `compareToIgnoreCase()` are used to lexicographically compare two Strings — basically, dictionary-style ordering.
```
compareTo() < 0   → first String comes before second
compareTo() == 0  → Strings are equal
compareTo() > 0   → first String comes after second
```

### Important

Don't normally do:

```java
s == "Hello World";  // ❌
```

Use:

```java
s.equals("Hello World");  // ✅
```

---

### 4. Searching

```java
String s = "Hello World";

s.contains("World");       // true

s.indexOf("World");        // 6
s.indexOf('o');            // 4

s.lastIndexOf('o');        // 7

s.startsWith("Hello");     // true
s.endsWith("World");       // true
```

You can also specify where to start searching:

```java
s.indexOf('o', 5);
```

---

### 5. Extracting parts of a String

#### `substring()`

```java
String s = "Hello World";

s.substring(6);
// "World"

s.substring(0, 5);
// "Hello"
```

Remember:

```text
substring(start, end)
                  ↑
             exclusive
```

---

### 6. Modifying / transforming

Remember that `String` is **immutable**, so these return a **new String**.

```java
s.toUpperCase();
s.toLowerCase();
```

```java
s.trim();
```

`trim()` removes leading/trailing characters ≤ U+0020.

Java 11+ also has:

```java
s.strip();
s.stripLeading();
s.stripTrailing();
```

---

#### `replace()`

```java
String s = "banana";

s.replace('a', 'o');
// "bonono"
```

You can replace strings too:

```java
s.replace("banana", "apple");
```

---

#### `replaceFirst()`

Uses regex:

```java
"abcabc".replaceFirst("a", "X");
// "Xbcabc"
```

#### `replaceAll()`

Also uses regex:

```java
"abc123".replaceAll("\\d", "X");
// "abcXXX"
```

---

### 7. Splitting

You've already asked about this one:

```java
String s = "apple,banana,mango";

String[] arr = s.split(",");
```

Result:

```text
["apple", "banana", "mango"]
```

You can specify a limit:

```java
s.split(",", 2);
```

---

### 8. Joining

`String.join()` is a **static method** of `String`. It's linear in TC becuase it first creates Bytes array then converts into String.

```java
String result = String.join("-", "2026", "08", "12");

System.out.println(result);
```

Output:

```text
2026-08-12
```

It also works with collections and arrays:

```java
List<String> list = List.of("A", "B", "C");

String result = String.join(",", list);
// "A,B,C"

String[] arr = {"Hello", "World", "Java"};
String result = String.join(" ", arr);
// "Hello World Java"
```

---

### 9. Converting to String

#### `valueOf()`

Static method:

```java
String.valueOf(123);      // int - "123"
String.valueOf(3.14);     // double - "3.14"
String.valueOf(true);     // boolean - "true"
String.valueOf('A');      // char - "A"
String.valueOf(123L);     // long - "123"
String.valueOf((Object) null); // Object - "null"
```

Very useful when converting primitive values to String.

---

### 10. Formatting

Modern Java has:

```java
String.format("Name: %s, Age: %d", "Sahil", 25);
```

Result:

```text
Name: Sahil, Age: 25
```

Java 15+ also has:

```java
String s = "Hello %s".formatted("Sahil");
```

---

### 11. Repeating

Java 11+:

```java
"abc".repeat(3);
```

Result:

```text
abcabcabc
```

---

### 12. Concatenation

```java
String s = "Hello";

s.concat(" World");
```

Result:

```text
Hello World
```

But in normal Java code you'll usually see:

```java
s + " World"
```

---

### 13. `matches()`

Checks whether the **entire String** matches a regular expression.

```java
"12345".matches("\\d+");
// true
```

```java
"123abc".matches("\\d+");
// false
```

---

### 14. `lines()`

Java 11+:

```java
String s = "Hello\nWorld\nJava";

s.lines();
```

Returns a `Stream<String>` containing:

```text
Hello
World
Java
```

---

# ⭐ Methods I'd memorize for DSA

If your goal is LeetCode/DSA, focus on these:

```text
length()
charAt()
toCharArray()

equals()
equalsIgnoreCase()
compareTo()

substring()

indexOf()
lastIndexOf()
contains()
startsWith()
endsWith()

replace()
replaceFirst()
replaceAll()

split()

toLowerCase()
toUpperCase()
trim()

concat()

isEmpty()
isBlank()

valueOf()
```

And remember the big distinction:

```text
String
  ↓
IMMUTABLE

StringBuilder
  ↓
MUTABLE + fast

StringBuffer
  ↓
MUTABLE + synchronized
```

One particularly useful thing to know next is **which of these methods use regex (`split`, `replaceAll`, `matches`) and which don't**, because that causes a lot of unexpected behavior in Java.
