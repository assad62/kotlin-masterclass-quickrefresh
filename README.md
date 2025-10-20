# Kotlin Interview Quick Reference

## Core Concepts (Most Asked)

### 1. Null Safety 🔑
```kotlin
// Elvis operator & safe calls
val name: String? = null
val length = name?.length ?: 0  // Safe call + default value
val upper = name?.uppercase()    // Returns null if name is null

// Safe cast
val any: Any = "Hello"
val str: String? = any as? String  // Returns null if cast fails

// Let for null safety
name?.let { println(it.length) }  // Only runs if name != null
```

### 2. Data Classes 📦
```kotlin
data class User(
    val name: String,
    var age: Int = 0  // Default value
) {
    val isAdult get() = age >= 18
}

// Auto-generated: equals(), hashCode(), toString(), copy()
val user = User("John", 25)
val copy = user.copy(age = 30)
val (name, age) = user  // Destructuring
```

### 3. Collections & Functions 🔄
```kotlin
// Immutable vs Mutable
val readonly = listOf(1, 2, 3)
val mutable = mutableListOf(1, 2, 3)

// Common operations
val doubled = list.map { it * 2 }
val filtered = list.filter { it > 0 }
val grouped = list.groupBy { it % 2 }
val sum = list.fold(0) { acc, num -> acc + num }

// Higher-order functions
fun List<Int>.transformAndFilter(
    predicate: (Int) -> Boolean,
    transform: (Int) -> Int
) = filter(predicate).map(transform)
```

### 4. Coroutines Basics 🧵
```kotlin
// Launch new coroutine
launch {
    delay(1000L)
    println("After 1 second")
}

// Async/Await
val result = async { fetchData() }
val data = result.await()

// Error handling
try {
    coroutineScope {
        launch { throw Exception("Error") }
    }
} catch (e: Exception) {
    // Handle error
}
```

### 5. Object & Companion Object 🎯
```kotlin
// Singleton
object Logger {
    fun log(msg: String) = println(msg)
}

// Factory pattern
class Database private constructor() {
    companion object {
        fun create(): Database = Database()
    }
}
```

### 6. Extension Functions 🔧
```kotlin
// String extensions
fun String.addExclamation() = "$this!"
fun String?.orEmpty() = this ?: ""

// Generic extensions
fun <T> T.println() = also { println(it) }
```

## Common Interview Tasks

### 1. State Management
```kotlin
data class UiState<T>(
    val data: T? = null,
    val loading: Boolean = false,
    val error: String? = null
)

// Immutable updates
fun updateState(state: UiState<String>): UiState<String> =
    state.copy(loading = false, data = "New Data")
```

### 2. Builder Pattern
```kotlin
class EmailBuilder {
    private var to = ""
    private var subject = ""
    
    fun to(to: String) = apply { this.to = to }
    fun subject(subject: String) = apply { this.subject = subject }
    fun build() = Email(to, subject)
}

// Usage
val email = EmailBuilder()
    .to("test@test.com")
    .subject("Hello")
    .build()
```

### 3. Delegation
```kotlin
// Property delegation
class Example {
    val lazy by lazy { computeExpensive() }
    var observable by Delegates.observable("") { _, old, new ->
        println("$old -> $new")
    }
}
```

## Quick Tips 💡

### Scope Functions
```kotlin
// let: for null safety and lambda results
nullable?.let { println(it) }

// apply: for object configuration
Person().apply { name = "John" }

// run: for object config + result
val length = str.run { length }

// with: multiple operations
with(person) {
    name = "John"
    age = 30
}
```

### Common Gotchas ⚠️
1. `val` doesn't mean immutable collection
2. Never use `!!` operator in production
3. Remember coroutine cancellation
4. Watch out for implicit platform types from Java

### Collection Operations Cheat Sheet
- `map`: Transform elements
- `filter`: Select elements
- `reduce`: Combine elements
- `groupBy`: Group by key
- `flatMap`: Transform and flatten
- `zip`: Combine two lists

### Smart Casts
```kotlin
if (obj is String) {
    // obj is automatically cast to String
    println(obj.length)
}
```

### Sealed Classes
```kotlin
sealed class Result<T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error<T>(val error: String) : Result<T>()
}

// Exhaustive when
when (result) {
    is Result.Success -> handleSuccess(result.data)
    is Result.Error -> handleError(result.error)
}
```