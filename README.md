# Kotlin Quick Reference (v2.x, Android Focus)

A concise, practical Kotlin reference guide for Android development. Kotlin v2.0.0-Beta1, Last Updated: Oct 19, 2025

## Table of Contents
- [Core Syntax](#core-syntax)
- [Classes & Objects](#classes--objects)
- [Functions & FP Patterns](#functions--fp-patterns)
- [Coroutines](#coroutines)
- [Flow Essentials](#flow-essentials)
- [Stdlib Power Moves](#stdlib-power-moves)
- [Common Patterns](#common-patterns)
- [Android + Compose](#android--compose)
- [Interoperability & Build](#interoperability--build)
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

### Collections
```kotlin
val list = listOf(1, 2, 3)             // Immutable list
val mutable = mutableListOf(1, 2, 3)    // Mutable list
val mapped = list.map { it * 2 }        // Transform
val filtered = list.filter { it > 1 }   // Filter
val grouped = list.groupBy { it % 2 }   // Group by condition
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

### Coroutines

```kotlin
fun main() = runBlocking {
    launch {  // Launch new coroutine
        delay(1000L)
        println("World!")
    }
    println("Hello")
}

suspend fun fetchUser() = coroutineScope {
    val user = async { api.getUser() }
    val profile = async { api.getProfile() }
    UserProfile(user.await(), profile.await())
}
```

## Flow Essentials

```kotlin
val flow = flow {
    for (i in 1..3) {
        delay(100)
        emit(i)
    }
}.map { it * 2 }
 .catch { e -> emit(-1) }

// StateFlow
val _state = MutableStateFlow(0)
val state: StateFlow<Int> = _state.asStateFlow()
```

## Stdlib Power Moves

```kotlin
// Result handling
runCatching { 
    throw Exception("Oops") 
}.onSuccess { 
    println("Success") 
}.onFailure { 
    println("Failed: ${it.message}") 
}

// Resource handling
file.bufferedReader().use { reader ->
    println(reader.readText())
}
```

## Common Patterns

### Immutable State Updates
```kotlin
data class State(
    val items: List<String> = emptyList(),
    val loading: Boolean = false
)

fun updateState(state: State): State =
    state.copy(items = state.items + "new item")
```

### Grid Utilities
```kotlin
data class Point(val x: Int, val y: Int)

fun Point.neighbors(): List<Point> =
    listOf(
        Point(x - 1, y), Point(x + 1, y),
        Point(x, y - 1), Point(x, y + 1)
    )
```

## Android + Compose

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}

// LaunchedEffect example
LaunchedEffect(key1 = true) {
    // Run once when component enters composition
}
```

## Interoperability & Build

### Java Interop
```kotlin
@JvmOverloads
fun createUser(name: String, age: Int = 0) = User(name, age)

@file:JvmName("StringUtils")
fun String.toSlug() = lowercase().replace(" ", "-")
```

### Gradle Setup
```groovy
dependencies {
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3"
    implementation "org.jetbrains.kotlinx:kotlinx-serialization-json:1.6.0"
}
```

## Pitfalls & Gotchas

- Never use `!!` operator unless absolutely necessary
- `equals()` vs `===`: Value comparison vs Reference comparison
- Avoid blocking calls on Main thread
- Always cancel coroutines in `onDestroy()`
- Be careful with mutable collections passed as parameters

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
3. [Coroutine Basics](#coroutines)
4. [Flow Implementation](#flow-essentials)
5. [State Management](#common-patterns)
6. [Compose Counter](#android--compose)
7. [Error Handling](#stdlib-power-moves)
8. [Extension Functions](#functions)
9. [Collection Operations](#collections)
10. [Scope Functions Usage](#cheat-tables)
