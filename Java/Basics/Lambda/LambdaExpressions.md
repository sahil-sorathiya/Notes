### What are Lambda Expressions in Java?

A **lambda expression** is a concise way to represent an **anonymous function** (a function without a name) that can be passed around as an object.

Introduced in **Java 8**, lambdas are mainly used to implement **functional interfaces** (interfaces with exactly one abstract method).

---

### Before Java 8

Suppose we have:

```java
interface Greeting {
    void sayHello();
}
```

Before Java 8:

```java
Greeting g = new Greeting() {
    @Override
    public void sayHello() {
        System.out.println("Hello");
    }
};

g.sayHello();
```

This creates an anonymous class.

---

### With Lambda

```java
Greeting g = () -> System.out.println("Hello");

g.sayHello();
```

Much shorter and easier to read.

---

## Syntax

General syntax:

```java
(parameters) -> expression
```

or

```java
(parameters) -> {
    statements;
}
```

Examples:

```java
() -> System.out.println("Hello");
```

```java
x -> x * x
```

```java
(a, b) -> a + b
```

```java
(a, b) -> {
    int sum = a + b;
    return sum;
}
```

## Lambdas with Collections

### Sorting

Before Java 8:

```java
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});
```

Using lambda:

```java
Collections.sort(list, (a, b) -> a.compareTo(b));
```

Or even:

```java
list.sort((a, b) -> a.compareTo(b));
```

---

### forEach

```java
List<String> names = List.of("A", "B", "C");

names.forEach(name -> System.out.println(name));
```

Equivalent to:

```java
for (String name : names) {
    System.out.println(name);
}
```

---

## Method References

Many lambdas can be shortened further.

Instead of:

```java
names.forEach(name -> System.out.println(name));
```

Use:

```java
names.forEach(System.out::println);
```

This is called a **method reference**.

---

## Variable Capture

A lambda can access local variables only if they are **final or effectively final**.

```java
int x = 10;

Runnable r = () -> System.out.println(x);
```

Valid because `x` is not modified.

```java
int x = 10;

Runnable r = () -> System.out.println(x);

x++; // Compilation error
```

Not allowed.

---

## How Lambda Works Internally

Consider:

```java
Comparator<Integer> cmp = (a, b) -> a - b;
```

The compiler does **not** create an anonymous class like before.

Instead, it uses the JVM instruction:

```text
invokedynamic
```

and generates the implementation dynamically using `LambdaMetafactory`, making lambdas generally more efficient than anonymous inner classes.

---

## Why Use Lambdas?

### Less Boilerplate

Before:

```java
new Comparator<Integer>() {
    @Override
    public int compare(Integer a, Integer b) {
        return a - b;
    }
}
```

After:

```java
(a, b) -> a - b
```

### Better Collection Processing

```java
list.stream()
    .filter(x -> x % 2 == 0)
    .map(x -> x * x)
    .forEach(System.out::println);
```

### Cleaner APIs

```java
button.addActionListener(e -> System.out.println("Clicked"));
```


## Quick Rule

Whenever you see:

```java
SomeInterface obj = (...) -> ...;
```

ask:

> Does `SomeInterface` have exactly one abstract method?

If **yes**, it's a functional interface, and the lambda is providing the implementation of that method.

Example:

```java
Comparator<Integer> cmp = (a, b) -> a - b;
```

The lambda implements:

```java
int compare(Integer a, Integer b);
```

from `Comparator`.

That's the core idea of lambda expressions in Java.