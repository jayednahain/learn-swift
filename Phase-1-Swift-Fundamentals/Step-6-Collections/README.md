# Step 6: Collections

## 📚 Description

Collections are fundamental data structures that store multiple values. Swift provides three primary collection types: Arrays (ordered lists), Dictionaries (key-value pairs), and Sets (unique unordered values). Understanding collections and their operations is essential for managing data in your applications.

### What You'll Learn:
- **Arrays**: Ordered collections of values
- **Dictionaries**: Key-value pair collections
- **Sets**: Unordered collections of unique values
- **Tuples**: Lightweight grouped values
- Collection operations: add, remove, modify, iterate
- Higher-order functions: map, filter, reduce

### Key Concepts:
- **Mutability**: `var` for mutable, `let` for immutable collections
- **Type Safety**: Collections hold values of specific types
- **Value Semantics**: Collections are copied when assigned (value types)
- **Functional Operations**: Transform collections without loops

---

## 💡 Clear Examples

### Example 1: Arrays - Ordered Collections

```swift
// Creating Arrays
var fruits: [String] = ["Apple", "Banana", "Orange"]
var numbers = [1, 2, 3, 4, 5]  // Type inferred: [Int]
var emptyArray: [Int] = []  // Empty array
var anotherEmpty = [String]()  // Another way to create empty array

// Accessing Elements
print(fruits[0])  // Apple
print(fruits[1])  // Banana
print(fruits.first)  // Optional("Apple") - safe access
print(fruits.last)  // Optional("Orange") - safe access

// Array Properties
print("Count: \(fruits.count)")  // Count: 3
print("Is empty: \(fruits.isEmpty)")  // Is empty: false

// Modifying Arrays (mutable with var)
fruits.append("Mango")  // Add to end
print(fruits)  // ["Apple", "Banana", "Orange", "Mango"]

fruits.insert("Grapes", at: 1)  // Insert at index
print(fruits)  // ["Apple", "Grapes", "Banana", "Orange", "Mango"]

fruits.remove(at: 2)  // Remove at index (returns removed element)
print(fruits)  // ["Apple", "Grapes", "Orange", "Mango"]

fruits.removeLast()  // Remove last element
fruits.removeFirst()  // Remove first element
print(fruits)  // ["Grapes", "Orange"]

// Updating elements
fruits[0] = "Watermelon"
print(fruits)  // ["Watermelon", "Orange"]

// Adding multiple elements
fruits += ["Pineapple", "Peach"]
print(fruits)  // ["Watermelon", "Orange", "Pineapple", "Peach"]

// Immutable Arrays (with let)
let immutableFruits = ["Apple", "Banana"]
// immutableFruits.append("Orange")  // ❌ Error: Cannot mutate

// Iterating through Arrays
print("\nAll fruits:")
for fruit in fruits {
    print("- \(fruit)")
}

// Iterate with index
print("\nWith index:")
for (index, fruit) in fruits.enumerated() {
    print("\(index + 1). \(fruit)")
}

// Array Range Access
let allFruits = ["Apple", "Banana", "Cherry", "Date", "Elderberry"]
let firstThree = Array(allFruits[0..<3])  // ["Apple", "Banana", "Cherry"]
let lastTwo = Array(allFruits[3...])  // ["Date", "Elderberry"]
print("First three: \(firstThree)")
print("Last two: \(lastTwo)")

// Checking for element
let hasBanana = fruits.contains("Banana")
print("Has Banana: \(hasBanana)")

// Finding index
if let index = fruits.firstIndex(of: "Orange") {
    print("Orange is at index: \(index)")
}

// Sorting
var unsorted = [5, 2, 8, 1, 9]
let sorted = unsorted.sorted()  // Returns new sorted array
print("Sorted: \(sorted)")  // [1, 2, 5, 8, 9]

unsorted.sort()  // Sorts in place
print("Sorted in place: \(unsorted)")

// Reversing
let reversed = sorted.reversed()  // Returns ReversedCollection
print("Reversed: \(Array(reversed))")  // [9, 8, 5, 2, 1]

// Functional operations (basics)
let numbers2 = [1, 2, 3, 4, 5]

// Map - transform each element
let doubled = numbers2.map { $0 * 2 }
print("Doubled: \(doubled)")  // [2, 4, 6, 8, 10]

// Filter - keep elements matching condition
let evens = numbers2.filter { $0 % 2 == 0 }
print("Evens: \(evens)")  // [2, 4]

// Reduce - combine into single value
let sum = numbers2.reduce(0, +)  // Start with 0, add each element
print("Sum: \(sum)")  // 15

let product = numbers2.reduce(1, *)  // Product of all numbers
print("Product: \(product)")  // 120

/* Output shown inline above */
```

**Explanation:**
- Arrays are ordered, can contain duplicates
- Index-based access: `array[0]`
- `var` arrays are mutable, `let` arrays are immutable
- Many built-in methods for common operations
- Functional methods (map, filter, reduce) are very powerful

---

### Example 2: Dictionaries - Key-Value Pairs

```swift
// Creating Dictionaries
var ages: [String: Int] = ["Alice": 25, "Bob": 30, "Charlie": 28]
var scores = ["Math": 95, "Science": 87, "English": 92]  // Type inferred
var emptyDict: [String: Int] = [:]  // Empty dictionary
var anotherEmpty = [String: Double]()

// Accessing Values (returns optional)
print(ages["Alice"])  // Optional(25)

if let aliceAge = ages["Alice"] {
    print("Alice is \(aliceAge) years old")  // Alice is 25 years old
}

// Safe access with nil-coalescing
let bobAge = ages["Bob"] ?? 0
print("Bob's age: \(bobAge)")  // 30

// Adding/Updating Values
ages["David"] = 35  // Add new key-value pair
print(ages)  // ["Alice": 25, "Bob": 30, "Charlie": 28, "David": 35]

ages["Alice"] = 26  // Update existing value
print(ages["Alice"]!)  // 26

// Another way to update (returns old value)
if let oldAge = ages.updateValue(27, forKey: "Alice") {
    print("Updated Alice's age from \(oldAge) to \(ages["Alice"]!)")
}

// Removing Values
ages["David"] = nil  // Remove by setting to nil
print(ages)  // David removed

ages.removeValue(forKey: "Bob")  // Another way to remove
print(ages)

// Dictionary Properties
print("Count: \(ages.count)")
print("Is empty: \(ages.isEmpty)")

// Iterating through Dictionary
let studentGrades = ["Alice": 95, "Bob": 87, "Charlie": 92, "Diana": 88]

print("\nAll grades:")
for (name, grade) in studentGrades {
    print("\(name): \(grade)")
}

// Iterate keys only
print("\nAll students:")
for student in studentGrades.keys {
    print("- \(student)")
}

// Iterate values only
print("\nAll scores:")
for grade in studentGrades.values {
    print("- \(grade)")
}

// Convert keys/values to arrays
let allStudents = Array(studentGrades.keys)
let allGrades = Array(studentGrades.values)
print("Students: \(allStudents)")
print("Grades: \(allGrades)")

// Checking for key
let hasAlice = studentGrades.keys.contains("Alice")
print("Has Alice: \(hasAlice)")

// Filtering dictionary
let topStudents = studentGrades.filter { $0.value >= 90 }
print("Top students (90+): \(topStudents)")

// Mapping values
let gradeLetters = studentGrades.mapValues { grade -> String in
    switch grade {
    case 90...100: return "A"
    case 80..<90: return "B"
    case 70..<80: return "C"
    default: return "F"
    }
}
print("Letter grades: \(gradeLetters)")

// Merging dictionaries
let additionalScores = ["Eve": 91, "Frank": 85]
var allScores = studentGrades
allScores.merge(additionalScores) { (current, _) in current }  // Keep current value if duplicate
print("All scores: \(allScores)")

// Real-world example: User profile
var userProfile: [String: Any] = [
    "username": "john_doe",
    "age": 25,
    "email": "john@example.com",
    "isPremium": true,
    "followerCount": 1250
]

print("\nUser Profile:")
print("Username: \(userProfile["username"] as? String ?? "Unknown")")
print("Age: \(userProfile["age"] as? Int ?? 0)")
print("Premium: \(userProfile["isPremium"] as? Bool ?? false)")

/* Output examples shown inline above */
```

**Explanation:**
- Dictionaries store key-value pairs
- Keys must be unique and hashable
- Access returns optional (key might not exist)
- Unordered (no guaranteed iteration order)
- Use `[String: Any]` for mixed value types (like JSON)

---

### Example 3: Sets and Tuples

```swift
// SETS - Unordered, unique values

// Creating Sets
var favoriteColors: Set<String> = ["Red", "Blue", "Green"]
var numbers: Set = [1, 2, 3, 4, 5]  // Type inferred
var emptySet = Set<Int>()

print(favoriteColors)  // Order may vary

// Adding elements
favoriteColors.insert("Yellow")
print(favoriteColors)

// Try adding duplicate (ignored)
favoriteColors.insert("Red")  // Already exists, no effect
print("Count: \(favoriteColors.count)")  // Still 4

// Removing elements
favoriteColors.remove("Blue")
print(favoriteColors)

// Checking membership
let hasRed = favoriteColors.contains("Red")
print("Has Red: \(hasRed)")  // true

// Set Operations
let setA: Set = [1, 2, 3, 4, 5]
let setB: Set = [3, 4, 5, 6, 7]

// Union (all elements from both)
let union = setA.union(setB)
print("Union: \(union)")  // [1, 2, 3, 4, 5, 6, 7]

// Intersection (common elements)
let intersection = setA.intersection(setB)
print("Intersection: \(intersection)")  // [3, 4, 5]

// Subtraction (in A but not in B)
let difference = setA.subtracting(setB)
print("Difference: \(difference)")  // [1, 2]

// Symmetric Difference (in either but not both)
let symmetricDiff = setA.symmetricDifference(setB)
print("Symmetric Difference: \(symmetricDiff)")  // [1, 2, 6, 7]

// Set relationships
let subset: Set = [1, 2, 3]
print("Is subset: \(subset.isSubset(of: setA))")  // true
print("Is superset: \(setA.isSuperset(of: subset))")  // true
print("Is disjoint: \(setA.isDisjoint(with: setB))")  // false (have common elements)

// Removing duplicates from array
let numbersWithDuplicates = [1, 2, 2, 3, 3, 3, 4, 4, 5]
let uniqueNumbers = Array(Set(numbersWithDuplicates))
print("Unique: \(uniqueNumbers)")  // [1, 2, 3, 4, 5] (order may vary)

// TUPLES - Lightweight grouped values

// Creating Tuples
let person = ("Alice", 25, "alice@example.com")
let coordinate = (x: 10, y: 20)  // Named elements
let response = (statusCode: 200, message: "OK")

// Accessing Tuple Elements
// By position
print("Name: \(person.0)")  // Alice
print("Age: \(person.1)")   // 25
print("Email: \(person.2)") // alice@example.com

// By name
print("X: \(coordinate.x)")  // 10
print("Y: \(coordinate.y)")  // 20

// Decomposing Tuples
let (name, age, email) = person
print("\(name) is \(age) years old")

// Ignore some values with _
let (statusCode, _) = response
print("Status: \(statusCode)")

// Tuples in functions (returning multiple values)
func getMinMax(numbers: [Int]) -> (min: Int, max: Int)? {
    guard let first = numbers.first else { return nil }
    
    var currentMin = first
    var currentMax = first
    
    for number in numbers {
        if number < currentMin { currentMin = number }
        if number > currentMax { currentMax = number }
    }
    
    return (currentMin, currentMax)
}

if let result = getMinMax(numbers: [5, 2, 9, 1, 7]) {
    print("Min: \(result.min), Max: \(result.max)")  // Min: 1, Max: 9
}

// Tuples in switch statements
let httpResponse = (404, "Not Found")

switch httpResponse {
case (200, let message):
    print("Success: \(message)")
case (404, let message):
    print("Error: \(message)")  // This executes
case (let code, let message):
    print("Status \(code): \(message)")
}

// Real-world iOS example: coordinate comparison
typealias Point = (x: Double, y: Double)

let point1: Point = (x: 10.5, y: 20.3)
let point2: Point = (x: 15.7, y: 18.2)

func distance(from p1: Point, to p2: Point) -> Double {
    let dx = p2.x - p1.x
    let dy = p2.y - p1.y
    return (dx * dx + dy * dy).squareRoot()
}

let dist = distance(from: point1, to: point2)
print("Distance: \(dist)")

/* Output examples shown inline above */
```

**Explanation:**
- **Sets**: Unordered, unique values, great for membership testing
- Set operations: union, intersection, difference
- **Tuples**: Group related values, lightweight alternative to structs
- Tuples great for returning multiple values from functions
- Named tuple elements improve code readability

---

## 🎯 2025 Interview Questions

### Question 1: What are the performance characteristics of Arrays, Sets, and Dictionaries in Swift? When should you use each?

**Answer:**
**Performance Characteristics:**

| Operation | Array | Set | Dictionary |
|-----------|-------|-----|------------|
| Access by index | O(1) | N/A | N/A |
| Access by key | O(n) | O(1) average | O(1) average |
| Insert at end | O(1) amortized | O(1) average | O(1) average |
| Insert at beginning | O(n) | O(1) average | O(1) average |
| Search | O(n) | O(1) average | O(1) average |
| Delete | O(n) | O(1) average | O(1) average |
| Iteration | O(n) | O(n) | O(n) |

**When to Use Each:**

**Arrays** - Use when:
- Order matters
- Duplicate values needed
- Frequent index-based access
- Iterating in sequence

```swift
// Examples:
let todoList = ["Task 1", "Task 2", "Task 3"]  // Order important
let temperatures = [72.5, 73.1, 74.0, 73.8]    // Sequential data
let userPosts: [Post] = []  // Timeline (chronological order)
```

**Sets** - Use when:
- Need unique values
- Membership testing is frequent
- Order doesn't matter
- Set operations (union, intersection)

```swift
// Examples:
var visitedPages: Set<String> = []  // Unique page visits
let uniqueUserIDs: Set<Int> = [1, 2, 3]  // No duplicates
let activeConnections: Set<Connection> = []  // Current connections

// Fast membership testing
if uniqueUserIDs.contains(userID) {
    print("Already exists")
}
```

**Dictionaries** - Use when:
- Key-value associations needed
- Fast lookup by key required
- Data mapping/caching
- Configuration/settings

```swift
// Examples:
var userCache: [String: User] = [:]  // Fast user lookup by ID
let config = ["apiKey": "abc123", "timeout": "30"]  // Settings
var scores: [String: Int] = ["Alice": 95, "Bob": 87]  // Name → Score
```

**Real-World iOS Examples (2025):**

**Array:**
```swift
// UITableView data source
var messages: [Message] = []  // Display in order received

// Animation sequence
let animations = [animation1, animation2, animation3]  // Run in order
```

**Set:**
```swift
// Tracking unique values
var downloadedImageIDs: Set<String> = []

func downloadImage(id: String) {
    guard !downloadedImageIDs.contains(id) else {
        print("Already downloaded")
        return
    }
    // Download...
    downloadedImageIDs.insert(id)
}
```

**Dictionary:**
```swift
// Caching network responses
var responseCache: [String: Data] = [:]

func fetchData(url: String) async -> Data? {
    // Check cache first (O(1))
    if let cached = responseCache[url] {
        return cached
    }
    // Fetch from network...
}
```

**Performance Tips:**
- Use `Set` for `contains()` checks if dealing with large datasets
- Use `Dictionary` for lookups by identifier
- Use `Array` when order is important
- Convert Array to Set temporarily for fast lookups

```swift
// ❌ Slow: O(n) for each contains check
let array = [1, 2, 3, /*...*/ 1000]
if array.contains(500) { }  // O(n)

// ✅ Fast: O(1) for contains check
let set = Set([1, 2, 3, /*...*/ 1000])
if set.contains(500) { }  // O(1)
```

---

### Question 2: Explain the difference between `map`, `filter`, and `reduce`. Provide iOS-specific examples.

**Answer:**
These are **higher-order functions** that transform collections functionally without explicit loops.

**map** - Transforms each element:
```swift
// Syntax: collection.map { transform }
let numbers = [1, 2, 3, 4, 5]
let doubled = numbers.map { $0 * 2 }
print(doubled)  // [2, 4, 6, 8, 10]
```

**filter** - Keeps elements matching condition:
```swift
// Syntax: collection.filter { condition }
let numbers = [1, 2, 3, 4, 5, 6]
let evens = numbers.filter { $0 % 2 == 0 }
print(evens)  // [2, 4, 6]
```

**reduce** - Combines elements into single value:
```swift
// Syntax: collection.reduce(initialValue) { accumulator, element in }
let numbers = [1, 2, 3, 4, 5]
let sum = numbers.reduce(0) { $0 + $1 }  // or .reduce(0, +)
print(sum)  // 15
```

**iOS-Specific Examples (2025):**

**1. SwiftUI - Displaying Transformed Data:**
```swift
import SwiftUI

struct ProductListView: View {
    let products: [Product]
    
    var body: some View {
        List {
            // Map: Transform products to views
            ForEach(products.map { $0.name }, id: \.self) { name in
                Text(name)
            }
            
            // Filter: Show only in-stock items
            ForEach(products.filter { $0.inStock }, id: \.id) { product in
                ProductRow(product: product)
            }
        }
    }
}
```

**2. Calculating Total Price:**
```swift
struct CartItem {
    let name: String
    let price: Double
    let quantity: Int
}

let cartItems = [
    CartItem(name: "iPhone", price: 999.99, quantity: 1),
    CartItem(name: "Case", price: 29.99, quantity: 2),
    CartItem(name: "Charger", price: 19.99, quantity: 1)
]

// Calculate total
let total = cartItems.reduce(0.0) { total, item in
    total + (item.price * Double(item.quantity))
}
print("Total: $\(total)")  // Total: $1079.96

// More concise
let total2 = cartItems.reduce(0.0) { $0 + ($1.price * Double($1.quantity)) }
```

**3. Processing API Response:**
```swift
struct User: Codable {
    let id: Int
    let name: String
    let isActive: Bool
    let email: String
}

let users: [User] = fetchUsers()  // From API

// Get all active user emails
let activeEmails = users
    .filter { $0.isActive }
    .map { $0.email }

print("Active user emails: \(activeEmails)")

// Get names of active users in uppercase
let activeNames = users
    .filter { $0.isActive }
    .map { $0.name.uppercased() }
```

**4. Image Processing:**
```swift
import UIKit

let images: [UIImage] = loadImages()

// Resize all images
let resizedImages = images.map { image in
    image.resized(to: CGSize(width: 100, height: 100))
}

// Filter images by size
let largeImages = images.filter { image in
    image.size.width > 1000 && image.size.height > 1000
}
```

**5. Data Aggregation:**
```swift
struct Transaction {
    let amount: Double
    let category: String
}

let transactions = [
    Transaction(amount: 50.0, category: "Food"),
    Transaction(amount: 30.0, category: "Transport"),
    Transaction(amount: 100.0, category: "Food"),
    Transaction(amount: 25.0, category: "Transport")
]

// Total spending
let totalSpending = transactions.reduce(0.0) { $0 + $1.amount }
print("Total: $\(totalSpending)")  // $205.0

// Group by category (more advanced)
let foodSpending = transactions
    .filter { $0.category == "Food" }
    .reduce(0.0) { $0 + $1.amount }
print("Food: $\(foodSpending)")  // $150.0
```

**6. Chaining Operations:**
```swift
struct Product {
    let name: String
    let price: Double
    let rating: Double
    let inStock: Bool
}

let products: [Product] = loadProducts()

// Find average price of highly-rated, in-stock products
let averagePrice = products
    .filter { $0.inStock }              // Only in stock
    .filter { $0.rating >= 4.0 }        // Highly rated
    .map { $0.price }                   // Get prices
    .reduce(0.0, +) /                   // Sum prices
    Double(products.count)              // Calculate average

print("Average price: $\(averagePrice)")
```

**Comparison Table:**

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `map` | Transform | [A] | [B] |
| `filter` | Select | [A] | [A] |
| `reduce` | Combine | [A] | B |

**Performance Note:** These functions are optimized by the compiler and often as fast as manual loops, but more readable.

---

### Question 3: How does Swift handle array copying? What are value semantics vs reference semantics?

**Answer:**
**Swift's Collections Use Value Semantics** - they're copied when assigned or passed to functions, but Swift optimizes with Copy-on-Write (COW).

**Value Semantics (Arrays, Sets, Dictionaries):**
```swift
var original = [1, 2, 3]
var copy = original  // Copied

copy.append(4)  // Modify copy

print(original)  // [1, 2, 3] - unchanged
print(copy)      // [1, 2, 3, 4] - modified
```

**Reference Semantics (Classes, for comparison):**
```swift
class Box {
    var values: [Int]
    init(values: [Int]) {
        self.values = values
    }
}

let original = Box(values: [1, 2, 3])
let reference = original  // Same instance

reference.values.append(4)

print(original.values)   // [1, 2, 3, 4] - changed!
print(reference.values)  // [1, 2, 3, 4] - same object
```

**Copy-on-Write (COW) Optimization:**
Swift doesn't actually copy the array immediately - it shares the same storage until one is modified.

```swift
var array1 = [1, 2, 3, 4, 5]  // Storage created
var array2 = array1  // Shares storage (no copy yet!)

// At this point, both arrays share the same underlying storage
// Copy only happens when one is modified

array2.append(6)  // NOW the copy happens

// array1 keeps original storage: [1, 2, 3, 4, 5]
// array2 gets new storage: [1, 2, 3, 4, 5, 6]
```

**iOS Examples:**

**1. Function Parameters (Value Semantics):**
```swift
func addItem(_ items: [String]) -> [String] {
    var mutableItems = items  // Local copy
    mutableItems.append("New Item")
    return mutableItems
}

let original = ["Item 1", "Item 2"]
let modified = addItem(original)

print(original)  // ["Item 1", "Item 2"] - unchanged
print(modified)  // ["Item 1", "Item 2", "New Item"]
```

**2. SwiftUI State Management:**
```swift
import SwiftUI

struct TodoListView: View {
    @State private var todos: [String] = []
    
    func addTodo(_ todo: String) {
        // This creates a copy due to value semantics
        // SwiftUI detects the change and updates UI
        todos.append(todo)
    }
    
    var body: some View {
        List(todos, id: \.self) { todo in
            Text(todo)
        }
    }
}
```

**3. Thread Safety (Value Types):**
```swift
var sharedData = [1, 2, 3, 4, 5]

DispatchQueue.global().async {
    var localCopy = sharedData  // Safe copy
    localCopy.append(6)
    // sharedData unchanged on main thread
}

// No race condition because of value semantics
```

**Benefits of Value Semantics:**
1. **Predictability**: No unexpected changes from elsewhere
2. **Thread Safety**: Natural isolation between threads
3. **Simpler reasoning**: No need to track references
4. **SwiftUI compatibility**: State changes detected automatically

**When Reference Semantics Needed:**
Use classes (reference types) when:
- Identity matters (same object vs equal object)
- Large data structures (avoid copying overhead)
- Shared mutable state required

```swift
// Use case for reference semantics
class ImageCache {
    var cachedImages: [String: UIImage] = [:]
    
    func cacheImage(_ image: UIImage, for key: String) {
        cachedImages[key] = image
    }
}

// Single shared instance
let cache = ImageCache()

// All parts of app reference same cache
```

**2025 Best Practice:**
- Prefer value types (structs with collections) by default
- Use reference types (classes) only when sharing is needed
- Trust Copy-on-Write optimization (don't worry about performance)

---

### Question 4: What are the differences between `compactMap`, `flatMap`, and regular `map`? When would you use each?

**Answer:**
These are specialized mapping functions for different scenarios:

**`map`** - Transform each element:
```swift
let numbers = [1, 2, 3]
let strings = numbers.map { String($0) }
print(strings)  // ["1", "2", "3"]
```

**`compactMap`** - Transform and remove `nil` values:
```swift
let strings = ["1", "2", "abc", "3"]
let numbers = strings.compactMap { Int($0) }
print(numbers)  // [1, 2, 3] - "abc" filtered out
```

**`flatMap`** - Transform and flatten nested collections:
```swift
let nested = [[1, 2], [3, 4], [5]]
let flattened = nested.flatMap { $0 }
print(flattened)  // [1, 2, 3, 4, 5]
```

**Detailed Examples:**

**1. `compactMap` - Removing Optionals:**
```swift
// Parse user input (some invalid)
let inputs = ["25", "30", "invalid", "40", "abc"]

// With regular map (keeps optionals)
let ages = inputs.map { Int($0) }
print(ages)  // [Optional(25), Optional(30), nil, Optional(40), nil]

// With compactMap (removes nil)
let validAges = inputs.compactMap { Int($0) }
print(validAges)  // [25, 30, 40]
```

**2. `flatMap` - Flattening Collections:**
```swift
// Nested arrays
let groups = [
    ["Alice", "Bob"],
    ["Charlie"],
    ["David", "Eve", "Frank"]
]

let allPeople = groups.flatMap { $0 }
print(allPeople)  // ["Alice", "Bob", "Charlie", "David", "Eve", "Frank"]

// Transform and flatten
let uppercased = groups.flatMap { $0.map { $0.uppercased() } }
print(uppercased)  // ["ALICE", "BOB", "CHARLIE", "DAVID", "EVE", "FRANK"]
```

**iOS-Specific Examples (2025):**

**1. Processing API Response:**
```swift
struct APIResponse {
    let users: [UserData?]  // Some might be nil
}

struct UserData {
    let id: Int
    let name: String?
}

let response = APIResponse(users: [
    UserData(id: 1, name: "Alice"),
    nil,
    UserData(id: 2, name: nil),
    UserData(id: 3, name: "Bob")
])

// compactMap to remove nil users
let validUsers = response.users.compactMap { $0 }
print(validUsers.count)  // 3 (nil removed)

// compactMap to get non-nil names
let names = validUsers.compactMap { $0.name }
print(names)  // ["Alice", "Bob"]
```

**2. File Processing:**
```swift
import Foundation

let filePaths = [
    "file1.txt",
    "invalid",
    "file2.txt",
    "file3.txt"
]

// compactMap: Try to load files, skip failures
let fileContents = filePaths.compactMap { path -> String? in
    try? String(contentsOfFile: path)
}

print("Loaded \(fileContents.count) files")
```

**3. Nested Collections (Tags/Categories):**
```swift
struct Article {
    let title: String
    let tags: [String]
}

let articles = [
    Article(title: "Swift", tags: ["Programming", "iOS"]),
    Article(title: "SwiftUI", tags: ["UI", "Swift", "iOS"]),
    Article(title: "Combine", tags: ["Reactive", "Swift"])
]

// flatMap to get all unique tags
let allTags = Set(articles.flatMap { $0.tags })
print(allTags)  // ["Programming", "iOS", "UI", "Swift", "Reactive"]
```

**4. SwiftUI - Processing Optional Data:**
```swift
import SwiftUI

struct UserListView: View {
    let users: [User?]
    
    var body: some View {
        List {
            // compactMap to show only valid users
            ForEach(users.compactMap { $0 }, id: \.id) { user in
                Text(user.name)
            }
        }
    }
}
```

**5. Chaining Different Maps:**
```swift
struct Post {
    let comments: [Comment?]
}

struct Comment {
    let text: String?
}

let posts = [
    Post(comments: [Comment(text: "Great!"), nil, Comment(text: "Nice")]),
    Post(comments: [Comment(text: nil), Comment(text: "Cool")])
]

// flatMap to flatten posts → compactMap to remove nils → compactMap to get text
let allCommentTexts = posts
    .flatMap { $0.comments }      // Flatten to [Comment?]
    .compactMap { $0 }            // Remove nil Comments
    .compactMap { $0.text }       // Remove nil texts

print(allCommentTexts)  // ["Great!", "Nice", "Cool"]
```

**When to Use Each:**

| Function | Use When | Returns |
|----------|----------|---------|
| `map` | Transform each element | Same count, different type |
| `compactMap` | Transform + remove nils | Fewer elements (non-nil only) |
| `flatMap` | Flatten nested structure | Flattened single-level array |

**Performance Consideration:**
All three have O(n) complexity, but `compactMap` and `flatMap` may do additional work (filtering nils, flattening).

**2025 Best Practice:**
```swift
// ✅ Use compactMap for optional transformation
let ids = users.compactMap { $0.id }

// ❌ Don't use map + filter
let ids = users.map { $0.id }.filter { $0 != nil }.map { $0! }

// ✅ Use flatMap for nested collections
let allItems = categories.flatMap { $0.items }

// ❌ Don't use reduce for flattening
let allItems = categories.reduce([], +)  // Less clear
```

---

### Question 5: How do you handle thread safety with collections in Swift? What are the common patterns for iOS development in 2025?

**Answer:**
Swift's collections are **not thread-safe** by default. Multiple threads accessing/modifying the same collection can cause **race conditions** and crashes.

**The Problem:**
```swift
var sharedArray = [1, 2, 3]

// ❌ Unsafe: Race condition
DispatchQueue.global().async {
    sharedArray.append(4)  // Thread 1
}

DispatchQueue.global().async {
    sharedArray.append(5)  // Thread 2
}

// Potential crash or data corruption!
```

**Common Patterns for Thread Safety (2025):**

**1. Serial DispatchQueue (Recommended):**
```swift
class ThreadSafeArray<Element> {
    private var array: [Element] = []
    private let queue = DispatchQueue(label: "com.app.threadsafe.array")
    
    func append(_ element: Element) {
        queue.async {
            self.array.append(element)
        }
    }
    
    func get() -> [Element] {
        return queue.sync {
            return self.array
        }
    }
    
    var count: Int {
        return queue.sync {
            return self.array.count
        }
    }
}

// Usage
let safeArray = ThreadSafeArray<Int>()

DispatchQueue.global().async {
    safeArray.append(1)
}

DispatchQueue.global().async {
    safeArray.append(2)
}

print(safeArray.count)  // Safe!
```

**2. Actor (Modern Swift 5.5+):**
```swift
actor ThreadSafeDataStore {
    private var data: [String: Any] = [:]
    
    func set(_ value: Any, for key: String) {
        data[key] = value
    }
    
    func get(_ key: String) -> Any? {
        return data[key]
    }
    
    func allKeys() -> [String] {
        return Array(data.keys)
    }
}

// Usage with async/await
let store = ThreadSafeDataStore()

Task {
    await store.set("Alice", for: "user1")
    let value = await store.get("user1")
    print(value)
}

Task {
    await store.set("Bob", for: "user2")
}

// Actors ensure serial access automatically!
```

**3. Barrier (Read-Write Lock Pattern):**
```swift
class ConcurrentArray<Element> {
    private var array: [Element] = []
    private let queue = DispatchQueue(label: "com.app.concurrent.array", attributes: .concurrent)
    
    // Multiple readers OK
    func get() -> [Element] {
        return queue.sync {
            return self.array
        }
    }
    
    // Single writer with barrier
    func append(_ element: Element) {
        queue.async(flags: .barrier) {
            self.array.append(element)
        }
    }
    
    var count: Int {
        return queue.sync {
            return self.array.count
        }
    }
}

// Allows multiple concurrent reads, exclusive writes
let concurrentArray = ConcurrentArray<String>()

// These can happen simultaneously
DispatchQueue.global().async {
    _ = concurrentArray.get()  // Read 1
}

DispatchQueue.global().async {
    _ = concurrentArray.get()  // Read 2 (concurrent with Read 1)
}

// This waits for all reads to finish
DispatchQueue.global().async {
    concurrentArray.append("New")  // Write (exclusive)
}
```

**4. @MainActor for UI Updates (SwiftUI):**
```swift
import SwiftUI

@MainActor
class ViewModel: ObservableObject {
    @Published var items: [String] = []
    
    // All access guaranteed on main thread
    func addItem(_ item: String) {
        items.append(item)  // Safe - always on main thread
    }
    
    // Network call with main thread updates
    func fetchItems() async {
        // Background work
        let newItems = await downloadItems()
        
        // Update UI on main thread automatically
        items = newItems  // Safe!
    }
}

struct ContentView: View {
    @StateObject private var viewModel = ViewModel()
    
    var body: some View {
        List(viewModel.items, id: \.self) { item in
            Text(item)
        }
        .task {
            await viewModel.fetchItems()
        }
    }
}
```

**5. Immutable Copy Pattern:**
```swift
class DataManager {
    private var _data: [String] = []
    private let queue = DispatchQueue(label: "com.app.data")
    
    // Return copy, not reference
    var data: [String] {
        return queue.sync { _data }
    }
    
    func append(_ item: String) {
        queue.async {
            self._data.append(item)
        }
    }
}

// Reads get snapshot, no synchronization needed
let manager = DataManager()
let snapshot = manager.data  // Safe copy
```

**Real-World iOS Examples (2025):**

**1. Network Response Caching:**
```swift
actor NetworkCache {
    private var cache: [String: Data] = [:]
    
    func cache(_ data: Data, for key: String) {
        cache[key] = data
    }
    
    func getData(for key: String) -> Data? {
        return cache[key]
    }
}

// Usage in network layer
class APIClient {
    private let cache = NetworkCache()
    
    func fetch(url: String) async throws -> Data {
        // Check cache first
        if let cached = await cache.getData(for: url) {
            return cached
        }
        
        // Fetch from network
        let data = try await URLSession.shared.data(from: URL(string: url)!).0
        
        // Cache result
        await cache.cache(data, for: url)
        
        return data
    }
}
```

**2. Download Queue:**
```swift
actor DownloadManager {
    private var activeDownloads: Set<String> = []
    private var completedDownloads: [String: Data] = [:]
    
    func startDownload(id: String) -> Bool {
        guard !activeDownloads.contains(id) else {
            return false  // Already downloading
        }
        activeDownloads.insert(id)
        return true
    }
    
    func completeDownload(id: String, data: Data) {
        activeDownloads.remove(id)
        completedDownloads[id] = data
    }
    
    func isDownloading(_ id: String) -> Bool {
        return activeDownloads.contains(id)
    }
}
```

**3. SwiftUI List with Concurrent Updates:**
```swift
@MainActor
class ItemStore: ObservableObject {
    @Published private(set) var items: [Item] = []
    
    func loadItems() async {
        // Fetch in background
        let fetchedItems = await fetchFromAPI()
        
        // Update on main thread automatically (@MainActor)
        items = fetchedItems
    }
    
    func addItem(_ item: Item) {
        items.append(item)  // Safe - on main thread
    }
}
```

**Best Practices (2025):**

1. **Use Actors for shared mutable state** (Swift 5.5+)
2. **Use @MainActor for UI-related code**
3. **Avoid locks when possible** (use actors/queues)
4. **Return copies, not references** (value semantics help!)
5. **Document thread safety guarantees**

**Common Mistakes:**
```swift
// ❌ Don't use global variables without protection
var globalArray = [String]()

// ✅ Do use actor or queue
actor GlobalStore {
    var array = [String]()
}

// ❌ Don't directly mutate from background
DispatchQueue.global().async {
    self.tableData.append(item)  // Race condition!
}

// ✅ Do use main queue for UI updates
DispatchQueue.global().async {
    let item = processData()
    DispatchQueue.main.async {
        self.tableData.append(item)
    }
}
```

**2025 Recommendation:** Use **actors** for new code - they're the modern, safe way to handle concurrency in Swift.

---

## 📝 Practice Exercises

1. Create an array of 10 numbers, filter evens, double them, and calculate the sum using map/filter/reduce
2. Build a simple dictionary-based phone book with add, remove, and search functions
3. Use Sets to find common elements between two arrays (intersection)
4. Write a function that takes an array of optionals and uses compactMap to remove nils
5. Create a tuple-based coordinate system and calculate distance between two points

---

## 🔗 Navigation
- [← Previous: Step 5 - Optionals](../Step-5-Optionals/README.md)
- [→ Next Phase: Phase 2 - Functions & Closures](../../Phase-2-Functions-and-Closures/Step-7-Functions/README.md)

---

## 🎉 Phase 1 Complete!

Congratulations! You've completed **Phase 1: Swift Fundamentals**. You now understand:
- ✅ Setup and basic syntax
- ✅ Data types and variables
- ✅ Operators
- ✅ Control flow
- ✅ Optionals (critical!)
- ✅ Collections (Arrays, Dictionaries, Sets, Tuples)

**Next:** Move on to **Phase 2: Functions & Closures** to learn functional programming concepts in Swift!
