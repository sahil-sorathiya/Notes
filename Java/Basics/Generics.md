# What Are Generics in Java?

**Generics** allow you to write classes, interfaces, and methods that work with different data types while providing **compile-time type safety**.

Before Java 5, collections stored objects as `Object`, so explicit casting was required.

## Without Generics

```java
ArrayList list = new ArrayList();

list.add("Hello");
list.add(10);

String s = (String) list.get(0); // OK
String t = (String) list.get(1); // Runtime ClassCastException
```

Problem:

* No type safety
* Errors occur at runtime

## With Generics

```java
ArrayList<String> list = new ArrayList<>();

list.add("Hello");
// list.add(10); // Compile-time error

String s = list.get(0);
```

Benefits:

* Type safety
* No explicit casting
* Better readability
* Errors detected during compilation

## Generic Classes

You can create classes that work with any type.

```java
class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

Usage:

```java
Box<String> box1 = new Box<>();
box1.set("Java");

String s = box1.get();

Box<Integer> box2 = new Box<>();
box2.set(100);

Integer x = box2.get();
```

`T` is called a **type parameter**.

## Common Type Parameter Names

| Symbol | Meaning |
| ------ | ------- |
| T      | Type    |
| E      | Element |
| K      | Key     |
| V      | Value   |
| N      | Number  |

Examples:

```java
HashMap<K, V>
ArrayList<E>
```

## Generic Methods

Methods can also be generic.

```java
class Utility {

    public static <T> void print(T value) {
        System.out.println(value);
    }
}
```

Usage:

```java
Utility.print("Hello");
Utility.print(100);
Utility.print(3.14);
```

The compiler infers `T`.

## Multiple Type Parameters

```java
class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

Usage:

```java
Pair<String, Integer> p =
        new Pair<>("Age", 25);
```

## Bounded Generics

Sometimes you want to restrict the allowed types.

### Upper Bound (`extends`)

```java
class Calculator<T extends Number> {

    public double square(T value) {
        return value.doubleValue() * value.doubleValue();
    }
}
```

Valid:

```java
Calculator<Integer> c1 = new Calculator<>();
Calculator<Double> c2 = new Calculator<>();
```

Invalid:

```java
Calculator<String> c3 = new Calculator<>(); // Error
```

## Wildcards

Wildcards are represented by `?`.

### Unbounded Wildcard

```java
public static void print(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

Can accept:

```java
List<String>
List<Integer>
List<Double>
```

### Upper Bounded Wildcard

```java
public static double sum(List<? extends Number> list) {
    double total = 0;

    for (Number n : list) {
        total += n.doubleValue();
    }

    return total;
}
```

Accepts:

```java
List<Integer>
List<Double>
List<Float>
```

### Lower Bounded Wildcard

```java
public static void addNumbers(List<? super Integer> list) {
    list.add(10);
}
```

Accepts:

```java
List<Integer>
List<Number>
List<Object>
```

## Type Erasure

A very important interview topic.

Java generics exist only at compile time.

```java
ArrayList<String> list1 = new ArrayList<>();
ArrayList<Integer> list2 = new ArrayList<>();
```

After compilation both become roughly:

```java
ArrayList list;
```

This process is called **Type Erasure**.

Because of this:

```java
System.out.println(
    list1.getClass() == list2.getClass()
);
```

Output:

```java
true
```

## Restrictions Due to Type Erasure

### Cannot Create Generic Arrays

```java
T[] arr = new T[10]; // Compile-time error
```

### Cannot Use Primitive Types

```java
ArrayList<int> list; // Error
```

Use wrapper classes:

```java
ArrayList<Integer> list;
```

### Cannot Instantiate Type Parameter

```java
class Box<T> {

    T obj = new T(); // Error
}
```

## Generics in Collections

```java
List<String> names = new ArrayList<>();

Set<Integer> numbers = new HashSet<>();

Map<String, Integer> map = new HashMap<>();
```

This is the most common use of generics in Java.

## Interview Question

Why is this illegal?

```java
List<Object> list = new ArrayList<String>();
```

Because generics are **invariant**.

If it were allowed:

```java
list.add(100);
```

Then an `ArrayList<String>` would contain an `Integer`, breaking type safety.

Instead use:

```java
List<?> list = new ArrayList<String>();
```

### Summary

Generics provide:

* Compile-time type safety
* Elimination of casts
* Reusable classes and methods
* Better readability
* Safer collection usage

A common rule is:

* **Use `T`, `E`, `K`, `V` when you need to name and reuse the type.**
* **Use `?` when you only need to accept a generic object and don't care about its exact type.**


Important topics:

1. Generic classes (`<T>`)
2. Generic methods
3. Wildcards (`?`)
4. `? extends T`
5. `? super T`
6. Type Erasure
