# Kotlin Data Structures & Collections Guide

## Built-in Data Structures 📚

### Array Types
```kotlin
// Basic Arrays
val intArray = IntArray(5)                    // Primitive int array [0, 0, 0, 0, 0]
val longArray = LongArray(5) { it.toLong() }  // [0, 1, 2, 3, 4]
val boolArray = BooleanArray(3) { true }      // [true, true, true]

// Object Arrays
val stringArray = Array<String>(3) { "Item $it" }  // ["Item 0", "Item 1", "Item 2"]
val nullableArray = Array<String?>(3) { null }     // [null, null, null]

// 2D Arrays
val matrix = Array(3) { IntArray(3) }         // 3x3 matrix of zeros
val matrix2D = Array(3) { i -> Array(3) { j -> i * 3 + j } }

// Array Operations
array.fill(0)                                 // Fill with value
array.slice(1..3)                            // Get range
array.copyOf(10)                             // Create larger array
array.copyOfRange(1, 4)                      // Copy section

// Performance Notes:
// - Fixed size (cannot grow/shrink)
// - O(1) access by index
// - Contiguous memory allocation
// - Primitive arrays avoid boxing overhead
```

### ArrayList (MutableList)
```kotlin
// Creation
val list = ArrayList<Int>()                   // Empty ArrayList
val initialized = ArrayList<Int>(10)          // With initial capacity
val fromElements = arrayListOf(1, 2, 3)       // From elements

// Operations
list.add(4)                                   // Append: O(1) amortized
list.add(0, 4)                               // Insert at index: O(n)
list.addAll(listOf(5, 6))                    // Add multiple: O(n)
list.removeAt(0)                             // Remove by index: O(n)
list[0] = 10                                 // Update by index: O(1)

// Capacity Management
list.ensureCapacity(20)                      // Reserve space
list.trimToSize()                            // Trim excess capacity

// Performance Notes:
// - Dynamic sizing (grows as needed)
// - O(1) access by index
// - O(1) append (amortized)
// - O(n) insert/remove at arbitrary position
// - Memory overhead for growth capacity
```

### LinkedList
```kotlin
// Creation
val linkedList = LinkedList<Int>()            // Empty LinkedList
val withElements = LinkedList(listOf(1, 2, 3))

// Queue/Deque Operations
linkedList.addFirst(0)                        // Prepend: O(1)
linkedList.addLast(4)                         // Append: O(1)
linkedList.removeFirst()                      // Remove first: O(1)
linkedList.removeLast()                       // Remove last: O(1)
val first = linkedList.first                  // Get first: O(1)
val last = linkedList.last                    // Get last: O(1)

// List Operations
linkedList.add(index = 1, element = 5)        // Insert at index: O(n)
linkedList.removeAt(1)                        // Remove at index: O(n)
linkedList[0] = 10                            // Update by index: O(n)

// Performance Notes:
// - O(1) operations at both ends
// - O(n) access/modify by index
// - More memory overhead per element
// - Non-contiguous memory allocation
```

### HashSet
```kotlin
// Creation
val hashSet = HashSet<String>()               // Empty HashSet
val withCapacity = HashSet<String>(20)        // With initial capacity
val fromElements = hashSetOf("a", "b", "c")   // From elements

// Operations
hashSet.add("d")                              // Add: O(1) average
hashSet.remove("a")                           // Remove: O(1) average
hashSet.contains("b")                         // Check existence: O(1)
hashSet.clear()                               // Remove all: O(1)

// Set Operations
val set1 = hashSetOf(1, 2, 3)
val set2 = hashSetOf(3, 4, 5)
set1 union set2                               // Union: O(n)
set1 intersect set2                           // Intersection: O(n)
set1 subtract set2                            // Difference: O(n)

// Performance Notes:
// - O(1) average for add/remove/contains
// - Unordered
// - Uses hashCode() and equals()
// - Load factor affects performance
```

### LinkedHashSet
```kotlin
// Creation
val linkedHashSet = LinkedHashSet<String>()   // Empty LinkedHashSet
val ordered = linkedHashSetOf("a", "b", "c")  // Maintains insertion order

// Operations (same as HashSet but maintains order)
linkedHashSet.add("d")                        // Add: O(1) average
linkedHashSet.remove("a")                     // Remove: O(1) average

// Performance Notes:
// - O(1) operations like HashSet
// - Maintains insertion order
// - Slightly more memory overhead than HashSet
```

### TreeSet (SortedSet)
```kotlin
// Creation
val treeSet = sortedSetOf(3, 1, 4, 1, 5)     // Creates TreeSet
val custom = TreeSet<String>(compareBy { it.length })  // Custom comparator

// Operations
treeSet.add(2)                                // Add: O(log n)
treeSet.remove(1)                             // Remove: O(log n)
treeSet.contains(3)                           // Check existence: O(log n)
val first = treeSet.first()                   // Get min element: O(1)
val last = treeSet.last()                     // Get max element: O(1)
val range = treeSet.subSet(2, 5)              // Get range: O(log n)

// Performance Notes:
// - O(log n) for most operations
// - Always maintains sorted order
// - Red-black tree implementation
// - More memory overhead than HashSet
```

### HashMap
```kotlin
// Creation
val hashMap = HashMap<String, Int>()          // Empty HashMap
val withCapacity = HashMap<String, Int>(20)   // With initial capacity
val fromPairs = hashMapOf("a" to 1, "b" to 2) // From pairs

// Operations
hashMap["key"] = 1                            // Put: O(1) average
hashMap.remove("key")                         // Remove: O(1) average
val value = hashMap["key"]                    // Get: O(1) average
hashMap.containsKey("key")                    // Check key: O(1)
hashMap.containsValue(1)                      // Check value: O(n)

// Safe Operations
val safe = hashMap.getValue("key")            // Throws if missing
val default = hashMap.getOrDefault("key", 0)  // Returns default if missing
val computed = hashMap.getOrPut("key") { computeValue() }  // Compute if missing

// Performance Notes:
// - O(1) average for most operations
// - Unordered
// - Uses hashCode() and equals()
// - Load factor affects performance
```

### LinkedHashMap
```kotlin
// Creation
val linkedHashMap = LinkedHashMap<String, Int>()  // Empty LinkedHashMap
val ordered = linkedMapOf("a" to 1, "b" to 2)     // Maintains insertion order

// Operations (same as HashMap but maintains order)
linkedHashMap["c"] = 3                            // Maintains insertion order
linkedHashMap.entries.forEach { println(it) }     // Predictable iteration order

// Performance Notes:
// - O(1) operations like HashMap
// - Maintains insertion order
// - Slightly more memory overhead than HashMap
```

### TreeMap (SortedMap)
```kotlin
// Creation
val treeMap = sortedMapOf("b" to 2, "a" to 1)    // Sorted by natural order
val custom = TreeMap<String, Int>(compareBy { it.length })  // Custom order

// Operations
treeMap["c"] = 3                                  // Put: O(log n)
treeMap.remove("a")                               // Remove: O(log n)
val value = treeMap["b"]                          // Get: O(log n)
val firstKey = treeMap.firstKey()                 // Min key: O(1)
val lastEntry = treeMap.lastEntry()               // Max entry: O(1)
val subMap = treeMap.subMap("a", "c")            // Range view: O(log n)

// Performance Notes:
// - O(log n) for most operations
// - Always maintains sorted keys
// - Red-black tree implementation
// - More memory overhead than HashMap
```

## Performance Comparison Table 📊

| Data Structure   | Access | Search | Insertion | Deletion | Space  | Ordered? |
|-----------------|---------|---------|------------|-----------|---------|-----------|
| Array           | O(1)    | O(n)    | N/A        | N/A       | O(n)    | Yes       |
| ArrayList       | O(1)    | O(n)    | O(1)*      | O(n)      | O(n)    | Yes       |
| LinkedList      | O(n)    | O(n)    | O(1)       | O(1)      | O(n)    | Yes       |
| HashSet         | N/A     | O(1)    | O(1)       | O(1)      | O(n)    | No        |
| LinkedHashSet   | N/A     | O(1)    | O(1)       | O(1)      | O(n)    | Yes†      |
| TreeSet         | N/A     | O(log n)| O(log n)   | O(log n)  | O(n)    | Yes‡      |
| HashMap         | O(1)    | O(1)    | O(1)       | O(1)      | O(n)    | No        |
| LinkedHashMap   | O(1)    | O(1)    | O(1)       | O(1)      | O(n)    | Yes†      |
| TreeMap         | O(log n)| O(log n)| O(log n)   | O(log n)  | O(n)    | Yes‡      |

* Amortized
† Maintains insertion order
‡ Maintains sorted order

## Usage Guidelines 🎯

### Choose ArrayList when:
- You need frequent access by index
- You mostly append elements
- You rarely insert/remove in the middle
- You need a dynamic size array

### Choose LinkedList when:
- You frequently add/remove at both ends
- You rarely need random access
- You need a queue or deque implementation

### Choose HashSet when:
- You need unique elements
- Order doesn't matter
- You need fast lookups
- You don't need to sort elements

### Choose TreeSet when:
- You need unique elements
- You need elements always sorted
- You need range queries
- You can accept O(log n) operations

### Choose HashMap when:
- You need key-value pairs
- Order doesn't matter
- You need fast lookups
- You don't need to sort keys

### Choose TreeMap when:
- You need key-value pairs
- You need keys always sorted
- You need range queries
- You can accept O(log n) operations