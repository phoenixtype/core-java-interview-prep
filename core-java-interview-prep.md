# Core Java Interview Mastery Guide
### Based on @Javatechie "Core Java Top FAQs" + Comprehensive Advanced Topics
> Covers: OOP · Collections · Generics · Exceptions · Multithreading · Streams API · Functional Interfaces · Lambda Expressions · Async/CompletableFuture · Modern Java (Java 9–21)

---

## Table of Contents

1. [OOP Fundamentals](#1-oop-fundamentals)
2. [Java Basics & Memory Model](#2-java-basics--memory-model)
3. [String & Immutability](#3-string--immutability)
4. [Collections Framework](#4-collections-framework)
5. [Exception Handling](#5-exception-handling)
6. [Generics](#6-generics)
7. [Functional Interfaces](#7-functional-interfaces)
8. [Lambda Expressions](#8-lambda-expressions)
9. [Streams API](#9-streams-api)
10. [Optionals](#10-optionals)
11. [Multithreading & Concurrency](#11-multithreading--concurrency)
12. [Async Programming – CompletableFuture](#12-async-programming--completablefuture)
13. [Modern Java (Java 9–21)](#13-modern-java-java-921)
14. [JVM Internals & Garbage Collection](#14-jvm-internals--garbage-collection)
15. [Design Patterns in Java](#15-design-patterns-in-java)
16. [QUIZ – 100 Questions](#16-quiz--100-questions)
17. [Quiz Answer Key](#17-quiz-answer-key)

---

## 1. OOP Fundamentals

> **FAANG Interview Tip:** At Google and Amazon, OOP questions are warm-up rounds — but getting them wrong is an instant red flag. Be crisp. Define each pillar in one sentence, then give a real-world analogy and a code example without hesitation.


### The Four Pillars

OOP is the backbone of Java. Every interview starts here. Think of OOP like building with LEGO: each brick is a class, each structure you build is an object.

---

#### 1.1 Encapsulation

> **Analogy:** Your bank account. You never hand someone your raw data — you expose controlled operations (deposit, withdraw).

Encapsulation bundles data (fields) and behaviour (methods) together, hiding internal details behind `private` access and exposing them via `public` getters/setters.

```java
public class BankAccount {
    private double balance; // hidden data

    public double getBalance() {     // controlled access
        return balance;
    }

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
}
```

**Interview tip:** Say *"encapsulation protects invariants"* — e.g., ensuring `balance` never goes negative through controlled setter logic.

---

#### 1.2 Inheritance

> **Analogy:** A generic `Vehicle` blueprint. A `Car` and a `Truck` both inherit engine and wheels, but add their own features.

```java
public class Vehicle {
    protected int speed;
    public void accelerate() { speed += 10; }
}

public class Car extends Vehicle {
    private int numDoors;

    @Override
    public void accelerate() {
        super.accelerate(); // reuse parent logic
        System.out.println("Vroom at " + speed);
    }
}
```

Java supports **single class inheritance** but **multiple interface implementation**. This is why interfaces matter.

---

#### 1.3 Polymorphism

**Two kinds:**
- **Compile-time (static):** Method overloading — same name, different parameters.
- **Runtime (dynamic):** Method overriding — subclass provides its own implementation.

```java
// Overloading (compile-time)
class Calculator {
    int add(int a, int b)          { return a + b; }
    double add(double a, double b) { return a + b; }
}

// Overriding (runtime)
class Animal     { public String sound() { return "..."; } }
class Dog extends Animal { @Override public String sound() { return "Woof"; } }
class Cat extends Animal { @Override public String sound() { return "Meow"; } }

Animal a = new Dog(); // reference type = Animal, actual object = Dog
System.out.println(a.sound()); // "Woof" — decided at runtime
```

---

#### 1.4 Abstraction

> **Analogy:** Driving a car — you use the steering wheel and pedals (the interface), not the combustion internals (the implementation).

**Abstract class vs Interface:**

| Feature | Abstract Class | Interface |
|---|---|---|
| Can have fields | ✅ | Only `static final` constants |
| Constructor | ✅ | ❌ |
| Multiple inheritance | ❌ (extend one) | ✅ (implement many) |
| Default methods (Java 8+) | ✅ | ✅ |
| When to use | Shared state + behaviour | Pure capability contract |

```java
// Abstract class — partial blueprint
abstract class Shape {
    String color;
    abstract double area(); // must be implemented
    void describe() {       // concrete — shared behaviour
        System.out.println("I am a " + color + " shape");
    }
}

// Interface — capability contract
interface Drawable {
    void draw();
    default void printLabel() {    // Java 8+ default method
        System.out.println("Drawing " + getClass().getSimpleName());
    }
}

class Circle extends Shape implements Drawable {
    double radius;
    Circle(String color, double radius) { this.color = color; this.radius = radius; }

    @Override public double area() { return Math.PI * radius * radius; }
    @Override public void draw()   { System.out.println("○"); }
}
```

---

#### 1.5 Method Overloading vs Overriding — Quick Cheat-Sheet

| | Overloading | Overriding |
|---|---|---|
| Binding | Compile-time | Runtime |
| Inheritance needed? | No | Yes |
| Return type | Can differ | Must be same (or covariant) |
| `static` methods? | Yes | No (they hide, not override) |
| `final` methods? | Yes | No |

---

### 1.6 `this` vs `super`

```java
class Parent {
    int x = 10;
    Parent() { System.out.println("Parent constructor"); }
}

class Child extends Parent {
    int x = 20;
    Child() {
        super();        // calls Parent constructor — must be first line
        System.out.println("Child x=" + this.x + ", Parent x=" + super.x);
    }
}
```

---


<details>
<summary><b>Check Yourself — Java Basics</b></summary>

- What happens in memory when you write `String s = new String("hello")`? How many objects are created?
- Can a `finally` block prevent a `System.exit()` from terminating the JVM?
- Draw the JVM architecture from memory. Include class loader, execution engine, and all memory areas.
</details>

**Deep Dive — Read More:**
- [JVM Architecture (Oracle)](https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-2.html) — official specification
- [Understanding JVM Internals (Baeldung)](https://www.baeldung.com/jvm-internals) — practical walkthrough
- [Java Memory Model (Jenkov)](https://jenkov.com/tutorials/java-concurrency/java-memory-model.html) — concurrency-focused

---

## 2. Java Basics & Memory Model


```
┌─────────────────────────────────────────────────────┐
│                    JDK (Development Kit)             │
│  ┌───────────────────────────────────────────────┐  │
│  │               JRE (Runtime Environment)       │  │
│  │  ┌─────────────────────────────────────────┐  │  │
│  │  │              JVM (Virtual Machine)       │  │  │
│  │  │                                          │  │  │
│  │  │  ┌──────────┐  ┌────────────────────┐   │  │  │
│  │  │  │Class     │  │ Runtime Memory     │   │  │  │
│  │  │  │Loader    │  │ ┌──────┐ ┌──────┐  │   │  │  │
│  │  │  │System    │  │ │Stack │ │ Heap │  │   │  │  │
│  │  │  └──────────┘  │ │(per  │ │(shared)│ │   │  │  │
│  │  │  ┌──────────┐  │ │thread│ │      │  │   │  │  │
│  │  │  │Execution │  │ └──────┘ └──────┘  │   │  │  │
│  │  │  │Engine    │  │ ┌──────┐ ┌──────┐  │   │  │  │
│  │  │  │(JIT +    │  │ │Method│ │  PC  │  │   │  │  │
│  │  │  │ Interp.) │  │ │ Area │ │Regist│  │   │  │  │
│  │  │  └──────────┘  │ └──────┘ └──────┘  │   │  │  │
│  │  │                └────────────────────┘   │  │  │
│  │  └─────────────────────────────────────────┘  │  │
│  │  + Standard Libraries (java.lang, java.util)  │  │
│  └───────────────────────────────────────────────┘  │
│  + javac, jdb, jar, javadoc tools                   │
└─────────────────────────────────────────────────────┘
```


### 2.1 JVM, JRE, JDK

> **Analogy:** JDK = the full kitchen (tools to cook). JRE = just the oven (environment to run). JVM = the chef's brain that reads the recipe (bytecode) and makes it happen on any stove (OS).

- **JVM** — executes bytecode, manages memory, platform-specific
- **JRE** — JVM + standard libraries (to *run*)
- **JDK** — JRE + compiler, debugger, tools (to *develop*)

---

### 2.2 Heap vs Stack Memory


```
  STACK (per thread)              HEAP (shared across threads)
 ┌──────────────────┐           ┌──────────────────────────────┐
 │ main()           │           │                              │
 │  int x = 10      │           │  ┌────────────────────┐      │
 │  Dog d ──────────┼──────────►│  │ Dog object         │      │
 │                  │           │  │  name = "Rex"      │      │
 ├──────────────────┤           │  │  age = 5           │      │
 │ calculate()      │           │  └────────────────────┘      │
 │  int result = 42 │           │                              │
 │  String s ───────┼──────────►│  ┌─────────────────┐        │
 │                  │           │  │ "Hello" (String) │        │
 └──────────────────┘           │  └─────────────────┘        │
                                └──────────────────────────────┘
  • Primitives live here          • Objects live here
  • Fast (LIFO)                   • Slower (GC managed)
  • Thread-safe                   • Shared = needs sync
  • StackOverflowError            • OutOfMemoryError
```


| | Stack | Heap |
|---|---|---|
| Stores | Primitive values, method call frames, object references | Objects, instance variables |
| Lifetime | Until method returns | Until GC collects |
| Thread safety | Per-thread (inherently safe) | Shared across threads (need synchronization) |
| Speed | Fast (LIFO) | Slower allocation |

```java
void method() {
    int x = 5;              // Stack — local primitive
    String s = new String("Hi"); // s reference on Stack; "Hi" object on Heap
}
```

---

### 2.3 Pass by Value vs Pass by Reference

Java is **always pass by value**. For objects, it passes a *copy of the reference* (address), not the object itself.

```java
void modify(int n, StringBuilder sb) {
    n = 100;           // changes local copy only — caller unchanged
    sb.append("!");    // mutates the OBJECT the reference points to — caller sees it
}

int num = 5;
StringBuilder msg = new StringBuilder("Hello");
modify(num, msg);
System.out.println(num); // 5  — unchanged
System.out.println(msg); // "Hello!" — modified through shared reference
```

---

### 2.4 `==` vs `.equals()`

- `==` compares **memory addresses** (references)
- `.equals()` compares **content** (when properly overridden)

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);       // false — different objects
System.out.println(a.equals(b));  // true  — same content

// String pool trick
String c = "Java";
String d = "Java";
System.out.println(c == d);       // true — same pool entry
```

---

### 2.5 `final`, `finally`, `finalize`

| Keyword | Purpose |
|---|---|
| `final` | Class can't be subclassed; method can't be overridden; variable is a constant |
| `finally` | Block in try-catch that **always** runs (even on exception), used for cleanup |
| `finalize()` | Deprecated method called by GC before object collection — avoid using it |

```java
final int MAX = 100; // constant

try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
} finally {
    System.out.println("Always runs — close DB connections here");
}
```

---


<details>
<summary><b>Check Yourself — Strings</b></summary>

- How many objects are in the String Pool after: `String a = "hello"; String b = "hello"; String c = new String("hello");`?
- Why is `StringBuilder` faster than `String` concatenation in a loop? What's happening under the hood?
- Can you modify a String using reflection? Should you? What breaks?
</details>

**Deep Dive — Read More:**
- [String Pool in Java (Baeldung)](https://www.baeldung.com/java-string-pool)
- [Why String is Immutable (JavaTechie)](https://www.youtube.com/watch?v=Bj9Mx_Lx3q4)

---

## 3. String & Immutability

> **Real Interview Scenario (Amazon):** "You have a method that concatenates strings in a loop 100,000 times. The candidate before you used `+=` and it took 30 seconds. How would you fix it and explain why the original approach was slow?" — This tests String Pool, immutability, and StringBuilder knowledge simultaneously.


### 3.1 Why is String Immutable?

> **Analogy:** A printed book. Once printed, the content can't change. If you want a different version, print a new book. The benefit: multiple people can read the same book simultaneously without conflict.

Benefits of String immutability:
1. **Thread safety** — multiple threads can share the same String safely
2. **String pool** — JVM can cache and reuse literals
3. **Security** — network connections, class names, file paths are Strings; they shouldn't change mid-use
4. **Hashing** — String's hashCode is cached after first computation

```java
String s1 = "Hello";
String s2 = s1.concat(" World"); // s1 is unchanged; new object created
System.out.println(s1); // "Hello"
System.out.println(s2); // "Hello World"
```

---

### 3.2 String vs StringBuilder vs StringBuffer

| | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Mutability | Immutable | Mutable | Mutable |
| Thread safe | Yes (immutable) | No | Yes (synchronized) |
| Performance | Slow for concatenation | Fast | Slower than StringBuilder |
| Use when | Fixed, shared values | Single-thread string building | Multi-thread string building |

```java
// Bad for loops — creates many intermediate String objects
String result = "";
for (int i = 0; i < 1000; i++) result += i; // wasteful!

// Good — single mutable buffer
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) sb.append(i);
String result2 = sb.toString();
```

---

### 3.3 String Pool

```java
String a = "Java";           // placed in String pool
String b = "Java";           // reuses same pool entry
String c = new String("Java"); // forces a new heap object

System.out.println(a == b);  // true  — same pool reference
System.out.println(a == c);  // false — c is on heap, not pool
System.out.println(a.equals(c)); // true — same content

String d = c.intern(); // moves c's value into pool
System.out.println(a == d);  // true
```

---


<details>
<summary><b>Check Yourself — Collections</b></summary>

- When does HashMap convert a linked list bucket to a red-black tree? When does it convert back?
- What is the time complexity of `TreeMap.get()` vs `HashMap.get()`? Why would you ever choose TreeMap?
- What happens if you modify a `HashSet` element's `hashCode()` after inserting it?
</details>

**Deep Dive — Read More:**
- [HashMap Internals (Baeldung)](https://www.baeldung.com/java-hashmap-advanced) — deep dive
- [ConcurrentHashMap (Oracle)](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)
- [When to Use Which Collection (Java Brains)](https://www.youtube.com/watch?v=GhslBwLRm6s)

---

## 4. Collections Framework


```
                       java.util Collection Hierarchy

                            Iterable<E>
                               │
                          Collection<E>
                       ┌───────┼────────┐
                       │       │        │
                    List<E>  Set<E>   Queue<E>
                       │       │        │
              ┌────────┤    ┌──┤     ┌──┴──┐
              │        │    │  │     │     │
          ArrayList LinkedList HashSet TreeSet PriorityQueue Deque
                                │                          │
                           LinkedHashSet              ArrayDeque

                            Map<K,V>  (separate hierarchy)
                       ┌───────┼────────┐
                       │       │        │
                   HashMap  TreeMap  LinkedHashMap
                       │
                  ConcurrentHashMap

   ┌──────────────┬──────────────┬──────────────┬──────────────┐
   │   List       │   Set        │   Queue      │   Map        │
   ├──────────────┼──────────────┼──────────────┼──────────────┤
   │ Ordered      │ No dupes     │ FIFO/Priority│ Key→Value    │
   │ Index access │ Fast lookup  │ Producer/    │ Fast lookup  │
   │ Allows dupes │              │ Consumer     │ No dupe keys │
   └──────────────┴──────────────┴──────────────┴──────────────┘
```

> **FAANG Interview Tip:** At Google, you'll be asked to choose the right collection for a problem, not just define them. Practice: "I need O(1) lookup, insertion order preserved, and no duplicates" → `LinkedHashSet`. Be instant.


### 4.1 Overview — The Big Picture

```
Iterable
  └── Collection
        ├── List       → ordered, allows duplicates
        │     ├── ArrayList
        │     └── LinkedList
        ├── Set        → no duplicates
        │     ├── HashSet
        │     ├── LinkedHashSet (insertion order)
        │     └── TreeSet (sorted order)
        └── Queue
              ├── LinkedList
              └── PriorityQueue

Map (separate hierarchy)
  ├── HashMap
  ├── LinkedHashMap
  ├── TreeMap
  └── ConcurrentHashMap
```

---

### 4.2 ArrayList vs LinkedList

> **Analogy:** ArrayList = numbered parking spots in a row. Fast to find spot #50 (random access). LinkedList = treasure hunt — each spot points to the next. Fast to add/remove at beginning.

| | ArrayList | LinkedList |
|---|---|---|
| Underlying structure | Dynamic array | Doubly-linked nodes |
| Random access (get) | O(1) | O(n) |
| Insert/delete at middle | O(n) | O(1) after traversal |
| Memory | Less (array) | More (node pointers) |
| Best for | Read-heavy, index access | Insert/delete heavy |

```java
List<String> arrayList = new ArrayList<>(); // add at end — O(1) amortized
arrayList.add("A");
arrayList.get(0); // O(1)

List<String> linkedList = new LinkedList<>();
((LinkedList<String>) linkedList).addFirst("Z"); // O(1)
linkedList.get(0); // O(n) — has to traverse!
```

---

### 4.3 HashMap Internals


```
HashMap Internal Structure (Java 8+)

   HashMap table (Node<K,V>[] array)
   ┌───┬───┬───┬───┬───┬───┬───┬───┐
   │ 0 │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │  ← bucket index = hash(key) & (n-1)
   └─┬─┴───┴─┬─┴───┴───┴─┬─┴───┴───┘
     │       │           │
     ▼       ▼           ▼
   ┌───┐   ┌───┐       ┌───┐
   │K:V│   │K:V│       │K:V│  ← Node (linked list)
   └─┬─┘   └─┬─┘       └─┬─┘
     │       │           │
     ▼       ▼           ▼
   ┌───┐   ┌───┐       ┌───┐
   │K:V│   │K:V│       │K:V│
   └───┘   └─┬─┘       └───┘
             │
             ▼          When chain length > 8:
           ┌───┐        Linked List → Red-Black Tree (O(n) → O(log n))
           │K:V│
           └───┘        When size > capacity × 0.75 (load factor):
                        Array doubles in size (rehashing)
```


> **Analogy:** A HashMap is a filing cabinet with 16 drawers (default buckets). The `hashCode()` of your key determines which drawer. The `equals()` check finds the exact file inside the drawer.

**How it works:**
1. `key.hashCode()` → hashed to a bucket index
2. Multiple keys can land in the same bucket → **collision** → stored as linked list (or tree in Java 8+ when > 8 entries)
3. Java 8 converts long chains to **balanced trees** for O(log n) worst-case

```java
Map<String, Integer> map = new HashMap<>();
map.put("Alice", 30);
map.put("Bob", 25);

// Null key allowed (goes to bucket 0)
map.put(null, 0);

// Iteration
map.forEach((k, v) -> System.out.println(k + " -> " + v));

// Common operations
map.getOrDefault("Charlie", -1); // -1 if not found
map.putIfAbsent("Alice", 99);    // won't overwrite existing
map.computeIfAbsent("Dave", k -> k.length()); // computes value if missing
```

**HashMap vs Hashtable vs ConcurrentHashMap:**

| | HashMap | Hashtable | ConcurrentHashMap |
|---|---|---|---|
| Thread safe | No | Yes (full sync) | Yes (segment locks) |
| Null key | 1 allowed | Not allowed | Not allowed |
| Performance | Best | Worst | Good |
| Use in | Single thread | Legacy (avoid) | Multi-thread |

---

### 4.4 HashSet vs TreeSet vs LinkedHashSet

```java
Set<String> hashSet = new HashSet<>();       // O(1) add/remove — no order guarantee
Set<String> treeSet = new TreeSet<>();       // O(log n) — always sorted alphabetically
Set<String> linkedHash = new LinkedHashSet<>(); // O(1) — preserves insertion order

treeSet.add("Banana"); treeSet.add("Apple"); treeSet.add("Cherry");
System.out.println(treeSet); // [Apple, Banana, Cherry] — sorted!
```

---

### 4.5 Comparable vs Comparator

> **Analogy:** `Comparable` is like giving an employee a default sort order (e.g., by ID). `Comparator` is like a custom sorting machine you plug in for a specific job (e.g., sort by salary for payroll).

```java
// Comparable — natural ordering baked into the class
class Employee implements Comparable<Employee> {
    int id; String name;
    @Override public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id); // sort by ID
    }
}

// Comparator — external, flexible
Comparator<Employee> bySalary = Comparator.comparingDouble(e -> e.salary);
Comparator<Employee> byNameDesc = Comparator.comparing((Employee e) -> e.name).reversed();

List<Employee> employees = new ArrayList<>();
Collections.sort(employees);               // uses Comparable (by id)
employees.sort(bySalary);                  // uses Comparator (by salary)
employees.sort(byNameDesc.thenComparing(e -> e.id)); // chained
```

---

### 4.6 fail-fast vs fail-safe Iterators

- **fail-fast** (ArrayList, HashMap): throws `ConcurrentModificationException` if the collection is modified during iteration
- **fail-safe** (ConcurrentHashMap, CopyOnWriteArrayList): iterates over a snapshot, no exception

```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));
// This throws ConcurrentModificationException:
for (String s : list) {
    if (s.equals("B")) list.remove(s); // DANGER!
}

// Safe removal during iteration:
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("B")) it.remove(); // safe!
}
```

---


<details>
<summary><b>Check Yourself — Exceptions</b></summary>

- What's the difference between `throw` and `throws`?
- Can you catch an `Error`? Should you? Give one case where you might.
- What happens if both `catch` and `finally` throw an exception?
</details>

**Deep Dive — Read More:**
- [Exception Handling Best Practices (Baeldung)](https://www.baeldung.com/java-exceptions)
- [Effective Java Item 69-77: Exceptions (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java/9780134686097/)

---

## 5. Exception Handling


```
                        Throwable
                     ┌─────┴─────┐
                     │           │
                   Error     Exception
                     │           │
              ┌──────┤     ┌─────┴──────┐
              │      │     │            │
         OutOfMemory Stack  │     RuntimeException
         Error    Overflow  │     (Unchecked)
                   Error    │        │
                            │   ┌────┼──────────┐
                     IOException │   │          │
                     SQLException NullPointer  IndexOutOf
                     (Checked)   Exception    BoundsException
                     
   Checked: Compiler forces you to handle (try-catch or throws)
   Unchecked: RuntimeException subclasses — compiler doesn't enforce
```

> **FAANG Interview Tip:** Never catch `Exception` or `Throwable` in production code. Interviewers at all FAANG companies will challenge you on this. Catch the most specific exception possible, and explain why.


### 5.1 Exception Hierarchy

```
Throwable
  ├── Error          → JVM problems (OutOfMemoryError) — don't catch
  └── Exception
        ├── Checked  → Must handle at compile-time (IOException, SQLException)
        └── RuntimeException (Unchecked) → Programming errors (NPE, ArrayIndexOutOfBounds)
```

> **Analogy:** Checked exceptions are like required form fields — you must handle them. Unchecked are bugs — you should prevent them with better code.

---

### 5.2 try-with-resources (Java 7+)

Automatically closes resources implementing `AutoCloseable`. No need for `finally` blocks to close streams.

```java
// Old way — messy
InputStream is = null;
try {
    is = new FileInputStream("file.txt");
    // use it
} finally {
    if (is != null) is.close(); // easy to forget!
}

// Modern way — clean and safe
try (InputStream is = new FileInputStream("file.txt");
     BufferedReader br = new BufferedReader(new InputStreamReader(is))) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} // both br and is are closed automatically, even on exception
```

---

### 5.3 Custom Exceptions

```java
public class InsufficientFundsException extends RuntimeException {
    private final double amount;

    public InsufficientFundsException(double amount) {
        super("Insufficient funds. Needed: " + amount);
        this.amount = amount;
    }

    public double getAmount() { return amount; }
}

// Usage
void withdraw(double amount) {
    if (amount > balance) throw new InsufficientFundsException(amount);
    balance -= amount;
}
```

---

### 5.4 Multi-catch (Java 7+)

```java
try {
    // risky code
} catch (IOException | SQLException e) {
    // handle both the same way
    log.error("Data error: " + e.getMessage());
}
```

---

## 6. Generics

### 6.1 Why Generics?

> **Analogy:** Without generics, a box can hold anything — you open it and hope it's what you expected. With generics, the box is labeled `Box<String>` — it will only ever contain Strings.

```java
// Without generics — type-unsafe
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0); // manual cast, can throw ClassCastException

// With generics — compile-time type checking
List<String> typedList = new ArrayList<>();
typedList.add("hello");
String s2 = typedList.get(0); // no cast needed
```

---

### 6.2 Bounded Type Parameters

```java
// Upper bound — accepts T or any subtype of Number
public <T extends Number> double sum(List<T> list) {
    return list.stream().mapToDouble(Number::doubleValue).sum();
}

sum(List.of(1, 2, 3));       // Integer is a Number ✅
sum(List.of(1.5, 2.5));      // Double is a Number ✅

// Wildcard — ? extends = read-only producer; ? super = write-only consumer (PECS rule)
void printAll(List<? extends Shape> shapes) {  // can READ shapes from list
    shapes.forEach(s -> System.out.println(s.area()));
}
void addShapes(List<? super Circle> list) {    // can ADD circles to list
    list.add(new Circle("red", 5));
}
```

**PECS = Producer Extends, Consumer Super**

---

## 7. Functional Interfaces


```
   Built-in Functional Interfaces Cheat Sheet

   ┌──────────────────┬──────────────┬──────────────┬─────────────┐
   │   Interface      │   Input      │   Output     │   Method    │
   ├──────────────────┼──────────────┼──────────────┼─────────────┤
   │ Function<T,R>    │   T          │   R          │ apply(T)    │
   │ BiFunction<T,U,R>│   T, U       │   R          │ apply(T,U)  │
   │ Predicate<T>     │   T          │   boolean    │ test(T)     │
   │ Consumer<T>      │   T          │   void       │ accept(T)   │
   │ Supplier<T>      │   (none)     │   T          │ get()       │
   │ UnaryOperator<T> │   T          │   T          │ apply(T)    │
   │ BinaryOperator<T>│   T, T       │   T          │ apply(T,T)  │
   └──────────────────┴──────────────┴──────────────┴─────────────┘

   Analogy:
   Function  = a machine (input → different output)
   Predicate = a filter (input → yes/no)
   Consumer  = a sink (input → gone, side effect)
   Supplier  = a factory (nothing → output)
```


### 7.1 What is a Functional Interface?

An interface with **exactly one abstract method** (SAM — Single Abstract Method). It can still have `default` and `static` methods.

The `@FunctionalInterface` annotation enforces this at compile time.

```java
@FunctionalInterface
interface Transformer<T, R> {
    R transform(T input); // the one abstract method
    // Can still have:
    default void log() { System.out.println("Transforming..."); }
    static Transformer<String, String> toUpper() { return String::toUpperCase; }
}
```

---

### 7.2 Built-in Functional Interfaces (java.util.function)

| Interface | Signature | Purpose | Example |
|---|---|---|---|
| `Predicate<T>` | `T → boolean` | Test/filter | `s -> s.isEmpty()` |
| `Function<T, R>` | `T → R` | Transform | `s -> s.length()` |
| `Consumer<T>` | `T → void` | Do something | `System.out::println` |
| `Supplier<T>` | `() → T` | Produce a value | `() -> new ArrayList<>()` |
| `BiFunction<T,U,R>` | `T, U → R` | Two inputs | `(a, b) -> a + b` |
| `UnaryOperator<T>` | `T → T` | Transform same type | `s -> s.toUpperCase()` |
| `BinaryOperator<T>` | `T, T → T` | Combine same type | `(a, b) -> a + b` |

```java
Predicate<String>  isLong   = s -> s.length() > 5;
Function<String, Integer> length = String::length;
Consumer<String>   printer  = System.out::println;
Supplier<List<String>> newList = ArrayList::new;

// Composing predicates
Predicate<String> startsWithA = s -> s.startsWith("A");
Predicate<String> longAndStartsA = isLong.and(startsWithA);
Predicate<String> longOrStartsA  = isLong.or(startsWithA);
Predicate<String> notLong        = isLong.negate();

// Composing functions
Function<String, String> trim = String::trim;
Function<String, String> upper = String::toUpperCase;
Function<String, String> trimThenUpper = trim.andThen(upper);

System.out.println(trimThenUpper.apply("  hello  ")); // "HELLO"
```

---

## 8. Lambda Expressions

### 8.1 What is a Lambda?

> **Analogy:** Before lambdas, passing behaviour was like hiring a full-time employee for a one-task job. Lambdas are like Uber — you call for exactly what you need, right then.

A lambda is a compact, inline implementation of a functional interface. It eliminates the need for anonymous inner classes.

```java
// Old way — anonymous inner class
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running the old way!");
    }
};

// Lambda way — clean and concise
Runnable r2 = () -> System.out.println("Running with lambda!");

// With parameters
Comparator<String> byLength = (a, b) -> Integer.compare(a.length(), b.length());

// Multi-line body
Comparator<String> byLengthVerbose = (a, b) -> {
    int diff = a.length() - b.length();
    return diff;
};
```

---

### 8.2 Method References

A shorthand for calling an existing method in lambda form: `ClassName::methodName`

| Type | Syntax | Equivalent Lambda |
|---|---|---|
| Static method | `Math::abs` | `x -> Math.abs(x)` |
| Instance method on instance | `str::toUpperCase` | `() -> str.toUpperCase()` |
| Instance method on type | `String::toUpperCase` | `s -> s.toUpperCase()` |
| Constructor | `ArrayList::new` | `() -> new ArrayList<>()` |

```java
List<String> names = List.of("Charlie", "Alice", "Bob");

// Without method reference
names.forEach(name -> System.out.println(name));

// With method reference
names.forEach(System.out::println);

// Sorting with method reference
names.stream()
     .sorted(String::compareToIgnoreCase)
     .forEach(System.out::println);
```

---

### 8.3 Variable Capture in Lambdas

Lambdas can capture local variables from the enclosing scope, but those variables must be **effectively final** (not modified after the lambda is defined).

```java
String prefix = "Hello, ";    // effectively final — never reassigned
Consumer<String> greeter = name -> System.out.println(prefix + name);
greeter.accept("Alice");       // "Hello, Alice"

// This would NOT compile:
// prefix = "Hi, "; // mutation breaks effective finality
```

---


<details>
<summary><b>Check Yourself — Streams</b></summary>

- What's the difference between `map()` and `flatMap()`? Give a real example where you need `flatMap()`.
- When should you use `parallelStream()`? When is it harmful?
- Write a stream pipeline that: reads lines from a file, filters blank lines, converts to uppercase, removes duplicates, sorts, and collects to a List.
</details>

**Deep Dive — Read More:**
- [Stream API (Oracle Tutorial)](https://docs.oracle.com/javase/tutorial/collections/streams/)
- [Collectors Guide (Baeldung)](https://www.baeldung.com/java-8-collectors)
- [Parallel Streams Pitfalls (DZone)](https://dzone.com/articles/think-twice-using-java-8)

---

## 9. Streams API


```
   Stream Pipeline Architecture

   Source ──────► Intermediate Ops ──────► Terminal Op ──────► Result
   (Collection,   (filter, map, sorted,   (collect, forEach,  (List, Map,
    Array,         flatMap, distinct,       reduce, count,      int, void)
    File, etc.)    peek, limit, skip)       findFirst, anyMatch)

   ┌──────┐   filter()   ┌──────┐   map()   ┌──────┐  collect()  ┌────────┐
   │[1,2, │──────────────►│[2,4, │──────────►│[4,8, │────────────►│List    │
   │ 3,4, │  keep evens   │ 6,8, │  × 2      │12,16,│  toList()   │[4,8,12,│
   │ 5,6, │               │ 10]  │           │ 20]  │             │ 16,20] │
   │ 7,8, │               └──────┘           └──────┘             └────────┘
   │ 9,10]│
   └──────┘
   
   KEY RULES:
   ✓ Streams are LAZY — nothing executes until a terminal op is called
   ✓ Streams are single-use — cannot be reused after terminal op
   ✓ Intermediate ops return a Stream (chainable)
   ✓ Terminal ops return a non-Stream result (end of pipeline)
```

> **Real Interview Scenario (Google):** "Given a list of 10 million transactions, filter those above $1000, group by merchant, and return the top 5 merchants by total revenue. Write this using Streams in under 5 minutes."

```java
Map<String, Double> top5 = transactions.stream()
    .filter(t -> t.getAmount() > 1000)
    .collect(Collectors.groupingBy(
        Transaction::getMerchant,
        Collectors.summingDouble(Transaction::getAmount)))
    .entrySet().stream()
    .sorted(Map.Entry.<String, Double>comparingByValue().reversed())
    .limit(5)
    .collect(Collectors.toMap(
        Map.Entry::getKey, Map.Entry::getValue,
        (a, b) -> a, LinkedHashMap::new));
```


### 9.1 What is a Stream?

> **Analogy:** A stream is an assembly line. Raw materials (your collection) enter at one end, pass through processing stations (intermediate operations), and finished products come out the other end (terminal operation). The line doesn't start until the last station is set up.

Key points:
- **Not a data structure** — doesn't store data
- **Lazy** — intermediate operations don't execute until a terminal operation is called
- **Single-use** — consumed once; must create a new stream to reuse

---

### 9.2 Stream Pipeline Structure

```
Collection/Array/Source
      ↓
  stream() / parallelStream()
      ↓
  [Intermediate Operations — lazy, return Stream]
  filter(), map(), flatMap(), sorted(), distinct(), limit(), skip(), peek()
      ↓
  [Terminal Operation — triggers execution]
  collect(), forEach(), reduce(), count(), findFirst(), anyMatch(), toList()
```

---

### 9.3 Common Operations

```java
List<Employee> employees = List.of(
    new Employee("Alice", "IT",  90_000),
    new Employee("Bob",   "IT",  75_000),
    new Employee("Carol", "HR",  80_000),
    new Employee("Dave",  "HR",  55_000),
    new Employee("Eve",   "IT",  95_000)
);

// Filter + map + collect
List<String> highEarnerNames = employees.stream()
    .filter(e -> e.salary() > 70_000)           // keep high earners
    .map(Employee::name)                         // extract name
    .sorted()                                    // alphabetical
    .collect(Collectors.toList());               // collect result

// Count
long itCount = employees.stream()
    .filter(e -> e.department().equals("IT"))
    .count();

// Average salary
OptionalDouble avgSalary = employees.stream()
    .mapToDouble(Employee::salary)
    .average();

// Group by department
Map<String, List<Employee>> byDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::department));

// Average salary per department
Map<String, Double> avgByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::department,
        Collectors.averagingDouble(Employee::salary)
    ));

// Find first IT employee
Optional<Employee> firstIT = employees.stream()
    .filter(e -> e.department().equals("IT"))
    .findFirst();
```

---

### 9.4 `map()` vs `flatMap()`

> **Analogy:** `map()` transforms each item (1-to-1). `flatMap()` opens nested boxes and flattens everything into one stream (1-to-many, then flatten).

```java
// map() — transforms each element
List<Integer> lengths = List.of("Java", "Python", "Go").stream()
    .map(String::length)
    .collect(Collectors.toList()); // [4, 6, 2]

// flatMap() — flattens nested collections
List<List<Integer>> nested = List.of(
    List.of(1, 2, 3),
    List.of(4, 5),
    List.of(6, 7, 8, 9)
);
List<Integer> flat = nested.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList()); // [1, 2, 3, 4, 5, 6, 7, 8, 9]

// Real use-case: get all words from multiple sentences
List<String> sentences = List.of("Hello World", "Java Streams");
List<String> words = sentences.stream()
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .collect(Collectors.toList()); // [Hello, World, Java, Streams]
```

---

### 9.5 `reduce()` — Aggregating to a Single Value

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

// Sum with reduce
int sum = numbers.stream()
    .reduce(0, Integer::sum); // identity=0, accumulator=sum function
// 0 + 1 + 2 + 3 + 4 + 5 = 15

// Product
int product = numbers.stream()
    .reduce(1, (a, b) -> a * b); // 120

// Find max manually
Optional<Integer> max = numbers.stream()
    .reduce(Integer::max); // Optional because stream might be empty
```

---

### 9.6 Parallel Streams

> **Analogy:** A serial stream is one person processing a huge stack of papers. A parallel stream splits the stack among a team and merges the results.

```java
// Parallel stream — uses ForkJoinPool under the hood
long count = employees.parallelStream()
    .filter(e -> e.salary() > 70_000)
    .count();

// When to use parallel:
// ✅ Large datasets (thousands+ elements)
// ✅ CPU-intensive, stateless operations
// ❌ Small collections (overhead outweighs benefit)
// ❌ Order-sensitive operations
// ❌ Shared mutable state (race conditions!)
```

---

### 9.7 Collectors Cheat Sheet

```java
// toList, toSet, toMap
Collectors.toList()
Collectors.toUnmodifiableList()
Collectors.toSet()
Collectors.toMap(Employee::name, Employee::salary)

// joining strings
String names = employees.stream()
    .map(Employee::name)
    .collect(Collectors.joining(", ", "[", "]")); // "[Alice, Bob, Carol]"

// counting
Collectors.counting()

// groupingBy
Collectors.groupingBy(Employee::department)                   // Map<String, List<Employee>>
Collectors.groupingBy(Employee::department, Collectors.counting()) // Map<String, Long>

// partitioningBy
Map<Boolean, List<Employee>> partitioned = employees.stream()
    .collect(Collectors.partitioningBy(e -> e.salary() > 70_000));
// true  → high earners
// false → others
```

---

## 10. Optionals

### 10.1 The Problem Optional Solves

> **Analogy:** Before Optional, getting a value from a method was like opening a box that might or might not have something in it — and exploding (NPE) if empty. Optional is a box with a label: "might be empty — please check first."

```java
// Old way — NPE-prone
String name = getUserById(42).getName(); // crashes if user not found!

// Optional way — explicit emptiness
Optional<User> userOpt = getUserById(42);

// Safe access patterns
String name = userOpt
    .map(User::getName)
    .orElse("Unknown");           // default if empty

String upper = userOpt
    .map(User::getName)
    .map(String::toUpperCase)
    .orElseThrow(() -> new UserNotFoundException(42)); // throw if empty

// Check and act
userOpt.ifPresent(user -> sendWelcomeEmail(user)); // only runs if present

// filter
Optional<User> activeUser = userOpt.filter(u -> u.isActive());
```

---

### 10.2 Creating Optionals

```java
Optional<String> empty   = Optional.empty();
Optional<String> present = Optional.of("Java");      // throws NPE if null
Optional<String> maybe   = Optional.ofNullable(null); // safe — returns empty Optional
```

---


<details>
<summary><b>Check Yourself — Concurrency</b></summary>

- What's the difference between `synchronized`, `volatile`, and `Atomic` classes?
- Write a producer-consumer solution using `BlockingQueue`.
- You have a counter incremented by 100 threads. It sometimes returns wrong values. Why? Fix it three different ways.
- What is a happens-before relationship? Why does it matter?
</details>

**Deep Dive — Read More:**
- [Java Concurrency in Practice (Brian Goetz)](https://jcip.net/) — the definitive book
- [Java Concurrency Tutorial (Jenkov)](https://jenkov.com/tutorials/java-concurrency/index.html) — comprehensive free guide
- [Virtual Threads (JEP 444)](https://openjdk.org/jeps/444) — the future of Java threading

---

## 11. Multithreading & Concurrency


```
   Thread Lifecycle State Diagram

                    ┌──────────┐
         new Thread()│          │
         ──────────►│   NEW    │
                    │          │
                    └────┬─────┘
                         │ .start()
                         ▼
                    ┌──────────┐   synchronized    ┌───────────┐
                    │          │──── (lock busy) ──►│  BLOCKED  │
                    │ RUNNABLE │                    │(waiting   │
                    │          │◄── (lock acquired)─│ for lock) │
                    │(READY or │                    └───────────┘
                    │ RUNNING) │   .wait() /
                    │          │── sleep() / ──────►┌───────────┐
                    │          │   .join()          │  WAITING  │
                    │          │◄── notify() ───────│  /TIMED   │
                    └────┬─────┘   /timeout         │  WAITING  │
                         │                          └───────────┘
                         │ run() completes
                         ▼
                    ┌──────────┐
                    │TERMINATED│
                    │          │
                    └──────────┘
```

> **FAANG Interview Tip:** Multithreading is the #1 topic where candidates fail at Amazon and Google. Don't just know the theory — be ready to identify race conditions in code snippets, explain why `volatile` alone doesn't make a counter thread-safe, and write a deadlock-free solution on the spot.

> **Real Interview Scenario (Netflix):** "Two threads are transferring money between accounts simultaneously: Thread 1 transfers A→B, Thread 2 transfers B→A. This deadlocks. Explain why and fix it." — Answer: Both threads acquire locks in different orders. Fix: always acquire locks in a consistent global order (e.g., by account ID).


### 11.1 Creating Threads

```java
// Way 1: Extend Thread
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread: " + Thread.currentThread().getName());
    }
}
new MyThread().start();

// Way 2: Implement Runnable (preferred — doesn't burn inheritance slot)
Runnable task = () -> System.out.println("Runnable running!");
Thread t = new Thread(task);
t.start();

// Way 3: ExecutorService (production standard)
ExecutorService executor = Executors.newFixedThreadPool(4);
executor.submit(() -> System.out.println("Task in pool"));
executor.shutdown();
```

---

### 11.2 Thread Lifecycle

```
NEW → RUNNABLE → (RUNNING) → BLOCKED/WAITING/TIMED_WAITING → TERMINATED

- NEW: Thread created, start() not yet called
- RUNNABLE: start() called; may or may not be executing (scheduler decides)
- BLOCKED: waiting to acquire a monitor lock (e.g., entering synchronized block)
- WAITING: waiting indefinitely (Object.wait(), Thread.join())
- TIMED_WAITING: waiting with timeout (Thread.sleep(), wait(timeout))
- TERMINATED: run() completed or exception thrown
```

---

### 11.3 Synchronization & The Race Condition Problem

> **Analogy:** Two cashiers (threads) share one register (shared variable). Without a lock, both might read `balance=100`, both add $50, and both write `balance=150` — losing one addition. A `synchronized` block is like a "one cashier at a time" sign on the register.

```java
class Counter {
    private int count = 0;

    // synchronized method — only one thread at a time can enter
    public synchronized void increment() {
        count++; // read-modify-write — needs atomicity
    }

    // synchronized block — more granular locking
    public void decrement() {
        synchronized (this) {
            count--;
        }
    }

    public int getCount() { return count; }
}
```

---

### 11.4 `volatile` Keyword

`volatile` guarantees **visibility** — changes by one thread are immediately visible to other threads. It does NOT guarantee atomicity for compound operations like `count++`.

```java
class StatusChecker {
    private volatile boolean running = true; // visible across threads

    public void stop()  { running = false; }
    public void run()   {
        while (running) { /* do work */ } // will see update from another thread
    }
}
```

Use `volatile` for **simple flags**. Use `synchronized` or `AtomicInteger` for **compound operations**.

---

### 11.5 Atomic Classes

```java
import java.util.concurrent.atomic.*;

AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet();  // atomic read-modify-write
counter.compareAndSet(5, 10); // CAS — update to 10 only if current value is 5

AtomicReference<String> ref = new AtomicReference<>("initial");
ref.compareAndSet("initial", "updated"); // safe reference update
```

---

### 11.6 ExecutorService & Thread Pools

> **Analogy:** Creating raw `Thread` objects is like hiring a new full-time employee for every tiny task. An `ExecutorService` is like a temp agency — you submit tasks, they manage a pool of workers.

```java
// Fixed pool — always N threads
ExecutorService fixedPool = Executors.newFixedThreadPool(4);

// Cached pool — grows on demand, reclaims idle threads
ExecutorService cachedPool = Executors.newCachedThreadPool();

// Single thread — serial execution guarantee
ExecutorService single = Executors.newSingleThreadExecutor();

// Scheduled — run at intervals
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);
scheduler.scheduleAtFixedRate(() -> System.out.println("tick"), 0, 1, TimeUnit.SECONDS);

// Submit a Callable — returns a Future
Future<Integer> future = fixedPool.submit(() -> {
    Thread.sleep(1000);
    return 42;
});
Integer result = future.get(); // blocks until complete

// Always shut down!
fixedPool.shutdown();
fixedPool.awaitTermination(5, TimeUnit.SECONDS);
```

---

### 11.7 Deadlock

> **Analogy:** Thread A holds Fork 1 and waits for Fork 2. Thread B holds Fork 2 and waits for Fork 1. Neither can proceed — classic dining philosophers deadlock.

```java
// Deadlock example
Object lock1 = new Object();
Object lock2 = new Object();

Thread t1 = new Thread(() -> {
    synchronized (lock1) {
        synchronized (lock2) { /* work */ } // waits for lock2
    }
});
Thread t2 = new Thread(() -> {
    synchronized (lock2) {
        synchronized (lock1) { /* work */ } // waits for lock1
    }
});

// Prevention: always acquire locks in the SAME order
// Or use tryLock() with timeout (ReentrantLock)
```

**Deadlock conditions (all four must be true):**
1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait

**Prevention:** Consistent lock ordering, `tryLock` with timeout, or lock-free data structures.

---

### 11.8 `wait()`, `notify()`, `notifyAll()`

These enable inter-thread communication using the monitor lock of an object.

```java
class MessageQueue {
    private String message;
    private boolean hasMessage = false;

    public synchronized void produce(String msg) throws InterruptedException {
        while (hasMessage) wait(); // wait until consumed
        this.message = msg;
        hasMessage = true;
        notify(); // wake up the consumer
    }

    public synchronized String consume() throws InterruptedException {
        while (!hasMessage) wait(); // wait until produced
        hasMessage = false;
        notify(); // wake up the producer
        return message;
    }
}
```

---

### 11.9 CountDownLatch vs CyclicBarrier

| | CountDownLatch | CyclicBarrier |
|---|---|---|
| One-time use | ✅ | ❌ (reusable) |
| Who counts down | Any thread | Each thread arriving at barrier |
| Use case | Wait for N tasks to finish | All threads wait for each other at a checkpoint |

```java
// CountDownLatch — wait for 3 services to initialize
CountDownLatch latch = new CountDownLatch(3);

Runnable service = () -> { initialize(); latch.countDown(); };
new Thread(service).start(); // service 1
new Thread(service).start(); // service 2
new Thread(service).start(); // service 3

latch.await(); // main thread waits here
System.out.println("All services ready!");

// CyclicBarrier — all threads meet at checkpoint before proceeding
CyclicBarrier barrier = new CyclicBarrier(3, () -> System.out.println("All arrived!"));
Runnable worker = () -> {
    doPhase1();
    barrier.await(); // wait for all 3 threads
    doPhase2();      // only starts when all finished phase 1
};
```

---

### 11.10 ThreadLocal

> **Analogy:** A ThreadLocal variable is like each employee having their own personal notepad. They don't share it — complete isolation per thread.

```java
ThreadLocal<SimpleDateFormat> dateFormat = ThreadLocal.withInitial(
    () -> new SimpleDateFormat("yyyy-MM-dd")
);

// In a web server: each request thread gets its own SimpleDateFormat
String formatted = dateFormat.get().format(new Date()); // thread-safe!
dateFormat.remove(); // important: clean up to prevent memory leaks in thread pools
```

---


<details>
<summary><b>Check Yourself — CompletableFuture</b></summary>

- What's the difference between `thenApply()` and `thenCompose()`?
- How do you handle errors in a CompletableFuture chain?
- When would you use `allOf()` vs `anyOf()`?
</details>

**Deep Dive — Read More:**
- [CompletableFuture Guide (Baeldung)](https://www.baeldung.com/java-completablefuture)
- [Async Programming (Java Brains)](https://www.youtube.com/watch?v=ImtZgX1nmr8)

---

## 12. Async Programming – CompletableFuture


```
   CompletableFuture Chaining Flow

   supplyAsync()──►thenApply()──►thenApply()──►thenAccept()
        │               │             │              │
   ┌────▼────┐    ┌─────▼─────┐ ┌────▼─────┐  ┌────▼────┐
   │Runs on  │    │Transform  │ │Transform │  │Consume  │
   │ForkJoin │───►│result     │►│again     │─►│final    │
   │pool     │    │(non-block)│ │          │  │result   │
   └─────────┘    └───────────┘ └──────────┘  └─────────┘

   Error Handling:
   supplyAsync()──►thenApply()──►exceptionally()──►thenAccept()
                        │              │
                   Exception?     Fallback value
                   
   Combining:
   future1 ─┐
             ├──►thenCombine()──► combined result
   future2 ─┘

   allOf(f1, f2, f3) = wait for ALL    (like CountDownLatch)
   anyOf(f1, f2, f3) = wait for FIRST  (like a race)
```


### 12.1 What is CompletableFuture?

> **Analogy:** `Future` is like ordering a pizza and then sitting by the door waiting (blocking). `CompletableFuture` is like getting a text notification when it's done — you can keep doing other things and react when the result arrives.

`CompletableFuture` (Java 8+) enables non-blocking, composable async programming.

---

### 12.2 Creating CompletableFutures

```java
// Run async task — no return value
CompletableFuture<Void> cf1 = CompletableFuture.runAsync(() -> {
    System.out.println("Running in: " + Thread.currentThread().getName());
});

// Supply async result
CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
    Thread.sleep(1000); // simulate DB call
    return "User: Alice";
});

// With custom executor
ExecutorService pool = Executors.newFixedThreadPool(4);
CompletableFuture<String> cf3 = CompletableFuture.supplyAsync(() -> fetchData(), pool);
```

---

### 12.3 Chaining & Composing

```java
CompletableFuture<String> result = CompletableFuture
    .supplyAsync(() -> fetchUserId())           // async: returns "42"
    .thenApply(id -> "User-" + id)             // transform: "User-42"
    .thenApply(String::toUpperCase)            // transform: "USER-42"
    .thenAccept(System.out::println);          // consume: print

// thenCompose — flatten nested CompletableFutures (like flatMap)
CompletableFuture<Order> order = CompletableFuture
    .supplyAsync(() -> getUserId())
    .thenCompose(userId -> getOrderForUser(userId)); // getOrderForUser returns CF<Order>

// thenCombine — combine two independent futures
CompletableFuture<Double> price    = supplyAsync(() -> getPrice("BTC"));
CompletableFuture<Double> quantity = supplyAsync(() -> getQuantity());
CompletableFuture<Double> total = price.thenCombine(quantity, (p, q) -> p * q);
```

---

### 12.4 Error Handling

```java
CompletableFuture<String> safe = CompletableFuture
    .supplyAsync(() -> {
        if (Math.random() > 0.5) throw new RuntimeException("Fetch failed!");
        return "data";
    })
    .exceptionally(ex -> {
        System.out.println("Error: " + ex.getMessage());
        return "fallback-data"; // default value on failure
    })
    .handle((result, ex) -> {          // handle runs regardless
        if (ex != null) return "error";
        return result.toUpperCase();
    });
```

---

### 12.5 Combining Multiple Futures

```java
// allOf — wait for ALL to complete
CompletableFuture<Void> allDone = CompletableFuture.allOf(cf1, cf2, cf3);
allDone.join(); // block until all complete
// Collect results after allOf:
List<String> results = Stream.of(cf1, cf2, cf3)
    .map(CompletableFuture::join)
    .collect(Collectors.toList());

// anyOf — first one to complete wins
CompletableFuture<Object> fastest = CompletableFuture.anyOf(cf1, cf2, cf3);
String winner = (String) fastest.get();
```

---


<details>
<summary><b>Check Yourself — Modern Java</b></summary>

- Rewrite a traditional POJO (constructor, getters, equals, hashCode, toString) as a Record. What can't Records do?
- What problem do sealed classes solve? How are they different from `final`?
- When would you use virtual threads vs platform threads? What are the caveats?
- Convert a 10-line if/else chain into a switch expression with pattern matching.
</details>

**Deep Dive — Read More:**
- [Records (JEP 395)](https://openjdk.org/jeps/395)
- [Sealed Classes (JEP 409)](https://openjdk.org/jeps/409)
- [Virtual Threads (JEP 444)](https://openjdk.org/jeps/444)
- [Pattern Matching (JEP 441)](https://openjdk.org/jeps/441)
- [Modern Java in Action (Manning)](https://www.manning.com/books/modern-java-in-action)

---

## 13. Modern Java (Java 9–21)

> **FAANG Interview Tip:** Modern Java features are increasingly tested. At Meta and Google, interviewers want to see you naturally reach for `records`, `sealed classes`, `pattern matching`, and `virtual threads` instead of old patterns. Show you write *modern* Java, not 2010 Java.

**Before / After: The Evolution of Java**

```
   Java 8 (2014)           Java 17 (2021)           Java 21 (2023)
   ─────────────           ──────────────           ──────────────
   Lambdas & Streams       Records                  Virtual Threads
   Optional                Sealed Classes           Sequenced Collections
   Default methods         Pattern Matching          Record Patterns
   java.time               Text Blocks              String Templates
   CompletableFuture       Switch Expressions       Scoped Values
```


### 13.1 var — Local Variable Type Inference (Java 10)

> **Analogy:** `var` is like saying "you figure out the type, compiler — it's obvious from context."

```java
var list    = new ArrayList<String>();  // ArrayList<String>
var map     = new HashMap<String, Integer>(); // HashMap<String, Integer>
var name    = "Alice";                  // String

// Good — obvious type
var employees = new ArrayList<Employee>();

// Bad — loses clarity
var x = getSomething(); // what type is this?

// Not allowed:
// var x; (no initializer)
// var x = null; (can't infer)
```

---

### 13.2 Records (Java 16)

> **Analogy:** Records are like pre-made LEGO sets — everything (constructor, getters, equals, hashCode, toString) is automatically included. You just name the pieces.

```java
// Traditional POJO — verbose
public class Point {
    private final int x;
    private final int y;
    public Point(int x, int y) { this.x = x; this.y = y; }
    public int x() { return x; }
    public int y() { return y; }
    // equals, hashCode, toString... 20+ more lines
}

// Record — all of the above in one line!
public record Point(int x, int y) {}

Point p = new Point(3, 4);
System.out.println(p.x());    // 3
System.out.println(p);        // Point[x=3, y=4]
System.out.println(p.equals(new Point(3, 4))); // true

// Compact constructor — add validation
public record Range(int min, int max) {
    public Range { // compact constructor — no () needed
        if (min > max) throw new IllegalArgumentException("min > max");
    }
}
```

---

### 13.3 Sealed Classes (Java 17)

> **Analogy:** Sealed classes are like a VIP guest list — only the classes you name are allowed to extend you.

```java
public sealed interface Shape permits Circle, Rectangle, Triangle {}

public record Circle(double radius)             implements Shape {}
public record Rectangle(double width, double height) implements Shape {}
public final class Triangle implements Shape {
    // must be final, sealed, or non-sealed
}

// Now the compiler knows EXACTLY which shapes exist
// → enables exhaustive switch (no default needed)
double area = switch (shape) {
    case Circle c       -> Math.PI * c.radius() * c.radius();
    case Rectangle r    -> r.width() * r.height();
    case Triangle t     -> computeTriangleArea(t);
    // No default needed — compiler verifies exhaustiveness
};
```

---

### 13.4 Pattern Matching for `instanceof` (Java 16)

```java
// Old way
if (obj instanceof String) {
    String s = (String) obj; // manual cast
    System.out.println(s.toUpperCase());
}

// Pattern matching — automatic binding
if (obj instanceof String s) {
    System.out.println(s.toUpperCase()); // s is already a String here!
}

// Combined with conditions (Java 21 guarded patterns)
if (obj instanceof String s && s.length() > 5) {
    System.out.println("Long string: " + s);
}
```

---

### 13.5 Switch Expressions (Java 14+)

```java
// Old switch — verbose, fall-through bugs
int day = 3;
String dayName;
switch (day) {
    case 1: dayName = "Monday"; break;
    case 2: dayName = "Tuesday"; break;
    default: dayName = "Other";
}

// New switch expression — concise, no fall-through, returns value
String dayName2 = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    default -> "Other";
};

// With blocks
String category = switch (score) {
    case 90, 91, 92, 93, 94, 95, 96, 97, 98, 99, 100 -> "A";
    case 80, 81, 82, 83, 84, 85, 86, 87, 88, 89       -> "B";
    default -> {
        System.out.println("Computing grade...");
        yield score >= 70 ? "C" : "F"; // yield returns value from block
    }
};
```

---

### 13.6 Text Blocks (Java 15)

```java
// Old way — messy escaping
String json = "{\n  \"name\": \"Alice\",\n  \"age\": 30\n}";

// Text block — clean multiline
String json2 = """
        {
          "name": "Alice",
          "age": 30
        }
        """;

String query = """
        SELECT name, salary
        FROM employees
        WHERE department = 'IT'
          AND salary > 80000
        ORDER BY salary DESC
        """;
```

---

### 13.7 Virtual Threads (Java 21 — Project Loom)

> **Analogy:** Traditional platform threads are like buses — expensive, limited in number, mostly idle while waiting. Virtual threads are like bikes — cheap, can have millions, managed by the JVM scheduler, perfect for waiting tasks.

```java
// Traditional platform thread — ~1MB memory per thread
Thread platformThread = new Thread(() -> handleRequest());

// Virtual thread — tiny memory footprint, JVM-managed
Thread virtualThread = Thread.ofVirtual().start(() -> handleRequest());

// Create via factory
Thread.ofVirtual().name("worker-", 0).start(task);

// With ExecutorService
try (ExecutorService vThreadPool = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        vThreadPool.submit(() -> {
            Thread.sleep(1000); // blocks virtual thread, not OS thread
            return processRequest();
        });
    } // handles 1 million concurrent tasks efficiently!
}
```

**Virtual threads excel at:** I/O-bound tasks (HTTP calls, DB queries, file reads)
**Not ideal for:** CPU-intensive work (use platform threads/ForkJoinPool)

---

### 13.8 Sequenced Collections (Java 21)

New interfaces that guarantee element order with consistent first/last access:

```java
SequencedCollection<String> list = new ArrayList<>(List.of("A", "B", "C"));
list.getFirst(); // "A"
list.getLast();  // "C"
list.addFirst("Z"); // insert at front
list.addLast("D");  // insert at back
list.reversed();    // reversed view

SequencedMap<String, Integer> map = new LinkedHashMap<>();
map.firstEntry(); // Map.Entry with first key
map.lastEntry();  // Map.Entry with last key
```

---

### 13.9 Java Module System (JPMS) – Java 9

> **Analogy:** Before modules, Java's JARs were like one big open-plan office — any class could see any other. Modules are like separate rooms with controlled doors.

```java
// module-info.java
module com.myapp.core {
    requires java.net.http;           // depends on this module
    requires com.myapp.utils;         // depends on internal module
    exports com.myapp.core.api;       // makes this package visible to others
    opens com.myapp.core.model;       // allows reflection (for frameworks)
}
```

---

### 13.10 New String Methods (Java 11–21)

```java
// Java 11
"  ".isBlank();                          // true — only whitespace
"Hello\nWorld\nJava".lines()            // Stream<String> of lines
    .forEach(System.out::println);
"  Hello  ".strip();                    // "Hello" — Unicode-aware trim
"  Hello  ".stripLeading();             // "Hello  "
"  Hello  ".stripTrailing();            // "  Hello"
"Java".repeat(3);                       // "JavaJavaJava"

// Java 12
"Hello World".indent(4);               // adds 4 spaces to each line
"42".transform(Integer::parseInt);     // 42 (apply function to string)

// Java 15
String html = """
    <html>
        <body>Hello</body>
    </html>
    """;                               // Text block

// Java 21
"Hello %s, you are %d years old".formatted("Alice", 30);
```

---


<details>
<summary><b>Check Yourself — JVM & GC</b></summary>

- Explain the difference between Minor GC, Major GC, and Full GC. Which causes stop-the-world pauses?
- What JVM flags would you use to diagnose a memory leak? Walk through your investigation process.
- When would you choose ZGC over G1GC?
</details>

**Deep Dive — Read More:**
- [G1 Garbage Collector (Oracle)](https://docs.oracle.com/en/java/javase/21/gctuning/)
- [ZGC (OpenJDK)](https://wiki.openjdk.org/display/zgc/Main)
- [JVM Tuning Guide (Baeldung)](https://www.baeldung.com/jvm-tuning-flags)
- [GC Logging & Analysis (GCEasy)](https://gceasy.io/)

---

## 14. JVM Internals & Garbage Collection


```
   JVM Memory Layout

   ┌─────────────────────────────────────────────────┐
   │                    HEAP                          │
   │  ┌───────────────────────┐  ┌────────────────┐  │
   │  │    Young Generation   │  │ Old Generation │  │
   │  │  ┌─────┐ ┌───┐ ┌───┐ │  │  (Tenured)     │  │
   │  │  │Eden │ │S0 │ │S1 │ │  │                │  │
   │  │  │     │ │   │ │   │ │  │  Long-lived    │  │
   │  │  │ New │ │Sur│ │Sur│ │  │  objects end    │  │
   │  │  │ obj │ │viv│ │viv│ │  │  up here       │  │
   │  │  └─────┘ └───┘ └───┘ │  │                │  │
   │  └───────────────────────┘  └────────────────┘  │
   │            Minor GC ▲              Major GC ▲   │
   └─────────────────────────────────────────────────┘
   ┌─────────────────────────────────────────────────┐
   │                  NON-HEAP                        │
   │  ┌─────────────┐  ┌──────────┐  ┌───────────┐  │
   │  │ Metaspace   │  │ Code     │  │ Thread    │  │
   │  │ (class meta)│  │ Cache    │  │ Stacks    │  │
   │  └─────────────┘  │ (JIT)    │  │ (per-     │  │
   │                    └──────────┘  │  thread)  │  │
   │                                  └───────────┘  │
   └─────────────────────────────────────────────────┘

   GC Algorithm Comparison:
   ┌─────────────┬────────────┬───────────┬──────────────┐
   │ Collector   │ Throughput │ Latency   │ Best For     │
   ├─────────────┼────────────┼───────────┼──────────────┤
   │ Serial      │ Low        │ High      │ Small apps   │
   │ Parallel    │ High       │ Medium    │ Batch jobs   │
   │ G1 (default)│ Good       │ Low       │ General use  │
   │ ZGC         │ Good       │ Ultra-low │ Large heaps  │
   │ Shenandoah  │ Good       │ Ultra-low │ Large heaps  │
   └─────────────┴────────────┴───────────┴──────────────┘
```


### 14.1 JVM Memory Areas

| Area | Contents | Shared? |
|---|---|---|
| Heap | All objects, arrays, instance vars | Yes (all threads) |
| Method Area / Metaspace | Class metadata, static vars, constants | Yes |
| Stack | Method frames, local vars, references | Per-thread |
| Program Counter (PC) | Address of current instruction | Per-thread |
| Native Method Stack | Native (C/C++) method calls | Per-thread |

---

### 14.2 Garbage Collection

> **Analogy:** GC is the JVM's janitor. It periodically walks through the heap, marks everything reachable from your code as "live", and sweeps away the rest.

**GC Roots:** Active threads, static variables, local variables on stack — anything that's reachable from these stays alive.

**Generational GC Assumption:** Most objects die young (short-lived temporaries). So the heap is split:

```
Heap
  ├── Young Generation
  │     ├── Eden        ← new objects born here
  │     ├── Survivor S0 ← survives minor GC
  │     └── Survivor S1 ← survives again
  └── Old Generation (Tenured) ← long-lived objects promoted here
```

**GC Types:**
- **Serial GC:** Single-threaded, for small apps
- **Parallel GC:** Multi-threaded GC, maximizes throughput
- **G1 GC (default Java 9+):** Region-based, balances throughput + pause time
- **ZGC (Java 15+):** Sub-millisecond pauses, scales to TB heaps
- **Shenandoah:** Concurrent, low-pause alternative

---

### 14.3 Strong, Soft, Weak, Phantom References

| Reference Type | GC'd when... | Use case |
|---|---|---|
| Strong | Never (while reachable) | Normal variables |
| Soft | Memory pressure | In-memory caches |
| Weak | Next GC cycle | Canonicalized maps, listeners |
| Phantom | After finalization | Pre-cleanup actions |

```java
WeakReference<CachedObject> weakRef = new WeakReference<>(new CachedObject());
CachedObject obj = weakRef.get(); // null if GC'd
if (obj == null) { /* cache miss — reload */ }
```

---


<details>
<summary><b>Check Yourself — Design Patterns</b></summary>

- Implement Singleton five ways. Which is thread-safe? Which is lazy? Which is the best?
- When would you use Strategy over Template Method?
- Explain the Builder pattern. Why is it better than telescoping constructors?
</details>

**Deep Dive — Read More:**
- [Design Patterns (Refactoring.Guru)](https://refactoring.guru/design-patterns/java) — visual guide
- [Head First Design Patterns](https://www.oreilly.com/library/view/head-first-design/9781492077992/)
- [Effective Java (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java/9780134686097/) — pattern-heavy

---

## 15. Design Patterns in Java

> **Real Interview Scenario (Meta):** "Implement a thread-safe Singleton that is lazy-loaded, serialization-safe, and reflection-proof." — Answer: Use an `enum`. It's the only approach that handles all three automatically. Bill Pugh's inner class holder handles the first two but not reflection.


### 15.1 Singleton

```java
// Thread-safe singleton with double-checked locking
public class DatabaseConnection {
    private static volatile DatabaseConnection instance;

    private DatabaseConnection() {}

    public static DatabaseConnection getInstance() {
        if (instance == null) {                      // first check — no lock
            synchronized (DatabaseConnection.class) {
                if (instance == null) {              // second check — with lock
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
}

// Better — enum singleton (thread-safe, serialization-safe)
public enum Config {
    INSTANCE;
    private final String apiKey = System.getenv("API_KEY");
    public String getApiKey() { return apiKey; }
}
```

---

### 15.2 Builder

```java
public class User {
    private final String name;
    private final String email;
    private final int age;

    private User(Builder builder) {
        this.name  = builder.name;
        this.email = builder.email;
        this.age   = builder.age;
    }

    public static class Builder {
        private String name;
        private String email;
        private int age;

        public Builder name(String name)   { this.name = name;   return this; }
        public Builder email(String email) { this.email = email; return this; }
        public Builder age(int age)        { this.age = age;     return this; }
        public User build()                { return new User(this); }
    }
}

User user = new User.Builder()
    .name("Alice")
    .email("alice@example.com")
    .age(30)
    .build();
```

---

### 15.3 Factory & Strategy

```java
// Factory — delegate object creation
interface Notification { void send(String message); }
class EmailNotification  implements Notification { ... }
class SMSNotification    implements Notification { ... }

class NotificationFactory {
    public static Notification create(String type) {
        return switch (type) {
            case "email" -> new EmailNotification();
            case "sms"   -> new SMSNotification();
            default      -> throw new IllegalArgumentException("Unknown: " + type);
        };
    }
}

// Strategy — swap algorithms at runtime
interface SortStrategy { void sort(int[] arr); }
class BubbleSort implements SortStrategy { ... }
class QuickSort  implements SortStrategy { ... }

class Sorter {
    private SortStrategy strategy;
    public Sorter(SortStrategy strategy) { this.strategy = strategy; }
    public void sort(int[] arr) { strategy.sort(arr); }
    public void setStrategy(SortStrategy s) { strategy = s; }
}
```

---

---

# 16. QUIZ – 100 Questions

*Test your understanding. Each question has one correct answer (A–D) unless noted.*

---

### Section A: OOP & Java Basics (Q1–Q20)

**Q1.** What is the output of:
```java
String a = "Java"; String b = "Java";
System.out.println(a == b);
```
- A) false
- B) true
- C) Compilation error
- D) NullPointerException

---

**Q2.** Which OOP principle allows a subclass to be referenced by a parent class variable?
- A) Encapsulation
- B) Abstraction
- C) Polymorphism
- D) Inheritance

---

**Q3.** What does the `final` keyword mean on a method?
- A) The method runs at the end of program execution
- B) The method cannot be overridden in subclasses
- C) The method must be called last
- D) The method is thread-safe

---

**Q4.** Java is "pass by value". What value is passed when you pass an object?
- A) The object itself (deep copy)
- B) A copy of the reference (memory address)
- C) A reference to the reference
- D) Null

---

**Q5.** Which of the following is NOT a valid way to create a String in Java?
- A) `String s = "Hello";`
- B) `String s = new String("Hello");`
- C) `String s = String.create("Hello");`
- D) `String s = "Hel" + "lo";`

---

**Q6.** What is the difference between an abstract class and an interface (pre-Java 8)?
- A) Interfaces can have constructors; abstract classes cannot
- B) Abstract classes can have instance fields and constructors; interfaces can only have abstract methods
- C) There is no difference
- D) Both can be instantiated directly

---

**Q7.** What is method hiding in Java?
- A) Making a method `private`
- B) Overriding a static method in a subclass
- C) Declaring a method without a body
- D) Using reflection to access private methods

---

**Q8.** A class declares: `public class Foo implements A, B`. Both `A` and `B` have a default method `greet()`. What happens?
- A) Foo inherits from A's version silently
- B) Compilation error — Foo must override `greet()`
- C) Runtime exception
- D) B's version always wins

---

**Q9.** What is the output?
```java
int x = 5;
System.out.println(x++ + " " + ++x);
```
- A) 5 7
- B) 6 7
- C) 5 6
- D) 6 6

---

**Q10.** Which access modifier allows access within the same class, same package, and subclasses (even in different packages)?
- A) public
- B) protected
- C) default (no modifier)
- D) private

---

**Q11.** What is autoboxing in Java?
- A) Automatic conversion of an object to a primitive
- B) Automatic conversion of a primitive to its wrapper class
- C) Casting a superclass reference to a subclass
- D) Converting String to Integer

---

**Q12.** Which statement about `StringBuilder` is FALSE?
- A) It is mutable
- B) It is thread-safe
- C) It is faster than `StringBuffer` in single-threaded code
- D) It has `append()` and `reverse()` methods

---

**Q13.** What is a covariant return type?
- A) A return type that is the same as the parent method
- B) A return type that is a subtype of the parent method's return type
- C) Returning `void` from a method
- D) Returning an array from a method

---

**Q14.** Which keyword prevents a class from being subclassed?
- A) `abstract`
- B) `static`
- C) `final`
- D) `sealed`

---

**Q15.** What is the String Pool in Java?
- A) A thread-safe pool of threads
- B) A cache in heap memory where string literals are stored and reused
- C) A collection class for storing strings
- D) A method in the String API

---

**Q16.** What happens when `try` and `finally` both have `return` statements?
- A) Compilation error
- B) The `try` block's return value is used
- C) The `finally` block's return value overrides the `try` block's
- D) An exception is thrown

---

**Q17.** What is the output?
```java
class Parent { static String name = "Parent"; }
class Child extends Parent { static String name = "Child"; }
Parent p = new Child();
System.out.println(p.name);
```
- A) Child
- B) Parent
- C) Compilation error
- D) null

---

**Q18.** What is an anonymous class?
- A) A class with no methods
- B) A class defined and instantiated in a single expression, often implementing an interface
- C) A class without a name declared with `var`
- D) A private inner class

---

**Q19.** What does `super()` do when called in a constructor?
- A) Calls the current class's own constructor
- B) Calls the parent class's constructor
- C) Calls the grandparent class's constructor
- D) Calls the static initializer of the parent

---

**Q20.** Which of the following is a marker interface?
- A) `Runnable`
- B) `Comparable`
- C) `Serializable`
- D) `Iterable`

---

### Section B: Collections & Generics (Q21–Q35)

**Q21.** What is the time complexity of `get(index)` on an `ArrayList`?
- A) O(n)
- B) O(log n)
- C) O(1)
- D) O(n²)

---

**Q22.** Which `Map` implementation maintains insertion order?
- A) HashMap
- B) TreeMap
- C) LinkedHashMap
- D) ConcurrentHashMap

---

**Q23.** What exception does a fail-fast iterator throw when the collection is modified during iteration?
- A) `IllegalStateException`
- B) `ConcurrentModificationException`
- C) `UnsupportedOperationException`
- D) `IndexOutOfBoundsException`

---

**Q24.** What is the PECS rule in generics?
- A) Producer Extends, Consumer Super
- B) Parameter Extends, Class Super
- C) Polymorphic Extends, Covariant Super
- D) Primitive Extends, Collection Super

---

**Q25.** What does `TreeSet` guarantee?
- A) No duplicates and insertion order
- B) No duplicates and sorted natural order
- C) No duplicates and constant-time access
- D) Allows `null` values

---

**Q26.** What is the difference between `HashMap` and `ConcurrentHashMap`?
- A) `ConcurrentHashMap` allows null keys; `HashMap` does not
- B) `HashMap` is thread-safe; `ConcurrentHashMap` is not
- C) `ConcurrentHashMap` uses segment-level locking; `HashMap` is not thread-safe
- D) They are identical

---

**Q27.** What does `Collections.unmodifiableList()` return?
- A) A new defensive copy of the list
- B) A view that throws `UnsupportedOperationException` on mutation attempts
- C) A thread-safe list
- D) An immutable list backed by an array

---

**Q28.** Which interface does `HashMap` implement?
- A) `List`
- B) `Collection`
- C) `Map`
- D) `Set`

---

**Q29.** What is the default initial capacity of a `HashMap`?
- A) 8
- B) 10
- C) 16
- D) 32

---

**Q30.** What happens when two keys have the same `hashCode()` in a `HashMap`?
- A) The second key overwrites the first
- B) A collision occurs; both entries are stored in the same bucket using a linked list or tree
- C) An exception is thrown
- D) Only the first entry is kept

---

**Q31.** Which collection allows `null` elements and preserves insertion order?
- A) `TreeSet`
- B) `HashSet`
- C) `LinkedHashSet`
- D) Both B and C

---

**Q32.** What does the `Comparator.thenComparing()` method do?
- A) Replaces the primary comparator
- B) Acts as a tie-breaker when the primary comparator returns 0
- C) Reverses the comparison
- D) Converts a Comparator to a Comparable

---

**Q33.** What is the difference between `List.of()` and `Arrays.asList()`?
- A) `List.of()` allows null; `Arrays.asList()` does not
- B) `Arrays.asList()` is fully immutable; `List.of()` allows setting elements
- C) `List.of()` is fully immutable; `Arrays.asList()` allows `set()` but not `add()`/`remove()`
- D) They are identical

---

**Q34.** Which data structure does `PriorityQueue` use internally?
- A) LinkedList
- B) Binary Heap (min-heap by default)
- C) TreeMap
- D) Array with sorted insertion

---

**Q35.** What is a `WeakHashMap`?
- A) A HashMap that throws `NullPointerException` on null keys
- B) A HashMap whose entries are removed when keys are garbage collected
- C) A thread-safe version of HashMap
- D) A HashMap with soft references for values

---

### Section C: Functional Interfaces, Lambdas & Streams (Q36–Q60)

**Q36.** What is the single abstract method of the `Predicate<T>` functional interface?
- A) `apply(T t)`
- B) `accept(T t)`
- C) `test(T t)`
- D) `get()`

---

**Q37.** What is the difference between `thenApply()` and `thenCompose()` in `CompletableFuture`?
- A) They are identical
- B) `thenApply` transforms the result synchronously; `thenCompose` flattens nested `CompletableFuture`s
- C) `thenCompose` is for errors only
- D) `thenApply` is asynchronous; `thenCompose` is synchronous

---

**Q38.** What does `flatMap()` do in a Stream?
- A) Filters elements and maps the result
- B) Maps each element to a stream and flattens all streams into one
- C) Reduces the stream to a single value
- D) Sorts the stream then maps it

---

**Q39.** Which of the following is NOT a terminal operation?
- A) `collect()`
- B) `forEach()`
- C) `filter()`
- D) `count()`

---

**Q40.** What is the output?
```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);
int result = nums.stream().reduce(0, (a, b) -> a + b);
System.out.println(result);
```
- A) 0
- B) 5
- C) 15
- D) 120

---

**Q41.** What is a method reference equivalent to `s -> s.toUpperCase()`?
- A) `String::toUpperCase`
- B) `String.toUpperCase()`
- C) `String::new`
- D) `s::toUpperCase()`

---

**Q42.** What is the `@FunctionalInterface` annotation used for?
- A) Marks an interface as thread-safe
- B) Enforces at compile-time that the interface has exactly one abstract method
- C) Makes all methods of the interface default
- D) Prevents the interface from being extended

---

**Q43.** Which built-in functional interface takes two arguments of the same type and returns the same type?
- A) `BiFunction<T,U,R>`
- B) `BinaryOperator<T>`
- C) `UnaryOperator<T>`
- D) `Consumer<T>`

---

**Q44.** What does `Collectors.groupingBy()` return?
- A) `List<T>`
- B) `Map<K, List<T>>`
- C) `Set<T>`
- D) `Optional<T>`

---

**Q45.** Can a lambda expression modify a local variable from the enclosing scope?
- A) Yes, always
- B) No — captured variables must be effectively final
- C) Yes, only if declared `volatile`
- D) Yes, only inside `synchronized` blocks

---

**Q46.** What is a lazy stream operation?
- A) An operation that always runs on a background thread
- B) An operation that only executes when a terminal operation is called
- C) An operation that skips null elements
- D) An operation that caches its result

---

**Q47.** What does `Stream.distinct()` use to identify duplicates?
- A) `==` operator
- B) Object identity (reference)
- C) `equals()` and `hashCode()`
- D) The natural ordering of the type

---

**Q48.** What is the difference between `findFirst()` and `findAny()` in streams?
- A) `findFirst()` always returns an element; `findAny()` may return empty
- B) `findFirst()` returns the first in encounter order; `findAny()` can return any element (better for parallel streams)
- C) They are identical
- D) `findAny()` finds the minimum element

---

**Q49.** What does `Predicate.negate()` do?
- A) Returns a new Predicate that always returns `false`
- B) Returns a Predicate that inverts the result of the original
- C) Removes the Predicate from a stream
- D) Chains two predicates with OR logic

---

**Q50.** What is the return type of `Stream.collect(Collectors.toMap(...))`?
- A) `List<T>`
- B) `Set<K>`
- C) `Map<K, V>`
- D) `Optional<Map<K, V>>`

---

**Q51.** What happens if you call a terminal operation on an already-consumed stream?
- A) The stream is reset and replayed
- B) `IllegalStateException` is thrown
- C) An empty result is returned
- D) The operation is silently ignored

---

**Q52.** What is `IntStream.range(1, 5)` equivalent to?
- A) [1, 2, 3, 4, 5]
- B) [1, 2, 3, 4]
- C) [0, 1, 2, 3, 4]
- D) [2, 3, 4, 5]

---

**Q53.** Which functional interface's abstract method signature is `void accept(T t, U u)`?
- A) `Consumer<T>`
- B) `BiConsumer<T, U>`
- C) `BiFunction<T, U, R>`
- D) `Function<T, U>`

---

**Q54.** What does `Function.identity()` return?
- A) `null`
- B) A Function that always returns its input unchanged
- C) A Function that converts to String
- D) A Predicate

---

**Q55.** What is the purpose of `peek()` in a stream?
- A) Returns the first element
- B) Performs an action on each element without consuming the stream (useful for debugging)
- C) Checks if a condition is true for any element
- D) Converts the stream to a list

---

**Q56.** What does `Collectors.partitioningBy()` produce?
- A) `Map<String, List<T>>`
- B) `Map<Boolean, List<T>>`
- C) `List<List<T>>`
- D) `Set<T>`

---

**Q57.** What is a `Supplier<T>` functional interface?
- A) Takes one argument, returns nothing
- B) Takes no argument, returns `T`
- C) Takes `T`, returns `boolean`
- D) Takes two arguments, returns `T`

---

**Q58.** When would you prefer `parallelStream()` over `stream()`?
- A) Always — it's always faster
- B) For large datasets with stateless, order-independent, CPU-intensive operations
- C) For small lists (< 100 elements)
- D) When you need guaranteed order of output

---

**Q59.** What does `Optional.orElseGet()` do differently from `Optional.orElse()`?
- A) They are identical
- B) `orElseGet()` takes a `Supplier` — the fallback is only computed if the Optional is empty
- C) `orElse()` throws an exception; `orElseGet()` returns null
- D) `orElseGet()` only works with primitive types

---

**Q60.** What is the result of:
```java
Stream.of(1, 2, 3).map(x -> x * 2).filter(x -> x > 3).count();
```
- A) 1
- B) 2
- C) 3
- D) 0

---

### Section D: Multithreading & Concurrency (Q61–Q80)

**Q61.** What is a race condition?
- A) A competition between two JVMs
- B) A bug where the outcome depends on the timing of thread execution of shared data
- C) A deadlock between two threads
- D) An exception thrown when two threads finish simultaneously

---

**Q62.** What does `volatile` guarantee?
- A) Atomicity of compound operations
- B) That only one thread can access the variable at a time
- C) Visibility — changes by one thread are immediately visible to all other threads
- D) Both atomicity and visibility

---

**Q63.** What is the difference between `Runnable` and `Callable`?
- A) `Callable` extends `Runnable`
- B) `Runnable` returns a result; `Callable` does not
- C) `Callable` can return a value and throw checked exceptions; `Runnable` cannot
- D) There is no difference in Java 8+

---

**Q64.** Which synchronization aid allows one or more threads to wait until a set of operations complete?
- A) `CyclicBarrier`
- B) `Semaphore`
- C) `CountDownLatch`
- D) `ReentrantLock`

---

**Q65.** What is a daemon thread?
- A) A high-priority thread that runs UI
- B) A background thread that does not prevent JVM shutdown when all user threads finish
- C) A thread that holds all locks in the system
- D) A thread managed by the OS, not the JVM

---

**Q66.** What is the purpose of `ThreadLocal`?
- A) To create a thread-safe collection
- B) To provide each thread with its own independent copy of a variable
- C) To synchronize access to a shared variable
- D) To limit the number of active threads

---

**Q67.** What is a deadlock?
- A) A thread waiting for an I/O operation
- B) A situation where two or more threads wait for each other's locks indefinitely
- C) A thread running out of stack memory
- D) A race condition in a synchronized block

---

**Q68.** What does `ExecutorService.shutdown()` do?
- A) Immediately stops all running tasks
- B) Stops accepting new tasks; allows already-submitted tasks to complete
- C) Destroys all threads in the pool immediately
- D) Pauses all threads until `resume()` is called

---

**Q69.** What is `AtomicInteger.compareAndSet(expected, update)` used for?
- A) Checks if the integer is in range
- B) Updates the value to `update` only if the current value equals `expected` (CAS operation)
- C) Compares two AtomicIntegers
- D) Sets a value and returns the old value

---

**Q70.** How is `ConcurrentHashMap` thread-safe without locking the entire map?
- A) It uses `synchronized` on every `get()` and `put()`
- B) It partitions the map into segments (Java 7) or uses CAS + node-level locking (Java 8+)
- C) It creates a copy of the map for each thread
- D) It uses `volatile` on every entry

---

**Q71.** What is `Thread.join()` used for?
- A) Makes the current thread wait until the specified thread terminates
- B) Merges two threads into one
- C) Starts a thread
- D) Pauses a thread for a specified time

---

**Q72.** What is the `ForkJoinPool` designed for?
- A) Managing a fixed number of threads for I/O tasks
- B) Executing tasks that can be recursively split into smaller subtasks (divide-and-conquer)
- C) Scheduling periodic tasks
- D) Single-threaded task execution

---

**Q73.** Which lock allows the same thread to acquire it multiple times without deadlocking?
- A) `synchronized`
- B) `ReentrantLock`
- C) `ReadWriteLock`
- D) Both A and B

---

**Q74.** What is a livelock?
- A) Threads are running but making no progress because they keep reacting to each other
- B) A thread is blocked waiting for a lock forever
- C) A thread that is running too fast
- D) A thread stuck in an infinite loop with no other threads involved

---

**Q75.** What does `semaphore.acquire()` do when the permit count is 0?
- A) Returns immediately with false
- B) Throws `InterruptedException`
- C) Blocks until a permit becomes available
- D) Terminates the current thread

---

**Q76.** Which thread state means the thread is waiting indefinitely for another thread's action?
- A) BLOCKED
- B) WAITING
- C) TIMED_WAITING
- D) RUNNABLE

---

**Q77.** What is the memory visibility problem in multithreading?
- A) Running out of heap memory
- B) One thread's writes to shared variables not being immediately visible to other threads due to CPU caches
- C) Stack overflow from deep recursion
- D) A thread reading from a null reference

---

**Q78.** What is `CopyOnWriteArrayList`?
- A) A list that copies itself on every `get()`
- B) A thread-safe list where write operations create a fresh copy of the underlying array
- C) A list that uses a copy of elements for comparison
- D) A list that copies elements from another list on creation

---

**Q79.** What is the `happens-before` relationship in the Java Memory Model?
- A) A guarantee about program execution order at runtime
- B) A guarantee that if action A happens-before B, then A's writes are visible to B
- C) A scheduling priority for threads
- D) A constraint on method call order within a class

---

**Q80.** What is `notifyAll()` vs `notify()` in `Object`?
- A) `notify()` wakes all waiting threads; `notifyAll()` wakes a random one
- B) `notifyAll()` wakes all threads waiting on the object's monitor; `notify()` wakes one (unpredictable which)
- C) They are identical
- D) `notifyAll()` is deprecated

---

### Section E: Modern Java (Q81–Q100)

**Q81.** What Java version introduced Records as a standard (non-preview) feature?
- A) Java 14
- B) Java 15
- C) Java 16
- D) Java 17

---

**Q82.** What does a `sealed` class control?
- A) Thread access to its methods
- B) Which classes are permitted to extend or implement it
- C) Whether the class can have static members
- D) The visibility of its constructors

---

**Q83.** What is Virtual Threads (Project Loom) — Java 21?
- A) Threads rendered in a VR interface
- B) JVM-managed lightweight threads with minimal memory footprint, ideal for I/O-bound concurrency
- C) Daemon threads with lower priority
- D) OS-level kernel threads

---

**Q84.** What does `var` do in Java 10+?
- A) Declares a variable without a type (dynamically typed)
- B) Allows local variable type inference — the compiler infers the type from the initializer
- C) Creates a final variable
- D) Declares a primitive variable

---

**Q85.** What is a Text Block in Java?
- A) A `char[]` type
- B) A multiline string literal enclosed with `"""` that preserves formatting
- C) A string that is stored in the String Pool
- D) A string that cannot contain escape sequences

---

**Q86.** What is `switch` expression's `yield` keyword used for?
- A) Returns a value from a `switch` block (multi-line case body)
- B) Pauses execution until the next case
- C) Breaks out of the switch entirely
- D) Throws an exception from a switch

---

**Q87.** Which of the following is true about Java Records?
- A) They can extend other classes
- B) They are mutable by default
- C) They are implicitly `final` and extend `java.lang.Record`
- D) They cannot implement interfaces

---

**Q88.** What is pattern matching for `instanceof`?
- A) Checking if a value matches a regex pattern
- B) Automatically binding a variable of the matched type in an `instanceof` check without an explicit cast
- C) A loop that matches elements by type
- D) A switch statement that uses regex

---

**Q89.** In Java 21, what is `Sequenced Collections` interface?
- A) A thread-safe collection
- B) An interface that provides `getFirst()`, `getLast()`, `addFirst()`, `addLast()`, and `reversed()` operations
- C) A sorted collection similar to `TreeSet`
- D) A collection that sequences parallel operations

---

**Q90.** What does the `module-info.java` file define?
- A) The entry point of a Java application
- B) The Java module — its name, dependencies (`requires`), and exported packages (`exports`)
- C) Global constants for the application
- D) The classpath configuration

---

**Q91.** What is the purpose of `CompletableFuture.allOf()`?
- A) Returns the result of the fastest completing future
- B) Returns a `CompletableFuture<Void>` that completes when all given futures complete
- C) Runs all futures sequentially
- D) Combines all future results into a list automatically

---

**Q92.** In Java 17, what is a `non-sealed` class in the context of sealed hierarchies?
- A) A class that cannot be extended at all
- B) A subclass of a sealed class that opens the hierarchy — it can be extended by any class
- C) An abstract class
- D) A class without access modifiers

---

**Q93.** What is `String.formatted()` in Java 15+?
- A) A method to parse formatted strings
- B) An instance method equivalent to `String.format(this, args)`
- C) A way to create text blocks
- D) A method to center-align a string

---

**Q94.** What is `Record Pattern` (Java 21)?
- A) A design pattern for creating records
- B) Deconstruction of a record's components directly in pattern matching expressions
- C) A validation annotation for records
- D) A way to serialize records

---

**Q95.** What does `String.isBlank()` (Java 11) return for `"\t\n "`?
- A) `false` — it contains characters
- B) `true` — it contains only whitespace
- C) Throws `NullPointerException`
- D) `false` — tabs are not whitespace to `isBlank()`

---

**Q96.** How do Virtual Threads differ from Platform Threads regarding blocking I/O?
- A) Virtual threads throw an exception on blocking I/O
- B) When a Virtual Thread blocks on I/O, the JVM suspends it and unmounts it from the carrier OS thread, freeing that thread for other work
- C) Virtual threads skip I/O operations
- D) Platform threads are faster for I/O-bound tasks

---

**Q97.** What is the G1 Garbage Collector's main advantage over Parallel GC?
- A) G1 always has zero pause times
- B) G1 is region-based, allowing it to predict and limit GC pause times better than Parallel GC
- C) G1 uses less memory
- D) G1 only runs on Java 21+

---

**Q98.** What is `Structured Concurrency` (Java 21 preview)?
- A) Using `synchronized` blocks in a structured way
- B) A new API where tasks spawned in a scope are treated as a unit — if one fails or is cancelled, all related tasks are cancelled together
- C) A way to structure thread priorities
- D) A replacement for `ExecutorService`

---

**Q99.** What is `ZGC` (Z Garbage Collector)?
- A) A GC algorithm for Java 8 only
- B) A scalable, concurrent GC with sub-millisecond pause times, designed for very large heaps (TBs)
- C) A GC that compresses the heap
- D) A GC that only runs during application idle time

---

**Q100.** In Java's type system, what is an `Enum`'s relationship to `Comparable`?
- A) Enums do not implement `Comparable`
- B) Enums implement `Comparable<E>` by default, ordered by their declaration order
- C) Enums must explicitly implement `Comparable`
- D) Enums are compared by their name alphabetically

---

---

# 17. Quiz Answer Key

| Q | Answer | Key Concept |
|---|---|---|
| 1 | **B** | String pool — literals share the same reference |
| 2 | **C** | Polymorphism — parent reference, child object |
| 3 | **B** | `final` on method prevents overriding |
| 4 | **B** | Java passes a copy of the object reference |
| 5 | **C** | `String.create()` doesn't exist |
| 6 | **B** | Abstract class has state; interface is a pure contract |
| 7 | **B** | Static methods are hidden, not overridden |
| 8 | **B** | Diamond problem — must override |
| 9 | **A** | `x++` = 5 (post), `++x` first increments to 7 |
| 10 | **B** | `protected` — class, package, subclass |
| 11 | **B** | Autoboxing: primitive → wrapper |
| 12 | **B** | StringBuilder is NOT thread-safe |
| 13 | **B** | Covariant = subtype return in override |
| 14 | **C** | `final` prevents subclassing |
| 15 | **B** | Pool in heap for string literal reuse |
| 16 | **C** | `finally` return overrides `try` return |
| 17 | **B** | Static fields use reference type, not runtime type |
| 18 | **B** | Anonymous class: inline class instantiation |
| 19 | **B** | `super()` calls parent constructor |
| 20 | **C** | `Serializable` is a marker interface |
| 21 | **C** | ArrayList `get(index)` is O(1) |
| 22 | **C** | LinkedHashMap preserves insertion order |
| 23 | **B** | `ConcurrentModificationException` |
| 24 | **A** | Producer Extends, Consumer Super |
| 25 | **B** | TreeSet: no duplicates + sorted |
| 26 | **C** | ConcurrentHashMap uses fine-grained locking |
| 27 | **B** | Unmodifiable view — throws on mutation |
| 28 | **C** | HashMap implements Map |
| 29 | **C** | Default capacity = 16 |
| 30 | **B** | Collision → stored in bucket as chain/tree |
| 31 | **D** | Both HashSet and LinkedHashSet allow null |
| 32 | **B** | `thenComparing()` = secondary sort key |
| 33 | **C** | `List.of()` fully immutable; `Arrays.asList()` allows `set()` |
| 34 | **B** | PriorityQueue = min binary heap |
| 35 | **B** | WeakHashMap — entries GC'd when keys are unreachable |
| 36 | **C** | `Predicate.test(T t)` |
| 37 | **B** | `thenCompose` = flatMap equivalent for CF |
| 38 | **B** | flatMap opens nested streams and flattens |
| 39 | **C** | `filter()` is an intermediate (lazy) operation |
| 40 | **C** | `0+1+2+3+4+5 = 15` |
| 41 | **A** | `String::toUpperCase` (instance method ref) |
| 42 | **B** | Compile-time SAM enforcement |
| 43 | **B** | `BinaryOperator<T>` — T, T → T |
| 44 | **B** | `Map<K, List<T>>` |
| 45 | **B** | Effectively final rule for captured vars |
| 46 | **B** | Lazy = triggered only by terminal op |
| 47 | **C** | `equals()` and `hashCode()` |
| 48 | **B** | `findFirst()` = encounter order; `findAny()` = parallel-friendly |
| 49 | **B** | `negate()` inverts the predicate result |
| 50 | **C** | `Map<K, V>` |
| 51 | **B** | `IllegalStateException` on consumed stream |
| 52 | **B** | `range(1, 5)` = [1, 2, 3, 4] (exclusive end) |
| 53 | **B** | `BiConsumer<T, U>` |
| 54 | **B** | `Function.identity()` returns input unchanged |
| 55 | **B** | `peek()` = non-consuming side-effect (debugging) |
| 56 | **B** | `Map<Boolean, List<T>>` |
| 57 | **B** | `Supplier` = `() → T` |
| 58 | **B** | Parallel best for large, stateless, CPU-heavy ops |
| 59 | **B** | `orElseGet()` lazily evaluates the Supplier |
| 60 | **B** | `2*2=4 > 3` ✅ and `3*2=6 > 3` ✅ → 2 elements |
| 61 | **B** | Race condition = timing-dependent shared-state bug |
| 62 | **C** | `volatile` = visibility only, not atomicity |
| 63 | **C** | `Callable` returns a value and throws checked exceptions |
| 64 | **C** | `CountDownLatch` |
| 65 | **B** | Daemon thread doesn't prevent JVM exit |
| 66 | **B** | ThreadLocal = per-thread variable isolation |
| 67 | **B** | Deadlock = circular lock wait |
| 68 | **B** | `shutdown()` = graceful stop |
| 69 | **B** | CAS operation |
| 70 | **B** | Segment/node-level locking |
| 71 | **A** | `join()` = wait for thread to die |
| 72 | **B** | ForkJoinPool = divide-and-conquer |
| 73 | **D** | Both `synchronized` and `ReentrantLock` are reentrant |
| 74 | **A** | Livelock = active but no progress |
| 75 | **C** | Semaphore.acquire() blocks if count = 0 |
| 76 | **B** | WAITING = indefinite wait |
| 77 | **B** | CPU cache visibility issue |
| 78 | **B** | CopyOnWriteArrayList = write creates new array copy |
| 79 | **B** | happens-before = visibility and ordering guarantee |
| 80 | **B** | `notifyAll()` wakes all; `notify()` wakes one |
| 81 | **C** | Records finalized in Java 16 |
| 82 | **B** | Sealed = controls permitted subclasses |
| 83 | **B** | Virtual threads = lightweight, JVM-managed |
| 84 | **B** | `var` = local type inference |
| 85 | **B** | Text block = `"""` multiline string |
| 86 | **A** | `yield` returns value from switch block |
| 87 | **C** | Records are `final` and extend `java.lang.Record` |
| 88 | **B** | Pattern matching = auto-cast in instanceof |
| 89 | **B** | Sequenced Collections = ordered first/last access |
| 90 | **B** | `module-info.java` = module descriptor |
| 91 | **B** | `allOf()` = waits for all futures |
| 92 | **B** | `non-sealed` opens hierarchy to unrestricted extension |
| 93 | **B** | `formatted()` = instance-based `String.format` |
| 94 | **B** | Record Pattern = destructuring in pattern matching |
| 95 | **B** | `isBlank()` returns true for whitespace-only strings |
| 96 | **B** | Virtual thread suspends, frees carrier OS thread on I/O |
| 97 | **B** | G1 = region-based, predictable pause times |
| 98 | **B** | Structured concurrency = task-scoped lifecycle management |
| 99 | **B** | ZGC = sub-ms pauses, massive heaps |
| 100 | **B** | Enums implement `Comparable` by declaration order |

---

## Study Tips for the Interview

1. **Know the WHY, not just the WHAT.** Interviewers ask *"Why is String immutable?"*, not just *"Is String immutable?"*
2. **Practice writing code from memory** — especially Stream pipelines, CompletableFuture chains, and synchronized blocks.
3. **Name the version** — knowing that Records came in Java 16, Virtual Threads in Java 21, `var` in Java 10 signals you stay current.
4. **Use analogies in answers** — they signal deep understanding, not memorized definitions.
5. **Mention trade-offs** — for every data structure or design choice, be ready to say "but here's the downside..."
6. **Deadlock prevention** is a favourite senior-level deep dive — always mention lock ordering and `tryLock`.
7. **For Streams** — draw the pipeline mentally: source → intermediates → terminal.
8. **CompletableFuture** vs **reactive** (Project Reactor / RxJava) — know the difference conceptually for senior roles.

---

*Good luck, Samuel — you've got the experience to back this up. Now it's about articulating it confidently.* 🚀
