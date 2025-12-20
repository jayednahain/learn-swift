# Step 12: Structs vs Classes ⭐ IMPORTANT

## 📚 Description

Understanding when to use **structs** versus **classes** is **fundamental** to writing idiomatic Swift code. This decision affects performance, memory management, code safety, and architecture. Apple recommends **structs by default**, making this one of the most important concepts to master.

### What You'll Learn:
- Value semantics vs reference semantics
- Performance implications
- Copy-on-write optimization
- When to choose struct vs class
- Real-world decision making
- Common pitfalls and best practices

### Key Concepts:
- **Value Types**: Structs, enums - copied on assignment
- **Reference Types**: Classes - shared on assignment
- **Copy-on-Write**: Performance optimization for collections
- **Thread Safety**: Value types are inherently safer
- **Identity vs Equality**: When object identity matters

---

## 💡 Clear Examples

### Example 1: Value Semantics vs Reference Semantics - The Core Difference

```swift
// ========== STRUCT (VALUE TYPE) ==========
struct Rectangle {
    var width: Double
    var height: Double
    
    var area: Double {
        return width * height
    }
}

var rect1 = Rectangle(width: 10, height: 20)
var rect2 = rect1  // COPIES the entire struct

rect2.width = 30

print("rect1: \(rect1.width) x \(rect1.height)")  // 10 x 20 (unchanged)
print("rect2: \(rect2.width) x \(rect2.height)")  // 30 x 20 (changed)

// Independent copies - changing one doesn't affect the other

// ========== CLASS (REFERENCE TYPE) ==========
class RectangleClass {
    var width: Double
    var height: Double
    
    var area: Double {
        return width * height
    }
    
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
}

var rect3 = RectangleClass(width: 10, height: 20)
var rect4 = rect3  // REFERENCES the same instance

rect4.width = 30

print("rect3: \(rect3.width) x \(rect3.height)")  // 30 x 20 (changed!)
print("rect4: \(rect4.width) x \(rect4.height)")  // 30 x 20 (changed!)

// Shared instance - changing one affects the other

// ========== VISUAL REPRESENTATION ==========
/*
STRUCT (Value Type):
rect1 → [Memory A] { width: 10, height: 20 }
rect2 → [Memory B] { width: 30, height: 20 }  // Separate copy

CLASS (Reference Type):
rect3 → [Memory C] { width: 30, height: 20 }
rect4 → [Memory C]  // Points to same memory!
*/

// ========== PASSING TO FUNCTIONS ==========

func modifyStruct(_ rect: inout Rectangle) {
    rect.width = 50
}

func modifyClass(_ rect: RectangleClass) {
    rect.width = 50
}

var structRect = Rectangle(width: 100, height: 200)
modifyStruct(&structRect)  // Requires 'inout'
print(structRect.width)  // 50 (modified)

var classRect = RectangleClass(width: 100, height: 200)
modifyClass(classRect)  // No 'inout' needed
print(classRect.width)  // 50 (modified)

// Classes are always passed by reference
// Structs are passed by value (use 'inout' to modify)

// ========== MUTABILITY ==========

// Struct: 'let' makes entire struct immutable
let structConst = Rectangle(width: 10, height: 20)
// structConst.width = 30  // ❌ Error: struct is immutable

// Class: 'let' only prevents reassignment
let classConst = RectangleClass(width: 10, height: 20)
classConst.width = 30  // ✅ Allowed! Properties can change
print(classConst.width)  // 30

// classConst = RectangleClass(width: 40, height: 50)  // ❌ Error: can't reassign

// ========== COLLECTIONS ==========

var structArray = [Rectangle(width: 10, height: 20)]
var structArray2 = structArray  // Copy of array

structArray2[0].width = 99

print(structArray[0].width)   // 10 (unchanged)
print(structArray2[0].width)  // 99 (changed)

var classArray = [RectangleClass(width: 10, height: 20)]
var classArray2 = classArray  // Array copied, but elements are references

classArray2[0].width = 99

print(classArray[0].width)   // 99 (changed!)
print(classArray2[0].width)  // 99 (same instance)

// Arrays themselves use copy-on-write
// But array elements keep their semantics (value or reference)
```

**Explanation:**
- Structs: Independent copies, local modifications
- Classes: Shared instances, modifications visible everywhere
- `let` means different things for structs vs classes
- Understanding this difference is **critical** for Swift

---

### Example 2: Performance and Memory Characteristics

```swift
import Foundation

// ========== MEMORY ALLOCATION ==========

struct StackStruct {
    var a: Int
    var b: Int
    var c: Int
}

class HeapClass {
    var a: Int
    var b: Int
    var c: Int
    
    init(a: Int, b: Int, c: Int) {
        self.a = a
        self.b = b
        self.c = c
    }
}

// Structs: Usually stack-allocated (fast)
// - Fast allocation/deallocation
// - No reference counting overhead
// - Better cache locality

// Classes: Heap-allocated (slower)
// - Dynamic allocation (slower)
// - Reference counting overhead (ARC)
// - Indirect access (pointer dereference)

// ========== BENCHMARK EXAMPLE ==========

func measureStructPerformance() -> TimeInterval {
    let start = Date()
    
    for _ in 0..<1_000_000 {
        var s = StackStruct(a: 1, b: 2, c: 3)
        s.a = 10
        _ = s.a + s.b
    }
    
    return Date().timeIntervalSince(start)
}

func measureClassPerformance() -> TimeInterval {
    let start = Date()
    
    for _ in 0..<1_000_000 {
        let c = HeapClass(a: 1, b: 2, c: 3)
        c.a = 10
        _ = c.a + c.b
    }
    
    return Date().timeIntervalSince(start)
}

let structTime = measureStructPerformance()
let classTime = measureClassPerformance()

print("Struct: \(structTime)s")
print("Class:  \(classTime)s")
print("Class is ~\(classTime / structTime)x slower")

// Typical results: Classes 2-3x slower

// ========== COPY-ON-WRITE OPTIMIZATION ==========

// Swift's Array uses COW for efficiency
var array1 = Array(repeating: 0, count: 1_000_000)
var array2 = array1  // No copy yet! Shares storage

// Modify triggers copy
array2[0] = 1  // NOW copies (only when needed)

// Custom COW implementation
final class Storage<T> {
    var value: T
    init(_ value: T) { self.value = value }
}

struct COWExample<T> {
    private var storage: Storage<T>
    
    init(_ value: T) {
        storage = Storage(value)
    }
    
    var value: T {
        get {
            return storage.value
        }
        set {
            if !isKnownUniquelyReferenced(&storage) {
                // Copy only if shared
                storage = Storage(newValue)
            } else {
                // Modify in place if not shared
                storage.value = newValue
            }
        }
    }
}

var cow1 = COWExample([1, 2, 3, 4, 5])
var cow2 = cow1  // Shares storage (fast)

// Now triggers copy
cow2.value.append(6)  // Copies because shared

// ========== MEMORY FOOTPRINT ==========

// Struct: Size = sum of properties
struct SmallStruct {
    var a: Int  // 8 bytes
    var b: Int  // 8 bytes
    // Total: 16 bytes
}

// Class: Size = pointer (8 bytes) + heap allocation
class SmallClass {
    var a: Int  // 8 bytes
    var b: Int  // 8 bytes
    // Total: 8 bytes (pointer) + 16 bytes (heap) + overhead
    // Plus ARC counters and metadata
}

print(MemoryLayout<SmallStruct>.size)  // 16 bytes
print(MemoryLayout<SmallClass>.size)   // 8 bytes (pointer only)

// But actual memory usage:
// SmallStruct: 16 bytes
// SmallClass: ~40-50 bytes (pointer + heap + metadata + ARC)

// ========== THREAD SAFETY ==========

// Struct: Thread-safe by default (each thread has own copy)
struct ThreadSafeCounter {
    var count = 0
}

func incrementStruct() {
    var counter = ThreadSafeCounter()
    for _ in 0..<1000 {
        counter.count += 1
    }
    print("Struct counter: \(counter.count)")  // Always 1000
}

// Run concurrently
DispatchQueue.concurrentPerform(iterations: 10) { _ in
    incrementStruct()
}

// Class: NOT thread-safe (shared instance needs synchronization)
class UnsafeCounter {
    var count = 0
}

let sharedCounter = UnsafeCounter()

func incrementClass() {
    for _ in 0..<1000 {
        sharedCounter.count += 1  // ⚠️  Race condition!
    }
}

// DispatchQueue.concurrentPerform(iterations: 10) { _ in
//     incrementClass()
// }
// print(sharedCounter.count)  // Random result < 10000

// Need synchronization for classes:
class SafeCounter {
    private var count = 0
    private let lock = NSLock()
    
    func increment() {
        lock.lock()
        count += 1
        lock.unlock()
    }
    
    var value: Int {
        lock.lock()
        defer { lock.unlock() }
        return count
    }
}

// ========== CACHE LOCALITY ==========

// Structs in array: contiguous memory (better cache performance)
let structArray = [StackStruct(a: 1, b: 2, c: 3),
                   StackStruct(a: 4, b: 5, c: 6),
                   StackStruct(a: 7, b: 8, c: 9)]
// Memory: [1,2,3,4,5,6,7,8,9] (contiguous, cache-friendly)

// Classes in array: scattered heap objects (worse cache performance)
let classArray = [HeapClass(a: 1, b: 2, c: 3),
                  HeapClass(a: 4, b: 5, c: 6),
                  HeapClass(a: 7, b: 8, c: 9)]
// Memory: [ptr1, ptr2, ptr3] → scattered heap objects
```

**Explanation:**
- Structs: Stack, fast, thread-safe, better cache locality
- Classes: Heap, slower, ARC overhead, require synchronization
- Copy-on-write optimizes struct performance
- Prefer structs for performance-critical code

---

### Example 3: When to Use Structs vs Classes - Decision Guide

```swift
// ========== USE STRUCT: SIMPLE DATA MODELS ==========

// ✅ Struct for value-like types
struct Point {
    var x: Double
    var y: Double
}

struct Size {
    var width: Double
    var height: Double
}

struct Color {
    var red: Double
    var green: Double
    var blue: Double
    var alpha: Double
}

// Why struct?
// - Represents simple values
// - No identity needed (two Points with x=10, y=20 are identical)
// - Want independent copies
// - Small data structure

let point1 = Point(x: 10, y: 20)
let point2 = Point(x: 10, y: 20)
// These are considered equivalent (same values)

// ========== USE CLASS: WHEN IDENTITY MATTERS ==========

// ✅ Class for entities with identity
class Person {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let alice1 = Person(name: "Alice", age: 30)
let alice2 = Person(name: "Alice", age: 30)
// These are DIFFERENT people (different identities)

print(alice1 === alice2)  // false (different persons)

// Why class?
// - Identity matters (each person is unique)
// - Need to track specific instance
// - Shared mutable state desired

// ========== USE STRUCT: IMMUTABLE DATA ==========

// ✅ Struct for configuration
struct AppConfiguration {
    let apiKey: String
    let baseURL: String
    let timeout: TimeInterval
    let maxRetries: Int
}

// Why struct?
// - Data doesn't change
// - Want snapshots/versions
// - Safe to copy

// ========== USE CLASS: SHARED MUTABLE STATE ==========

// ✅ Class for services/managers
class NetworkManager {
    static let shared = NetworkManager()
    
    var isConnected = false
    var requestsInFlight: Set<UUID> = []
    
    private init() {}
    
    func makeRequest() {
        // All parts of app use same instance
    }
}

// Why class?
// - Singleton pattern
// - Global state
// - Need same instance everywhere

// ========== USE STRUCT: NO INHERITANCE NEEDED ==========

// ✅ Struct with protocol conformance
protocol Drawable {
    func draw()
}

struct Circle: Drawable {
    var radius: Double
    
    func draw() {
        print("Drawing circle with radius \(radius)")
    }
}

struct Rectangle: Drawable {
    var width: Double
    var height: Double
    
    func draw() {
        print("Drawing rectangle \(width)x\(height)")
    }
}

// Why struct?
// - No inheritance needed
// - Protocol provides polymorphism
// - Composition over inheritance

// ========== USE CLASS: INHERITANCE REQUIRED ==========

// ✅ Class hierarchy
class UIViewController {
    func viewDidLoad() {
        print("Base view loaded")
    }
}

class ProfileViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        print("Profile view loaded")
    }
}

// Why class?
// - Inheritance required
// - Override behavior
// - UIKit/AppKit require classes

// ========== USE STRUCT: FUNCTION PARAMETERS ==========

struct UserProfile {
    var name: String
    var email: String
    var avatar: URL?
}

func updateProfile(_ profile: UserProfile) {
    // Receives copy - original unchanged
    var mutableProfile = profile
    mutableProfile.name = "Updated"
    print(mutableProfile.name)
}

var profile = UserProfile(name: "Alice", email: "alice@example.com", avatar: nil)
updateProfile(profile)
print(profile.name)  // "Alice" (unchanged)

// Why struct?
// - Clear semantics (copy)
// - No unintended mutations
// - Safer API

// ========== USE CLASS: NEED DEINITIALIZER ==========

// ✅ Class for resource management
class FileHandle {
    let path: String
    
    init(path: String) {
        self.path = path
        print("Opening file: \(path)")
    }
    
    deinit {
        print("Closing file: \(path)")
        // Release resources
    }
}

func processFile() {
    let file = FileHandle(path: "/tmp/data.txt")
    // Use file
}  // deinit called here

processFile()

// Why class?
// - Need cleanup (deinit)
// - Resource management
// - RAII pattern

// ========== REAL-WORLD EXAMPLES ==========

// ✅ STRUCT: SwiftUI Views
struct ContentView: View {
    var body: some View {
        Text("Hello")
    }
}

// Why struct?
// - Lightweight
// - Created/destroyed frequently
// - Value semantics for state

// ✅ CLASS: View Models
class ContentViewModel: ObservableObject {
    @Published var items: [Item] = []
    @Published var isLoading = false
    
    func loadData() {
        // Shared state across views
    }
}

// Why class?
// - ObservableObject requires class
// - Shared state
// - Identity matters

// ✅ STRUCT: API Models
struct User: Codable {
    let id: String
    let name: String
    let email: String
}

// Why struct?
// - Data transfer object
// - Immutable after decoding
// - No identity needed

// ✅ CLASS: Coordinator
class AppCoordinator {
    weak var navigationController: UINavigationController?
    
    func showDetail(_ item: Item) {
        let vc = DetailViewController(item: item)
        navigationController?.pushViewController(vc, animated: true)
    }
}

// Why class?
// - Manages navigation flow
// - Weak references needed
// - Single instance

// ========== DECISION FLOWCHART ==========

func chooseType() {
    /*
    Start Here
    ↓
    Does it need inheritance?
    ├─ YES → Class
    └─ NO  → Does it need deinit?
        ├─ YES → Class
        └─ NO  → Does identity matter?
            ├─ YES → Class
            └─ NO  → Does it need to be shared (singleton)?
                ├─ YES → Class
                └─ NO  → Must it be a class (UIKit, ObservableObject)?
                    ├─ YES → Class
                    └─ NO  → USE STRUCT ✅
    */
}

// ========== MIXED APPROACH ==========

// Often use both together:

// Struct for data
struct Article {
    let id: UUID
    var title: String
    var content: String
}

// Class for service
class ArticleService {
    static let shared = ArticleService()
    
    func fetch() async throws -> [Article] {
        // Returns array of structs
        return []
    }
}

// Class for view model
class ArticleListViewModel: ObservableObject {
    @Published var articles: [Article] = []
    
    private let service = ArticleService.shared
    
    func loadArticles() async {
        articles = try! await service.fetch()
    }
}

// Struct for view
struct ArticleListView: View {
    @StateObject var viewModel = ArticleListViewModel()
    
    var body: some View {
        List(viewModel.articles, id: \.id) { article in
            Text(article.title)
        }
    }
}
```

**Explanation:**
- Default to structs unless you need class features
- Classes for identity, inheritance, Objective-C interop
- Structs for data, values, safety
- Mix both based on needs

---

## 🎯 2025 Interview Questions

### Question 1: Explain the performance difference between structs and classes. When does struct copying become expensive?

**Answer:**

**Performance Characteristics:**

**Structs:**
- **Stack allocation**: Fast (just moving stack pointer)
- **No ARC overhead**: No reference counting
- **Inline storage**: Better cache locality
- **Copy cost**: O(n) where n = size of struct

**Classes:**
- **Heap allocation**: Slower (malloc/free)
- **ARC overhead**: Reference counting on every assignment
- **Pointer indirection**: Extra memory access
- **Copy cost**: O(1) (just copying reference)

**Benchmark:**

```swift
import Foundation

// Small struct (fast)
struct SmallStruct {
    var a: Int
    var b: Int
}

// Large struct (expensive to copy)
struct LargeStruct {
    var data: [Int] = Array(repeating: 0, count: 10000)
}

// Class (always fast to copy reference)
class DataClass {
    var data: [Int] = Array(repeating: 0, count: 10000)
}

// Test small struct
func testSmallStruct() -> TimeInterval {
    let start = Date()
    var s = SmallStruct(a: 1, b: 2)
    for _ in 0..<1_000_000 {
        var copy = s  // Fast copy (16 bytes)
        copy.a = 10
    }
    return Date().timeIntervalSince(start)
}

// Test large struct
func testLargeStruct() -> TimeInterval {
    let start = Date()
    var s = LargeStruct()
    for _ in 0..<10_000 {
        var copy = s  // Expensive! Copies 80KB
        copy.data[0] = 10
    }
    return Date().timeIntervalSince(start)
}

// Test class
func testClass() -> TimeInterval {
    let start = Date()
    let c = DataClass()
    for _ in 0..<1_000_000 {
        let copy = c  // Fast (just 8-byte pointer)
        copy.data[0] = 10
    }
    return Date().timeIntervalSince(start)
}

print("Small struct: \(testSmallStruct())s")
print("Large struct: \(testLargeStruct())s")  // Much slower!
print("Class:        \(testClass())s")
```

**When Struct Copying Becomes Expensive:**

**1. Large Data:**
```swift
// ❌ Bad: Large struct
struct BadImage {
    var pixels: [UInt8] = Array(repeating: 0, count: 1920 * 1080 * 4)
    // 8MB! Expensive to copy
}

// ✅ Better: Use class or COW
final class GoodImage {
    var pixels: [UInt8]
    
    init() {
        pixels = Array(repeating: 0, count: 1920 * 1080 * 4)
    }
}
```

**2. Many Nested Structs:**
```swift
// ❌ Expensive: Nested structs
struct Layer {
    var data: [Double] = Array(repeating: 0, count: 1000)
}

struct DeepStruct {
    var layers: [Layer] = Array(repeating: Layer(), count: 100)
    // 100 * 1000 * 8 bytes = 800KB
}

var deep1 = DeepStruct()
var deep2 = deep1  // Copies 800KB!
```

**3. Frequent Copying:**
```swift
// ❌ Bad: Struct passed frequently
struct HeavyData {
    var values: [Double] = Array(repeating: 0, count: 10000)
}

func process(_ data: HeavyData) {  // Copies on every call
    // Use data
}

for _ in 0..<1000 {
    process(heavy)  // 1000 copies!
}

// ✅ Better: Pass by reference or use inout
func process(_ data: inout HeavyData) {  // No copy
    // Modify in place
}
```

**Copy-on-Write to the Rescue:**

Swift's Array uses COW, so even "large" structs are efficient:

```swift
struct EfficientStruct {
    var data: [Int] = Array(repeating: 0, count: 1_000_000)
    // Array uses COW - no immediate copy!
}

var s1 = EfficientStruct()
var s2 = s1  // Fast! Shares array storage

// Only copies when modified
s2.data[0] = 42  // NOW copies
```

**Guidelines:**

| Struct Size | Copy Cost | Recommendation |
|-------------|-----------|----------------|
| < 64 bytes | Negligible | Always use struct |
| 64-512 bytes | Small | Usually struct |
| > 512 bytes | Notable | Consider COW or class |
| > 4KB | Expensive | Use class or COW |

**Real-World Optimization:**

```swift
// ✅ Optimal: Small value types
struct Point {
    var x, y: Double  // 16 bytes
}

struct Color {
    var r, g, b, a: UInt8  // 4 bytes
}

// ✅ Optimal: COW for large data
struct Document {
    private var storage: [Character]  // COW array
    
    var text: String {
        get { String(storage) }
        set { storage = Array(newValue) }
    }
}

// ❌ Problematic: Large fixed array
struct BadBuffer {
    var data: (Int, Int, Int, /* ... 1000 more */)
    // Huge tuple, expensive to copy
}

// ✅ Better: Use unsafe buffer or class
final class GoodBuffer {
    var data: UnsafeMutableBufferPointer<Int>
    
    init(count: Int) {
        data = UnsafeMutableBufferPointer.allocate(capacity: count)
    }
    
    deinit {
        data.deallocate()
    }
}
```

**Key Takeaway:** Structs are fast for small data. For large data, use classes or ensure copy-on-write is in effect.

---

### Question 2: What is copy-on-write (COW), and how does Swift implement it for collections?

**Answer:**

**Copy-on-Write (COW)** delays copying until a modification happens, giving you value semantics with reference-like performance.

**The Problem Without COW:**

```swift
// Naive struct implementation
struct NaiveArray {
    private var storage: [Int]
    
    init(_ elements: [Int]) {
        self.storage = elements  // Immediate copy
    }
    
    subscript(index: Int) -> Int {
        get { storage[index] }
        set { storage[index] = newValue }
    }
}

var arr1 = NaiveArray(Array(0..<1_000_000))
var arr2 = arr1  // ❌ Copies 1 million integers immediately!
// Expensive even if arr2 never modified
```

**Swift's COW Implementation:**

```swift
// Simplified version of how Swift's Array works
final class Storage<T> {
    var elements: [T]
    
    init(_ elements: [T]) {
        self.elements = elements
    }
}

struct COWArray<T> {
    private var storage: Storage<T>
    
    init(_ elements: [T]) {
        storage = Storage(elements)
    }
    
    var count: Int {
        return storage.elements.count
    }
    
    subscript(index: Int) -> T {
        get {
            return storage.elements[index]
        }
        set {
            // Check if storage is uniquely referenced
            if !isKnownUniquelyReferenced(&storage) {
                // Shared! Make a copy
                print("⚠️  Making copy")
                storage = Storage(storage.elements)
            }
            // Now modify
            storage.elements[index] = newValue
        }
    }
}

var arr1 = COWArray([1, 2, 3, 4, 5])
var arr2 = arr1  // Fast! Just copies reference

print("Reading arr2[0]...")
print(arr2[0])  // No copy needed

print("Modifying arr2[0]...")
arr2[0] = 99  // Triggers copy
// Output: ⚠️  Making copy

print("arr1[0]: \(arr1[0])")  // 1 (unchanged)
print("arr2[0]: \(arr2[0])")  // 99 (modified copy)
```

**How Swift's Array Uses COW:**

```swift
var array1 = [1, 2, 3, 4, 5]
var array2 = array1  // Shares storage (no copy)

// Both reference same storage buffer
// array1 → [Storage A: [1, 2, 3, 4, 5]]
// array2 → [Storage A: [1, 2, 3, 4, 5]]

// Modify array2
array2.append(6)  // Triggers copy

// Now separate storage
// array1 → [Storage A: [1, 2, 3, 4, 5]]
// array2 → [Storage B: [1, 2, 3, 4, 5, 6]]

print(array1)  // [1, 2, 3, 4, 5]
print(array2)  // [1, 2, 3, 4, 5, 6]
```

**Key Function: `isKnownUniquelyReferenced`**

```swift
final class Box<T> {
    var value: T
    init(_ value: T) { self.value = value }
}

var box1 = Box(42)
var box2 = box1

print(isKnownUniquelyReferenced(&box1))  // false (shared)

box2 = Box(99)
print(isKnownUniquelyReferenced(&box1))  // true (unique)
```

**Real-World COW Implementation:**

```swift
// Custom COW container
final class Storage<T> {
    var value: T
    
    init(_ value: T) {
        self.value = value
    }
    
    func copy() -> Storage<T> {
        return Storage(value)
    }
}

struct COWBox<T> {
    private var storage: Storage<T>
    
    init(_ value: T) {
        storage = Storage(value)
    }
    
    var value: T {
        get {
            return storage.value
        }
        set {
            if !isKnownUniquelyReferenced(&storage) {
                storage = storage.copy()
            }
            storage.value = newValue
        }
    }
    
    mutating func modify(_ transform: (inout T) -> Void) {
        if !isKnownUniquelyReferenced(&storage) {
            storage = storage.copy()
        }
        transform(&storage.value)
    }
}

// Usage
struct Document {
    private var content: COWBox<String>
    
    init(_ text: String) {
        content = COWBox(text)
    }
    
    var text: String {
        get { content.value }
        set { content.value = newValue }
    }
    
    mutating func append(_ text: String) {
        content.modify { $0 += text }
    }
}

var doc1 = Document("Hello")
var doc2 = doc1  // Shares storage

doc2.append(" World")  // Triggers copy

print(doc1.text)  // "Hello"
print(doc2.text)  // "Hello World"
```

**Performance Benefits:**

```swift
// Without COW
let start1 = Date()
for _ in 0..<10000 {
    var arr = Array(0..<1000)
    let copy = arr  // Immediate copy
}
let time1 = Date().timeIntervalSince(start1)

// With COW (Swift's actual behavior)
let start2 = Date()
for _ in 0..<10000 {
    var arr = Array(0..<1000)
    let copy = arr  // Shared storage
    // No modification, no copy
}
let time2 = Date().timeIntervalSince(start2)

print("Without COW: \(time1)s")
print("With COW:    \(time2)s")
print("Speedup:     \(time1 / time2)x")
// Typical: 100x+ faster
```

**Swift Standard Library COW Types:**

- ✅ `Array` - Uses COW
- ✅ `Dictionary` - Uses COW
- ✅ `Set` - Uses COW
- ✅ `String` - Uses COW
- ❌ `Int`, `Double`, etc. - Value types (no need for COW)

**When COW Happens:**

```swift
var arr = [1, 2, 3]

// ✅ No copy (read-only)
let count = arr.count
let first = arr[0]
let isEmpty = arr.isEmpty

// ✅ Triggers copy (mutation)
arr.append(4)
arr[0] = 10
arr.remove(at: 0)

// ✅ No copy (creates new array)
let mapped = arr.map { $0 * 2 }
let filtered = arr.filter { $0 > 1 }
```

**Key Takeaway:** COW gives value semantics (safety) with reference performance (speed).

---

### Question 3: In SwiftUI, why are views always structs and not classes?

**Answer:**

SwiftUI **requires views to be structs** for performance, safety, and architectural reasons.

**1. Lightweight and Cheap to Create**

SwiftUI creates and destroys views frequently. Structs are ideal:

```swift
// ✅ Struct view (lightweight)
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello")
            Button("Tap") { }
        }
    }
}

// SwiftUI creates this view many times per second
// Stack allocation is fast
```

**2. Value Semantics for State**

Views are **data**, not objects. They describe UI, not manage it:

```swift
struct CounterView: View {
    let count: Int  // View is snapshot of state
    
    var body: some View {
        Text("Count: \(count)")
    }
}

// Each render creates new view struct with current count
// Old view is discarded, new one created
```

**3. Immutability by Default**

Structs enforce immutability, preventing accidental mutations:

```swift
struct ProfileView: View {
    let user: User
    
    var body: some View {
        // ❌ Can't do: user.name = "New Name"
        // View doesn't own data, just displays it
        Text(user.name)
    }
}
```

**4. Efficient Diffing**

SwiftUI compares views to detect changes. Structs are fast to compare:

```swift
struct ItemRow: View {
    let item: Item
    
    var body: some View {
        Text(item.name)
    }
}

// SwiftUI diffs:
// Old: ItemRow(item: Item(name: "Old"))
// New: ItemRow(item: Item(name: "New"))
// → Only rebuilds if different
```

**5. No Reference Cycles**

Structs can't create retain cycles:

```swift
// ✅ No memory leaks possible
struct ContentView: View {
    @StateObject var viewModel = ViewModel()
    
    var body: some View {
        Button("Action") {
            viewModel.action()  // No [weak self] needed
        }
    }
}
```

**6. Composition Over Inheritance**

SwiftUI uses protocols and composition, not inheritance:

```swift
struct CustomView: View {  // Conforms to protocol
    var body: some View {
        VStack {
            HeaderView()  // Composition
            ContentArea()
            FooterView()
        }
    }
}

// No need for base classes
```

**Why NOT Classes?**

```swift
// ❌ Hypothetical class view (problems)
class BadView: View {
    var count = 0  // Mutable state
    
    var body: some View {
        Button("Increment") {
            self.count += 1  // Mutation doesn't trigger re-render!
        }
        Text("\(count)")
    }
}

// Problems:
// 1. Mutation doesn't notify SwiftUI
// 2. Reference semantics break diffing
// 3. Potential retain cycles
// 4. Heap allocation overhead
// 5. Identity confusion (which instance is "this view"?)
```

**Correct SwiftUI Pattern:**

```swift
// ✅ View is struct, state is in ObservableObject
struct CounterView: View {
    @StateObject private var viewModel = CounterViewModel()
    
    var body: some View {
        VStack {
            Text("Count: \(viewModel.count)")
            Button("Increment") {
                viewModel.increment()
            }
        }
    }
}

class CounterViewModel: ObservableObject {
    @Published var count = 0
    
    func increment() {
        count += 1  // @Published triggers view update
    }
}
```

**Performance:**

```swift
// Struct view (fast)
struct ListView: View {
    let items: [Item]
    
    var body: some View {
        List(items) { item in
            ItemRow(item: item)  // Creates struct for each row
        }
    }
}

// SwiftUI creates 1000s of ItemRow structs per frame
// Stack allocation makes this feasible
// Classes would be too slow (heap allocation)
```

**Apple's Design Choice:**

From WWDC 2019:
> "Views are cheap. Create them, throw them away, create new ones. They're just struct descriptions of what should be on screen."

**Real Example:**

```swift
struct MessagesView: View {
    let messages: [Message]
    
    var body: some View {
        List(messages) { message in
            MessageRow(message: message)
        }
    }
}

struct MessageRow: View {
    let message: Message
    
    var body: some View {
        HStack {
            Text(message.sender)
            Text(message.text)
        }
    }
}

// For 100 messages:
// - 1 MessagesView struct
// - 100 MessageRow structs
// All created fresh each render
// Fast because they're structs (stack allocated)
```

**Key Takeaway:** Views are **descriptions** (data), not **objects** (identity). Structs are perfect for this.

---

### Question 4: Explain when you should use a struct with protocol conformance vs a class with inheritance.

**Answer:**

This is a **fundamental Swift design decision**: Protocol-Oriented Programming (POP) vs Object-Oriented Programming (OOP).

**Struct + Protocol (Preferred in Swift):**

```swift
// Define behavior
protocol Drawable {
    func draw()
}

// Implement with structs
struct Circle: Drawable {
    var radius: Double
    
    func draw() {
        print("Drawing circle, radius: \(radius)")
    }
}

struct Rectangle: Drawable {
    var width: Double
    var height: Double
    
    func draw() {
        print("Drawing rectangle: \(width)x\(height)")
    }
}

struct Triangle: Drawable {
    var base: Double
    var height: Double
    
    func draw() {
        print("Drawing triangle: base \(base), height \(height)")
    }
}

// Use polymorphically
let shapes: [Drawable] = [
    Circle(radius: 5),
    Rectangle(width: 10, height: 20),
    Triangle(base: 8, height: 12)
]

shapes.forEach { $0.draw() }
```

**Class + Inheritance (Traditional OOP):**

```swift
// Base class
class Shape {
    func draw() {
        fatalError("Must override")
    }
}

// Subclasses
class CircleClass: Shape {
    var radius: Double
    
    init(radius: Double) {
        self.radius = radius
    }
    
    override func draw() {
        print("Drawing circle, radius: \(radius)")
    }
}

class RectangleClass: Shape {
    var width: Double
    var height: Double
    
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
    
    override func draw() {
        print("Drawing rectangle: \(width)x\(height)")
    }
}
```

**When to Use Struct + Protocol:**

**1. Multiple "Inheritances" Needed:**

```swift
// ✅ Struct can conform to multiple protocols
protocol Drawable {
    func draw()
}

protocol Animatable {
    func animate()
}

protocol Serializable {
    func toJSON() -> String
}

struct Button: Drawable, Animatable, Serializable {
    func draw() { }
    func animate() { }
    func toJSON() -> String { return "{}" }
}

// ❌ Class can only inherit from one class
// class Button: Drawable, Animatable { }  // Not possible with classes
```

**2. Value Semantics Desired:**

```swift
protocol Configurable {
    var name: String { get set }
}

struct Config: Configurable {
    var name: String
}

var config1 = Config(name: "Original")
var config2 = config1

config2.name = "Modified"

print(config1.name)  // "Original" (independent)
print(config2.name)  // "Modified"
```

**3. No Shared State:**

```swift
protocol Calculator {
    func calculate(_ x: Int, _ y: Int) -> Int
}

struct AddCalculator: Calculator {
    func calculate(_ x: Int, _ y: Int) -> Int {
        return x + y
    }
}

struct MultiplyCalculator: Calculator {
    func calculate(_ x: Int, _ y: Int) -> Int {
        return x * y
    }
}

// Each instance is independent
// No risk of shared mutable state
```

**4. Protocol Extensions for Default Behavior:**

```swift
protocol Vehicle {
    var wheels: Int { get }
    func describe() -> String
}

extension Vehicle {
    func describe() -> String {
        return "A vehicle with \(wheels) wheels"
    }
}

struct Car: Vehicle {
    let wheels = 4
    // Gets describe() for free
}

struct Motorcycle: Vehicle {
    let wheels = 2
}

let vehicles: [Vehicle] = [Car(), Motorcycle()]
vehicles.forEach { print($0.describe()) }
```

**When to Use Class + Inheritance:**

**1. Shared Mutable State:**

```swift
class ViewController: UIViewController {
    var data: [Item] = []
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Setup
    }
}

class DetailViewController: ViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        // Additional setup
        // Can access parent's data
    }
}
```

**2. Inheritance IS the Right Model:**

```swift
// Clear "is-a" relationship
class Animal {
    func eat() {
        print("Eating...")
    }
}

class Mammal: Animal {
    func breathe() {
        print("Breathing air...")
    }
}

class Dog: Mammal {
    func bark() {
        print("Woof!")
    }
}

let dog = Dog()
dog.eat()      // From Animal
dog.breathe()  // From Mammal
dog.bark()     // Own method
```

**3. Objective-C Interop:**

```swift
// Must use class to inherit from Objective-C classes
class CustomView: UIView {
    override func draw(_ rect: CGRect) {
        super.draw(rect)
        // Custom drawing
    }
}
```

**4. Identity Matters:**

```swift
class User {
    let id: UUID
    var name: String
    
    init(id: UUID, name: String) {
        self.id = id
        self.name = name
    }
}

let user1 = User(id: UUID(), name: "Alice")
let user2 = user1  // Same user

print(user1 === user2)  // true (same identity)
```

**Real-World Comparison:**

**Protocol-Oriented (Modern Swift):**

```swift
// Storage protocol
protocol Storage {
    func save(_ data: Data, for key: String)
    func load(for key: String) -> Data?
}

// Multiple implementations
struct UserDefaultsStorage: Storage {
    func save(_ data: Data, for key: String) {
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func load(for key: String) -> Data? {
        return UserDefaults.standard.data(forKey: key)
    }
}

struct KeychainStorage: Storage {
    func save(_ data: Data, for key: String) {
        // Keychain save
    }
    
    func load(for key: String) -> Data? {
        // Keychain load
        return nil
    }
}

struct FileStorage: Storage {
    func save(_ data: Data, for key: String) {
        // File save
    }
    
    func load(for key: String) -> Data? {
        // File load
        return nil
    }
}

// Easy to swap implementations
func save(to storage: Storage) {
    storage.save(Data(), for: "key")
}
```

**Class-Based (Traditional OOP):**

```swift
class BaseStorage {
    func save(_ data: Data, for key: String) {
        fatalError("Must override")
    }
    
    func load(for key: String) -> Data? {
        fatalError("Must override")
    }
}

class UserDefaultsStorageClass: BaseStorage {
    override func save(_ data: Data, for key: String) {
        UserDefaults.standard.set(data, forKey: key)
    }
    
    override func load(for key: String) -> Data? {
        return UserDefaults.standard.data(forKey: key)
    }
}

// Requires subclassing
```

**Decision Matrix:**

| Need | Struct + Protocol | Class + Inheritance |
|------|-------------------|---------------------|
| Multiple behaviors | ✅ Multiple protocols | ❌ Single inheritance |
| Value semantics | ✅ Natural | ❌ Reference semantics |
| Default implementations | ✅ Protocol extensions | ⚠️  Base class methods |
| Shared state | ❌ Not designed for | ✅ Natural |
| UIKit/AppKit | ❌ Can't subclass | ✅ Required |
| Identity | ❌ No identity | ✅ Has identity |
| Performance | ✅ Stack, fast | ⚠️  Heap, slower |
| Thread safety | ✅ Safer | ⚠️  Needs synchronization |

**Apple's Guidance:**

From Swift team:
> "Prefer structs with protocol conformance. Use classes only when you need inheritance, identity, or Objective-C interoperability."

**Key Takeaway:** Default to structs + protocols (POP). Use classes + inheritance only when specifically needed.

---

### Question 5: What happens when you pass a large struct to a function? Is it always copied?

**Answer:**

**Short Answer:** No, not always! Swift optimizes away unnecessary copies.

**Naive Understanding (Wrong):**

```swift
struct LargeStruct {
    var data: [Int] = Array(repeating: 0, count: 10000)
}

func process(_ large: LargeStruct) {
    // Reads data
    print(large.data.count)
}

let myStruct = LargeStruct()
process(myStruct)  // ❌ Doesn't always copy!
```

**Reality: Swift Optimizations**

**1. Read-Only Access (No Copy):**

```swift
struct BigData {
    var array: [Int] = Array(repeating: 0, count: 100000)
}

func readOnly(_ data: BigData) {
    // Just reading - no mutation
    print(data.array.count)
    let first = data.array.first
    
    // Swift doesn't copy!
    // Passes by reference internally
}

let big = BigData()
readOnly(big)  // No copy! Swift optimizes
```

**2. Copy-on-Write:**

```swift
struct Document {
    var content: [Character]
}

func modify(_ doc: Document) {
    var mutableDoc = doc  // No copy yet (COW)
    
    // Now triggers copy
    mutableDoc.content.append("!")
}

let doc = Document(content: Array("Hello"))
modify(doc)  // Copy only happens at mutation
```

**3. Compiler Optimizations:**

```swift
func createAndReturn() -> LargeStruct {
    let s = LargeStruct()
    return s  // No copy! Return value optimization
}

let result = createAndReturn()  // Direct construction
```

**4. Inout Parameters (No Copy):**

```swift
func modifyInPlace(_ data: inout BigData) {
    data.array[0] = 42
    // Modifies original, no copy
}

var big = BigData()
modifyInPlace(&big)  // Passed by reference
```

**When Copies Actually Happen:**

**1. Explicit Mutation:**

```swift
func actualCopy(_ data: BigData) {
    var mutableData = data  // Potentially copied
    mutableData.array[0] = 99  // Definitely copied here
}
```

**2. Escaping Closures:**

```swift
func escape(_ data: BigData) {
    DispatchQueue.global().async {
        // data captured - likely copied
        print(data.array.count)
    }
}
```

**3. Non-COW Types:**

```swift
struct Custom {
    var tuple: (Int, Int, Int, Int)  // Not COW
    var fixed: [Int] = [1, 2, 3]      // Small array
}

func process(_ c: Custom) {
    var modified = c  // Full copy (no COW)
    modified.tuple.0 = 42
}
```

**Performance Test:**

```swift
import Foundation

struct Huge {
    var data: [Int]
    
    init(size: Int) {
        data = Array(repeating: 0, count: size)
    }
}

// Test read-only
func testReadOnly() -> TimeInterval {
    let huge = Huge(size: 1_000_000)
    let start = Date()
    
    for _ in 0..<10000 {
        readOnly(huge)  // Passed 10000 times
    }
    
    return Date().timeIntervalSince(start)
}

func readOnly(_ h: Huge) {
    _ = h.data.count  // Just reading
}

// Test mutation
func testMutation() -> TimeInterval {
    let start = Date()
    
    for _ in 0..<10000 {
        var huge = Huge(size: 1_000_000)
        mutate(&huge)  // Passed 10000 times
    }
    
    return Date().timeIntervalSince(start)
}

func mutate(_ h: inout Huge) {
    h.data[0] = 42  // Mutating
}

let readTime = testReadOnly()
let mutateTime = testMutation()

print("Read-only: \(readTime)s")     // Very fast
print("Mutation:  \(mutateTime)s")    // Much slower
```

**Best Practices:**

**1. Use `inout` for Large Structs:**

```swift
// ❌ Potentially expensive
func update(_ config: AppConfig) -> AppConfig {
    var modified = config
    modified.setting = "new"
    return modified
}

// ✅ Efficient
func update(_ config: inout AppConfig) {
    config.setting = "new"
}
```

**2. Don't Worry About Small Structs:**

```swift
struct Point {
    var x, y: Double  // 16 bytes
}

func process(_ point: Point) {
    // Copy is trivial, don't use inout
}
```

**3. Use Copy-on-Write Types:**

```swift
// ✅ Efficient: Array uses COW
struct Efficient {
    var items: [Item]  // COW array
}

// ❌ Inefficient: Tuple doesn't use COW
struct Inefficient {
    var items: (Item, Item, Item, /* ... many more */)
}
```

**4. Measure, Don't Guess:**

```swift
// Use Instruments to profile
// Check actual copy behavior
// Optimize based on measurements
```

**Swift Compiler Optimizations:**

The compiler applies **aggressive optimizations**:

- **Return Value Optimization (RVO)**
- **Move semantics** (similar to C++)
- **Inline function calls**
- **Devirtualization**

**Example:**

```swift
// Looks like 3 copies:
func create() -> BigData {
    return BigData()  // Copy 1?
}

func transform(_ data: BigData) -> BigData {
    var modified = data  // Copy 2?
    modified.array[0] = 42
    return modified      // Copy 3?
}

let result = transform(create())

// Reality: Compiler optimizes to 0-1 copies!
```

**Key Takeaway:**

- Swift is **smart** about copying
- Read-only access often doesn't copy
- Copy-on-write prevents unnecessary copies
- Use `inout` for explicit in-place modification
- **Measure, don't assume**

---

## 📝 Practice Exercises

1. Create a performance comparison tool that measures struct vs class speed
2. Implement a custom copy-on-write container
3. Build a data model using only structs and protocols (no classes)
4. Refactor a class hierarchy into a protocol-oriented design
5. Create a large data structure and benchmark copy performance

---

## 🔗 Navigation
- [← Previous: Step 11 - Classes](../Step-11-Classes/README.md)
- [→ Next: Step 13 - Properties](../Step-13-Properties/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 12!** This is one of the most important concepts in Swift. You now understand:
- ✅ Value semantics vs reference semantics
- ✅ Performance implications
- ✅ Copy-on-write optimization
- ✅ When to use struct vs class
- ✅ Protocol-oriented programming
- ✅ Real-world decision making
- ✅ Swift's design philosophy

**Next:** Deep dive into **Properties** in Step 13!
