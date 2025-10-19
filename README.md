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

### Declarations & Types
```kotlin
// Basic declarations
val immutable: String = "can't change"  // Type explicit
var mutable = 42                        // Type inferred
const val COMPILE_TIME = "constant"     // Compile-time constant
lateinit var late: String              // Initialize later

// Type inference and basic types
val number = 42        // Int
val long = 42L        // Long
val double = 42.0     // Double
val float = 42.0f     // Float
val hex = 0xFF        // Int from hexadecimal
val binary = 0b1010   // Int from binary

// Type checking and casting
val anyValue: Any = "Hello"
if (anyValue is String) {
    println(anyValue.length)  // Smart cast
}
val asString = anyValue as? String  // Safe cast
```

### Functions
```kotlin
// Basic function with default arguments
fun greet(name: String = "World", greeting: String = "Hello") {
    println("$greeting, $name!")
}

// Single-expression functions
fun add(a: Int, b: Int) = a + b

// Function with multiple return values using data class
data class Result(val sum: Int, val product: Int)
fun calculate(a: Int, b: Int) = Result(a + b, a * b)

// Local functions
fun processData(data: List<Int>): Int {
    fun validate(value: Int) = value >= 0
    
    return data.filter(::validate).sum()
}

// Extension functions with generics
fun <T> List<T>.secondOrNull(): T? = 
    if (size >= 2) this[1] else null

// Infix functions
infix fun Int.times(str: String) = str.repeat(this)
val result = 3 times "hello " // "hello hello hello "
```

### Control Flow
```kotlin
// Advanced when expressions
val number = 42
val description = when {
    number < 0 -> "Negative"
    number == 0 -> "Zero"
    number in 1..10 -> "Small positive"
    number in 11..100 -> "Medium positive"
    else -> "Large positive"
}

// For loops with different ranges
for (i in 1..5) print(i)            // 12345
for (i in 5 downTo 1) print(i)      // 54321
for (i in 1..10 step 2) print(i)    // 13579
for ((index, value) in list.withIndex()) {
    println("Element at $index is $value")
}

// Custom iterator
class DateRange(val start: Date, val end: Date) {
    operator fun iterator() = object : Iterator<Date> {
        var current = start
        override fun hasNext() = current <= end
        override fun next(): Date {
            val result = current
            current = // increment date
            return result
        }
    }
}
```

### Null Safety
```kotlin
// Comprehensive null safety examples
val nullable: String? = null
val length = nullable?.length ?: 0      // Elvis operator

// Safe calls with let
nullable?.let { 
    println("Length is ${it.length}")
} ?: println("String is null")

// Multiple safe calls
data class Address(val street: String?)
data class Person(val address: Address?)

val person: Person? = null
val streetLength = person?.address?.street?.length ?: 0

// Not-null assertion (use carefully!)
val nonNull: String = nullable!! // Throws NPE if nullable is null

// Platform types (from Java)
@Nullable String javaString = null  // From Java
val kotlinString: String? = javaString // Explicit nullable in Kotlin
```

### Collections & Sequences
```kotlin
// List operations
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }
val even = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, num -> acc + num }
val product = numbers.fold(1) { acc, num -> acc * num }

// Advanced collection operations
val nested = listOf(listOf(1, 2), listOf(3, 4))
val flattened = nested.flatten() // [1, 2, 3, 4]
val chunks = numbers.chunked(2) // [[1, 2], [3, 4], [5]]
val windowed = numbers.windowed(3, 1) // [[1,2,3], [2,3,4], [3,4,5]]

// Sequences for lazy evaluation
val sequence = generateSequence(1) { it + 1 }
    .take(5)
    .filter { it % 2 == 0 }
    .map { it * it }
    .toList() // [4, 16]

// Custom sequence
val fibonacci = sequence {
    var terms = Pair(0, 1)
    while (true) {
        yield(terms.first)
        terms = terms.second to terms.first + terms.second
    }
}.take(10).toList() // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

## Classes & Objects

### Data Classes
```kotlin
// Comprehensive data class example
data class User(
    val name: String,
    var age: Int = 0,
    val email: String? = null
) {
    // Custom property
    val isAdult get() = age >= 18
    
    // Secondary constructor
    constructor(name: String) : this(name, 0)
    
    // Custom equals implementation
    override fun equals(other: Any?) = when {
        this === other -> true
        other !is User -> false
        else -> name == other.name && email == other.email
    }
    
    // Custom hash code
    override fun hashCode() = name.hashCode() * 31 + (email?.hashCode() ?: 0)
}

// Data class usage
val user1 = User("John", 25)
val user2 = user1.copy(age = 26)
val (name, age, email) = user2 // Destructuring

// List of data classes
val users = listOf(
    User("Alice", 20),
    User("Bob", 25),
    User("Charlie", 30)
)
val adults = users.filter { it.isAdult }
val nameMap = users.associateBy { it.name }
```

### Objects & Companion
```kotlin
// Singleton pattern
object DatabaseConfig {
    const val URL = "jdbc:mysql://localhost:3306/db"
    const val USERNAME = "user"
    private var connectionCount = 0
    
    fun connect() {
        connectionCount++
        println("Connected! Total connections: $connectionCount")
    }
}

// Companion object with factory pattern
class MyClass private constructor(val id: Int) {
    companion object Factory {
        private var counter = 0
        
        fun create(): MyClass {
            counter++
            return MyClass(counter)
        }
        
        fun from(id: Int): MyClass? = 
            if (id > 0) MyClass(id) else null
    }
}

// Object expression (anonymous object)
val clickListener = object {
    var clickCount = 0
    fun onClick() {
        clickCount++
        println("Clicked! Count: $clickCount")
    }
}
```

### Inheritance & Interfaces
```kotlin
// Interface with default implementation
interface Animal {
    val species: String
    fun makeSound()
    fun eat() {
        println("$species is eating")
    }
}

// Abstract class
abstract class Mammal : Animal {
    abstract val numberOfLegs: Int
    
    override fun makeSound() {
        println("Generic mammal sound")
    }
    
    fun walk() {
        println("Walking on $numberOfLegs legs")
    }
}

// Concrete class with multiple interfaces
interface Flyable {
    fun fly()
}

class Bat(
    override val species: String,
    override val numberOfLegs: Int = 2
) : Mammal(), Flyable {
    override fun makeSound() {
        println("Ultrasonic sounds")
    }
    
    override fun fly() {
        println("Flying in the dark")
    }
}
```

### Delegation
```kotlin
// Interface delegation
interface Engine {
    fun start()
    fun stop()
}

class ElectricEngine : Engine {
    override fun start() = println("Silent start")
    override fun stop() = println("Silent stop")
}

class Car(engine: Engine) : Engine by engine {
    // Additional car-specific functionality
    fun park() = println("Parking")
}

// Property delegation
class Example {
    // Lazy initialization
    val heavyComputation by lazy {
        println("Computing...")
        42
    }
    
    // Observable property
    var state by Delegates.observable("initial") { prop, old, new ->
        println("$old -> $new")
    }
    
    // Vetoable property
    var age by Delegates.vetoable(0) { prop, old, new ->
        new >= 0 // Only allow non-negative ages
    }
    
    // Custom delegation
    var customProp by object {
        private var value = ""
        
        operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
            return value.uppercase()
        }
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
            this.value = value.lowercase()
        }
    }
}
```

## Functions & FP Patterns

### Higher-Order Functions
```kotlin
// Function types and lambdas
val sum: (Int, Int) -> Int = { a, b -> a + b }
val printer: (Any) -> Unit = { println(it) }

// Higher-order function examples
fun <T, R> List<T>.transform(
    predicate: (T) -> Boolean,
    transformer: (T) -> R
): List<R> = filter(predicate).map(transformer)

// Inline functions for better performance
inline fun measureTimeMillis(block: () -> Unit): Long {
    val start = System.currentTimeMillis()
    block()
    return System.currentTimeMillis() - start
}

// Function references
class Person(val name: String)
val people = listOf(Person("Alice"), Person("Bob"))
val names = people.map(Person::name)

// Return function from function
fun createMultiplier(factor: Int): (Int) -> Int = { it * factor }
val double = createMultiplier(2)
val triple = createMultiplier(3)
```

### Extension Functions
```kotlin
// String extensions
fun String.truncate(maxLength: Int): String =
    if (length <= maxLength) this
    else "${take(maxLength - 3)}..."

// Generic extensions
fun <T> T.applyIf(condition: Boolean, block: T.() -> T): T =
    if (condition) block() else this

// Extension properties
val String.lastChar: Char
    get() = this[length - 1]

// Nullable extensions
fun <T : Any> T?.orDefault(default: T): T = this ?: default

// Usage examples
val text = "Hello, World!"
    .truncate(8)  // "Hello..."
    .applyIf(true) { uppercase() }  // "HELLO..."
println(text.lastChar)  // '.'

val nullableString: String? = null
val result = nullableString.orDefault("default")
```

## Stdlib Features

### Result Handling
```kotlin
// Basic Result usage
fun divide(a: Int, b: Int): Result<Int> = runCatching {
    require(b != 0) { "Cannot divide by zero" }
    a / b
}

// Chain operations
divide(10, 2)
    .map { it * 2 }
    .recover { 0 }
    .onSuccess { println("Result: $it") }
    .onFailure { println("Error: ${it.message}") }

// Custom Result extensions
fun <T> Result<T>.withDefault(default: T): T =
    getOrDefault(default)

fun <T> Result<T>.withLogging(): Result<T> = onFailure {
    println("Operation failed: ${it.message}")
}

// Combining multiple Results
fun <T> List<Result<T>>.allSuccess(): Result<List<T>> = runCatching {
    map { it.getOrThrow() }
}
```

### Scope Functions
```kotlin
// let - for null safety and lambda results
nullable?.let {
    println(it.length)
    it.length * 2
}

// with - for object configuration
val result = with(StringBuilder()) {
    append("Hello")
    append(" ")
    append("World")
    toString()
}

// run - for object configuration and lambda result
val length = "Hello".run {
    println("Processing: $this")
    length
}

// apply - for object configuration
val person = Person().apply {
    name = "John"
    age = 30
}

// also - for side effects
person.also {
    println("Created person: ${it.name}")
    saveToDatabase(it)
}

// Nested scope functions
person.apply {
    name = "John"
}.also {
    validate(it)
}.let {
    processPerson(it)
}
```

## Common Patterns

### Immutable State Updates
```kotlin
// State class with immutable collections
data class State(
    val items: List<String> = emptyList(),
    val selected: String? = null,
    val loading: Boolean = false
)

// Update functions
fun State.addItem(item: String): State =
    copy(items = items + item)

fun State.removeItem(item: String): State =
    copy(items = items - item)

fun State.selectItem(item: String): State =
    copy(selected = item.takeIf { it in items })

// Usage
var state = State()
state = state
    .addItem("Item 1")
    .addItem("Item 2")
    .selectItem("Item 1")
```

### Type-Safe Builders
```kotlin
// HTML DSL example
class HTML {
    private val content = StringBuilder()
    
    fun head(init: Head.() -> Unit) {
        content.append(Head().apply(init))
    }
    
    fun body(init: Body.() -> Unit) {
        content.append(Body().apply(init))
    }
    
    override fun toString() = content.toString()
}

class Head {
    private val content = StringBuilder()
    
    fun title(text: String) {
        content.append("<title>$text</title>")
    }
    
    override fun toString() = "<head>$content</head>"
}

class Body {
    private val content = StringBuilder()
    
    fun p(text: String) {
        content.append("<p>$text</p>")
    }
    
    fun div(init: Body.() -> Unit) {
        content.append("<div>")
        this.init()
        content.append("</div>")
    }
    
    override fun toString() = "<body>$content</body>"
}

// Usage
fun html(init: HTML.() -> Unit): HTML = HTML().apply(init)

val page = html {
    head {
        title("Welcome")
    }
    body {
        div {
            p("Hello, World!")
        }
    }
}
```

## Interoperability

### Java Interop
```kotlin
// Java annotations
@JvmOverloads
fun createUser(
    name: String,
    age: Int = 0,
    email: String? = null
) = User(name, age, email)

// File-level annotations
@file:JvmName("StringUtils")
package com.example.utils

fun String.toSlug() = lowercase()
    .replace(Regex("[^a-z0-9]+"), "-")
    .trim('-')

// Java static field compatibility
class Constants {
    companion object {
        @JvmField
        val MAX_COUNT = 100
        
        @JvmStatic
        fun isValid(count: Int) = count <= MAX_COUNT
    }
}

// Java checked exceptions
@Throws(IOException::class)
fun readFile(path: String): String {
    // Implementation
}
```

## Pitfalls & Gotchas

### Common Mistakes and Solutions
```kotlin
// 1. Null Safety Pitfalls
val str: String? = null
// BAD
val length = str!!.length  // Can throw NPE
// GOOD
val length = str?.length ?: 0

// 2. Late Initialization
class MyClass {
    private lateinit var late: String
    
    fun initialize() {
        if (!::late.isInitialized) {
            late = "value"
        }
    }
}

// 3. Collection Mutability
// BAD
val list = mutableListOf<String>()
fun processList(items: List<String>) {
    (items as MutableList<String>).add("new")  // Unsafe cast
}

// GOOD
fun processList(items: MutableList<String>) {
    items.add("new")
}

// 4. Property Access vs Method Call
class Example {
    val computed: Int
        get() = expensiveComputation()  // Called every time
    
    val cached by lazy { expensiveComputation() }  // Computed once
}

// 5. Extension Function Visibility
// BAD
private fun String.process(): String = // ...
fun useString(s: String) = s.process()  // Won't compile

// GOOD
private fun processString(s: String): String = // ...
fun useString(s: String) = processString(s)
```

## Cheat Tables

### Scope Functions
| Function | Object reference | Return value | Use case |
|----------|-----------------|--------------|----------|
| let      | it             | lambda result| Null safety, chaining |
| run      | this           | lambda result| Object config, computing values |
| with     | this           | lambda result| Multiple calls on object |
| apply    | this           | context obj  | Object initialization |
| also     | it             | context obj  | Side effects, logging |

### Collection Operations
| Operation | Purpose | Example | Result |
|-----------|---------|---------|---------|
| map       | Transform | `list.map { it * 2 }` | New list with transformed elements |
| filter    | Select subset | `list.filter { it > 0 }` | New list with matching elements |
| reduce    | Accumulate | `list.reduce { acc, i -> acc + i }` | Single value |
| fold      | Accumulate with initial | `list.fold(0) { acc, i -> acc + i }` | Single value |
| groupBy   | Group elements | `list.groupBy { it % 2 }` | Map of lists |
| flatMap   | Transform and flatten | `list.flatMap { listOf(it, it) }` | Flattened transformed list |
| zip       | Combine lists | `list1.zip(list2) { a, b -> a + b }` | Combined list |

## Snippet Index

1. [Null Safety Examples](#null-safety)
2. [Data Class Usage](#data-classes)
3. [Extension Functions](#functions--fp-patterns)
4. [Collection Operations](#collections--sequences)
5. [State Management](#common-patterns)
6. [Error Handling](#stdlib-features)
7. [Scope Functions Usage](#scope-functions)
8. [Type-Safe Builders](#common-patterns)
9. [Property Delegation](#delegation)
10. [Java Interoperability](#interoperability)