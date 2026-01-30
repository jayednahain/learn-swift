# Step 18: Generics

## 📚 Description

**Generics** enable you to write flexible, reusable code that works with any type while maintaining type safety. They are the foundation of Swift's most powerful features, including Arrays, Dictionaries, Optionals, and the standard library. Generics eliminate code duplication and enable abstract, type-safe solutions.

### What You'll Learn:
- Generic functions and types
- Type parameters and constraints
- Associated types in protocols
- Generic where clauses
- Type erasure patterns
- Advanced generic patterns

### Key Concepts:
- **Generic**: Code that works with any type
- **Type Parameter**: Placeholder type (e.g., `<T>`)
- **Type Constraint**: Restricting type parameters (e.g., `<T: Equatable>`)
- **Associated Type**: Placeholder in protocol definition
- **Where Clause**: Additional constraints on generic types
- **Type Erasure**: Hiding concrete generic types

### Benefits:
- **Code Reuse**: Write once, works with many types
- **Type Safety**: Compiler enforces types at compile-time
- **Performance**: No runtime overhead (unlike type erasure in some languages)
- **Abstraction**: Express algorithms independently of specific types
- **Standard Library**: Foundation of Swift's collections and protocols

---

## 💡 Clear Examples

### Example 1: Generic Functions and Basic Type Parameters

```swift
// ========== NON-GENERIC APPROACH (BAD) ==========

func swapInts(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

func swapStrings(_ a: inout String, _ b: inout String) {
    let temp = a
    a = b
    b = temp
}

// Code duplication! ❌

// ========== GENERIC APPROACH (GOOD) ==========

func swap<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

var x = 5
var y = 10
swap(&x, &y)
print(x, y)  // 10 5

var str1 = "Hello"
var str2 = "World"
swap(&str1, &str2)
print(str1, str2)  // World Hello

// ========== GENERIC FUNCTION WITH RETURN TYPE ==========

func makeArray<T>(repeating value: T, count: Int) -> [T] {
    var result: [T] = []
    for _ in 0..<count {
        result.append(value)
    }
    return result
}

let integers = makeArray(repeating: 42, count: 3)
// [42, 42, 42]

let strings = makeArray(repeating: "Swift", count: 2)
// ["Swift", "Swift"]

// ========== MULTIPLE TYPE PARAMETERS ==========

func combine<T, U>(_ first: T, _ second: U) -> (T, U) {
    return (first, second)
}

let result1 = combine(42, "Answer")
// (42, "Answer") - type is (Int, String)

let result2 = combine(3.14, true)
// (3.14, true) - type is (Double, Bool)

// ========== GENERIC FUNCTION WITH CONSTRAINTS ==========

func findMax<T: Comparable>(_ array: [T]) -> T? {
    guard !array.isEmpty else { return nil }
    
    var maximum = array[0]
    for item in array[1...] {
        if item > maximum {
            maximum = item
        }
    }
    return maximum
}

let maxInt = findMax([3, 7, 2, 9, 5])
print(maxInt ?? 0)  // 9

let maxString = findMax(["apple", "zebra", "banana"])
print(maxString ?? "")  // zebra

// Won't compile with non-Comparable types:
// struct Person { var name: String }
// let maxPerson = findMax([Person(name: "A")])  // ❌ Error
```

**Key Points:**
- Use `<T>` for type parameters
- Can have multiple type parameters `<T, U, V>`
- Constraints using `:` (e.g., `<T: Comparable>`)
- Works with any type meeting constraints

---

### Example 2: Generic Types (Structs, Classes, Enums)

```swift
// ========== GENERIC STACK ==========

struct Stack<Element> {
    private var items: [Element] = []
    
    var isEmpty: Bool {
        return items.isEmpty
    }
    
    var count: Int {
        return items.count
    }
    
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
    
    func peek() -> Element? {
        return items.last
    }
}

// Usage with Int
var intStack = Stack<Int>()
intStack.push(1)
intStack.push(2)
intStack.push(3)
print(intStack.pop() ?? 0)  // 3
print(intStack.peek() ?? 0)  // 2

// Usage with String
var stringStack = Stack<String>()
stringStack.push("Swift")
stringStack.push("Generics")
print(stringStack.pop() ?? "")  // "Generics"

// ========== GENERIC RESULT TYPE ==========

enum Result<Success, Failure: Error> {
    case success(Success)
    case failure(Failure)
    
    var value: Success? {
        if case .success(let val) = self {
            return val
        }
        return nil
    }
    
    func map<NewSuccess>(_ transform: (Success) -> NewSuccess) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return .success(transform(value))
        case .failure(let error):
            return .failure(error)
        }
    }
}

enum NetworkError: Error {
    case timeout
    case invalidResponse
}

// Usage
let successResult: Result<Int, NetworkError> = .success(42)
let failureResult: Result<Int, NetworkError> = .failure(.timeout)

if let value = successResult.value {
    print("Got value: \(value)")  // Got value: 42
}

// Map over result
let doubled = successResult.map { $0 * 2 }
print(doubled.value ?? 0)  // 84

// ========== GENERIC WRAPPER ==========

struct Box<Value> {
    var value: Value
    
    func map<U>(_ transform: (Value) -> U) -> Box<U> {
        return Box<U>(value: transform(value))
    }
}

let intBox = Box(value: 42)
let stringBox = intBox.map { "The answer is \($0)" }
print(stringBox.value)  // The answer is 42

// ========== GENERIC QUEUE ==========

struct Queue<T> {
    private var elements: [T] = []
    
    mutating func enqueue(_ element: T) {
        elements.append(element)
    }
    
    mutating func dequeue() -> T? {
        guard !elements.isEmpty else { return nil }
        return elements.removeFirst()
    }
    
    func peek() -> T? {
        return elements.first
    }
    
    var count: Int {
        return elements.count
    }
}

var queue = Queue<String>()
queue.enqueue("First")
queue.enqueue("Second")
queue.enqueue("Third")

print(queue.dequeue() ?? "")  // First
print(queue.peek() ?? "")     // Second
print(queue.count)            // 2
```

**Key Points:**
- Generic types use `<Element>` in declaration
- Can be structs, classes, or enums
- Type parameter used throughout the implementation
- Each instance has its own concrete type

---

### Example 3: Associated Types and Protocol Generics

```swift
// ========== PROTOCOL WITH ASSOCIATED TYPE ==========

protocol Container {
    associatedtype Item
    
    var count: Int { get }
    mutating func append(_ item: Item)
    subscript(index: Int) -> Item { get }
}

// ========== CONFORMING WITH SPECIFIC TYPE ==========

struct IntStack: Container {
    // Type inference: Item = Int
    private var items: [Int] = []
    
    var count: Int {
        return items.count
    }
    
    mutating func append(_ item: Int) {
        items.append(item)
    }
    
    subscript(index: Int) -> Int {
        return items[index]
    }
}

// ========== GENERIC TYPE CONFORMING TO PROTOCOL ==========

struct GenericStack<Element>: Container {
    // Type inference: Item = Element
    private var items: [Element] = []
    
    var count: Int {
        return items.count
    }
    
    mutating func append(_ item: Element) {
        items.append(item)
    }
    
    subscript(index: Int) -> Element {
        return items[index]
    }
}

var stack = GenericStack<String>()
stack.append("Swift")
stack.append("Generics")
print(stack[0])  // Swift

// ========== ASSOCIATED TYPE WITH CONSTRAINTS ==========

protocol SummableContainer {
    associatedtype Item: Numeric
    var items: [Item] { get }
}

extension SummableContainer {
    func sum() -> Item {
        return items.reduce(0, +)
    }
}

struct NumberContainer<T: Numeric>: SummableContainer {
    var items: [T]
}

let intContainer = NumberContainer(items: [1, 2, 3, 4, 5])
print(intContainer.sum())  // 15

let doubleContainer = NumberContainer(items: [1.5, 2.5, 3.5])
print(doubleContainer.sum())  // 7.5

// ========== PROTOCOL WITH MULTIPLE ASSOCIATED TYPES ==========

protocol Graph {
    associatedtype Node: Hashable
    associatedtype Edge
    
    func neighbors(of node: Node) -> [Node]
    func edges(from node: Node) -> [Edge]
}

struct SimpleGraph: Graph {
    typealias Node = String
    typealias Edge = (from: String, to: String)
    
    private var adjacencyList: [String: [String]] = [:]
    
    func neighbors(of node: String) -> [String] {
        return adjacencyList[node] ?? []
    }
    
    func edges(from node: String) -> [(from: String, to: String)] {
        return neighbors(of: node).map { (from: node, to: $0) }
    }
}

// ========== GENERIC FUNCTION WITH ASSOCIATED TYPE ==========

func allItemsMatch<C1: Container, C2: Container>(_ container1: C1, _ container2: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {
    
    guard container1.count == container2.count else {
        return false
    }
    
    for i in 0..<container1.count {
        if container1[i] != container2[i] {
            return false
        }
    }
    
    return true
}

var stack1 = GenericStack<String>()
stack1.append("Swift")
stack1.append("Generics")

var stack2 = GenericStack<String>()
stack2.append("Swift")
stack2.append("Generics")

print(allItemsMatch(stack1, stack2))  // true
```

**Key Points:**
- `associatedtype` creates placeholder in protocol
- Swift infers associated type from implementation
- Can constrain associated types
- Use `where` clauses for complex constraints

---

## 🎯 Interview Questions

### Q1: What are generics and why are they important in Swift? Explain with examples.

**Answer:**

**Generics** allow you to write flexible, reusable code that works with any type while maintaining type safety at compile time.

**Without Generics (Code Duplication):**
```swift
func addInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func addDoubles(_ a: Double, _ b: Double) -> Double {
    return a + b
}

func addStrings(_ a: String, _ b: String) -> String {
    return a + b
}
```

**With Generics (Reusable):**
```swift
func add<T: AdditiveArithmetic>(_ a: T, _ b: T) -> T {
    return a + b
}

let intResult = add(5, 3)          // 8
let doubleResult = add(5.5, 3.3)   // 8.8
```

**Why Important:**

1. **Type Safety**: Compile-time type checking
```swift
var intArray: [Int] = [1, 2, 3]
// intArray.append("string")  // ❌ Compile error
```

2. **Code Reuse**: Write once, works with many types
```swift
struct Pair<T, U> {
    var first: T
    var second: U
}

let intStringPair = Pair(first: 42, second: "answer")
let boolDoublePair = Pair(first: true, second: 3.14)
```

3. **Performance**: No runtime overhead (unlike type erasure)
```swift
// Generic version is just as fast as specific type version
func identity<T>(_ value: T) -> T {
    return value
}
```

4. **Standard Library Foundation**: Arrays, Dictionaries, Optionals are all generic
```swift
// Array<Element>, Dictionary<Key, Value>, Optional<Wrapped>
let array: Array<Int> = [1, 2, 3]
let dict: Dictionary<String, Int> = ["one": 1]
let optional: Optional<String> = "value"
```

**Real-world Example:**
```swift
// API Response handler
struct APIResponse<T: Decodable> {
    let data: T
    let statusCode: Int
}

// Works with any Decodable type
let userResponse = APIResponse(data: User(), statusCode: 200)
let productResponse = APIResponse(data: Product(), statusCode: 200)
```

---

### Q2: Explain associated types in protocols. How do they differ from generic type parameters?

**Answer:**

**Associated Types** are placeholders in protocols that are specified by the conforming type. They're similar to generics but work at the protocol level.

**Generic Type Parameter (Concrete Type):**
```swift
struct Stack<Element> {  // Element is known upfront
    private var items: [Element] = []
    
    mutating func push(_ item: Element) { }
    func pop() -> Element? { return nil }
}

// Must specify type when creating instance
let intStack = Stack<Int>()  // Element = Int
```

**Associated Type (Protocol):**
```swift
protocol Container {
    associatedtype Item  // Item determined by conformer
    
    mutating func append(_ item: Item)
    func get(at index: Int) -> Item?
}

// Type inferred from implementation
struct StringContainer: Container {
    // Item = String (inferred)
    private var items: [String] = []
    
    mutating func append(_ item: String) {
        items.append(item)
    }
    
    func get(at index: Int) -> String? {
        guard index < items.count else { return nil }
        return items[index]
    }
}
```

**Key Differences:**

| Generic Type Parameter | Associated Type |
|----------------------|----------------|
| Used in concrete types | Used in protocols |
| Specified when creating instance | Inferred from conformance |
| `Stack<Int>` | Compiler infers from implementation |
| Can have multiple parameters | Can have multiple associated types |

**Complex Example:**
```swift
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    associatedtype Element where Iterator.Element == Element
    
    func makeIterator() -> Iterator
}

// Conforming type
struct MySequence: Sequence {
    typealias Element = Int
    typealias Iterator = IndexingIterator<[Int]>
    
    private let items: [Int]
    
    func makeIterator() -> IndexingIterator<[Int]> {
        return items.makeIterator()
    }
}
```

**When to Use:**
- **Generic Type Parameters**: When building concrete types
- **Associated Types**: When designing protocol requirements

**Combining Both:**
```swift
protocol Repository {
    associatedtype Model
    
    func save(_ model: Model)
    func load() -> Model?
}

// Generic type conforming to protocol
class GenericRepository<T>: Repository {
    typealias Model = T  // Associated type = generic parameter
    
    func save(_ model: T) { }
    func load() -> T? { return nil }
}

let userRepo = GenericRepository<User>()
```

---

### Q3: What are generic constraints and where clauses? Provide examples of complex constraints.

**Answer:**

**Generic Constraints** restrict type parameters to types that satisfy certain requirements using protocols, classes, or where clauses.

**Basic Constraints:**
```swift
// Constraint with protocol
func findIndex<T: Equatable>(of value: T, in array: [T]) -> Int? {
    for (index, item) in array.enumerated() {
        if item == value {
            return index
        }
    }
    return nil
}

// Constraint with class
func processViewController<T: UIViewController>(_ vc: T) {
    // T must be UIViewController or subclass
}

// Multiple constraints
func compare<T: Comparable & CustomStringConvertible>(_ a: T, _ b: T) {
    if a > b {
        print("\(a.description) is greater than \(b.description)")
    }
}
```

**Where Clauses:**
```swift
// Where clause for additional constraints
func allItemsMatch<C1: Collection, C2: Collection>(
    _ collection1: C1,
    _ collection2: C2
) -> Bool where C1.Element == C2.Element, C1.Element: Equatable {
    guard collection1.count == collection2.count else {
        return false
    }
    
    for (item1, item2) in zip(collection1, collection2) {
        if item1 != item2 {
            return false
        }
    }
    return true
}

let array1 = [1, 2, 3]
let array2 = [1, 2, 3]
print(allItemsMatch(array1, array2))  // true
```

**Complex Constraints:**
```swift
// Multiple where clauses
func merge<C1: Collection, C2: Collection>(
    _ col1: C1,
    _ col2: C2
) -> [C1.Element] where 
    C1.Element == C2.Element,
    C1.Element: Comparable {
    
    return (col1 + col2).sorted()
}

// Constraining associated types
protocol Graph {
    associatedtype Node: Hashable
    associatedtype Edge
    
    func addEdge(_ edge: Edge)
}

func printGraph<G: Graph>(_ graph: G) where G.Node == String {
    // Only works with graphs where nodes are Strings
}

// Extension with constraints
extension Array where Element: Numeric {
    func sum() -> Element {
        return reduce(0, +)
    }
}

[1, 2, 3].sum()  // Works
// ["a", "b"].sum()  // ❌ Error: String not Numeric

// Conditional conformance
extension Array: Equatable where Element: Equatable {
    // Array is Equatable only if elements are
}

// Generic type with where clause
struct Wrapper<T> where T: Codable & Hashable {
    let value: T
    
    func encoded() -> Data? {
        return try? JSONEncoder().encode(value)
    }
}
```

**Real-World Example:**
```swift
// Repository with complex constraints
protocol Repository {
    associatedtype Model: Identifiable & Codable
    
    func fetch(id: Model.ID) async throws -> Model
    func save(_ model: Model) async throws
}

// Function working with repositories
func syncData<R: Repository>(
    from repository: R
) async throws where 
    R.Model.ID == UUID {
    
    let id = UUID()
    let model = try await repository.fetch(id: id)
    try await repository.save(model)
}
```

---

### Q4: What is type erasure and when do you need it? Implement an example.

**Answer:**

**Type Erasure** hides specific generic type information, allowing you to store different generic types in the same collection or variable. It's needed when protocol with associated types (PAT) creates constraints.

**The Problem:**
```swift
protocol Container {
    associatedtype Item
    func getItem() -> Item
}

struct IntContainer: Container {
    func getItem() -> Int { return 42 }
}

struct StringContainer: Container {
    func getItem() -> String { return "Hello" }
}

// ❌ Error: Protocol with associated type can't be used as type
// let containers: [Container] = [IntContainer(), StringContainer()]
```

**Solution: Type Erasure**
```swift
// Type-erased wrapper
struct AnyContainer<T>: Container {
    typealias Item = T
    
    private let _getItem: () -> T
    
    init<C: Container>(_ container: C) where C.Item == T {
        _getItem = container.getItem
    }
    
    func getItem() -> T {
        return _getItem()
    }
}

// Now it works!
struct IntContainer: Container {
    func getItem() -> Int { return 42 }
}

struct DoubleIntContainer: Container {
    func getItem() -> Int { return 84 }
}

let containers: [AnyContainer<Int>] = [
    AnyContainer(IntContainer()),
    AnyContainer(DoubleIntContainer())
]

for container in containers {
    print(container.getItem())
}
// Output: 42, 84
```

**Real-World Example: AnyPublisher (Combine)**
```swift
protocol Publisher {
    associatedtype Output
    associatedtype Failure: Error
    
    func subscribe<S: Subscriber>(_ subscriber: S) 
        where S.Input == Output, S.Failure == Failure
}

// Type-erased version
struct AnyPublisher<Output, Failure: Error>: Publisher {
    private let _subscribe: (AnySubscriber<Output, Failure>) -> Void
    
    init<P: Publisher>(_ publisher: P) 
        where P.Output == Output, P.Failure == Failure {
        _subscribe = { subscriber in
            publisher.subscribe(AnySubscriber(subscriber))
        }
    }
    
    func subscribe<S: Subscriber>(_ subscriber: S) 
        where S.Input == Output, S.Failure == Failure {
        _subscribe(AnySubscriber(subscriber))
    }
}

// Usage
let publisher1: AnyPublisher<Int, Never> = /* ... */
let publisher2: AnyPublisher<Int, Never> = /* ... */
let publishers = [publisher1, publisher2]  // ✅ Works!
```

**Another Example: AnySequence**
```swift
struct AnySequence<Element>: Sequence {
    private let _makeIterator: () -> AnyIterator<Element>
    
    init<S: Sequence>(_ sequence: S) where S.Element == Element {
        _makeIterator = {
            var iterator = sequence.makeIterator()
            return AnyIterator { iterator.next() }
        }
    }
    
    func makeIterator() -> AnyIterator<Element> {
        return _makeIterator()
    }
}

// Usage
let array: AnySequence<Int> = AnySequence([1, 2, 3])
let set: AnySequence<Int> = AnySequence(Set([4, 5, 6]))
let sequences = [array, set]  // ✅ Can mix different sequence types
```

**When to Use Type Erasure:**
- Storing protocol types with associated types in collections
- Returning protocol types from functions
- Hiding implementation details
- Working with Combine publishers
- SwiftUI views (`AnyView`)

**Downsides:**
- Performance overhead (indirect calls)
- Loses type information
- More complex code
- Larger binary size

---

### Q5: How do generics work under the hood in Swift? Explain specialization and witness tables.

**Answer:**

Swift uses **generic specialization** and **witness tables** for generics implementation, balancing performance and code size.

**Generic Specialization:**

When compiler knows concrete types, it creates specialized versions:

```swift
func swap<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

swap(&x, &y)  // where x, y are Int
// Compiler generates: swap_Int(inout Int, inout Int)

swap(&str1, &str2)  // where str1, str2 are String
// Compiler generates: swap_String(inout String, inout String)
```

**Benefit**: Optimal performance (inlined, no overhead)
**Cost**: Larger binary size (multiple versions)

**Witness Tables (Protocol Witness Table - PWT):**

For protocol constraints, Swift uses witness tables (similar to vtables):

```swift
protocol Drawable {
    func draw()
}

struct Circle: Drawable {
    func draw() { print("Circle") }
}

struct Square: Drawable {
    func draw() { print("Square") }
}

func render<T: Drawable>(_ shape: T) {
    shape.draw()
}

render(Circle())
render(Square())
```

**Under the hood:**
```
Circle_Drawable_Witness_Table:
    - draw: Circle.draw

Square_Drawable_Witness_Table:
    - draw: Square.draw

render<T>(shape: T, witnessTable: ProtocolWitnessTable):
    witnessTable.draw(shape)
```

**Existential Containers:**

When using protocol as type (not generic), Swift uses existential containers:

```swift
let shape: Drawable = Circle()  // Existential container
shape.draw()
```

Container holds:
- Value buffer (inline for small types, heap for large)
- Metadata pointer (type info)
- Witness table pointers

**Performance Comparison:**
```swift
// 1. Generic (fastest - specialized)
func process<T: Protocol>(value: T) { }

// 2. Existential (slower - dynamic dispatch)
func process(value: Protocol) { }

// 3. Type-erased (slowest - extra indirection)
func process(value: AnyProtocol) { }
```

**Optimization Flags:**

```swift
// @_specialize tells compiler to create specialized version
@_specialize(where T == Int)
@_specialize(where T == String)
func genericFunction<T>(_ value: T) {
    // ...
}
```

**Key Takeaways:**
1. **Specialization**: Creates type-specific versions (fast, larger binary)
2. **Witness Tables**: Dynamic dispatch for protocols (slower, smaller binary)
3. **Existential Containers**: Store protocol types with overhead
4. **Generic code**: Almost zero cost when specialized
5. **Protocol types**: Have runtime cost (acceptable trade-off)

**Practical Impact:**
```swift
// Fast: Generic with specialization
func sum<T: Numeric>(_ array: [T]) -> T {
    return array.reduce(0, +)
}

// Slower: Protocol type
func sum(_ array: [any Numeric]) -> any Numeric {
    return array.reduce(0, +)
}
```

---

## 📝 Practice Exercises

### Exercise 1: Generic Data Structures
Implement commonly used generic data structures.

**Requirements:**
1. Create a generic `LinkedList<Element>`:
   - `append(_ value: Element)`
   - `removeFirst() -> Element?`
   - `removeLast() -> Element?`
   - Make it conform to `Collection`

2. Create a generic `BinaryTree<T: Comparable>`:
   - `insert(_ value: T)`
   - `contains(_ value: T) -> Bool`
   - `inOrderTraversal() -> [T]`

3. Create a generic `LRUCache<Key: Hashable, Value>`:
   - `get(_ key: Key) -> Value?`
   - `set(_ key: Key, value: Value)`
   - Maximum capacity with eviction

**Challenge:** Add `where` clauses to enable special operations for specific types.

---

### Exercise 2: Protocol-Oriented Repository
Build a complete generic repository system.

**Requirements:**
1. Create protocol with associated types:
   ```swift
   protocol Repository {
       associatedtype Model: Identifiable & Codable
       associatedtype Error: Swift.Error
   }
   ```

2. Add constrained extensions for:
   - `where Model: Equatable`: Implement `contains(_ model: Model)`
   - `where Model: Comparable`: Implement `sorted()` method

3. Implement type-erased wrapper `AnyRepository<Model>`

4. Create concrete implementations:
   - `MemoryRepository<T>`
   - `FileRepository<T>`

**Challenge:** Add async/await support and error handling.

---

### Exercise 3: Generic Functional Programming
Implement functional programming utilities.

**Requirements:**
1. Create generic `Result<Success, Failure: Error>` with:
   - `map<U>(_ transform: (Success) -> U) -> Result<U, Failure>`
   - `flatMap<U>(_ transform: (Success) -> Result<U, Failure>)`
   - `mapError<E: Error>(_ transform: (Failure) -> E)`

2. Create generic `Optional` extensions:
   - `flatMap` for chaining optionals
   - `zip` for combining multiple optionals

3. Create generic `Either<Left, Right>`:
   - Similar to Result but without Error constraint
   - Implement functor and monad operations

**Challenge:** Create a generic parser combinator library.

---

## 🔗 Key Takeaways

1. **Generics enable reusability** - Write once, works with many types
2. **Type safety at compile time** - No runtime type checking overhead
3. **Type parameters** - Use `<T>` for placeholder types
4. **Constraints** - Restrict types with protocols (`<T: Equatable>`)
5. **Associated types** - Placeholders in protocols
6. **Where clauses** - Complex generic constraints
7. **Type erasure** - Hide generic type details (use sparingly)
8. **Specialization** - Compiler optimizes for concrete types
9. **Standard library foundation** - Arrays, Dictionaries, Optionals
10. **Balance** - Generic code vs. code duplication

**Best Practices:**
- Use descriptive type parameter names (not just `T`)
- Add constraints to enable operations
- Document generic types thoroughly
- Prefer generics over `Any` or type erasure
- Use `where` clauses for readability

**Remember:** Generics are essential to writing clean, reusable Swift code!
