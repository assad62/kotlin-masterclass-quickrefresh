# Kotlin Quick Reference (v2.x, Core Focus)

A concise, practical Kotlin reference guide focusing on core language features. Kotlin v2.0.0-Beta1, Last Updated: Oct 19, 2025

## Table of Contents
- [Core Syntax](#core-syntax)
- [Classes & Objects](#classes--objects)
- [Functions & FP Patterns](#functions--fp-patterns)
- [Collections & Sequences](#collections--sequences)
- [Stdlib Features](#stdlib-features)
- [Common Patterns](#common-patterns)
- [Interoperability](#interoperability)
- [Pitfalls & Gotchas](#pitfalls--gotchas)
- [Cheat Tables](#cheat-tables)
- [Snippet Index](#snippet-index)

## Core Syntax

### Declarations
```kotlin
val immutable: String = "can't change"  // Type explicit
var mutable = 42                        // Type inferred
const val COMPILE_TIME = "constant"     // Compile-time constant
lateinit var late: String              // Initialize later
```

### Functions
```kotlin
fun add(a: Int, b: Int = 0) = a + b    // Single-expression, default arg
fun greet(name: String = "World") {     // Named arguments
    println("Hello, $name!")
}
fun String.addExclamation() = "$this!"  // Extension function
```

### Control Flow
```kotlin
val x = 10
when (x) {
    in 1..10 -> println("1-10")
    else -> println("other")
}
for (i in 0..10 step 2) print(i)       // Range with step
(1..5).forEach { print(it) }           // Lambda iteration
```

### Null Safety
```kotlin
val nullable: String? = null
val length = nullable?.length ?: 0      // Elvis operator
val safe = nullable?.let { it.length }  // Safe call with let
val list = listOf(1, null, 2).filterNotNull() // Remove nulls
```

### Collections & Sequences
```kotlin
val list = listOf(1, 2, 3)             // Immutable list
val mutable = mutableListOf(1, 2, 3)    // Mutable list
val mapped = list.map { it * 2 }        // Transform
val filtered = list.filter { it > 1 }   // Filter
val grouped = list.groupBy { it % 2 }   // Group by condition

// Sequences for lazy evaluation
val sequence = generateSequence(1) { it + 1 }
    .take(5)
    .filter { it % 2 == 0 }
    .toList() // [2, 4]
```

## Classes & Objects

### Data Classes
```kotlin
data class User(
    val name: String,
    val age: Int = 0
) {
    fun isAdult() = age >= 18
}

// Usage
val user = User("John", 25)
val copy = user.copy(age = 30)
val (name, age) = user  // Destructuring
```

### Objects & Companion
```kotlin
object Singleton {
    fun doSomething() = "I'm single"
}

class MyClass {
    companion object {
        const val TAG = "MyClass"
        fun create() = MyClass()
    }
}
```

### Inheritance & Interfaces
```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("Clickable!")  // Default implementation
}

abstract class View {
    abstract fun draw()
}

class Button : View(), Clickable {
    override fun draw() { /* Implementation */ }
    override fun click() { /* Implementation */ }
}
```

### Delegation
```kotlin
interface Base {
    fun print()
}

class BaseImpl : Base {
    override fun print() = println("BaseImpl")
}

class Derived(b: Base) : Base by b  // Delegation

// Property delegation
class Example {
    val lazy by lazy { computeExpensive() }
    var observed by Delegates.observable("") { _, old, new ->
        println("$old -> $new")
    }
}
```

## Functions & FP Patterns

### Higher-Order Functions
```kotlin
inline fun <T> measureTime(block: () -> T): Pair<T, Long> {
    val start = System.nanoTime()
    val result = block()
    val end = System.nanoTime()
    return result to (end - start)
}

// Function references
val numbers = listOf(1, 2, 3)
numbers.fold(0, Int::plus)
```

### Extension Functions
```kotlin
fun String.truncate(maxLength: Int): String =
    if (length <= maxLength) this
    else "${take(maxLength - 3)}..."

fun <T> List<T>.secondOrNull(): T? =
    if (size >= 2) this[1] else null
```

## Stdlib Features

### Result Handling
```kotlin
fun divide(a: Int, b: Int): Result<Int> = runCatching {
    if (b == 0) throw ArithmeticException("Division by zero")
    a / b
}

// Usage
divide(10, 2)
    .onSuccess { println(it) }
    .onFailure { println("Error: ${it.message}") }
```

### Scope Functions
```kotlin
data class Person(var name: String, var age: Int)

Person("Alice", 20).apply {
    age = 21              // this.age = 21
    name = "Alice Smith"  // this.name = "Alice Smith"
}

"test".let {
    println(it.uppercase())
    it.length
}
```

## Common Patterns

### Immutable State Updates
```kotlin
data class State(
    val items: List<String> = emptyList(),
    val selected: String? = null
)

fun updateState(state: State): State =
    state.copy(items = state.items + "new item")
```

### Type-Safe Builders
```kotlin
class HTMLBuilder {
    fun table(init: TableBuilder.() -> Unit): Table =
        TableBuilder().apply(init).build()
}

class TableBuilder {
    private val cells = mutableListOf<String>()
    
    fun cell(value: String) {
        cells.add(value)
    }
    
    fun build(): Table = Table(cells)
}
```

## Interoperability

### Java Interop
```kotlin
@JvmOverloads
fun createUser(name: String, age: Int = 0) = User(name, age)

@file:JvmName("StringUtils")
fun String.toSlug() = lowercase().replace(" ", "-")
```

## Pitfalls & Gotchas

- Never use `!!` operator unless absolutely necessary
- `equals()` vs `===`: Value comparison vs Reference comparison
- Be careful with mutable collections passed as parameters
- Remember that nullable types are not covariant
- Watch out for platform types from Java (possible runtime nulls)

## Cheat Tables

### Scope Functions
| Function | Object reference | Return value | Use case |
|----------|-----------------|--------------|----------|
| let      | it             | lambda result| Null safety |
| run      | this           | lambda result| Object config |
| with     | this           | lambda result| Multiple calls |
| apply    | this           | context obj  | Object config |
| also     | it             | context obj  | Side effects |

### Collection Operations
| Operation | Purpose | Example |
|-----------|---------|---------|
| map       | Transform | `list.map { it * 2 }` |
| filter    | Select subset | `list.filter { it > 0 }` |
| reduce    | Accumulate | `list.reduce { acc, i -> acc + i }` |
| groupBy   | Group elements | `list.groupBy { it % 2 }` |

## Snippet Index

1. [Null Safety Example](#null-safety)
2. [Data Class Usage](#data-classes)
3. [Extension Functions](#functions)
4. [Collection Operations](#collections--sequences)
5. [State Management](#common-patterns)
6. [Error Handling](#stdlib-features)
7. [Scope Functions Usage](#scope-functions)
8. [Type-Safe Builders](#common-patterns)
9. [Property Delegation](#delegation)
10. [Java Interoperability](#interoperability)