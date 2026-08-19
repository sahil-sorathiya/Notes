
## Functional Interfaces

* Interfaces with exactly one abstract method
* A lambda can only be assigned to a **functional interface**.

Example:

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

Using lambda:

```java
Calculator c = (a, b) -> a + b;

System.out.println(c.add(10, 20));
```

Output:

```
30
```

---

## Common Functional Interfaces

These are the six most important **built-in functional interfaces** in Java's `java.util.function` package. They are heavily used with **lambda expressions**, **Streams API**, and **method references**.

A functional interface has exactly **one abstract method**.

---

# 1. Predicate<T>

Represents a function that takes an input and returns a **boolean**.

### Signature

```java
boolean test(T t);
```

### Example

```java
Predicate<Integer> isEven = n -> n % 2 == 0;

System.out.println(isEven.test(4)); // true
System.out.println(isEven.test(5)); // false
```

### Usage

Filtering data.

```java
List<Integer> nums = List.of(1,2,3,4,5);

nums.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);
```

Output:

```text
2
4
```

### Common Methods

```java
and()
or()
negate()
```

Example:

```java
Predicate<Integer> positive = n -> n > 0;
Predicate<Integer> even = n -> n % 2 == 0;

Predicate<Integer> positiveAndEven =
        positive.and(even);
```

---

# 2. Function<T, R>

Represents a function that takes one value and returns another value.

### Signature

```java
R apply(T t);
```

### Example

```java
Function<String, Integer> length =
        s -> s.length();

System.out.println(length.apply("Java"));
```

Output:

```text
4
```

### Usage

Transformation / Mapping.

```java
List<String> names = List.of("John", "Alex");

names.stream()
     .map(String::length)
     .forEach(System.out::println);
```

### Common Methods

```java
andThen()
compose()
identity()
```

Example:

```java
Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> addOne = x -> x + 1;

System.out.println(
    doubleIt.andThen(addOne).apply(5)
);
```

Result:

```text
11
```

---

# 3. Consumer<T>

Represents an operation that accepts an input and returns **nothing**.

### Signature

```java
void accept(T t);
```

### Example

```java
Consumer<String> printer =
        s -> System.out.println(s);

printer.accept("Hello");
```

### Usage

Performing side effects.

```java
List<String> names = List.of("A", "B", "C");

names.forEach(System.out::println);
```

Here `forEach()` expects a `Consumer`.

### Common Method

```java
andThen()
```

Example:

```java
Consumer<String> c1 = s -> System.out.print(s);
Consumer<String> c2 = s -> System.out.println("!");

c1.andThen(c2).accept("Hello");
```

Output:

```text
HelloHello!
```

(Each consumer receives the same input.)

---

# 4. Supplier<T>

Represents a source of values.

Takes **no input**, returns a value.

### Signature

```java
T get();
```

### Example

```java
Supplier<Double> random =
        () -> Math.random();

System.out.println(random.get());
```

### Usage

Lazy object creation.

```java
Supplier<List<String>> listSupplier =
        ArrayList::new;

List<String> list = listSupplier.get();
```

---

# 5. UnaryOperator<T>

A special case of `Function<T, T>`.

Input and output types are the same.

### Signature

```java
T apply(T t);
```

### Example

```java
UnaryOperator<Integer> square =
        x -> x * x;

System.out.println(square.apply(5));
```

Output:

```text
25
```

### Equivalent To

```java
Function<Integer, Integer> square = x -> x * x;
```

but `UnaryOperator` makes the intent clearer.

### Usage

Modifying an existing value.

```java
list.replaceAll(String::toUpperCase);
```

`replaceAll()` expects a `UnaryOperator`.

---

# 6. BinaryOperator<T>

A special case of `BiFunction<T, T, T>`.

Takes two values of the same type and returns the same type.

### Signature

```java
T apply(T t1, T t2);
```

### Example

```java
BinaryOperator<Integer> add =
        (a, b) -> a + b;

System.out.println(add.apply(10, 20));
```

Output:

```text
30
```

### Usage

Reduction operations.

```java
List<Integer> nums = List.of(1,2,3,4);

int sum = nums.stream()
              .reduce(0, (a, b) -> a + b);
```

The lambda is a `BinaryOperator<Integer>`.

---

# Relationship Diagram

```text
Predicate<T>
    T -> boolean

Function<T,R>
    T -> R

Consumer<T>
    T -> void

Supplier<T>
    () -> T

UnaryOperator<T>
    T -> T
    extends Function<T,T>

BinaryOperator<T>
    (T,T) -> T
    extends BiFunction<T,T,T>
```

---

# Stream API Mapping

| Stream Method | Functional Interface |
| ------------- | -------------------- |
| filter()      | Predicate            |
| map()         | Function             |
| forEach()     | Consumer             |
| generate()    | Supplier             |
| replaceAll()  | UnaryOperator        |
| reduce()      | BinaryOperator       |

A good way to remember them:

```text
Predicate  -> Ask a question      -> boolean
Function   -> Transform a value   -> result
Consumer   -> Use a value         -> void
Supplier   -> Produce a value     -> result
UnaryOp    -> Modify one value    -> same type
BinaryOp   -> Combine two values  -> same type
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
