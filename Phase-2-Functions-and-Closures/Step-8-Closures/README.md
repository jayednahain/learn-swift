# Step 8: Closures

## 📚 Description

Closures are self-contained blocks of functionality that can be passed around and used in your code. They're similar to lambdas in Java or anonymous functions in JavaScript. Closures are one of Swift's most powerful features, enabling functional programming patterns and making code more concise and expressive.

### What You'll Learn:
- What closures are and why they're useful
- Closure syntax and expression
- Trailing closure syntax
- Capturing values from surrounding context
- Escaping closures (`@escaping`)
- Autoclosures (`@autoclosure`)
- Common use cases: map, filter, reduce, sorted

### Key Concepts:
- **Closures**: Unnamed functions that can capture variables from their context
- **Closure Expressions**: Lightweight syntax for inline closures
- **Capturing**: Closures can "remember" and modify variables from their scope
- **Escaping**: Closures that outlive the function they're passed to
- **Higher-Order Functions**: Functions that take closures as parameters

---

## 💡 Clear Examples

### Example 1: Closure Syntax and Basics

```swift
// NAMED FUNCTION (from previous step)
func add(a: Int, b: Int) -> Int {
    return a + b
}

let result1 = add(a: 5, b: 3)
print(result1)  // 8

// CLOSURE (anonymous function)
let addClosure = { (a: Int, b: Int) -> Int in
    return a + b
}

let result2 = addClosure(5, 3)
print(result2)  // 8

// Closure Syntax Breakdown:
// { (parameters) -> ReturnType in
//     code
// }

// Full closure syntax
let greet = { (name: String) -> String in
    return "Hello, \(name)!"
}
print(greet("Alice"))  // Hello, Alice!

// Simplified: Type inference
let greet2 = { (name: String) in
    return "Hello, \(name)!"
}

// More simplified: Implicit return (single expression)
let greet3 = { (name: String) in "Hello, \(name)!" }

// Even more simplified: Shorthand argument names ($0, $1, etc.)
let add2: (Int, Int) -> Int = { $0 + $1 }
print(add2(10, 5))  // 15

// Multiple shorthand arguments
let subtract: (Int, Int) -> Int = { $0 - $1 }
let multiply: (Int, Int) -> Int = { $0 * $1 }
let divide: (Int, Int) -> Int = { $0 / $1 }

print(subtract(10, 3))  // 7
print(multiply(4, 5))   // 20

// Closure with no parameters
let sayHello: () -> Void = {
    print("Hello!")
}
sayHello()  // Hello!

// Closure with multiple statements
let processNumber: (Int) -> String = { number in
    let doubled = number * 2
    let message = "Doubled value is \(doubled)"
    return message
}
print(processNumber(5))  // Doubled value is 10

// CLOSURES AS FUNCTION PARAMETERS

func performOperation(_ a: Int, _ b: Int, operation: (Int, Int) -> Int) -> Int {
    return operation(a, b)
}

// Pass closure inline
let sum = performOperation(10, 5, operation: { (a, b) in
    return a + b
})
print("Sum: \(sum)")  // 15

// Simplified with trailing closure (explained next)
let product = performOperation(10, 5) { $0 * $1 }
print("Product: \(product)")  // 50

// Real array operations
let numbers = [1, 2, 3, 4, 5]

// map - transform each element
let doubled = numbers.map { $0 * 2 }
print(doubled)  // [2, 4, 6, 8, 10]

// filter - keep elements matching condition
let evens = numbers.filter { $0 % 2 == 0 }
print(evens)  // [2, 4]

// reduce - combine into single value
let total = numbers.reduce(0) { $0 + $1 }
print(total)  // 15

// sorted - sort with custom logic
let names = ["Charlie", "Alice", "Bob"]
let sorted = names.sorted { $0 < $1 }
print(sorted)  // ["Alice", "Bob", "Charlie"]
```

**Explanation:**
- Closures are inline, unnamed functions
- Syntax progressively simplifies with type inference
- Shorthand argument names (`$0`, `$1`) make code concise
- Closures can be assigned to variables or passed as parameters
- Single-expression closures have implicit return

---

### Example 2: Trailing Closure Syntax and Capturing Values

```swift
// TRAILING CLOSURE SYNTAX
// If closure is the last parameter, you can write it outside ()

// Standard syntax
let result1 = numbers.map({ $0 * 2 })

// Trailing closure syntax
let result2 = numbers.map { $0 * 2 }

// More readable for complex closures
let filtered = numbers.filter { number in
    let isEven = number % 2 == 0
    let isGreaterThanTwo = number > 2
    return isEven && isGreaterThanTwo
}
print(filtered)  // [4]

// Multiple parameters with trailing closure
func performAsync(delay: Int, completion: () -> Void) {
    print("Starting...")
    // Simulate delay
    completion()
}

// Without trailing closure
performAsync(delay: 2, completion: {
    print("Done!")
})

// With trailing closure (cleaner)
performAsync(delay: 2) {
    print("Done!")
}

// CAPTURING VALUES
// Closures can capture and store references to variables

func makeIncrementer(incrementAmount: Int) -> () -> Int {
    var total = 0
    
    // Closure captures 'total' and 'incrementAmount'
    let incrementer: () -> Int = {
        total += incrementAmount  // Modifies captured variable
        return total
    }
    
    return incrementer
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)
print(incrementByTwo())  // 2
print(incrementByTwo())  // 4
print(incrementByTwo())  // 6

let incrementByTen = makeIncrementer(incrementAmount: 10)
print(incrementByTen())  // 10
print(incrementByTen())  // 20

// Each closure has its own copy of captured variables
print(incrementByTwo())  // 8 (independent from incrementByTen)

// Capturing in Array Operations
var multiplier = 3
let multiplied = numbers.map { $0 * multiplier }
print(multiplied)  // [3, 6, 9, 12, 15]

multiplier = 5  // Changes captured variable
let multiplied2 = numbers.map { $0 * multiplier }
print(multiplied2)  // [5, 10, 15, 20, 25]

// CLOSURE WITH CUSTOM TYPES

struct Person {
    let name: String
    let age: Int
}

let people = [
    Person(name: "Alice", age: 30),
    Person(name: "Bob", age: 25),
    Person(name: "Charlie", age: 35)
]

// Sort by age
let sortedByAge = people.sorted { $0.age < $1.age }
sortedByAge.forEach { print("\($0.name): \($0.age)") }
/* Output:
Bob: 25
Alice: 30
Charlie: 35
*/

// Filter adults over 30
let over30 = people.filter { $0.age > 30 }
print(over30.map { $0.name })  // ["Charlie"]

// Map to just names
let names = people.map { $0.name }
print(names)  // ["Alice", "Bob", "Charlie"]

// Complex chaining
let result = people
    .filter { $0.age >= 30 }
    .sorted { $0.name < $1.name }
    .map { $0.name.uppercased() }

print(result)  // ["ALICE", "CHARLIE"]

// Real-world iOS example: Button actions
typealias ButtonAction = () -> Void

struct Button {
    let title: String
    let action: ButtonAction
    
    func tap() {
        print("Button '\(title)' tapped")
        action()
    }
}

let saveButton = Button(title: "Save") {
    print("Saving data...")
}

let cancelButton = Button(title: "Cancel") {
    print("Operation cancelled")
}

saveButton.tap()
// Button 'Save' tapped
// Saving data...

cancelButton.tap()
// Button 'Cancel' tapped
// Operation cancelled
```

**Explanation:**
- Trailing closure syntax improves readability
- Closures capture variables from their surrounding context
- Captured variables are stored by reference (modifications persist)
- Each closure instance has its own captured variables
- Trailing closures work great with SwiftUI and async operations

---

### Example 3: Escaping Closures, Autoclosures, and Advanced Patterns

```swift
// ESCAPING CLOSURES
// Closures that are called after the function returns

// Non-escaping (default) - closure must complete before function returns
func performImmediately(action: () -> Void) {
    print("Before")
    action()
    print("After")
}

performImmediately {
    print("Action")
}
/* Output:
Before
Action
After
*/

// Escaping - closure may be called later (after function returns)
var storedClosure: (() -> Void)?

func storeForLater(action: @escaping () -> Void) {
    storedClosure = action  // Store closure to call later
}

storeForLater {
    print("This runs later")
}

// Call the stored closure
storedClosure?()  // This runs later

// Real-world: Network completion handlers
typealias CompletionHandler = (Result<String, Error>) -> Void

func fetchData(completion: @escaping CompletionHandler) {
    // Simulate async operation
    DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
        completion(.success("Data fetched"))
    }
}

fetchData { result in
    switch result {
    case .success(let data):
        print(data)
    case .failure(let error):
        print("Error: \(error)")
    }
}

// Escaping with class self reference
class ViewController {
    var data: String = ""
    
    func loadData() {
        fetchData { [weak self] result in
            // Use [weak self] to avoid retain cycles
            guard let self = self else { return }
            
            if case .success(let data) = result {
                self.data = data  // Safe to use self
                print("Loaded: \(self.data)")
            }
        }
    }
}

// AUTOCLOSURES
// Automatically wraps expression in closure

// Without autoclosure
func logIfTrue(_ condition: () -> Bool, message: String) {
    if condition() {
        print(message)
    }
}

logIfTrue({ 2 > 1 }, message: "Math works")

// With autoclosure - cleaner syntax
func logIfTrue(_ condition: @autoclosure () -> Bool, message: String) {
    if condition() {
        print(message)
    }
}

logIfTrue(2 > 1, message: "Math works")  // No { } needed!

// Autoclosure use case: Short-circuit evaluation
func &&<T>(lhs: @autoclosure () -> Bool, rhs: @autoclosure () -> T?) -> T? {
    return lhs() ? rhs() : nil
}

// Only evaluates rhs if lhs is true (lazy evaluation)

// Custom assert function
func customAssert(
    _ condition: @autoclosure () -> Bool,
    _ message: @autoclosure () -> String
) {
    #if DEBUG
    if !condition() {
        print("Assertion failed: \(message())")
    }
    #endif
}

customAssert(2 + 2 == 4, "Math is broken!")  // Clean syntax

// ADVANCED PATTERNS

// 1. Lazy loading with closures
class ImageLoader {
    lazy var processImage: (Data) -> UIImage? = {
        print("Creating image processor")
        return { data in
            return UIImage(data: data)
        }
    }()
    
    func load(_ data: Data) -> UIImage? {
        return processImage(data)
    }
}

// 2. Builder pattern with closures
class NetworkRequest {
    var url: URL?
    var method: String = "GET"
    var headers: [String: String] = [:]
    
    @discardableResult
    func setURL(_ url: URL) -> Self {
        self.url = url
        return self
    }
    
    @discardableResult
    func setMethod(_ method: String) -> Self {
        self.method = method
        return self
    }
    
    @discardableResult
    func configure(_ closure: (inout NetworkRequest) -> Void) -> Self {
        closure(&self)
        return self
    }
}

let request = NetworkRequest()
    .setURL(URL(string: "https://api.example.com")!)
    .setMethod("POST")
    .configure { req in
        req.headers["Authorization"] = "Bearer token"
        req.headers["Content-Type"] = "application/json"
    }

// 3. Callback chaining
class AsyncOperation {
    var completion: ((String) -> Void)?
    
    func execute() {
        // Simulate async work
        DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
            self.completion?("Operation complete")
        }
    }
    
    @discardableResult
    func onComplete(_ handler: @escaping (String) -> Void) -> Self {
        completion = handler
        return self
    }
}

let operation = AsyncOperation()
    .onComplete { result in
        print("Got result: \(result)")
    }

operation.execute()

// 4. Type-safe selectors (modern Swift)
class TableManager {
    var cellConfigurator: ((UITableViewCell, IndexPath) -> Void)?
    
    func configure(_ closure: @escaping (UITableViewCell, IndexPath) -> Void) {
        cellConfigurator = closure
    }
    
    func cellForRow(at indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell()
        cellConfigurator?(cell, indexPath)
        return cell
    }
}

let manager = TableManager()
manager.configure { cell, indexPath in
    cell.textLabel?.text = "Row \(indexPath.row)"
}

// 5. Functional pipeline
let pipeline: [(Int) -> Int] = [
    { $0 * 2 },      // Double
    { $0 + 10 },     // Add 10
    { $0 * $0 }      // Square
]

func applyPipeline(_ value: Int, operations: [(Int) -> Int]) -> Int {
    return operations.reduce(value) { result, operation in
        operation(result)
    }
}

let result = applyPipeline(5, operations: pipeline)
print(result)  // ((5 * 2) + 10) ^ 2 = 20 ^ 2 = 400

// 6. Error handling with closures
enum NetworkError: Error {
    case noData
    case decodingFailed
}

func fetchUser(
    completion: @escaping (Result<User, NetworkError>) -> Void
) {
    // Simulate network call
    DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
        let user = User(name: "Alice", age: 30)
        completion(.success(user))
    }
}

fetchUser { result in
    switch result {
    case .success(let user):
        print("User: \(user.name)")
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

**Explanation:**
- `@escaping` required when closure might be called after function returns
- `@autoclosure` wraps expressions in closures automatically
- Use `[weak self]` or `[unowned self]` to avoid retain cycles
- Closures enable powerful functional programming patterns
- Modern Swift uses closures extensively for async operations

---

## 🎯 2025 Interview Questions

### Question 1: Explain the difference between escaping and non-escaping closures. When should you use `@escaping`?

**Answer:**
**Non-Escaping Closures (Default):**
- Execute **before** the function returns
- Cannot be stored or used after function completes
- Safer (no risk of retain cycles or unexpected behavior)
- Default in Swift

```swift
func performSync(action: () -> Void) {
    print("Before")
    action()  // Executes immediately
    print("After")
}  // action is done here

performSync {
    print("Action")
}
/* Output:
Before
Action
After
*/
```

**Escaping Closures:**
- May execute **after** the function returns
- Can be stored for later use
- Required for async operations
- Must be marked with `@escaping`

```swift
var storedClosures: [() -> Void] = []

func performAsync(action: @escaping () -> Void) {
    storedClosures.append(action)  // Store for later
}  // Function returns, but action hasn't executed yet

performAsync {
    print("This runs later")
}

// Execute stored closures
storedClosures.forEach { $0() }  // This runs later
```

**When to Use `@escaping`:**

**1. Async Network Calls:**
```swift
func fetchData(completion: @escaping (Data?) -> Void) {
    URLSession.shared.dataTask(with: url) { data, _, _ in
        completion(data)  // Called after fetchData returns
    }.resume()
}  // Function returns immediately

fetchData { data in
    // Processes data later
}
```

**2. Storing Closures:**
```swift
class Button {
    var action: (() -> Void)?  // Stored property
    
    func setAction(_ handler: @escaping () -> Void) {
        action = handler  // Store for later use
    }
    
    func tap() {
        action?()  // Execute stored closure
    }
}
```

**3. Dispatch Queues:**
```swift
func performLater(action: @escaping () -> Void) {
    DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
        action()  // Executes after delay
    }
}
```

**4. Completion Handlers:**
```swift
func processImage(
    _ image: UIImage,
    completion: @escaping (UIImage) -> Void
) {
    DispatchQueue.global().async {
        // Heavy processing
        let processed = /* ... */
        DispatchQueue.main.async {
            completion(processed)  // Call after processing
        }
    }
}
```

**Memory Management with Escaping Closures:**

```swift
class ViewController {
    var name = "ViewController"
    
    func loadData() {
        // ❌ Strong reference cycle (memory leak)
        fetchData { data in
            self.name = data  // Captures self strongly
        }
        
        // ✅ Use weak self
        fetchData { [weak self] data in
            self?.name = data  // Safe, no memory leak
        }
        
        // ✅ Use unowned self (if self guaranteed to exist)
        fetchData { [unowned self] data in
            self.name = data  // Crashes if self is deallocated
        }
    }
}
```

**Key Differences:**

| Aspect | Non-Escaping | Escaping |
|--------|--------------|----------|
| Default | Yes | No (explicit `@escaping`) |
| Execution | Before return | After return possible |
| Storage | Cannot store | Can store |
| Memory | No retain issues | Potential retain cycles |
| Performance | Faster (can optimize) | Slight overhead |
| Use case | Immediate use | Async operations |

**Swift Compiler Optimization:**
Non-escaping closures allow compiler optimizations because it knows the closure's lifetime.

**2025 Best Practice:**
```swift
// ✅ Use @escaping for async operations
func fetchUser(completion: @escaping (User?) -> Void) {
    // Async network call
}

// ✅ Use weak self to avoid cycles
fetchUser { [weak self] user in
    self?.updateUI(with: user)
}

// ❌ Don't use @escaping unnecessarily
func calculate(numbers: [Int], operation: (Int) -> Int) -> [Int] {
    // No @escaping needed - operation used immediately
    return numbers.map(operation)
}
```

---

### Question 2: What is the difference between `[weak self]` and `[unowned self]` in closure capture lists? When should you use each?

**Answer:**
Both prevent **retain cycles** in escaping closures, but handle memory differently.

**The Problem: Retain Cycles**
```swift
class ViewController {
    var name = "VC"
    var closure: (() -> Void)?
    
    func setup() {
        // ❌ Retain cycle: self → closure → self
        closure = {
            print(self.name)  // Captures self strongly
        }
    }
}
// ViewController never deallocates (memory leak)
```

**Solution 1: `[weak self]`** (Optional)
```swift
closure = { [weak self] in
    guard let self = self else { return }  // self is Optional
    print(self.name)
}
```

**Solution 2: `[unowned self]`** (Non-optional)
```swift
closure = { [unowned self] in
    print(self.name)  // self is non-optional
}
```

**Detailed Comparison:**

**`[weak self]`:**
- `self` becomes **Optional** (`self?`)
- Safe: Returns `nil` if object deallocated
- Must unwrap before use
- Slower (requires checking)

```swift
class NetworkManager {
    func fetchData(completion: @escaping (Data) -> Void) {
        URLSession.shared.dataTask(with: url) { [weak self] data, _, _ in
            guard let self = self else {
                print("NetworkManager was deallocated")
                return
            }
            self.processData(data)  // Safe even if self is nil
        }
    }
}
```

**`[unowned self]`:**
- `self` is **non-optional**
- Crashes if object deallocated (like force unwrapping)
- No unwrapping needed
- Faster (no optional checking)

```swift
class TimerController {
    var timer: Timer?
    
    func startTimer() {
        timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [unowned self] _ in
            self.tick()  // Assumes self exists - crashes if not
        }
    }
    
    deinit {
        timer?.invalidate()  // Must cleanup to avoid crash
    }
}
```

**When to Use Each:**

**Use `[weak self]` when:**
- Object **might** be deallocated before closure executes
- Async operations (network calls, animations)
- Closures with uncertain lifetime
- **Default choice** (safer)

```swift
// ✅ Network request (user might leave screen)
func loadImage(url: URL, completion: @escaping (UIImage?) -> Void) {
    URLSession.shared.dataTask(with: url) { [weak self] data, _, _ in
        guard let self = self else { return }
        // User might have closed the view
        self.updateImage(data)
    }.resume()
}

// ✅ Animation (view might be removed)
UIView.animate(withDuration: 1.0) { [weak self] in
    self?.view.alpha = 1.0
}

// ✅ Notification observers
NotificationCenter.default.addObserver(forName: .didUpdate, object: nil, queue: .main) { [weak self] _ in
    self?.refresh()
}
```

**Use `[unowned self]` when:**
- Object is **guaranteed** to exist when closure runs
- Closure lifecycle tied to object lifecycle
- Performance-critical code (avoid optional overhead)
- **Use cautiously** (can crash)

```swift
// ✅ Closure called before object deallocates
class ViewController {
    lazy var viewModel: ViewModel = {
        return ViewModel { [unowned self] in
            self.updateUI()  // Called immediately, self exists
        }
    }()
}

// ✅ Dispatch to main queue immediately
func updateUI() {
    DispatchQueue.main.async { [unowned self] in
        self.label.text = "Updated"
        // If on main thread already, self definitely exists
    }
}
```

**⚠️ Danger of `[unowned self]`:**
```swift
class BrokenExample {
    var closure: (() -> Void)?
    
    func setup() {
        closure = { [unowned self] in
            print(self.name)  // ❌ CRASH if BrokenExample deallocated
        }
    }
}

let example = BrokenExample()
example.setup()
// example deallocates here
example.closure?()  // ❌ CRASH: Unowned reference to deallocated object
```

**Capture Multiple Values:**
```swift
class Controller {
    let id = "ABC"
    var data: Data?
    
    func process() {
        fetch { [weak self, id] result in
            // id captured as copy (value type)
            // self captured as weak reference
            guard let self = self else { return }
            print("Processing \(id) for \(self.data)")
        }
    }
}
```

**iOS Real-World Example (2025):**
```swift
// SwiftUI with Combine
class ViewModel: ObservableObject {
    @Published var items: [Item] = []
    var cancellables = Set<AnyCancellable>()
    
    func loadData() {
        apiService.fetchItems()
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.handleCompletion(completion)
                },
                receiveValue: { [weak self] items in
                    self?.items = items
                }
            )
            .store(in: &cancellables)
    }
}
```

**Decision Tree:**

```
Can object be deallocated before closure runs?
├─ Yes → Use [weak self] ✅
└─ No  → Guaranteed to exist?
    ├─ Yes → [unowned self] (performance) ⚡
    └─ Uncertain → Use [weak self] ✅ (safer)
```

**Best Practice (2025):**
- **Default to `[weak self]`** - it's safer
- Only use `[unowned self]` with clear justification
- Document why `[unowned self]` is safe
- In production code, prefer safety over minor performance gains

---

### Question 3: Explain the `map`, `filter`, and `reduce` functions. How do they work with closures?

**Answer:**
These are **higher-order functions** that take closures as parameters and transform collections functionally.

**`map` - Transform Each Element:**

Applies a transformation to each element, returning a new array with transformed values.

```swift
let numbers = [1, 2, 3, 4, 5]

// Long form
let doubled = numbers.map { (number: Int) -> Int in
    return number * 2
}

// Short form (type inference + implicit return)
let doubled2 = numbers.map { $0 * 2 }
print(doubled2)  // [2, 4, 6, 8, 10]
```

**Use Cases:**
```swift
// Extract property from objects
struct Person {
    let name: String
    let age: Int
}

let people = [
    Person(name: "Alice", age: 30),
    Person(name: "Bob", age: 25)
]

let names = people.map { $0.name }
print(names)  // ["Alice", "Bob"]

// Convert types
let strings = ["1", "2", "3"]
let integers = strings.compactMap { Int($0) }  // compactMap removes nils
print(integers)  // [1, 2, 3]

// iOS: Transform API response
let users: [User] = apiResponse.map { json in
    User(json: json)
}
```

**`filter` - Select Elements:**

Returns new array containing only elements that match a condition.

```swift
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Keep even numbers
let evens = numbers.filter { $0 % 2 == 0 }
print(evens)  // [2, 4, 6, 8, 10]

// Keep numbers > 5
let large = numbers.filter { $0 > 5 }
print(large)  // [6, 7, 8, 9, 10]
```

**Use Cases:**
```swift
// Filter objects by property
let adults = people.filter { $0.age >= 18 }

// Multiple conditions
let eligiblePeople = people.filter { person in
    person.age >= 18 && person.age <= 65
}

// iOS: Filter valid form inputs
let validEmails = emails.filter { email in
    email.contains("@") && email.contains(".")
}

// Remove empty strings
let nonEmpty = strings.filter { !$0.isEmpty }
```

**`reduce` - Combine Into Single Value:**

Combines all elements into a single value using a closure.

```swift
let numbers = [1, 2, 3, 4, 5]

// Sum all numbers
let sum = numbers.reduce(0) { result, number in
    return result + number
}
print(sum)  // 15

// Short form
let sum2 = numbers.reduce(0, +)
print(sum2)  // 15

// Product
let product = numbers.reduce(1, *)
print(product)  // 120
```

**Use Cases:**
```swift
// Build string
let words = ["Hello", "World", "Swift"]
let sentence = words.reduce("") { $0 + " " + $1 }
print(sentence)  // " Hello World Swift"

// Better with joined:
let sentence2 = words.joined(separator: " ")

// Count occurrences
let letters = ["a", "b", "a", "c", "a"]
let counts = letters.reduce(into: [:]) { dict, letter in
    dict[letter, default: 0] += 1
}
print(counts)  // ["a": 3, "b": 1, "c": 1]

// Calculate total price
struct Product {
    let name: String
    let price: Double
}

let cart = [
    Product(name: "iPhone", price: 999.99),
    Product(name: "Case", price: 29.99)
]

let total = cart.reduce(0.0) { $0 + $1.price }
print(total)  // 1029.98
```

**Chaining Multiple Operations:**

```swift
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Find sum of squares of even numbers
let result = numbers
    .filter { $0 % 2 == 0 }      // [2, 4, 6, 8, 10]
    .map { $0 * $0 }             // [4, 16, 36, 64, 100]
    .reduce(0, +)                // 220

print(result)  // 220

// More complex: Process user data
let users = [
    User(name: "Alice", age: 30, isPremium: true),
    User(name: "Bob", age: 25, isPremium: false),
    User(name: "Charlie", age: 35, isPremium: true)
]

let premiumUsernames = users
    .filter { $0.isPremium }
    .map { $0.name.uppercased() }
    .sorted()

print(premiumUsernames)  // ["ALICE", "CHARLIE"]
```

**Performance Comparison:**

```swift
// ❌ Less efficient (creates intermediate arrays)
let result = numbers
    .filter { $0 % 2 == 0 }      // Creates array 1
    .map { $0 * $0 }             // Creates array 2
    .reduce(0, +)                // Final result

// ✅ More efficient (single pass, no intermediate arrays)
let result2 = numbers.reduce(0) { sum, number in
    guard number % 2 == 0 else { return sum }
    return sum + (number * number)
}
```

**iOS Real-World Examples (2025):**

```swift
// 1. SwiftUI List filtering
struct ContentView: View {
    @State private var searchText = ""
    let items: [Item]
    
    var filteredItems: [Item] {
        items.filter { item in
            searchText.isEmpty || item.name.contains(searchText)
        }
    }
    
    var body: some View {
        List(filteredItems) { item in
            Text(item.name)
        }
    }
}

// 2. Calculate statistics
let scores = [85, 92, 78, 95, 88]

let average = scores.reduce(0, +) / scores.count
let highest = scores.max()
let passing = scores.filter { $0 >= 70 }.count

// 3. Group by category
let transactions = [/* ... */]
let byCategory = transactions.reduce(into: [:]) { dict, transaction in
    dict[transaction.category, default: []].append(transaction)
}

// 4. Format API data
let apiUsers = apiResponse
    .compactMap { try? JSONDecoder().decode(User.self, from: $0) }
    .filter { $0.isActive }
    .sorted { $0.name < $1.name }
```

**Comparison Table:**

| Function | Input | Output | Purpose |
|----------|-------|--------|---------|
| `map` | [A] | [B] | Transform each element |
| `filter` | [A] | [A] | Select matching elements |
| `reduce` | [A] | B | Combine into single value |
| `compactMap` | [A?] | [A] | Transform + remove nils |
| `flatMap` | [[A]] | [A] | Flatten nested arrays |

**Best Practices (2025):**
- Use for readability and conciseness
- Chain operations for complex transformations
- Be mindful of performance with large datasets
- Consider lazy evaluation for very large collections

---

### Question 4: What are autoclosures (`@autoclosure`) in Swift? When and why would you use them?

**Answer:**
**`@autoclosure`** automatically wraps an expression in a closure without requiring explicit closure syntax.

**Without `@autoclosure`:**
```swift
func logIfTrue(condition: () -> Bool, message: String) {
    if condition() {
        print(message)
    }
}

// Must wrap expression in closure
logIfTrue(condition: { 2 > 1 }, message: "Math works")
```

**With `@autoclosure`:**
```swift
func logIfTrue(condition: @autoclosure () -> Bool, message: String) {
    if condition() {
        print(message)
    }
}

// Cleaner syntax - expression wrapped automatically
logIfTrue(condition: 2 > 1, message: "Math works")
```

**How It Works:**

The compiler automatically transforms:
```swift
logIfTrue(condition: 2 > 1, message: "Test")
```

Into:
```swift
logIfTrue(condition: { 2 > 1 }, message: "Test")
```

**Key Benefits:**

**1. Lazy Evaluation:**
```swift
func &&(lhs: Bool, rhs: @autoclosure () -> Bool) -> Bool {
    return lhs ? rhs() : false  // rhs only evaluated if lhs is true
}

// Short-circuit evaluation
let result = false && expensiveOperation()  // expensiveOperation() NOT called
```

**2. Cleaner API:**
```swift
// Verbose without @autoclosure
assert({ 2 + 2 == 4 }, { "Math error" })

// Clean with @autoclosure
assert(2 + 2 == 4, "Math error")
```

**Swift Standard Library Examples:**

**1. `assert`:**
```swift
func assert(
    _ condition: @autoclosure () -> Bool,
    _ message: @autoclosure () -> String = ""
) {
    #if DEBUG
    if !condition() {
        fatalError(message())
    }
    #endif
}

// Usage - message only evaluated if assertion fails
assert(user != nil, "User should not be nil")
```

**2. Nil-Coalescing Operator `??`:**
```swift
func ??<T>(optional: T?, defaultValue: @autoclosure () throws -> T) rethrows -> T {
    switch optional {
    case .some(let value):
        return value  // Default not evaluated
    case .none:
        return try defaultValue()  // Only evaluate if needed
    }
}

// Default value only created if needed
let name = optionalName ?? getDefaultName()  // getDefaultName() not called if optionalName exists
```

**3. `precondition`:**
```swift
func precondition(
    _ condition: @autoclosure () -> Bool,
    _ message: @autoclosure () -> String = ""
) {
    if !condition() {
        fatalError(message())
    }
}
```

**Real-World Use Cases:**

**1. Logging with Levels:**
```swift
enum LogLevel {
    case debug, info, warning, error
}

func log(
    _ level: LogLevel,
    _ message: @autoclosure () -> String,
    file: String = #file,
    line: Int = #line
) {
    #if DEBUG
    if level == .debug || level == .error {
        print("[\(level)] \(file):\(line) - \(message())")
    }
    #endif
}

// Message only evaluated if logged
log(.debug, "User data: \(expensiveSerialize(user))")
```

**2. Optional Unwrapping Helper:**
```swift
func unwrap<T>(
    _ optional: T?,
    orError message: @autoclosure () -> String
) throws -> T {
    guard let value = optional else {
        throw NSError(domain: message(), code: 0)
    }
    return value
}

// Usage
let user = try unwrap(optionalUser, orError: "User not found: \(userId)")
```

**3. Validation:**
```swift
func require(
    _ condition: @autoclosure () -> Bool,
    _ errorMessage: @autoclosure () -> String
) throws {
    guard condition() else {
        throw ValidationError(message: errorMessage())
    }
}

// Clean validation
try require(age >= 18, "User must be 18 or older")
try require(!email.isEmpty, "Email is required")
```

**4. Performance-Critical Checks:**
```swift
func expensive Calculation() -> Int {
    print("Calculating...")
    return 42
}

func doWork(shouldCalculate: @autoclosure () -> Bool) {
    if shouldCalculate() {
        // Heavy work only done if needed
        performHeavyWork()
    }
}

// Expensive calculation not performed if flag is false
let flag = false
doWork(shouldCalculate: flag && expensiveCalculation() > 0)
```

**iOS-Specific Example (2025):**

```swift
// SwiftUI conditional rendering helper
func renderIf<Content: View>(
    _ condition: @autoclosure () -> Bool,
    @ViewBuilder content: () -> Content
) -> some View {
    Group {
        if condition() {
            content()
        }
    }
}

// Usage
renderIf(user.isPremium) {
    PremiumBadge()
}
```

**Important Considerations:**

**1. Can Be Escaping:**
```swift
var storedClosure: (() -> Bool)?

func store(condition: @autoclosure @escaping () -> Bool) {
    storedClosure = condition  // Can combine with @escaping
}
```

**2. Type Must Match:**
```swift
// ❌ Can't convert complex expressions
func process(@autoclosure value: () -> Int) { }

// Must return exact type
// process(value: "42")  // Error: String not convertible to Int
```

**3. Don't Overuse:**
```swift
// ❌ Overuse makes code confusing
func weird(@autoclosure a: () -> Int, @autoclosure b: () -> Int) { }

// ✅ Use for readability, not everywhere
func log(_ message: @autoclosure () -> String) { }
```

**Best Practices (2025):**

- **Use for:**
  - Assertions and preconditions
  - Lazy evaluation (expensive operations)
  - Short-circuit evaluation
  - Cleaner APIs

- **Don't use for:**
  - Complex transformations
  - When closure syntax is clearer
  - Every function (only when it improves readability)

**Memory:** `@autoclosure` creates a closure behind the scenes, so there's a small performance cost. Use only when lazy evaluation provides value.

---

### Question 5: Explain trailing closure syntax and when it improves code readability. Provide iOS-specific examples.

**Answer:**
**Trailing closure syntax** allows you to write a closure **outside** of the parentheses when it's the **last parameter** of a function.

**Standard Syntax:**
```swift
func performAction(name: String, completion: () -> Void) {
    print("Performing: \(name)")
    completion()
}

// Standard call
performAction(name: "Task", completion: {
    print("Done!")
})
```

**Trailing Closure Syntax:**
```swift
// Closure moved outside parentheses
performAction(name: "Task") {
    print("Done!")
}

// If closure is only parameter, can omit parentheses entirely
func doSomething(action: () -> Void) {
    action()
}

doSomething {
    print("Action!")
}
```

**Why It Improves Readability:**

**1. Multi-Line Closures:**
```swift
// ❌ Hard to read
UIView.animate(withDuration: 0.3, animations: {
    self.view.alpha = 0
    self.view.transform = CGAffineTransform(scaleX: 0.5, y: 0.5)
}, completion: { finished in
    self.view.removeFromSuperview()
})

// ✅ Much clearer
UIView.animate(withDuration: 0.3) {
    self.view.alpha = 0
    self.view.transform = CGAffineTransform(scaleX: 0.5, y: 0.5)
} completion: { finished in
    self.view.removeFromSuperview()
}
```

**2. Natural Language Flow:**
```swift
// Reads like English: "fetch data from URL"
fetchData(from: url) { data in
    processData(data)
}

// vs awkward:
fetchData(from: url, completion: { data in processData(data) })
```

**iOS-Specific Examples (2025):**

**1. UIKit Animations:**
```swift
// Spring animation
UIView.animate(
    withDuration: 0.6,
    delay: 0,
    usingSpringWithDamping: 0.7,
    initialSpringVelocity: 0,
    options: [.curveEaseOut]
) {
    self.button.transform = .identity
    self.button.alpha = 1.0
} completion: { _ in
    self.enableUserInteraction()
}
```

**2. Network Requests:**
```swift
// URLSession with trailing closure
URLSession.shared.dataTask(with: url) { data, response, error in
    guard let data = data else { return }
    
    DispatchQueue.main.async {
        self.updateUI(with: data)
    }
}.resume()
```

**3. SwiftUI:**
```swift
// Button with action
Button("Save") {
    saveData()
    dismiss()
}

// List with row content
List(items) { item in
    HStack {
        Image(systemName: item.icon)
        Text(item.name)
    }
}

// NavigationLink
NavigationLink {
    DetailView(item: item)
} label: {
    Text(item.name)
}
```

**4. Combine:**
```swift
// Publisher chain
publisher
    .map { $0.uppercased() }
    .filter { !$0.isEmpty }
    .sink { value in
        print("Received: \(value)")
    }
    .store(in: &cancellables)
```

**5. Grand Central Dispatch:**
```swift
DispatchQueue.global().async {
    // Background work
    let result = performHeavyCalculation()
    
    DispatchQueue.main.async {
        self.displayResult(result)
    }
}
```

**6. Array Operations:**
```swift
let numbers = [1, 2, 3, 4, 5]

// Map
let doubled = numbers.map { $0 * 2 }

// Filter
let evens = numbers.filter { $0 % 2 == 0 }

// Sorted
let sorted = numbers.sorted { $0 > $1 }

// ForEach
numbers.forEach { number in
    print(number)
}
```

**7. Custom Builders:**
```swift
class AlertBuilder {
    func show(
        title: String,
        message: String,
        onConfirm: @escaping () -> Void
    ) {
        // Show alert
        onConfirm()
    }
}

// Clean API
alertBuilder.show(title: "Delete", message: "Are you sure?") {
    deleteItem()
    refreshList()
}
```

**8. Async/Await with Tasks:**
```swift
Task {
    do {
        let data = try await fetchData()
        await processData(data)
    } catch {
        handleError(error)
    }
}
```

**Multiple Trailing Closures (Swift 5.3+):**

```swift
// Multiple closures
UIView.animate(withDuration: 0.3) {
    // Animation
    self.view.alpha = 0
} completion: { finished in
    // Completion
    self.view.removeFromSuperview()
}

// Custom function
func performOperation(
    setup: () -> Void,
    execution: () -> Void,
    cleanup: () -> Void
) {
    setup()
    execution()
    cleanup()
}

// Call with multiple trailing closures
performOperation {
    print("Setup")
} execution: {
    print("Execute")
} cleanup: {
    print("Cleanup")
}
```

**When NOT to Use:**

```swift
// ❌ Single line - standard syntax is fine
let sum = numbers.reduce(0, { $0 + $1 })

// ✅ Or use operator
let sum = numbers.reduce(0, +)

// ❌ Not the last parameter
func weird(closure: () -> Void, name: String) { }
// weird { print("Hi") }, name: "Test"  // Syntax error!

// ✅ Must use standard syntax
weird(closure: { print("Hi") }, name: "Test")
```

**Best Practices (2025):**

1. **Use for multi-line closures**
2. **Improves nested code readability**
3. **Natural for DSLs** (Domain Specific Languages like SwiftUI)
4. **Consistent with Swift API design**
5. **Omit parameter label** if closure is only parameter

**Formatting:**
```swift
// ✅ Good formatting
fetchData(from: url) { data in
    processData(data)
    updateUI()
}

// ❌ Inconsistent
fetchData(from: url) {
    data in processData(data); updateUI()
}
```

Trailing closures are a defining feature of Swift's clean, readable syntax and are ubiquitous in iOS development.

---

## 📝 Practice Exercises

1. Create a function that takes an array and a transform closure, returning a new transformed array
2. Write a custom sort function using closures to compare elements
3. Implement a retry mechanism that accepts a closure and retries on failure
4. Build a simple calculator using closures for operations (+, -, *, /)
5. Create a validation system with multiple validation rules as closures

---

## 🔗 Navigation
- [← Previous: Step 7 - Functions](../Step-7-Functions/README.md)
- [→ Next Phase: Phase 3 - Object-Oriented Swift](../../Phase-3-Object-Oriented-Swift/Step-9-Enumerations/README.md)

---

## 🎉 Phase 2 Complete!

Congratulations! You've completed **Phase 2: Functions & Closures**. You now understand:
- ✅ Function syntax, parameters, and return types
- ✅ External/internal parameter names
- ✅ Default values and variadic parameters
- ✅ `inout` parameters and function types
- ✅ Closures and closure expressions
- ✅ Trailing closure syntax
- ✅ Capturing values
- ✅ Escaping closures and autoclosures
- ✅ Functional programming with map/filter/reduce

**Next:** Move on to **Phase 3: Object-Oriented Swift** to learn about enums, structs, and classes!
