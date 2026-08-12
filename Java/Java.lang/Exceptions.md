## 1. Exception hierarchy (All below classes/interfaces are defined in "java.lang")

The important hierarchy is:

```text
Object
  └── Throwable
       ├── Error
       │    ├── OutOfMemoryError
       │    └── StackOverflowError
       │
       └── Exception
            ├── RuntimeException (Unchecked Exceptions)
            │    ├── NullPointerException
            │    ├── ArithmeticException
            │    ├── ArrayIndexOutOfBoundsException
            │    └── ...
            │
            ├── IOException
            ├── SQLException
            └── ... (Other Checked Exceptions)
```

The key distinction is:

## `Error`

Usually indicates a serious JVM/system-level problem that your application generally shouldn't try to recover from.

```java
StackOverflowError
OutOfMemoryError
```

## `Exception`

Represents conditions that an application may reasonably handle.

---

# 2. Checked vs unchecked exceptions

This is one of the **most important concepts** in Java.

## Checked exceptions

**These are direct subclasses of `Exception`.**
Exceptions that are checked by the **compiler**.

Examples:

```java
IOException
SQLException
ClassNotFoundException
```

Suppose:

```java
FileReader f = new FileReader("abc.txt");
```

`FileReader` can throw `FileNotFoundException`, so Java forces you to handle it:

```java
try {
    FileReader f = new FileReader("abc.txt");
} catch (FileNotFoundException e) {
    System.out.println("File not found");
}
```

Or declare it:

```java
void readFile() throws FileNotFoundException {
    FileReader f = new FileReader("abc.txt");
}
```

---

## Unchecked exceptions

**These are direct subclasses of `RuntimeException`.**

```text
RuntimeException
    ├── NullPointerException
    ├── ArithmeticException
    ├── IndexOutOfBoundsException
    ├── IllegalArgumentException
    └── ...
```

The compiler **doesn't force you to handle them**.

For example:

```java
String s = null;

System.out.println(s.length());
```

This throws:

```text
NullPointerException
```

You don't have to write:

```java
try {
    ...
} catch (...) {
    ...
}
```

---

# 3. `try-catch`

Use `try` for code that might throw an exception:

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Flow:

```text
try
 │
 │ exception occurs
 ▼
catch
 │
 ▼
continue program
```

---

# 4. `finally`

`finally` normally executes whether an exception occurs or not.

```java
try {
    int x = 10 / 2;
} catch (ArithmeticException e) {
    System.out.println("Error");
} finally {
    System.out.println("Always executed");
}
```

Output:

```text
Always executed
```

Even when an exception occurs:

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error");
} finally {
    System.out.println("Always executed");
}
```

Output:

```text
Error
Always executed
```

`finally` is commonly used for cleanup, although for resources Java generally prefers **try-with-resources**.

---

# 5. `throw`

`throw` is used when **you explicitly want to throw an exception**.

```java
int age = -5;

if (age < 0) {
    throw new IllegalArgumentException("Age cannot be negative");
}
```

Here you're creating and throwing the exception yourself.

```text
throw new IllegalArgumentException(...)
      ↑
      create exception object
```

---

# 6. `throws`

`throws` is used in a method declaration to say:

> "This method may throw this exception; whoever calls me needs to deal with it."

```java
void readFile() throws IOException {
    // ...
}
```

Then:

```java
try {
    readFile();
} catch (IOException e) {
    // handle it
}
```

So remember:

```text
throw   → actually throw an exception
throws  → declare that a method may throw an exception
```

---

# 7. Multiple exceptions

You can have multiple `catch` blocks:

```java
try {
    int x = arr[10];
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Invalid index");
} catch (Exception e) {
    System.out.println("Some other exception");
}
```

The more specific exception must come first.

This is wrong:

```java
catch (Exception e) {
    ...
}
catch (ArithmeticException e) {   // unreachable
    ...
}
```

Because `ArithmeticException` is already caught by `Exception`.

---

# 8. Multi-catch

Java also allows:

```java
try {
    // ...
} catch (IOException | SQLException e) {
    System.out.println("Something went wrong");
}
```

Useful when different exceptions need identical handling.

---

# 9. Exception object

When Java throws an exception, an **exception object** is created.

For example:

```java
throw new IllegalArgumentException("Invalid age");
```

The object contains information about the problem.

You can access things such as:

```java
catch (Exception e) {
    System.out.println(e.getMessage());
    System.out.println(e.getClass());
    e.printStackTrace();
}
```

For:

```java
throw new IllegalArgumentException("Invalid age");
```

`e.getMessage()` gives:

```text
Invalid age
```

---

# 10. Stack trace

Consider:

```java
static void a() {
    b();
}

static void b() {
    c();
}

static void c() {
    int x = 10 / 0;
}
```

You might get:

```text
java.lang.ArithmeticException: / by zero
    at Main.c(Main.java:...)
    at Main.b(Main.java:...)
    at Main.a(Main.java:...)
    at Main.main(Main.java:...)
```

This is the **stack trace**.

It tells you the chain of method calls that led to the exception.

```text
main()
  ↓
a()
  ↓
b()
  ↓
c()
  ↓
exception
```

---

# 11. Exception propagation

Suppose:

```java
static void c() {
    int x = 10 / 0;
}

static void b() {
    c();
}

static void a() {
    b();
}
```

If nobody catches the exception inside `c()`, it propagates upward:

```text
c()
 ↓
b()
 ↓
a()
 ↓
main()
```

If `main()` catches it:

```java
public static void main(String[] args) {
    try {
        a();
    } catch (ArithmeticException e) {
        System.out.println("Caught!");
    }
}
```

then propagation stops at `main()`.

---

# 12. Custom exceptions

You can create your own exception.

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Then:

```java
static void checkAge(int age) throws InvalidAgeException {
    if (age < 18) {
        throw new InvalidAgeException("Must be 18 or older");
    }
}
```

And:

```java
try {
    checkAge(15);
} catch (InvalidAgeException e) {
    System.out.println(e.getMessage());
}
```

You can make it checked:

```java
extends Exception
```

or unchecked:

```java
extends RuntimeException
```

---

## The big picture

Think of Java exceptions like this:

```text
                    Throwable
                   /         \
                Error       Exception
                             /       \
                     RuntimeException  Other exceptions
                           │                  │
                           │                  ├── IOException
                           │                  ├── SQLException
                           │                  └── ...
                           │
                           ├── NullPointerException
                           ├── ArithmeticException
                           ├── ArrayIndexOutOfBoundsException
                           └── ...
```

And the five keywords to remember are:

| Keyword   | Meaning                                      |
| --------- | -------------------------------------------- |
| `try`     | Code that might fail                         |
| `catch`   | Handle an exception                          |
| `finally` | Cleanup code that normally runs regardless   |
| `throw`   | Explicitly throw an exception                |
| `throws`  | Declare that a method may throw an exception |

**One particularly important distinction:** `Exception` is a class, but `Throwable` is the actual top-level type that Java's `throw` mechanism works with. Both `Exception` and `Error` extend `Throwable`.
