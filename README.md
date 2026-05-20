# Core Java Interview Prep

A comprehensive Core Java interview guide covering OOP fundamentals through modern Java 21 features, with detailed explanations, code examples, and a 100-question quiz. Based on JavaTechie's "Core Java Top FAQs" plus advanced topics.

---

## Table of Contents

### Foundations
- [1. OOP Fundamentals](core-java-interview-prep.md#1-oop-fundamentals)
  - The Four Pillars (Encapsulation, Inheritance, Polymorphism, Abstraction)
  - `this` vs `super`
- [2. Java Basics & Memory Model](core-java-interview-prep.md#2-java-basics--memory-model)
  - [JVM, JRE, JDK](core-java-interview-prep.md#21-jvm-jre-jdk)
  - [Heap vs Stack Memory](core-java-interview-prep.md#22-heap-vs-stack-memory)
  - [Pass by Value vs Pass by Reference](core-java-interview-prep.md#23-pass-by-value-vs-pass-by-reference)
  - [`==` vs `.equals()`](core-java-interview-prep.md#24--vs-equals)
  - [`final`, `finally`, `finalize`](core-java-interview-prep.md#25-final-finally-finalize)

### Core Language Features
- [3. String & Immutability](core-java-interview-prep.md#3-string--immutability)
  - [Why is String Immutable?](core-java-interview-prep.md#31-why-is-string-immutable)
  - [String vs StringBuilder vs StringBuffer](core-java-interview-prep.md#32-string-vs-stringbuilder-vs-stringbuffer)
  - [String Pool](core-java-interview-prep.md#33-string-pool)
- [4. Collections Framework](core-java-interview-prep.md#4-collections-framework)
  - [Overview — The Big Picture](core-java-interview-prep.md#41-overview--the-big-picture)
  - [ArrayList vs LinkedList](core-java-interview-prep.md#42-arraylist-vs-linkedlist)
  - [HashMap Internals](core-java-interview-prep.md#43-hashmap-internals)
  - [HashSet vs TreeSet vs LinkedHashSet](core-java-interview-prep.md#44-hashset-vs-treeset-vs-linkedhashset)
  - [Comparable vs Comparator](core-java-interview-prep.md#45-comparable-vs-comparator)
  - [fail-fast vs fail-safe Iterators](core-java-interview-prep.md#46-fail-fast-vs-fail-safe-iterators)
- [5. Exception Handling](core-java-interview-prep.md#5-exception-handling)
  - [Exception Hierarchy](core-java-interview-prep.md#51-exception-hierarchy)
  - [try-with-resources (Java 7+)](core-java-interview-prep.md#52-try-with-resources-java-7)
  - [Custom Exceptions](core-java-interview-prep.md#53-custom-exceptions)
  - [Multi-catch (Java 7+)](core-java-interview-prep.md#54-multi-catch-java-7)
- [6. Generics](core-java-interview-prep.md#6-generics)
  - [Why Generics?](core-java-interview-prep.md#61-why-generics)
  - [Bounded Type Parameters](core-java-interview-prep.md#62-bounded-type-parameters)

### Functional Programming
- [7. Functional Interfaces](core-java-interview-prep.md#7-functional-interfaces)
  - [What is a Functional Interface?](core-java-interview-prep.md#71-what-is-a-functional-interface)
  - [Built-in Functional Interfaces (`java.util.function`)](core-java-interview-prep.md#72-built-in-functional-interfaces-javautilfunction)
- [8. Lambda Expressions](core-java-interview-prep.md#8-lambda-expressions)
  - [What is a Lambda?](core-java-interview-prep.md#81-what-is-a-lambda)
  - [Method References](core-java-interview-prep.md#82-method-references)
  - [Variable Capture in Lambdas](core-java-interview-prep.md#83-variable-capture-in-lambdas)
- [9. Streams API](core-java-interview-prep.md#9-streams-api)
  - [What is a Stream?](core-java-interview-prep.md#91-what-is-a-stream)
  - [Stream Pipeline Structure](core-java-interview-prep.md#92-stream-pipeline-structure)
  - [Common Operations](core-java-interview-prep.md#93-common-operations)
  - [`map()` vs `flatMap()`](core-java-interview-prep.md#94-map-vs-flatmap)
  - [`reduce()` — Aggregating to a Single Value](core-java-interview-prep.md#95-reduce--aggregating-to-a-single-value)
  - [Parallel Streams](core-java-interview-prep.md#96-parallel-streams)
  - [Collectors Cheat Sheet](core-java-interview-prep.md#97-collectors-cheat-sheet)
- [10. Optionals](core-java-interview-prep.md#10-optionals)
  - [The Problem Optional Solves](core-java-interview-prep.md#101-the-problem-optional-solves)
  - [Creating Optionals](core-java-interview-prep.md#102-creating-optionals)

### Concurrency
- [11. Multithreading & Concurrency](core-java-interview-prep.md#11-multithreading--concurrency)
  - [Creating Threads](core-java-interview-prep.md#111-creating-threads)
  - [Thread Lifecycle](core-java-interview-prep.md#112-thread-lifecycle)
  - [Synchronization & Race Conditions](core-java-interview-prep.md#113-synchronization--the-race-condition-problem)
  - [`volatile` Keyword](core-java-interview-prep.md#114-volatile-keyword)
  - [Atomic Classes](core-java-interview-prep.md#115-atomic-classes)
  - [ExecutorService & Thread Pools](core-java-interview-prep.md#116-executorservice--thread-pools)
  - [Deadlock](core-java-interview-prep.md#117-deadlock)
  - [`wait()`, `notify()`, `notifyAll()`](core-java-interview-prep.md#118-wait-notify-notifyall)
  - [CountDownLatch vs CyclicBarrier](core-java-interview-prep.md#119-countdownlatch-vs-cyclicbarrier)
  - [ThreadLocal](core-java-interview-prep.md#1110-threadlocal)
- [12. Async Programming — CompletableFuture](core-java-interview-prep.md#12-async-programming--completablefuture)
  - [What is CompletableFuture?](core-java-interview-prep.md#121-what-is-completablefuture)
  - [Creating CompletableFutures](core-java-interview-prep.md#122-creating-completablefutures)
  - [Chaining & Composing](core-java-interview-prep.md#123-chaining--composing)
  - [Error Handling](core-java-interview-prep.md#124-error-handling)
  - [Combining Multiple Futures](core-java-interview-prep.md#125-combining-multiple-futures)

### Advanced Topics
- [13. Modern Java (Java 9–21)](core-java-interview-prep.md#13-modern-java-java-921)
  - [`var` — Local Variable Type Inference (Java 10)](core-java-interview-prep.md#131-var--local-variable-type-inference-java-10)
  - [Records (Java 16)](core-java-interview-prep.md#132-records-java-16)
  - [Sealed Classes (Java 17)](core-java-interview-prep.md#133-sealed-classes-java-17)
  - [Pattern Matching for `instanceof` (Java 16)](core-java-interview-prep.md#134-pattern-matching-for-instanceof-java-16)
  - [Switch Expressions (Java 14+)](core-java-interview-prep.md#135-switch-expressions-java-14)
  - [Text Blocks (Java 15)](core-java-interview-prep.md#136-text-blocks-java-15)
  - [Virtual Threads — Project Loom (Java 21)](core-java-interview-prep.md#137-virtual-threads-java-21--project-loom)
  - [Sequenced Collections (Java 21)](core-java-interview-prep.md#138-sequenced-collections-java-21)
  - [Java Module System — JPMS (Java 9)](core-java-interview-prep.md#139-java-module-system-jpms--java-9)
  - [New String Methods (Java 11–21)](core-java-interview-prep.md#1310-new-string-methods-java-1121)
- [14. JVM Internals & Garbage Collection](core-java-interview-prep.md#14-jvm-internals--garbage-collection)
  - [JVM Memory Areas](core-java-interview-prep.md#141-jvm-memory-areas)
  - [Garbage Collection](core-java-interview-prep.md#142-garbage-collection)
  - [Strong, Soft, Weak, Phantom References](core-java-interview-prep.md#143-strong-soft-weak-phantom-references)
- [15. Design Patterns in Java](core-java-interview-prep.md#15-design-patterns-in-java)
  - [Singleton](core-java-interview-prep.md#151-singleton)
  - [Builder](core-java-interview-prep.md#152-builder)
  - [Factory & Strategy](core-java-interview-prep.md#153-factory--strategy)

### Quiz
- [16. Quiz — 100 Questions](core-java-interview-prep.md#16-quiz--100-questions)
  - [Section A: OOP & Java Basics (Q1–Q20)](core-java-interview-prep.md#section-a-oop--java-basics-q1q20)
  - [Section B: Collections & Generics (Q21–Q35)](core-java-interview-prep.md#section-b-collections--generics-q21q35)
  - [Section C: Functional Interfaces, Lambdas & Streams (Q36–Q60)](core-java-interview-prep.md#section-c-functional-interfaces-lambdas--streams-q36q60)
  - [Section D: Multithreading & Concurrency (Q61–Q80)](core-java-interview-prep.md#section-d-multithreading--concurrency-q61q80)
  - [Section E: Modern Java (Q81–Q100)](core-java-interview-prep.md#section-e-modern-java-q81q100)
- [17. Quiz Answer Key](core-java-interview-prep.md#17-quiz-answer-key)
- [Study Tips for the Interview](core-java-interview-prep.md#study-tips-for-the-interview)

---

## How to Use

1. **Work through sections in order.** Foundations (1–6) build the base, functional programming (7–10) introduces modern Java idioms, concurrency (11–12) covers the hardest interview topics, and advanced topics (13–15) round out your knowledge.
2. **Run every code example.** Understanding comes from execution, not just reading.
3. **Take the quiz cold** before checking the answer key — it reveals your weak spots.
4. **Focus on sections 4, 9, and 11** — Collections, Streams, and Multithreading are the most frequently asked topics in interviews.
