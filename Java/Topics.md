# 1. Java Fundamentals (Beginner)

### Introduction
- What is Java?
- Features of Java
- JVM, JRE, JDK
- Java Architecture
- Compilation and Execution Process
- Installing Java and IDE setup

### Basic Syntax
- Structure of Java Program
- Main Method
- Comments
- Variables
- Data Types
  - Primitive
  - Non-Primitive
- Literals
- Type Casting
- Operators
  - Arithmetic
  - Relational
  - Logical
  - Bitwise
  - Assignment
  - Ternary

### Input and Output
- Scanner Class
- BufferedReader
- Console Class
- Formatting Output

### Control Flow
- if
- if-else
- nested if
- switch
- enhanced switch expressions

### Loops
- for
- while
- do-while
- enhanced for loop
- break
- continue
- labeled statements

### Methods
- Method Declaration
- Parameters
- Return Types
- Method Overloading
- Recursion
- Pass by Value

### Arrays
- One-Dimensional Arrays ✅
- Multi-Dimensional Arrays ✅
- Jagged Arrays ✅
- Array Operations ✅
- Command Line Arguments ✅

### String Handling
- String ✅
- StringBuilder ✅
- StringBuffer ✅
- String Pool ✅
- Immutable Objects ✅
- String Methods ✅
- Regular Expressions (Regex) ❌

---

# 2. Object-Oriented Programming (Core Java)

### Classes and Objects
- Class
- Object
- Instance Variables
- Local Variables
- Constructors
- this Keyword

### Encapsulation
- Access Modifiers
  - private
  - protected
  - public
  - default
- Getters and Setters

### Inheritance
- Single Inheritance
- Multilevel Inheritance
- Hierarchical Inheritance
- super Keyword

### Polymorphism
- Method Overloading
- Method Overriding
- Dynamic Method Dispatch

### Abstraction
- Abstract Classes
- Abstract Methods

### Interfaces
- Interface Basics
- Multiple Inheritance using Interfaces
- Default Methods
- Static Methods in Interface
- Functional Interfaces

### Object Class
- equals()
- hashCode()
- toString()
- clone()

---

# 3. Advanced OOP Concepts

- Association
- Aggregation
- Composition
- Dependency
- Coupling
- Cohesion
- SOLID Principles
- Design Principles

### Nested Classes
- Inner Classes
- Static Nested Classes
- Local Inner Classes
- Anonymous Inner Classes

### Enumerations
- Enum Basics
- Enum Methods
- Enum with Constructors

---

# 4. Exception Handling

### Exceptions
- Exception Hierarchy ✅
- Checked Exceptions ✅
- Unchecked Exceptions ✅

### Keywords
- try ✅
- catch ✅
- finally ✅
- throw ✅
- throws ✅

### Advanced Exception Handling
- Multiple Catch Blocks ✅
- Custom Exceptions ✅
- Try-With-Resources ✅

---

# 5. Collections Framework

### List
- ArrayList ✅
- LinkedList ✅
- Vector ✅
- Stack

### Set
- HashSet ✅
- LinkedHashSet ✅
- TreeSet ✅

### Queue
- PriorityQueue ✅
- ArrayDeque ✅

### Map
- HashMap ✅
- LinkedHashMap ✅
- TreeMap ✅
- Hashtable ✅
- ConcurrentHashMap ✅

### Iteration Techniques
- Iterator ✅
- ListIterator ✅
- forEach ✅

### Collection Utilities
- Collections Class ✅
- Comparable ✅
- Comparator ✅

---

# 6. Generics

- Generic Classes
- Generic Methods
- Type Parameters
- Wildcards
- Bounded Types
- Type Erasure

---

# 7. Java I/O

### File Handling
- File Class
- Reading Files
- Writing Files

### Streams
- InputStream
- OutputStream
- Reader
- Writer

### Serialization
- Serializable Interface
- Object Streams
- Externalization

---

# 8. Java NIO (New I/O)

- Path
- Files
- Channels
- Buffers
- Selectors
- Asynchronous Channels

---

# 9. Multithreading and Concurrency

### Basics
- Thread Class
- Runnable Interface
- Life Cycle of Threads

### Thread Synchronization
- synchronized
- Locks
- Deadlock

### Advanced Concurrency
- Executor Framework
- Callable
- Future
- CompletableFuture
- ForkJoinPool
- CountDownLatch
- CyclicBarrier
- Semaphore
- Atomic Classes

### Concurrent Collections
- ConcurrentHashMap
- CopyOnWriteArrayList

---

# 10. Lambda Expressions & Functional Programming

### Lambda Expressions
- Syntax ✅
- Functional Interfaces ✅

### Built-in Functional Interfaces
- Predicate ✅
- Function ✅
- Consumer ✅
- Supplier ✅
- UnaryOperator ✅
- BinaryOperator ✅

### Method References
- Static Method References ✅
- Instance Method References ✅
- Constructor References ✅

---

# 11. Stream API

### Streams
- Creation of Streams
- Intermediate Operations
- Terminal Operations

### Common Operations
- filter
- map
- flatMap
- distinct
- sorted
- limit
- reduce
- collect

### Collectors
- groupingBy
- partitioningBy
- joining
- counting
- summarizing

### Parallel Streams

---

# 12. Date and Time API

### java.time Package
- LocalDate
- LocalTime
- LocalDateTime
- ZonedDateTime
- Duration
- Period
- DateTimeFormatter

---

# 13. JDBC (Database Connectivity)

### Database Basics
- JDBC Architecture
- Drivers

### Database Operations
- Connection
- Statement
- PreparedStatement
- CallableStatement
- ResultSet

### Transactions
- Commit
- Rollback

### Connection Pooling

---

# 14. Reflection API

- Class Class
- Methods Inspection
- Field Inspection
- Annotations Processing
- Dynamic Object Creation

---

# 15. Annotations

### Built-in Annotations
- @Override
- @Deprecated
- @SuppressWarnings

### Custom Annotations
- Annotation Creation
- Retention Policies
- Targets

---

# 16. Serialization and Memory Management

- Heap Memory
- Stack Memory
- Method Area
- Metaspace
- Garbage Collection
- G1 GC
- ZGC
- Shenandoah GC
- Memory Leaks

---

# 17. Java Modules (Java 9+)

- Module System
- module-info.java
- Exports
- Requires

---

# 18. JVM Internals

### JVM Architecture
- Class Loader
- Bytecode
- JIT Compiler

### Memory Model
- Heap
- Stack
- GC Regions

### Performance
- JVM Tuning
- Profiling
- Monitoring

---

# 19. Design Patterns

### Creational
- Singleton
- Factory
- Abstract Factory
- Builder
- Prototype

### Structural
- Adapter
- Facade
- Decorator
- Proxy
- Composite

### Behavioral
- Observer
- Strategy
- Command
- State
- Template Method
- Chain of Responsibility

---

# 20. Advanced Java

- Immutable Objects
- Records
- Sealed Classes
- Pattern Matching
- Virtual Threads
- Structured Concurrency
- Foreign Function API
- Reactive Programming

---

# 21. Java Testing

### Unit Testing
- JUnit 5
- Assertions
- Test Lifecycle

### Mocking
- Mockito

### Integration Testing
- Spring Test

---

# 22. Build Tools

### Maven
- Project Structure
- Dependencies
- Plugins

### Gradle
- Build Scripts
- Dependency Management

---

# 23. Logging

- java.util.logging
- Log4j
- SLF4J
- Logback

---

# 24. Spring Ecosystem (Java Backend)

### Spring Core
- Dependency Injection
- Bean Lifecycle
- IoC Container

### Spring Boot
- REST APIs
- Configuration
- Profiles
- Validation

### Spring Data JPA
- Hibernate
- ORM
- Repositories

### Spring Security
- Authentication
- Authorization
- JWT

### Microservices
- Spring Cloud
- Eureka
- Gateway
- Circuit Breaker

---

# 25. Enterprise & Distributed Systems

- REST APIs
- SOAP
- GraphQL
- Kafka
- RabbitMQ
- Redis
- Elasticsearch
- Docker
- Kubernetes

---

# 26. Expert-Level Topics

- Java Memory Model (JMM)
- Lock-Free Programming
- Unsafe API
- VarHandle
- Bytecode Engineering
- ASM Library
- Javassist
- JVM Garbage Collector Internals
- Custom Class Loaders
- High-Performance Systems
- Reactive Streams
- Netty

---

# Suggested Learning Order

```text
Java Basics
↓
Methods & Arrays
↓
Strings
↓
OOP
↓
Exception Handling
↓
Collections
↓
Generics
↓
File Handling
↓
Multithreading
↓
Lambda & Stream API
↓
JDBC
↓
Annotations & Reflection
↓
JVM Internals
↓
Design Patterns
↓
Testing
↓
Maven/Gradle
↓
Spring Boot
↓
Microservices
↓
Advanced JVM & Performance Tuning
```

For an engineer role (including Qualcomm backend/tools development), focus heavily on:
**Core Java → Collections → Multithreading → Stream API → JVM Internals → Design Patterns → Maven → Spring Boot → Microservices**, as these are the most commonly tested in interviews and used in production systems.
