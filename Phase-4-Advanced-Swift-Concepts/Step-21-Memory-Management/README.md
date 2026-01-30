# Step 21: Memory Management (ARC)

## 📚 Description

**Automatic Reference Counting (ARC)** is Swift's memory management system that automatically tracks and manages your app's memory usage. Understanding ARC, reference types, strong/weak/unowned references, and retain cycles is critical for building memory-efficient iOS apps without leaks.

### What You'll Learn:
- How ARC works under the hood
- Strong, weak, and unowned references
- Retain cycles and how to break them
- Closure capture lists
- Value types vs reference types memory
- Memory management best practices

### Key Concepts:
- **ARC**: Automatic Reference Counting
- **Reference Count**: Number of strong references to an object
- **Strong Reference**: Default, increases retain count
- **Weak Reference**: Doesn't increase retain count, becomes `nil` when deallocated
- **Unowned Reference**: Doesn't increase retain count, assumes always valid
- **Retain Cycle**: Strong reference loop preventing deallocation
- **Capture List**: Specifies how closures capture values

### Benefits:
- **Automatic**: No manual memory management
- **Predictable**: Deallocation happens immediately when count reaches zero
- **Efficient**: Minimal overhead compared to garbage collection
- **Safe**: Compile-time and runtime checks

---

## 💡 Clear Examples

### Example 1: ARC Basics and Strong References

```swift
// ========== HOW ARC WORKS ==========

class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

// Strong reference
var reference1: Person? = Person(name: "Alice")  // RC = 1
// Output: Alice is being initialized

var reference2 = reference1  // RC = 2 (another strong reference)
var reference3 = reference1  // RC = 3

reference1 = nil  // RC = 2
reference2 = nil  // RC = 1
reference3 = nil  // RC = 0, object deallocated
// Output: Alice is being deinitialized

// ========== AUTOMATIC COUNTING ==========

func createPerson() {
    let person = Person(name: "Bob")  // RC = 1
    print("Function body")
    // person goes out of scope, RC = 0
}  // Bob is being deinitialized

createPerson()

// ========== STRONG REFERENCES IN PROPERTIES ==========

class Apartment {
    let unit: String
    var tenant: Person?  // Strong reference
    
    init(unit: String) {
        self.unit = unit
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

var john: Person? = Person(name: "John")        // Person RC = 1
var unit4A: Apartment? = Apartment(unit: "4A")  // Apartment RC = 1

unit4A?.tenant = john  // Person RC = 2

john = nil     // Person RC = 1 (still referenced by apartment)
unit4A = nil   // Apartment RC = 0, deallocated
// Output: Apartment 4A is being deinitialized
// Output: John is being deinitialized
```

**Key Points:**
- ARC tracks strong references automatically
- Object deallocated when reference count reaches 0
- Strong references (default) increase retain count

---

### Example 2: Retain Cycles and How to Break Them

```swift
// ========== RETAIN CYCLE PROBLEM ==========

class Person {
    let name: String
    var apartment: Apartment?  // Strong reference
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class Apartment {
    let unit: String
    var tenant: Person?  // Strong reference
    
    init(unit: String) {
        self.unit = unit
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

var john: Person? = Person(name: "John")        // Person RC = 1
var unit4A: Apartment? = Apartment(unit: "4A")  // Apartment RC = 1

john?.apartment = unit4A  // Apartment RC = 2
unit4A?.tenant = john     // Person RC = 2

// Retain cycle:
// Person --strong--> Apartment
// Apartment --strong--> Person

john = nil     // Person RC = 1 (still held by apartment)
unit4A = nil   // Apartment RC = 1 (still held by person)

// ❌ MEMORY LEAK: Neither deallocates!

// ========== SOLUTION 1: WEAK REFERENCE ==========

class PersonFixed {
    let name: String
    var apartment: ApartmentFixed?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class ApartmentFixed {
    let unit: String
    weak var tenant: PersonFixed?  // ✅ Weak reference
    
    init(unit: String) {
        self.unit = unit
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

var alice: PersonFixed? = PersonFixed(name: "Alice")
var unit5B: ApartmentFixed? = ApartmentFixed(unit: "5B")

alice?.apartment = unit5B  // Apartment RC = 2 (alice + unit5B)
unit5B?.tenant = alice     // Person RC = 1 (only alice, weak doesn't count)

alice = nil     // Person RC = 0, deallocated
// Output: Alice is being deinitialized
// tenant becomes nil automatically

unit5B = nil    // Apartment RC = 0, deallocated
// Output: Apartment 5B is being deinitialized

// ========== SOLUTION 2: UNOWNED REFERENCE ==========

class Customer {
    let name: String
    var card: CreditCard?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer  // ✅ Unowned (always has value)
    
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    
    deinit {
        print("Card #\(number) is being deinitialized")
    }
}

var bob: Customer? = Customer(name: "Bob")
bob?.card = CreditCard(number: 1234_5678_9012_3456, customer: bob!)

bob = nil  // Customer RC = 0, deallocates both
// Output: Bob is being deinitialized
// Output: Card #1234567890123456 is being deinitialized
```

**Key Points:**
- `weak`: Optional, becomes `nil` when deallocated
- `unowned`: Non-optional, crashes if accessed after deallocation
- Use `weak` when reference can be `nil`
- Use `unowned` when reference always valid during lifetime

---

### Example 3: Closures and Capture Lists

```swift
// ========== CLOSURE RETAIN CYCLES ==========

class HTMLElement {
    let name: String
    let text: String?
    
    // ❌ RETAIN CYCLE
    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    // Closure captures self strongly
    // self holds closure strongly
    
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

var heading: HTMLElement? = HTMLElement(name: "h1", text: "Hello")
print(heading!.asHTML())  // <h1>Hello</h1>
heading = nil  // ❌ NOT deallocated (retain cycle)

// ========== SOLUTION: CAPTURE LIST WITH WEAK ==========

class HTMLElementFixed {
    let name: String
    let text: String?
    
    // ✅ WEAK CAPTURE LIST
    lazy var asHTML: () -> String = { [weak self] in
        guard let self = self else {
            return ""
        }
        
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

var paragraph: HTMLElementFixed? = HTMLElementFixed(name: "p", text: "Text")
print(paragraph!.asHTML())
paragraph = nil  // ✅ Deallocated
// Output: p is being deinitialized

// ========== UNOWNED IN CLOSURES ==========

class Document {
    let title: String
    
    // Closure called only during document lifetime
    lazy var formatter: () -> String = { [unowned self] in
        return "Document: \(self.title)"
    }
    
    init(title: String) {
        self.title = title
    }
    
    deinit {
        print("\(title) is being deinitialized")
    }
}

var doc: Document? = Document(title: "Report")
print(doc!.formatter())
doc = nil  // ✅ Deallocated

// ========== CAPTURE SPECIFIC VALUES ==========

class Counter {
    var count = 0
    
    func makeIncrementer() -> () -> Int {
        // Captures only count value, not self
        return { [count] in
            return count + 1  // Uses captured value
        }
    }
    
    func makeIncrementerStrong() -> () -> Int {
        // Captures self
        return {
            self.count += 1
            return self.count
        }
    }
}

// ========== ESCAPING CLOSURES ==========

class NetworkManager {
    var completionHandlers: [() -> Void] = []
    
    func fetchData(completion: @escaping () -> Void) {
        // Closure stored, creates potential retain cycle
        completionHandlers.append(completion)
    }
}

class ViewController {
    let manager = NetworkManager()
    
    func loadData() {
        // ❌ RETAIN CYCLE
        manager.fetchData {
            self.updateUI()  // Captures self strongly
        }
        
        // ✅ CORRECT
        manager.fetchData { [weak self] in
            self?.updateUI()
        }
    }
    
    func updateUI() {
        print("UI updated")
    }
}
```

**Key Points:**
- Closures capture references strongly by default
- Use `[weak self]` or `[unowned self]` in capture lists
- `weak self` requires optional chaining (`self?`)
- `unowned self` assumes self always exists

---

## 🎯 Interview Questions

### Q1: Explain how ARC works. When is an object deallocated?

**Answer:**

**How ARC Works:**

ARC (Automatic Reference Counting) tracks the number of strong references to each class instance. When the count reaches zero, the instance is immediately deallocated.

**Reference Counting Process:**
```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) allocated")
    }
    deinit {
        print("\(name) deallocated")
    }
}

// Step by step:
var ref1: Person? = Person(name: "Alice")  // RC = 1
var ref2 = ref1                             // RC = 2
var ref3 = ref1                             // RC = 3

ref1 = nil  // RC = 2 (decreased by 1)
ref2 = nil  // RC = 1 (decreased by 1)  
ref3 = nil  // RC = 0 → DEALLOCATE immediately
```

**Deallocation Happens:**
1. When last strong reference is removed
2. When variable goes out of scope
3. When owning object is deallocated
4. Immediately (not during garbage collection cycle)

**ARC vs Garbage Collection:**

| ARC (Swift) | Garbage Collection (Java, C#) |
|-------------|-------------------------------|
| Counts references | Traces reachable objects |
| Immediate deallocation | Periodic collection |
| Predictable timing | Non-deterministic |
| Low overhead | Higher overhead |
| Can have retain cycles | Handles cycles automatically |

**Example with Scope:**
```swift
func example() {
    let person = Person(name: "Bob")  // RC = 1
    print("Inside function")
    // person goes out of scope here
}  // RC = 0, deallocated immediately

example()
// Output:
// Bob allocated
// Inside function
// Bob deallocated
```

**Example with Property:**
```swift
class Room {
    var occupant: Person?
    
    deinit {
        print("Room deallocated")
    }
}

var room: Room? = Room()
room?.occupant = Person(name: "Charlie")  // Person RC = 1

room = nil  // Room deallocated, Person RC = 0, also deallocated
// Output:
// Charlie allocated
// Room deallocated
// Charlie deallocated
```

**Key Points:**
- Deterministic: Deallocation timing is predictable
- Efficient: No stop-the-world pauses
- Problem: Doesn't handle reference cycles automatically
- Solution: Use weak/unowned for cycles

---

### Q2: What's the difference between weak and unowned? When should you use each?

**Answer:**

**Weak References:**
- Optional (`weak var`)
- Automatically becomes `nil` when referenced object deallocates
- Used when reference can be `nil` at some point

```swift
class Parent {
    var child: Child?
}

class Child {
    weak var parent: Parent?  // Can be nil
}

var parent: Parent? = Parent()
var child = Child()

parent?.child = child
child.parent = parent  // Weak reference

parent = nil  // Parent deallocated
print(child.parent)  // nil (weak reference zeroed automatically)
```

**Unowned References:**
- Non-optional (`unowned let/var`)
- Assumes referenced object always exists
- Crashes if accessed after deallocation
- Slightly more efficient than weak

```swift
class Country {
    let name: String
    var capital: City?
    init(name: String) {
        self.name = name
    }
}

class City {
    let name: String
    unowned let country: Country  // Always valid
    
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}

var country: Country? = Country(name: "USA")
country?.capital = City(name: "Washington", country: country!)

print(country?.capital?.country.name)  // USA

country = nil  // Both deallocated together
```

**When to Use Each:**

| Scenario | Use |
|----------|-----|
| Reference can be nil | `weak` |
| Reference always valid during lifetime | `unowned` |
| Parent-child relationship (child optional) | `weak` in child |
| Tightly coupled lifetime | `unowned` |
| Delegate pattern | `weak` |
| Closure capturing self (might deallocate) | `weak` |

**Common Patterns:**

**1. Delegate Pattern (weak):**
```swift
protocol ViewControllerDelegate: AnyObject {
    func didTapButton()
}

class ViewController {
    weak var delegate: ViewControllerDelegate?  // Delegate can be nil
}
```

**2. Parent owns child (weak in child):**
```swift
class Post {
    var comments: [Comment] = []
}

class Comment {
    weak var post: Post?  // Post might be deleted
}
```

**3. Tightly coupled (unowned):**
```swift
class Customer {
    var card: CreditCard?
}

class CreditCard {
    unowned let customer: Customer  // Always has customer
}
```

**Danger of Unowned:**
```swift
class A {
    var b: B?
}

class B {
    unowned let a: A
    init(a: A) {
        self.a = a
    }
}

var a: A? = A()
let b = B(a: a!)
a?.b = b

a = nil  // A deallocated

// print(b.a.name)  // ⚠️ CRASH! Unowned reference to deallocated object
```

**Best Practice:**
- Default to `weak` when unsure
- Use `unowned` only when lifetime guarantee is clear
- Document why `unowned` is safe

---

### Q3: How do closures cause retain cycles? How do you fix them?

**Answer:**

**How Closures Create Retain Cycles:**

Closures are reference types and capture references to values they use. When a class stores a closure that captures `self`, a retain cycle forms.

**Problem:**
```swift
class ViewController {
    var name = "Main"
    var onButtonTap: (() -> Void)?
    
    func setupButton() {
        // ❌ RETAIN CYCLE
        onButtonTap = {
            print(self.name)  // Closure captures self strongly
        }
        // self → onButtonTap (strong)
        // onButtonTap → self (strong capture)
    }
    
    deinit {
        print("ViewController deallocated")
    }
}

var vc: ViewController? = ViewController()
vc?.setupButton()
vc = nil  // ❌ NOT deallocated (retain cycle)
```

**Solution 1: Weak Capture List:**
```swift
class ViewController {
    var name = "Main"
    var onButtonTap: (() -> Void)?
    
    func setupButton() {
        // ✅ WEAK SELF
        onButtonTap = { [weak self] in
            guard let self = self else { return }
            print(self.name)
        }
    }
    
    deinit {
        print("ViewController deallocated")
    }
}

var vc: ViewController? = ViewController()
vc?.setupButton()
vc = nil  // ✅ Deallocated
```

**Solution 2: Unowned (when self always exists):**
```swift
class ImageLoader {
    var onComplete: ((UIImage) -> Void)?
    
    func load() {
        onComplete = { [unowned self] image in
            self.display(image)  // self always exists when closure runs
        }
    }
    
    func display(_ image: UIImage) { }
}
```

**Solution 3: Capture Specific Properties:**
```swift
class ViewController {
    var name = "Main"
    var onButtonTap: (() -> Void)?
    
    func setupButton() {
        // ✅ Capture only name (value type)
        onButtonTap = { [name] in
            print(name)  // No capture of self
        }
    }
}
```

**Escaping Closures:**
```swift
class NetworkManager {
    var handlers: [() -> Void] = []
    
    func request(completion: @escaping () -> Void) {
        handlers.append(completion)  // Stored, potential retain cycle
    }
}

class DataController {
    let manager = NetworkManager()
    var data: [String] = []
    
    func loadData() {
        // ❌ RETAIN CYCLE
        manager.request {
            self.data.append("loaded")
        }
        
        // ✅ CORRECT
        manager.request { [weak self] in
            self?.data.append("loaded")
        }
    }
}
```

**When Weak Self is Needed:**

✅ **Always use [weak self] when:**
- Closure is stored as property
- Closure is `@escaping`
- Self might deallocate before closure executes
- Asynchronous operations (networking, timers)

❌ **Not needed when:**
- Non-escaping closure (default)
- Self can't deallocate before closure completes
- Trailing closure for immediate execution

```swift
// ✅ No weak needed (non-escaping)
[1, 2, 3].map { value in
    return value * self.multiplier
}

// ✅ Weak needed (escaping)
DispatchQueue.main.asyncAfter(deadline: .now() + 1) { [weak self] in
    self?.update()
}

// ✅ Weak needed (stored)
button.onTap = { [weak self] in
    self?.handleTap()
}
```

**Guard Let Self Pattern:**
```swift
// Old style (Swift < 5.8)
button.onTap = { [weak self] in
    guard let self = self else { return }
    self.property = newValue
    self.method()
}

// New style (Swift 5.8+)
button.onTap = { [weak self] in
    guard let self else { return }  // Shorter syntax
    property = newValue
    method()
}
```

---

### Q4: What are common memory leak patterns in iOS development?

**Answer:**

**1. Delegate Retain Cycles:**
```swift
// ❌ WRONG
protocol SomeDelegate {
    func didFinish()
}

class Manager {
    var delegate: SomeDelegate?  // Strong reference
}

class Controller: SomeDelegate {
    let manager = Manager()
    
    init() {
        manager.delegate = self  // Retain cycle
    }
    
    func didFinish() { }
}

// ✅ CORRECT
protocol SomeDelegate: AnyObject {  // Class-only protocol
    func didFinish()
}

class Manager {
    weak var delegate: SomeDelegate?  // Weak reference
}
```

**2. Closure Captures:**
```swift
// ❌ WRONG
class ViewController: UIViewController {
    var timer: Timer?
    
    override func viewDidLoad() {
        timer = Timer.scheduledTimer(withTimeInterval: 1, repeats: true) { _ in
            self.updateUI()  // Strong capture of self
        }
    }
}

// ✅ CORRECT
class ViewController: UIViewController {
    var timer: Timer?
    
    override func viewDidLoad() {
        timer = Timer.scheduledTimer(withTimeInterval: 1, repeats: true) { [weak self] _ in
            self?.updateUI()
        }
    }
    
    deinit {
        timer?.invalidate()
    }
}
```

**3. NotificationCenter Observers:**
```swift
// ❌ WRONG (pre-iOS 9)
class ViewController: UIViewController {
    override func viewDidLoad() {
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(handleNotification),
            name: .someNotification,
            object: nil
        )
        // Forgot to remove observer - retained by notification center
    }
}

// ✅ CORRECT
class ViewController: UIViewController {
    var observer: NSObjectProtocol?
    
    override func viewDidLoad() {
        observer = NotificationCenter.default.addObserver(
            forName: .someNotification,
            object: nil,
            queue: .main
        ) { [weak self] _ in
            self?.handleNotification()
        }
    }
    
    deinit {
        if let observer = observer {
            NotificationCenter.default.removeObserver(observer)
        }
    }
}
```

**4. Completion Handler Storage:**
```swift
// ❌ WRONG
class ImageDownloader {
    var completionHandlers: [(UIImage) -> Void] = []
    
    func download(url: URL, completion: @escaping (UIImage) -> Void) {
        completionHandlers.append(completion)  // Stored forever
    }
}

class ImageView: UIView {
    let downloader = ImageDownloader()
    
    func load(url: URL) {
        downloader.download(url: url) { image in
            self.image = image  // Self retained by closure
        }
    }
}

// ✅ CORRECT
class ImageDownloader {
    private var completionHandlers: [String: (UIImage) -> Void] = [:]
    
    func download(url: URL, completion: @escaping (UIImage) -> Void) -> String {
        let id = UUID().uuidString
        completionHandlers[id] = completion
        
        // Async download
        Task {
            let image = await fetchImage(url)
            completionHandlers[id]?(image)
            completionHandlers[id] = nil  // Remove after execution
        }
        
        return id
    }
}

class ImageView: UIView {
    var downloadID: String?
    
    func load(url: URL) {
        downloadID = downloader.download(url: url) { [weak self] image in
            self?.image = image
        }
    }
}
```

**5. Two-Way Strong References:**
```swift
// ❌ WRONG
class Parent {
    var children: [Child] = []
}

class Child {
    var parent: Parent?  // Strong reference
}

let parent = Parent()
let child = Child()
parent.children.append(child)
child.parent = parent  // Retain cycle

// ✅ CORRECT
class Child {
    weak var parent: Parent?  // Weak reference
}
```

**6. Dispatch Queue Closures:**
```swift
// ❌ WRONG
class DataManager {
    func fetchData() {
        DispatchQueue.global().async {
            let data = self.processData()  // Strong capture
            DispatchQueue.main.async {
                self.updateUI(data)  // Nested strong capture
            }
        }
    }
}

// ✅ CORRECT
class DataManager {
    func fetchData() {
        DispatchQueue.global().async { [weak self] in
            guard let self = self else { return }
            let data = self.processData()
            
            DispatchQueue.main.async { [weak self] in
                self?.updateUI(data)
            }
        }
    }
}
```

**7. Combine/Publishers:**
```swift
// ❌ WRONG
class ViewModel {
    var cancellables = Set<AnyCancellable>()
    
    func observe() {
        publisher
            .sink { value in
                self.handleValue(value)  // Strong capture
            }
            .store(in: &cancellables)
    }
}

// ✅ CORRECT
class ViewModel {
    var cancellables = Set<AnyCancellable>()
    
    func observe() {
        publisher
            .sink { [weak self] value in
                self?.handleValue(value)
            }
            .store(in: &cancellables)
    }
}
```

**Detection Tools:**
- Xcode Memory Graph Debugger
- Instruments (Leaks, Allocations)
- `deinit` print statements
- Runtime sanitizers

---

### Q5: How does memory management differ for value types vs reference types?

**Answer:**

**Value Types (Struct, Enum) - Stack Allocated:**

**No ARC:**
```swift
struct Point {
    var x: Int
    var y: Int
}

func example() {
    let point = Point(x: 10, y: 20)  // Stack allocated
    print(point)
}  // Automatically deallocated when out of scope
```

**Copy Semantics:**
```swift
var point1 = Point(x: 10, y: 20)
var point2 = point1  // COPIED, not referenced

point2.x = 30
print(point1.x)  // 10 (unchanged)
print(point2.x)  // 30
```

**No Retain Cycles Possible:**
```swift
struct Node {
    var value: Int
    var next: Node?  // ❌ Error: recursive value type
}

// Structs can't have circular references
```

**Reference Types (Class) - Heap Allocated:**

**ARC Managed:**
```swift
class Person {
    var name: String
    init(name: String) { self.name = name }
    deinit { print("Deallocated") }
}

func example() {
    let person = Person(name: "Alice")  // Heap allocated, RC = 1
}  // RC = 0, deallocated
```

**Reference Semantics:**
```swift
var person1 = Person(name: "Bob")
var person2 = person1  // REFERENCED, not copied

person2.name = "Charlie"
print(person1.name)  // Charlie (same object)
print(person2.name)  // Charlie
```

**Can Have Retain Cycles:**
```swift
class Node {
    var value: Int
    var next: Node?  // ✅ Allowed, but watch for cycles
}

let node1 = Node(value: 1)
let node2 = Node(value: 2)
node1.next = node2
node2.next = node1  // Retain cycle
```

**Mixed Types:**
```swift
struct Container {
    var items: [String]  // Value type containing reference type (Array)
}

var container1 = Container(items: ["A", "B"])
var container2 = container1  // Container copied, but Array referenced

container2.items.append("C")
print(container1.items)  // ["A", "B"] (Array uses copy-on-write)
print(container2.items)  // ["A", "B", "C"]
```

**Copy-on-Write (CoW):**
Standard library collections optimize copying:

```swift
var array1 = [1, 2, 3]  // Heap allocated
var array2 = array1      // Shared storage (no copy yet)

array2.append(4)         // NOW copied (modified)

print(array1)  // [1, 2, 3]
print(array2)  // [1, 2, 3, 4]
```

**Performance Comparison:**

| Aspect | Value Type | Reference Type |
|--------|-----------|----------------|
| Allocation | Stack (fast) | Heap (slower) |
| Copying | May copy | Shares reference |
| ARC Overhead | None | Yes |
| Thread Safety | Safer | Requires synchronization |
| Memory Layout | Predictable | Scattered (heap) |

**When to Use:**

**Value Types:**
- Data models (simple types)
- Immutable data
- Thread-safe code
- No identity needed

**Reference Types:**
- Shared state
- Identity matters
- Inheritance needed
- Large objects (avoid copying)

**Best Practice:**
- Prefer value types (structs) by default
- Use classes when sharing state or need inheritance
- Be aware of reference types inside value types

---

## 📝 Practice Exercises

### Exercise 1: Fix Memory Leaks
Given code with retain cycles, identify and fix all leaks.

**Requirements:**
1. Identify all retain cycles
2. Fix using weak/unowned
3. Verify with deinit prints
4. Test edge cases

---

### Exercise 2: Build Observer Pattern
Create a memory-safe observer pattern implementation.

**Requirements:**
1. Support multiple observers
2. Automatic cleanup when observers deallocate
3. No retain cycles
4. Thread-safe

---

### Exercise 3: Implement Weak Collection
Create a data structure that holds weak references.

**Requirements:**
1. `WeakArray<T: AnyObject>`
2. Automatically removes nil entries
3. Count property
4. Iterate over valid references

---

## 🔗 Key Takeaways

1. **ARC is automatic** - Reference counting happens automatically
2. **Strong by default** - All references are strong unless specified
3. **Weak for optional cycles** - Use when reference can be nil
4. **Unowned for guaranteed cycles** - Use when reference always valid
5. **[weak self] in closures** - Required for escaping closures
6. **No cycles in value types** - Structs can't have retain cycles
7. **Delegates should be weak** - Standard pattern
8. **Deinit for verification** - Use to verify deallocation
9. **Memory graph debugger** - Xcode tool for finding leaks
10. **Immediate deallocation** - Objects deallocated when RC = 0

**Best Practices:**
- Use value types (structs) when possible
- Always use `weak` for delegates
- Add `[weak self]` to escaping closures
- Implement `deinit` during development
- Profile with Instruments
- Test deallocation in unit tests

**Remember:** Understanding ARC is critical for building efficient iOS apps!
