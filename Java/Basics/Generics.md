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

## PECS Rule

A famous rule for wildcards:

> **Producer Extends, Consumer Super (PECS)**

### Producer → `extends`

If you only read values:

```java
List<? extends Number>
```

Example:

```java
Number n = list.get(0);
```

## Valid usage of Wildcards

Wildcards (`?`) can be used **when referring to a generic type**, but **not when declaring a generic class**.

### Valid Usage

Suppose you have:

```java
class Box<T> {
    T value;
}
```

You can declare references like:

```java
Box<?> box;
Box<? extends Number> numBox;
Box<? super Integer> intBox;
```

These are all valid.

### Invalid Usage

You cannot define a class like:

```java
class Box<?> {   // Compilation Error
}
```

or

```java
class Box<? extends Number> {   // Compilation Error
}
```

Wildcards are only for **using** generic types, not for **defining** them.

## Why?

When defining a class, you need a concrete type parameter name that can be referenced throughout the class:

```java
class Box<T> {
    private T value;

    public T get() {
        return value;
    }

    public void set(T value) {
        this.value = value;
    }
}
```

If Java allowed:

```java
class Box<?> {
    private ? value;   // What is the type name?
}
```

there would be no way to refer to that unknown type inside the class definition.

## Common Usage Example

```java
public static void printBox(Box<?> box) {
    System.out.println(box.value);
}
```

This method can accept:

```java
Box<String>
Box<Integer>
Box<Double>
```

because `?` means "some unknown type".

## Wildcard vs Type Parameter

### Wildcard

```java
void print(Box<?> box)
```

Meaning:

> I don't care what the type is.

### Type Parameter

```java
<T> void print(Box<T> box)
```

Meaning:

> There is a type `T`, and I may want to use it in the method.

For example:

```java
<T> T getValue(Box<T> box) {
    return box.get();
}
```

This is impossible with `?` because the exact type is unknown.

### Consumer → `super`

If you only write values:

```java
List<? super Integer>
```

Example:

```java
list.add(10);
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


The most important topics to master are:

1. Generic classes (`<T>`)
2. Generic methods
3. Wildcards (`?`)
4. `? extends T`
5. `? super T`
6. PECS rule
7. Type Erasure

These seven topics cover about 90% of generics questions asked in Java interviews.


## Difference betweeen \<T> and <? extends T>

You can write:

```java id="lt33ot"
Box<Number> numBox;
```

But it is **not the same** as:

```java id="pkkw9g"
Box<? extends Number> numBox;
```

This is one of the most important concepts in Java generics.

### `Box<Number>`

Means:

> A box whose type parameter is exactly `Number`.

```java id="8tqzbf"
Box<Number> box = new Box<>();
```

Valid:

```java id="q00t2g"
box.set(10);      // Integer
box.set(3.14);    // Double
```

because both are subclasses of `Number`.

But:

```java id="fq59b4"
Box<Integer> intBox = new Box<>();

Box<Number> box = intBox; // ERROR
```

Generics are invariant.

### `Box<? extends Number>`

Means:

> A box of some unknown type that extends `Number`.

Could be:

```java id="xyjlwm"
Box<Integer>
Box<Double>
Box<Float>
Box<Number>
```

All of these are assignable:

```java id="yv9fcw"
Box<Integer> intBox = new Box<>();
Box<? extends Number> box = intBox;
```

or

```java id="pk23me"
Box<Double> doubleBox = new Box<>();
Box<? extends Number> box = doubleBox;
```

### Why Use `? extends Number`?

Suppose you want a method that can accept any numeric box:

```java id="rms14u"
void print(Box<? extends Number> box) {
    System.out.println(box.get());
}
```

Now all of these work:

```java id="v6zqvt"
print(new Box<Integer>());
print(new Box<Double>());
print(new Box<Number>());
```

If you wrote:

```java id="s8dzje"
void print(Box<Number> box)
```

then only:

```java id="s6x4if"
print(new Box<Number>());
```

would work.

`Box<Integer>` and `Box<Double>` would be rejected.

### A Helpful Analogy

Think of:

```java id="q1y6ot"
Box<Number>
```

as:

> Exactly a Number box.

while

```java id="5byu04"
Box<? extends Number>
```

means:

> Any kind of Number box.

### Another Important Difference

With:

```java id="5d1rga"
Box<Number> box;
```

you can safely write:

```java id="6rtqik"
box.set(10);
box.set(3.14);
```

But with:

```java id="7j60j5"
Box<? extends Number> box;
```

you generally **cannot add anything**:

```java id="hpnv5v"
box.set(10); // Compile-time error
```

because the compiler doesn't know whether the actual box is:

```java id="z0ys1w"
Box<Integer>
```

or

```java id="iqn3l7"
Box<Double>
```

So it only allows reading safely.

This is the essence of the **PECS rule**:

* `? extends Number` → read/produce values
* `? super Integer` → write/consume values

So **`Box<Number>` and `Box<? extends Number>` are very different**:

* `Box<Number>` = exactly `Number`
* `Box<? extends Number>` = `Number` or any subtype of `Number` (`Integer`, `Double`, etc.)
