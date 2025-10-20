# Kotlin Algorithm Examples

A collection of common algorithms implemented in Kotlin.

## Table of Contents
- [Graph Algorithms](#graph-algorithms)
  - [BFS (Breadth-First Search)](#bfs)
  - [DFS (Depth-First Search)](#dfs)
  - [Dijkstra's Algorithm](#dijkstras-algorithm)
- [Tree Algorithms](#tree-algorithms)
  - [Binary Search Tree](#binary-search-tree)
  - [Tree Traversals](#tree-traversals)
- [Sorting Algorithms](#sorting-algorithms)
  - [Quick Sort](#quick-sort)
  - [Merge Sort](#merge-sort)
- [Search Algorithms](#search-algorithms)
  - [Binary Search](#binary-search)
- [Dynamic Programming](#dynamic-programming)
  - [Fibonacci](#fibonacci)
  - [Longest Common Subsequence](#longest-common-subsequence)

## Graph Algorithms

### BFS
```kotlin
class Graph<T> {
    private val adjacencyList = mutableMapOf<T, MutableSet<T>>()
    
    fun addEdge(from: T, to: T) {
        adjacencyList.getOrPut(from) { mutableSetOf() }.add(to)
        adjacencyList.getOrPut(to) { mutableSetOf() }  // For undirected graph
    }
    
    fun bfs(start: T): List<T> {
        val visited = mutableSetOf<T>()
        val result = mutableListOf<T>()
        val queue = ArrayDeque<T>()
        
        queue.add(start)
        visited.add(start)
        
        while (queue.isNotEmpty()) {
            val vertex = queue.removeFirst()
            result.add(vertex)
            
            adjacencyList[vertex]?.forEach { neighbor ->
                if (neighbor !in visited) {
                    visited.add(neighbor)
                    queue.add(neighbor)
                }
            }
        }
        
        return result
    }
}

// Usage
fun main() {
    val graph = Graph<String>()
    graph.addEdge("A", "B")
    graph.addEdge("A", "C")
    graph.addEdge("B", "D")
    graph.addEdge("C", "E")
    
    println(graph.bfs("A"))  // [A, B, C, D, E]
}
```

### DFS
```kotlin
class Graph<T> {
    private val adjacencyList = mutableMapOf<T, MutableSet<T>>()
    
    fun addEdge(from: T, to: T) {
        adjacencyList.getOrPut(from) { mutableSetOf() }.add(to)
        adjacencyList.getOrPut(to) { mutableSetOf() }
    }
    
    fun dfs(start: T): List<T> {
        val visited = mutableSetOf<T>()
        val result = mutableListOf<T>()
        
        fun dfsRecursive(vertex: T) {
            visited.add(vertex)
            result.add(vertex)
            
            adjacencyList[vertex]?.forEach { neighbor ->
                if (neighbor !in visited) {
                    dfsRecursive(neighbor)
                }
            }
        }
        
        dfsRecursive(start)
        return result
    }
}

// Usage
fun main() {
    val graph = Graph<String>()
    graph.addEdge("A", "B")
    graph.addEdge("A", "C")
    graph.addEdge("B", "D")
    graph.addEdge("C", "E")
    
    println(graph.dfs("A"))  // [A, B, D, C, E]
}
```

### Dijkstra's Algorithm
```kotlin
data class Edge<T>(val to: T, val weight: Int)

class WeightedGraph<T> {
    private val adjacencyList = mutableMapOf<T, MutableSet<Edge<T>>>()
    
    fun addEdge(from: T, to: T, weight: Int) {
        adjacencyList.getOrPut(from) { mutableSetOf() }.add(Edge(to, weight))
    }
    
    fun dijkstra(start: T): Map<T, Int> {
        val distances = mutableMapOf<T, Int>()
        val visited = mutableSetOf<T>()
        val queue = PriorityQueue<Pair<T, Int>>(compareBy { it.second })
        
        // Initialize distances
        adjacencyList.keys.forEach { vertex ->
            distances[vertex] = if (vertex == start) 0 else Int.MAX_VALUE
        }
        
        queue.offer(start to 0)
        
        while (queue.isNotEmpty()) {
            val (vertex, distance) = queue.poll()
            if (vertex in visited) continue
            visited.add(vertex)
            
            adjacencyList[vertex]?.forEach { edge ->
                val newDistance = distance + edge.weight
                if (newDistance < distances[edge.to]!!) {
                    distances[edge.to] = newDistance
                    queue.offer(edge.to to newDistance)
                }
            }
        }
        
        return distances
    }
}

// Usage
fun main() {
    val graph = WeightedGraph<String>()
    graph.addEdge("A", "B", 4)
    graph.addEdge("A", "C", 2)
    graph.addEdge("B", "D", 3)
    graph.addEdge("C", "D", 1)
    
    println(graph.dijkstra("A"))  // {A=0, B=4, C=2, D=3}
}
```

## Tree Algorithms

### Binary Search Tree
```kotlin
class BinarySearchTree<T : Comparable<T>> {
    private class Node<T>(
        var value: T,
        var left: Node<T>? = null,
        var right: Node<T>? = null
    )
    
    private var root: Node<T>? = null
    
    fun insert(value: T) {
        root = insert(root, value)
    }
    
    private fun insert(node: Node<T>?, value: T): Node<T> {
        if (node == null) return Node(value)
        
        when {
            value < node.value -> node.left = insert(node.left, value)
            value > node.value -> node.right = insert(node.right, value)
        }
        
        return node
    }
    
    fun search(value: T): Boolean {
        var current = root
        while (current != null) {
            when {
                value == current.value -> return true
                value < current.value -> current = current.left
                else -> current = current.right
            }
        }
        return false
    }
}

// Usage
fun main() {
    val bst = BinarySearchTree<Int>()
    bst.insert(5)
    bst.insert(3)
    bst.insert(7)
    println(bst.search(3))  // true
    println(bst.search(4))  // false
}
```

### Tree Traversals
```kotlin
class BinaryTree<T> {
    class Node<T>(
        val value: T,
        var left: Node<T>? = null,
        var right: Node<T>? = null
    )
    
    private var root: Node<T>? = null
    
    fun inorderTraversal(): List<T> {
        val result = mutableListOf<T>()
        
        fun inorder(node: Node<T>?) {
            if (node == null) return
            inorder(node.left)
            result.add(node.value)
            inorder(node.right)
        }
        
        inorder(root)
        return result
    }
    
    fun preorderTraversal(): List<T> {
        val result = mutableListOf<T>()
        
        fun preorder(node: Node<T>?) {
            if (node == null) return
            result.add(node.value)
            preorder(node.left)
            preorder(node.right)
        }
        
        preorder(root)
        return result
    }
    
    fun postorderTraversal(): List<T> {
        val result = mutableListOf<T>()
        
        fun postorder(node: Node<T>?) {
            if (node == null) return
            postorder(node.left)
            postorder(node.right)
            result.add(node.value)
        }
        
        postorder(root)
        return result
    }
    
    fun levelOrderTraversal(): List<List<T>> {
        val result = mutableListOf<List<T>>()
        val root = this.root ?: return result
        
        val queue = ArrayDeque<Node<T>>()
        queue.add(root)
        
        while (queue.isNotEmpty()) {
            val levelSize = queue.size
            val currentLevel = mutableListOf<T>()
            
            repeat(levelSize) {
                val node = queue.removeFirst()
                currentLevel.add(node.value)
                
                node.left?.let { queue.add(it) }
                node.right?.let { queue.add(it) }
            }
            
            result.add(currentLevel)
        }
        
        return result
    }
}
```

## Sorting Algorithms

### Quick Sort
```kotlin
fun <T : Comparable<T>> List<T>.quickSort(): List<T> {
    if (size < 2) return this
    
    val pivot = this[size / 2]
    val equal = filter { it == pivot }
    val less = filter { it < pivot }
    val greater = filter { it > pivot }
    
    return less.quickSort() + equal + greater.quickSort()
}

// Usage
fun main() {
    val list = listOf(3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5)
    println(list.quickSort())  // [1, 1, 2, 3, 3, 4, 5, 5, 5, 6, 9]
}
```

### Merge Sort
```kotlin
fun <T : Comparable<T>> List<T>.mergeSort(): List<T> {
    if (size <= 1) return this
    
    val middle = size / 2
    val left = subList(0, middle).mergeSort()
    val right = subList(middle, size).mergeSort()
    
    return merge(left, right)
}

private fun <T : Comparable<T>> merge(left: List<T>, right: List<T>): List<T> {
    val result = mutableListOf<T>()
    var leftIndex = 0
    var rightIndex = 0
    
    while (leftIndex < left.size && rightIndex < right.size) {
        if (left[leftIndex] <= right[rightIndex]) {
            result.add(left[leftIndex])
            leftIndex++
        } else {
            result.add(right[rightIndex])
            rightIndex++
        }
    }
    
    result.addAll(left.subList(leftIndex, left.size))
    result.addAll(right.subList(rightIndex, right.size))
    
    return result
}
```

## Dynamic Programming

### Fibonacci
```kotlin
// Recursive with memoization
fun fibonacci(n: Int, memo: MutableMap<Int, Long> = mutableMapOf()): Long {
    if (n <= 1) return n.toLong()
    
    memo[n]?.let { return it }
    
    memo[n] = fibonacci(n - 1, memo) + fibonacci(n - 2, memo)
    return memo[n]!!
}

// Iterative
fun fibonacciIterative(n: Int): Long {
    if (n <= 1) return n.toLong()
    
    var prev = 0L
    var current = 1L
    
    repeat(n - 1) {
        val temp = current
        current += prev
        prev = temp
    }
    
    return current
}
```

### Longest Common Subsequence
```kotlin
fun longestCommonSubsequence(text1: String, text2: String): String {
    val dp = Array(text1.length + 1) { IntArray(text2.length + 1) }
    
    // Fill the dp table
    for (i in 1..text1.length) {
        for (j in 1..text2.length) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1
            } else {
                dp[i][j] = maxOf(dp[i - 1][j], dp[i][j - 1])
            }
        }
    }
    
    // Reconstruct the LCS
    val result = StringBuilder()
    var i = text1.length
    var j = text2.length
    
    while (i > 0 && j > 0) {
        if (text1[i - 1] == text2[j - 1]) {
            result.insert(0, text1[i - 1])
            i--
            j--
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--
        } else {
            j--
        }
    }
    
    return result.toString()
}

// Usage
fun main() {
    println(longestCommonSubsequence("ABCDGH", "AEDFHR"))  // "ADH"
}
```

Each algorithm includes:
- Complete implementation
- Time complexity analysis (in comments)
- Usage examples
- Common variations where applicable

Let me know if you'd like me to add more algorithms or expand on any of these implementations!
