## Method References

A **method reference** is just a shorter form of a lambda expression when the lambda simply calls an existing method.

For example:

```java
names.forEach(name -> System.out.println(name));
```

can be written as:

```java
names.forEach(System.out::println);
```

The `::` operator is called the **method reference operator**.

---

## 1. Static Method References

### Syntax

```java
ClassName::staticMethod
```

### Example

```java
class MathUtil {
    public static int square(int x) {
        return x * x;
    }
}
```

Lambda:

```java
Function<Integer, Integer> f = x -> MathUtil.square(x);
```

Method Reference:

```java
Function<Integer, Integer> f = MathUtil::square;
```

Usage:

```java
System.out.println(f.apply(5));
```

Output:

```text
25
```

---

### Another Example

```java
List<String> nums = List.of("1", "2", "3");

nums.stream()
    .map(Integer::parseInt)
    .forEach(System.out::println);
```

Equivalent lambda:

```java
.map(s -> Integer.parseInt(s))
```

Here:

```java
Integer::parseInt
```

is a static method reference.

---

## 2. Instance Method References

There are actually two forms.

### A. Particular Object's Instance Method

Syntax:

```java
objectRef::instanceMethod
```

Example:

```java
String prefix = "Java ";

Supplier<String> s = prefix::toUpperCase;
```

Equivalent lambda:

```java
Supplier<String> s = () -> prefix.toUpperCase();
```

Calling:

```java
System.out.println(s.get());
```

Output:

```text
JAVA
```

---

### B. Instance Method of an Arbitrary Object of a Type

Syntax:

```java
ClassName::instanceMethod
```

This one confuses many people.

Example:

```java
List<String> names = List.of("java", "python", "go");

names.stream()
     .map(String::toUpperCase)
     .forEach(System.out::println);
```

Equivalent lambda:

```java
.map(s -> s.toUpperCase())
```

Notice:

```java
String::toUpperCase
```

doesn't refer to one specific String object.

The stream supplies each String as the receiver.

So:

```java
String::toUpperCase
```

means roughly:

```java
s -> s.toUpperCase()
```

---

### Another Example

```java
Comparator<String> cmp = String::compareToIgnoreCase;
```

Equivalent:

```java
Comparator<String> cmp =
    (a, b) -> a.compareToIgnoreCase(b);
```

The first parameter becomes the object on which the method is called.

---

## 3. Constructor References

Used to invoke constructors.

### Syntax

```java
ClassName::new
```

### Example

Class:

```java
class Employee {
    Employee() {
        System.out.println("Created");
    }
}
```

Lambda:

```java
Supplier<Employee> s = () -> new Employee();
```

Method Reference:

```java
Supplier<Employee> s = Employee::new;
```

Usage:

```java
Employee e = s.get();
```

---

### Constructor with Parameters

```java
class Employee {
    String name;

    Employee(String name) {
        this.name = name;
    }
}
```

Lambda:

```java
Function<String, Employee> f =
    name -> new Employee(name);
```

Method Reference:

```java
Function<String, Employee> f =
    Employee::new;
```

Usage:

```java
Employee e = f.apply("Sahil");
```

---

## Array Constructor Reference

You can even create arrays.

Lambda:

```java
Function<Integer, int[]> f =
    size -> new int[size];
```

Method Reference:

```java
Function<Integer, int[]> f =
    int[]::new;
```

Usage:

```java
int[] arr = f.apply(10);
```

Creates:

```java
new int[10]
```

---

## Summary

| Method Reference              | Equivalent Lambda                   |
| ----------------------------- | ----------------------------------- |
| `Integer::parseInt`           | `s -> Integer.parseInt(s)`          |
| `MathUtil::square`            | `x -> MathUtil.square(x)`           |
| `System.out::println`         | `x -> System.out.println(x)`        |
| `String::toUpperCase`         | `s -> s.toUpperCase()`              |
| `String::compareToIgnoreCase` | `(a,b) -> a.compareToIgnoreCase(b)` |
| `Employee::new`               | `name -> new Employee(name)`        |
| `int[]::new`                  | `size -> new int[size]`             |

### Easy way to remember

* `ClassName::staticMethod` → Static method reference
* `object::method` → Instance method on a specific object
* `ClassName::instanceMethod` → Instance method on whatever object is passed as first argument
* `ClassName::new` → Constructor reference

A method reference is never new functionality—it is simply a more concise form of a lambda that delegates to an existing method or constructor.
