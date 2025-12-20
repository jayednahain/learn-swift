# Step 10: Structures (Structs)

## 📚 Description

Structures are custom data types that encapsulate related properties and methods. In Swift, structs are **value types** (copied when assigned or passed), making them fundamental to Swift programming. Most Swift standard library types (String, Array, Dictionary) are structs!

### What You'll Learn:
- Struct syntax and definition
- Properties (stored and computed)
- Methods (instance and static/type methods)
- Initializers (default and custom)
- Value semantics (copy-on-write)
- Mutating methods
- When to use structs vs classes

### Key Concepts:
- **Value Types**: Structs are copied when assigned (unlike reference types)
- **Memberwise Initializer**: Automatic initialization from properties
- **Mutating**: Keyword for methods that modify the struct
- **Copy-on-Write**: Efficient copying behavior
- **No Inheritance**: Structs can't inherit (use protocols instead)

---

## 💡 Clear Examples

### Example 1: Basic Struct Syntax and Properties

```swift
// BASIC STRUCT DEFINITION
struct Person {
    var name: String
    var age: Int
}

// Create instance (using automatic memberwise initializer)
var person1 = Person(name: "Alice", age: 30)
print(person1.name)  // Alice
print(person1.age)   // 30

// Modify properties (only if struct is var)
person1.age = 31
print(person1.age)  // 31

// Constants can't be modified
let person2 = Person(name: "Bob", age: 25)
// person2.age = 26  // ❌ Error: person2 is a constant

// VALUE TYPE SEMANTICS
var person3 = person1  // Creates a COPY
person3.name = "Alice Smith"

print(person1.name)  // Alice (unchanged)
print(person3.name)  // Alice Smith (changed)

// PROPERTIES

struct Rectangle {
    // Stored properties
    var width: Double
    var height: Double
    
    // Computed property (calculated from other properties)
    var area: Double {
        return width * height
    }
    
    var perimeter: Double {
        return 2 * (width + height)
    }
    
    // Computed property with getter and setter
    var center: (x: Double, y: Double) {
        get {
            return (width / 2, height / 2)
        }
        set {
            // newValue is the new value being set
            width = newValue.x * 2
            height = newValue.y * 2
        }
    }
}

var rect = Rectangle(width: 10, height: 20)
print(rect.area)       // 200.0
print(rect.perimeter)  // 60.0
print(rect.center)     // (5.0, 10.0)

rect.center = (x: 3, y: 4)
print(rect.width)   // 6.0
print(rect.height)  // 8.0

// PROPERTY OBSERVERS

struct Temperature {
    var celsius: Double {
        willSet {
            print("About to change from \(celsius) to \(newValue)")
        }
        didSet {
            print("Changed from \(oldValue) to \(celsius)")
        }
    }
    
    var fahrenheit: Double {
        get {
            return celsius * 9/5 + 32
        }
        set {
            celsius = (newValue - 32) * 5/9
        }
    }
}

var temp = Temperature(celsius: 20)
// About to change from 20.0 to 25.0
// Changed from 20.0 to 25.0
temp.celsius = 25

print(temp.fahrenheit)  // 77.0

// DEFAULT PROPERTY VALUES

struct User {
    var username: String
    var isActive: Bool = true  // Default value
    var loginCount: Int = 0
}

let user1 = User(username: "alice")  // isActive and loginCount use defaults
print(user1.isActive)    // true
print(user1.loginCount)  // 0

// Can override defaults
let user2 = User(username: "bob", isActive: false, loginCount: 5)
print(user2.isActive)    // false

// LAZY PROPERTIES (computed only when accessed)

struct DataImporter {
    var filename = "data.txt"
    
    init() {
        print("DataImporter initialized")
    }
}

struct DataManager {
    lazy var importer = DataImporter()
    var data: [String] = []
}

var manager = DataManager()
print("Manager created")
// DataImporter not yet initialized

manager.importer.filename = "test.txt"
// Now prints: DataImporter initialized

// TYPE PROPERTIES (static)

struct Math {
    static let pi = 3.14159
    static var computationCount = 0
    
    static func square(_ n: Double) -> Double {
        computationCount += 1
        return n * n
    }
}

print(Math.pi)  // 3.14159
print(Math.square(5))  // 25.0
print(Math.computationCount)  // 1

// iOS Example: View Configuration
struct ButtonStyle {
    var backgroundColor: String
    var textColor: String
    var cornerRadius: Double
    var fontSize: Double
    
    // Common presets as static properties
    static let primary = ButtonStyle(
        backgroundColor: "#007AFF",
        textColor: "#FFFFFF",
        cornerRadius: 8.0,
        fontSize: 16.0
    )
    
    static let secondary = ButtonStyle(
        backgroundColor: "#E0E0E0",
        textColor: "#000000",
        cornerRadius: 8.0,
        fontSize: 14.0
    )
}

let button = ButtonStyle.primary
print(button.backgroundColor)  // #007AFF
```

**Explanation:**
- Structs group related data together
- Memberwise initializer created automatically
- Value semantics: assignments create copies
- Computed properties calculate values on-the-fly
- Property observers react to changes
- Lazy properties delay initialization
- Static properties belong to the type, not instances

---

### Example 2: Methods and Mutating Functions

```swift
// INSTANCE METHODS

struct Counter {
    var count = 0
    
    // Method that reads properties
    func isZero() -> Bool {
        return count == 0
    }
    
    func currentValue() -> Int {
        return count
    }
}

var counter = Counter()
print(counter.isZero())  // true

// MUTATING METHODS
// Methods that modify struct properties must be marked 'mutating'

struct Point {
    var x: Double
    var y: Double
    
    // ❌ This won't work - trying to modify self
    // func moveBy(x deltaX: Double, y deltaY: Double) {
    //     x += deltaX  // Error: self is immutable
    //     y += deltaY
    // }
    
    // ✅ Must use mutating
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
    
    // Mutating can replace self entirely
    mutating func reset() {
        self = Point(x: 0, y: 0)
    }
    
    // Non-mutating method (reads only)
    func distance(to other: Point) -> Double {
        let dx = x - other.x
        let dy = y - other.y
        return (dx * dx + dy * dy).squareRoot()
    }
}

var point = Point(x: 10, y: 20)
point.moveBy(x: 5, y: -3)
print(point)  // Point(x: 15.0, y: 17.0)

let point2 = Point(x: 0, y: 0)
// point2.moveBy(x: 1, y: 1)  // ❌ Error: point2 is a constant

let distance = point.distance(to: point2)
print(distance)  // ~23.0

// TYPE METHODS (static)

struct Calculator {
    static func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
    
    static func multiply(_ a: Int, _ b: Int) -> Int {
        return a * b
    }
}

// Call on type, not instance
print(Calculator.add(5, 3))       // 8
print(Calculator.multiply(4, 7))  // 28

// METHODS WITH RETURN VALUES

struct BankAccount {
    var balance: Double
    
    func canWithdraw(_ amount: Double) -> Bool {
        return balance >= amount
    }
    
    mutating func deposit(_ amount: Double) {
        balance += amount
    }
    
    mutating func withdraw(_ amount: Double) -> Bool {
        if canWithdraw(amount) {
            balance -= amount
            return true
        }
        return false
    }
    
    func formattedBalance() -> String {
        return String(format: "$%.2f", balance)
    }
}

var account = BankAccount(balance: 100.0)
account.deposit(50.0)
print(account.formattedBalance())  // $150.00

if account.withdraw(75.0) {
    print("Withdrawal successful")
    print(account.formattedBalance())  // $75.00
} else {
    print("Insufficient funds")
}

// SELF PROPERTY

struct Size {
    var width: Double
    var height: Double
    
    func aspectRatio() -> Double {
        return width / height
    }
    
    // Use 'self' to distinguish parameter from property
    mutating func scale(by factor: Double) {
        self.width *= factor
        self.height *= factor
    }
    
    func scaled(by factor: Double) -> Size {
        return Size(width: self.width * factor, height: self.height * factor)
    }
}

var size = Size(width: 100, height: 50)
size.scale(by: 2)
print(size)  // Size(width: 200.0, height: 100.0)

let doubled = size.scaled(by: 2)
print(doubled)  // Size(width: 400.0, height: 200.0)

// REAL-WORLD iOS EXAMPLE

struct Message {
    let id: String
    var text: String
    var isRead: Bool
    let timestamp: Date
    let senderId: String
    
    mutating func markAsRead() {
        isRead = true
    }
    
    func timeAgo() -> String {
        let seconds = Date().timeIntervalSince(timestamp)
        
        if seconds < 60 {
            return "Just now"
        } else if seconds < 3600 {
            return "\(Int(seconds / 60))m ago"
        } else if seconds < 86400 {
            return "\(Int(seconds / 3600))h ago"
        } else {
            return "\(Int(seconds / 86400))d ago"
        }
    }
    
    func isFromUser(_ userId: String) -> Bool {
        return senderId == userId
    }
}

var message = Message(
    id: "msg-1",
    text: "Hello!",
    isRead: false,
    timestamp: Date().addingTimeInterval(-3600),
    senderId: "user-123"
)

print(message.timeAgo())  // 1h ago
message.markAsRead()
print(message.isRead)  // true

// NESTED TYPES

struct ChessBoard {
    struct Square {
        let row: Int
        let column: Int
        
        var isValid: Bool {
            return (0...7).contains(row) && (0...7).contains(column)
        }
        
        var color: Color {
            return (row + column) % 2 == 0 ? .black : .white
        }
    }
    
    enum Color {
        case black, white
    }
    
    var pieces: [[String?]] = Array(repeating: Array(repeating: nil, count: 8), count: 8)
    
    func squareAt(row: Int, column: Int) -> Square {
        return Square(row: row, column: column)
    }
}

let board = ChessBoard()
let square = board.squareAt(row: 0, column: 0)
print(square.isValid)  // true
print(square.color)    // black
```

**Explanation:**
- Instance methods operate on specific instances
- Mutating methods modify the struct (require `var` instance)
- Type methods (static) are called on the type itself
- Methods can return values or be void
- Nested types organize related code
- `self` refers to the current instance

---

### Example 3: Custom Initializers and Advanced Patterns

```swift
// CUSTOM INITIALIZERS

struct Color {
    let red: Double
    let green: Double
    let blue: Double
    let alpha: Double
    
    // Custom initializer
    init(red: Double, green: Double, blue: Double, alpha: Double = 1.0) {
        self.red = red
        self.green = green
        self.blue = blue
        self.alpha = alpha
    }
    
    // Convenience initializer for gray
    init(white: Double, alpha: Double = 1.0) {
        self.init(red: white, green: white, blue: white, alpha: alpha)
    }
    
    // Initializer from hex string
    init?(hex: String) {
        let hex = hex.trimmingCharacters(in: CharacterSet.alphanumerics.inverted)
        var int: UInt64 = 0
        
        guard Scanner(string: hex).scanHexInt64(&int) else {
            return nil  // Failable initializer
        }
        
        let r, g, b: Double
        switch hex.count {
        case 6:  // RGB (e.g., "FF0000")
            (r, g, b) = (
                Double((int >> 16) & 0xFF) / 255.0,
                Double((int >> 8) & 0xFF) / 255.0,
                Double(int & 0xFF) / 255.0
            )
        default:
            return nil
        }
        
        self.init(red: r, green: g, blue: b)
    }
    
    // Static factory methods
    static let black = Color(white: 0)
    static let white = Color(white: 1)
    static let red = Color(red: 1, green: 0, blue: 0)
    static let green = Color(red: 0, green: 1, blue: 0)
    static let blue = Color(red: 0, green: 0, blue: 1)
}

let color1 = Color(red: 0.5, green: 0.7, blue: 0.9)
let color2 = Color(white: 0.5)
let color3 = Color.red

if let hexColor = Color(hex: "#FF0000") {
    print("Created color from hex")
} else {
    print("Invalid hex")
}

// MEMBERWISE INITIALIZER
// Automatically generated if no custom init defined

struct Product {
    var name: String
    var price: Double
    var inStock: Bool
    
    // Swift generates:
    // init(name: String, price: Double, inStock: Bool) {
    //     self.name = name
    //     self.price = price
    //     self.inStock = inStock
    // }
}

let product = Product(name: "iPhone", price: 999.99, inStock: true)

// CUSTOM INIT + MEMBERWISE
// If you want both, use extension

struct Book {
    var title: String
    var author: String
    var pages: Int
}

extension Book {
    init(title: String, author: String) {
        self.init(title: title, author: author, pages: 0)
    }
}

let book1 = Book(title: "1984", author: "George Orwell", pages: 328)  // Memberwise
let book2 = Book(title: "Animal Farm", author: "George Orwell")       // Custom

// FAILABLE INITIALIZERS

struct Email {
    let address: String
    
    init?(address: String) {
        guard address.contains("@"), address.contains(".") else {
            return nil
        }
        self.address = address
    }
}

if let email = Email(address: "user@example.com") {
    print("Valid: \(email.address)")
} else {
    print("Invalid email")
}

let invalid = Email(address: "not-an-email")
print(invalid)  // nil

// COMPUTED PROPERTY IN INIT

struct Circle {
    var radius: Double
    var area: Double
    var circumference: Double
    
    init(radius: Double) {
        self.radius = radius
        self.area = .pi * radius * radius
        self.circumference = 2 * .pi * radius
    }
}

let circle = Circle(radius: 5)
print(circle.area)  // ~78.54

// REAL-WORLD: API MODEL

struct APIUser: Codable {
    let id: String
    let username: String
    let email: String
    var fullName: String?
    var avatarURL: String?
    let createdAt: Date
    
    // Custom coding keys (if API uses different names)
    enum CodingKeys: String, CodingKey {
        case id
        case username
        case email
        case fullName = "full_name"
        case avatarURL = "avatar_url"
        case createdAt = "created_at"
    }
    
    // Convenience computed properties
    var displayName: String {
        return fullName ?? username
    }
    
    var hasAvatar: Bool {
        return avatarURL != nil
    }
}

// BUILDER PATTERN

struct NetworkRequest {
    let url: URL
    let method: String
    let headers: [String: String]
    let body: Data?
    let timeout: TimeInterval
    
    // Builder
    struct Builder {
        private var url: URL?
        private var method = "GET"
        private var headers: [String: String] = [:]
        private var body: Data?
        private var timeout: TimeInterval = 30
        
        mutating func url(_ url: URL) -> Builder {
            self.url = url
            return self
        }
        
        mutating func method(_ method: String) -> Builder {
            self.method = method
            return self
        }
        
        mutating func header(key: String, value: String) -> Builder {
            headers[key] = value
            return self
        }
        
        mutating func body(_ data: Data) -> Builder {
            self.body = data
            return self
        }
        
        mutating func timeout(_ seconds: TimeInterval) -> Builder {
            self.timeout = seconds
            return self
        }
        
        func build() -> NetworkRequest? {
            guard let url = url else { return nil }
            return NetworkRequest(
                url: url,
                method: method,
                headers: headers,
                body: body,
                timeout: timeout
            )
        }
    }
}

var builder = NetworkRequest.Builder()
let request = builder
    .url(URL(string: "https://api.example.com/users")!)
    .method("POST")
    .header(key: "Authorization", value: "Bearer token")
    .timeout(60)
    .build()

// COPY-ON-WRITE OPTIMIZATION

struct LargeDataSet {
    private var storage: [Int]
    
    init(count: Int) {
        storage = Array(repeating: 0, count: count)
    }
    
    var count: Int {
        return storage.count
    }
    
    subscript(index: Int) -> Int {
        get {
            return storage[index]
        }
        set {
            // Copy-on-write: only copy if storage is shared
            if !isKnownUniquelyReferenced(&storage) {
                storage = storage  // Creates copy
            }
            storage[index] = newValue
        }
    }
}

// PROTOCOL CONFORMANCE

struct Task: Identifiable, Equatable, Hashable {
    let id: UUID
    var title: String
    var isCompleted: Bool
    let createdAt: Date
    
    init(title: String, isCompleted: Bool = false) {
        self.id = UUID()
        self.title = title
        self.isCompleted = false
        self.createdAt = Date()
    }
    
    // Equatable (can be auto-synthesized)
    static func == (lhs: Task, rhs: Task) -> Bool {
        return lhs.id == rhs.id
    }
    
    // Hashable (can be auto-synthesized)
    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}

let task1 = Task(title: "Learn Swift")
let task2 = Task(title: "Build App")

print(task1 == task2)  // false (different IDs)

let taskSet: Set<Task> = [task1, task2]
print(taskSet.count)  // 2

// SWIFTUI EXAMPLE

struct Post: Identifiable {
    let id: UUID
    var title: String
    var content: String
    var author: String
    let publishedAt: Date
    var likes: Int
    var isBookmarked: Bool
    
    init(title: String, content: String, author: String) {
        self.id = UUID()
        self.title = title
        self.content = content
        self.author = author
        self.publishedAt = Date()
        self.likes = 0
        self.isBookmarked = false
    }
    
    mutating func toggleBookmark() {
        isBookmarked.toggle()
    }
    
    mutating func like() {
        likes += 1
    }
    
    var timeAgoString: String {
        let seconds = Date().timeIntervalSince(publishedAt)
        let minutes = Int(seconds / 60)
        let hours = minutes / 60
        let days = hours / 24
        
        if days > 0 {
            return "\(days)d ago"
        } else if hours > 0 {
            return "\(hours)h ago"
        } else if minutes > 0 {
            return "\(minutes)m ago"
        } else {
            return "Just now"
        }
    }
}

var post = Post(
    title: "Understanding Swift Structs",
    content: "Structs are value types...",
    author: "Alice"
)

post.like()
post.toggleBookmark()
print("\(post.title) - \(post.likes) likes")
```

**Explanation:**
- Custom initializers provide flexible object creation
- Failable initializers return optional (nil on failure)
- Memberwise initializers are automatic
- Use extensions to keep both memberwise and custom inits
- Builder pattern enables fluent API design
- Protocol conformance makes structs work with standard library
- Value semantics prevent unintended sharing

---

## 🎯 2025 Interview Questions

### Question 1: What's the difference between value types (structs) and reference types (classes) in Swift? When should you use each?

**Answer:**

**Value Types (Struct, Enum):**
- **Copied** when assigned or passed
- Each instance has **independent copy** of data
- Stored on the **stack** (usually faster)
- **No inheritance**
- **Thread-safe** by default (no shared state)

**Reference Types (Class):**
- **Referenced** when assigned or passed
- Multiple variables can **share same instance**
- Stored on the **heap** (slightly slower)
- Supports **inheritance**
- Requires **synchronization** for thread safety

**Demonstration:**

```swift
// VALUE TYPE (Struct)
struct PointStruct {
    var x: Int
    var y: Int
}

var point1 = PointStruct(x: 10, y: 20)
var point2 = point1  // COPY created

point2.x = 100

print(point1.x)  // 10 (unchanged)
print(point2.x)  // 100 (changed)

// REFERENCE TYPE (Class)
class PointClass {
    var x: Int
    var y: Int
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

var point3 = PointClass(x: 10, y: 20)
var point4 = point3  // SAME instance referenced

point4.x = 100

print(point3.x)  // 100 (changed!)
print(point4.x)  // 100 (same instance)
```

**When to Use Structs:**

1. **Simple data structures**
```swift
struct User {
    let id: String
    var name: String
    var email: String
}
```

2. **Immutable data**
```swift
struct Coordinates {
    let latitude: Double
    let longitude: Double
}
```

3. **No inheritance needed**
```swift
struct Color {
    let red, green, blue: Double
}
```

4. **Value semantics desired (local modifications)**
```swift
var originalUser = User(id: "1", name: "Alice", email: "alice@example.com")
var modifiedUser = originalUser
modifiedUser.name = "Alice Smith"
// originalUser.name is still "Alice"
```

5. **Most Swift types** (String, Array, Dictionary, Int, etc.)

**When to Use Classes:**

1. **Identity matters** (two instances with same data are different)
```swift
class ViewController: UIViewController {
    // Each screen is a unique object
}
```

2. **Shared mutable state**
```swift
class NetworkManager {
    static let shared = NetworkManager()  // Singleton
    var isConnected = false
}
```

3. **Inheritance needed**
```swift
class Animal {
    func makeSound() {}
}

class Dog: Animal {
    override func makeSound() {
        print("Woof!")
    }
}
```

4. **Objective-C interop** (required for UIKit classes)
```swift
class CustomView: UIView {
    // Must be class to inherit from UIView
}
```

5. **Deinitializers needed** (cleanup when object destroyed)
```swift
class FileHandler {
    deinit {
        // Close file handle
    }
}
```

**Apple's Recommendation (2025):**

> "Use structs by default. Use classes when you need inheritance or reference semantics."

**Real-World Example:**

```swift
// ✅ Struct - Independent user profiles
struct UserProfile {
    var name: String
    var bio: String
    var followers: Int
}

// Can safely copy and modify without affecting original
var draft = currentProfile
draft.bio = "New bio"
// currentProfile.bio unchanged

// ✅ Class - Shared network service
class APIService {
    static let shared = APIService()
    var authToken: String?
    
    func request() {
        // All parts of app use same instance
    }
}

// ✅ Struct - SwiftUI Views (immutable, redrawn frequently)
struct ProfileView: View {
    let user: UserProfile
    var body: some View {
        VStack {
            Text(user.name)
            Text(user.bio)
        }
    }
}

// ✅ Class - View Controllers (single instance per screen)
class ProfileViewController: UIViewController {
    // Unique instance manages screen lifecycle
}
```

**Performance Comparison:**

| Aspect | Struct | Class |
|--------|--------|-------|
| Allocation | Stack (fast) | Heap (slower) |
| Copy | O(n) | O(1) reference |
| Deallocation | Automatic (stack) | ARC (overhead) |
| Thread safety | Default | Requires locks |
| Memory | Inline | Pointer indirection |

**Key Takeaway:** Prefer structs for data models, use classes for identity/inheritance/shared state.

---

### Question 2: Explain the `mutating` keyword. Why is it needed for structs but not classes?

**Answer:**

**The Problem:**

Structs are **value types** passed **by value** to methods. Methods receive a **constant copy** of self by default.

```swift
struct Counter {
    var count = 0
    
    // ❌ Won't compile
    // func increment() {
    //     count += 1  // Error: Cannot assign to property: 'self' is immutable
    // }
}
```

**The Solution: `mutating`**

```swift
struct Counter {
    var count = 0
    
    mutating func increment() {
        count += 1  // ✅ Allowed with mutating
    }
    
    mutating func reset() {
        self = Counter()  // Can even replace entire instance
    }
}

var counter = Counter()
counter.increment()
print(counter.count)  // 1

// ❌ Can't call mutating method on constant
let constantCounter = Counter()
// constantCounter.increment()  // Error: Cannot use mutating member on immutable value
```

**Why Classes Don't Need `mutating`:**

Classes are **reference types**. Methods receive a **reference** to the same instance, not a copy.

```swift
class CounterClass {
    var count = 0
    
    func increment() {  // No 'mutating' needed
        count += 1
    }
}

let counterClass = CounterClass()  // Even with 'let'
counterClass.increment()  // Can modify properties
print(counterClass.count)  // 1

// 'let' prevents reassignment, not property changes
// counterClass = CounterClass()  // ❌ Error
```

**What `mutating` Actually Does:**

```swift
// Conceptually:
// func increment() {
//     var copy = self
//     copy.count += 1
//     self = copy  // Replace self with modified copy
// }
```

**Real-World Examples:**

**1. Game State:**
```swift
struct PlayerState {
    var health: Int
    var position: (x: Int, y: Int)
    var inventory: [String]
    
    mutating func takeDamage(_ amount: Int) {
        health = max(0, health - amount)
    }
    
    mutating func move(dx: Int, dy: Int) {
        position.x += dx
        position.y += dy
    }
    
    mutating func pickUpItem(_ item: String) {
        inventory.append(item)
    }
    
    // Non-mutating
    func isAlive() -> Bool {
        return health > 0
    }
}

var player = PlayerState(health: 100, position: (0, 0), inventory: [])
player.takeDamage(30)
player.move(dx: 5, dy: 3)
player.pickUpItem("sword")
```

**2. Array Operations (built-in):**
```swift
var numbers = [1, 2, 3]

// mutating methods
numbers.append(4)        // mutating func append(_ element: Int)
numbers.remove(at: 0)    // mutating func remove(at: Int) -> Int
numbers.sort()           // mutating func sort()

// non-mutating methods (return new array)
let doubled = numbers.map { $0 * 2 }  // func map<T>(_ transform: (Int) -> T) -> [T]
let sorted = numbers.sorted()         // func sorted() -> [Int]
```

**3. SwiftUI Binding:**
```swift
struct ContentView: View {
    @State private var text = ""
    
    var body: some View {
        TextField("Enter text", text: $text)
        // $ creates a Binding (allows mutation from child view)
    }
}
```

**Compiler Optimization:**

Swift optimizes structs with copy-on-write for performance:

```swift
var array1 = [1, 2, 3]
var array2 = array1  // No copy yet (shares storage)

array2.append(4)  // NOW copies (because of mutation)

// array1 and array2 now have separate storage
```

**Key Rules:**

1. **Mutating methods require `var` instance:**
```swift
var mutablePoint = Point(x: 0, y: 0)
mutablePoint.move(dx: 5, dy: 3)  // ✅

let immutablePoint = Point(x: 0, y: 0)
// immutablePoint.move(dx: 5, dy: 3)  // ❌ Error
```

2. **Can't call mutating methods in non-mutating methods:**
```swift
struct Example {
    var value = 0
    
    mutating func increment() {
        value += 1
    }
    
    func tryIncrement() {
        // increment()  // ❌ Error: Cannot use mutating member
    }
}
```

3. **Enums also need mutating:**
```swift
enum State {
    case loading, loaded, error
    
    mutating func fail() {
        self = .error
    }
}
```

**Interview Answer Summary:**

- `mutating` allows struct methods to modify properties
- Needed because structs are value types (methods receive constant copy)
- Classes don't need it (reference semantics)
- Compiler enforces with `var` instances
- Swift optimizes with copy-on-write

---

### Question 3: How does Swift implement copy-on-write optimization for collection types?

**Answer:**

**Copy-on-Write (COW)** is a performance optimization where copies are **delayed** until modification happens. Swift collections (Array, Dictionary, Set, String) use COW to balance value semantics with performance.

**Without COW (Naive Implementation):**

```swift
// ❌ Expensive: copies 1 million elements immediately
var array1 = Array(repeating: 0, count: 1_000_000)
var array2 = array1  // Copies all 1M elements
// Most are never used!
```

**With COW (Swift's Implementation):**

```swift
// ✅ Efficient: shares storage until mutation
var array1 = Array(repeating: 0, count: 1_000_000)
var array2 = array1  // Shares storage (no copy yet!)

// Only NOW does it copy (because of mutation)
array2.append(1)
```

**How It Works:**

Arrays internally use a **reference-counted storage buffer**:

```swift
// Simplified internal implementation
struct Array<Element> {
    private var storage: ArrayStorage<Element>  // Reference type
    
    var count: Int {
        return storage.count
    }
    
    subscript(index: Int) -> Element {
        get {
            return storage[index]  // No copy needed for reads
        }
        set {
            // Check if storage is shared
            if !isKnownUniquelyReferenced(&storage) {
                // COPY only if shared
                storage = storage.copy()
            }
            storage[index] = newValue
        }
    }
}
```

**Demonstration:**

```swift
var array1 = [1, 2, 3, 4, 5]
var array2 = array1

// At this point:
// array1 and array2 share the same storage buffer
// No copy has been made

print(array1)  // [1, 2, 3, 4, 5]
print(array2)  // [1, 2, 3, 4, 5]

// NOW modify array2
array2.append(6)

// At this point:
// array2 got its own copy
// array1 still has original

print(array1)  // [1, 2, 3, 4, 5]
print(array2)  // [1, 2, 3, 4, 5, 6]
```

**Key Function: `isKnownUniquelyReferenced`**

```swift
class Storage {
    var data: [Int]
    init(data: [Int]) {
        self.data = data
    }
}

struct MyArray {
    private var storage: Storage
    
    init(_ data: [Int]) {
        storage = Storage(data: data)
    }
    
    var count: Int {
        return storage.data.count
    }
    
    subscript(index: Int) -> Int {
        get {
            return storage.data[index]
        }
        set {
            // Check if we're the only owner
            if !isKnownUniquelyReferenced(&storage) {
                print("Making a copy!")
                storage = Storage(data: storage.data)
            }
            storage.data[index] = newValue
        }
    }
}

var arr1 = MyArray([1, 2, 3])
var arr2 = arr1  // Shares storage

print(arr1[0])  // 1 (no copy)
arr2[0] = 100   // Triggers copy!
// Output: Making a copy!

print(arr1[0])  // 1 (original unchanged)
print(arr2[0])  // 100 (modified copy)
```

**Benefits:**

1. **Performance:** Avoids unnecessary copies
2. **Memory:** Shares storage when possible
3. **Value Semantics:** Still behaves like independent copies

**Performance Comparison:**

```swift
// Measure array copy performance
import Foundation

func testNaiveCopy() {
    var array1 = Array(repeating: 0, count: 1_000_000)
    let start = Date()
    
    // Force immediate copy
    var array2 = array1
    array2[0] = 1  // Triggers COW
    
    let elapsed = Date().timeIntervalSince(start)
    print("COW copy: \(elapsed)s")
}

testNaiveCopy()  // Very fast (~0.001s)

// If Swift copied immediately on assignment:
// Would need to copy 1M elements twice → much slower!
```

**When COW Happens:**

```swift
var arr = [1, 2, 3]

// ✅ No copy (read-only)
let value = arr[0]
let count = arr.count
let isEmpty = arr.isEmpty

// ✅ Copy triggered (mutation)
arr.append(4)
arr[0] = 10
arr.remove(at: 0)
arr.sort()

// ✅ No copy (creates new array)
let mapped = arr.map { $0 * 2 }
let filtered = arr.filter { $0 > 2 }
```

**Custom COW Implementation:**

```swift
final class Storage<T> {
    var value: T
    init(_ value: T) {
        self.value = value
    }
}

struct COWArray<Element> {
    private var storage: Storage<[Element]>
    
    init() {
        storage = Storage([])
    }
    
    init(_ elements: [Element]) {
        storage = Storage(elements)
    }
    
    private mutating func ensureUnique() {
        if !isKnownUniquelyReferenced(&storage) {
            storage = Storage(storage.value)
        }
    }
    
    var count: Int {
        return storage.value.count
    }
    
    mutating func append(_ element: Element) {
        ensureUnique()
        storage.value.append(element)
    }
    
    subscript(index: Int) -> Element {
        get {
            return storage.value[index]
        }
        set {
            ensureUnique()
            storage.value[index] = newValue
        }
    }
}

var cow1 = COWArray([1, 2, 3])
var cow2 = cow1  // Shares storage

cow2.append(4)  // Copies now
// cow1: [1, 2, 3]
// cow2: [1, 2, 3, 4]
```

**iOS Example:**

```swift
struct MessageList {
    private var messages: [Message] = []
    
    // Efficient: shares storage until mutation
    mutating func add(_ message: Message) {
        messages.append(message)  // COW handles copy if needed
    }
    
    func filtered(by userId: String) -> [Message] {
        // No copy: just iterates shared storage
        return messages.filter { $0.senderId == userId }
    }
}

var list1 = MessageList()
// ... add 10,000 messages

var list2 = list1  // Fast: no copy
let userMessages = list2.filtered(by: "user-123")  // Fast: no copy
```

**Key Takeaway:**

COW gives you the **safety of value semantics** with the **performance of reference semantics** for collections. Swift handles this automatically for Array, Dictionary, Set, and String.

---

### Question 4: When should you use a struct versus a class in SwiftUI? How does this affect view updates?

**Answer:**

**SwiftUI Views: Always Structs**

```swift
// ✅ Correct: SwiftUI views are structs
struct ContentView: View {
    var body: some View {
        Text("Hello")
    }
}

// ❌ Wrong: Views should NOT be classes
// class BadView: View {  // Compiles but defeats SwiftUI optimizations
//     var body: some View {
//         Text("Hello")
//     }
// }
```

**Why Structs for Views?**

1. **Lightweight:** Created and destroyed frequently
2. **Immutable by default:** View is description, not object
3. **Diffing:** SwiftUI compares structs to detect changes
4. **Performance:** Stack allocation, no reference counting

**State Management:**

**Property Wrappers** determine update behavior:

```swift
struct ProfileView: View {
    // Local state (triggers view updates)
    @State private var isEditing = false
    
    // Observed object (class, triggers view updates)
    @StateObject private var viewModel = ProfileViewModel()
    
    // Passed binding (triggers view updates)
    @Binding var showSheet: Bool
    
    // Environment (global, triggers view updates)
    @EnvironmentObject var appState: AppState
    
    // Simple property (NO auto-updates)
    let user: User
    
    var body: some View {
        VStack {
            Text(user.name)
            if isEditing {
                TextField("Name", text: $viewModel.name)
            }
            Button("Edit") {
                isEditing.toggle()  // Triggers view update
            }
        }
    }
}
```

**Struct vs Class for Models:**

**Use Struct (Immutable Data):**

```swift
// ✅ Struct for simple data
struct User: Identifiable {
    let id: UUID
    let name: String
    let email: String
}

struct ProfileView: View {
    let user: User  // Passed as value
    
    var body: some View {
        VStack {
            Text(user.name)
            Text(user.email)
        }
    }
}

// Updates require replacing entire struct
@State private var user = User(id: UUID(), name: "Alice", email: "alice@example.com")

Button("Update") {
    user = User(id: user.id, name: "Alice Smith", email: user.email)
    // Entire struct replaced → triggers view update
}
```

**Use Class (Observable/Mutable Data):**

```swift
// ✅ Class for complex mutable state
class ProfileViewModel: ObservableObject {
    @Published var name = ""
    @Published var email = ""
    @Published var isLoading = false
    
    func updateProfile() {
        isLoading = true
        // API call...
        isLoading = false
    }
}

struct ProfileView: View {
    @StateObject private var viewModel = ProfileViewModel()
    
    var body: some View {
        Form {
            TextField("Name", text: $viewModel.name)
            TextField("Email", text: $viewModel.email)
            
            if viewModel.isLoading {
                ProgressView()
            }
            
            Button("Save") {
                viewModel.updateProfile()
                // @Published properties trigger view update
            }
        }
    }
}
```

**When Updates Happen:**

```swift
// STRUCTS: Replace entire value
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        Button("Count: \(count)") {
            count += 1  // Replaces Int → triggers update
        }
    }
}

// CLASSES: Modify properties
class Counter: ObservableObject {
    @Published var count = 0
    
    func increment() {
        count += 1  // @Published → triggers update
    }
}

struct CounterView2: View {
    @StateObject private var counter = Counter()
    
    var body: some View {
        Button("Count: \(counter.count)") {
            counter.increment()  // Modifies class → triggers update
        }
    }
}
```

**Performance Implications:**

**Structs (Lightweight):**
```swift
struct ItemRow: View {
    let item: Item  // Copied by value (fast for small types)
    
    var body: some View {
        HStack {
            Text(item.name)
            Text(item.price)
        }
    }
}

// SwiftUI creates/destroys thousands of these
List(items) { item in
    ItemRow(item: item)  // Fast: small struct copy
}
```

**Classes (Heavier):**
```swift
class ItemViewModel: ObservableObject {
    @Published var item: Item
    @Published var isFavorite = false
    
    init(item: Item) {
        self.item = item
    }
}

struct ItemRow: View {
    @StateObject var viewModel: ItemViewModel  // Persists across updates
    
    var body: some View {
        HStack {
            Text(viewModel.item.name)
            Button(viewModel.isFavorite ? "❤️" : "🤍") {
                viewModel.isFavorite.toggle()
            }
        }
    }
}
```

**Best Practices (2025):**

**1. View = Struct (Always)**
```swift
struct MyView: View {  // ✅
    var body: some View { ... }
}
```

**2. Simple Data = Struct**
```swift
struct Product: Identifiable {
    let id: UUID
    let name: String
    let price: Double
}
```

**3. Complex State = ObservableObject Class**
```swift
class CartViewModel: ObservableObject {
    @Published var items: [Product] = []
    @Published var total: Double = 0
    
    func addItem(_ product: Product) {
        items.append(product)
        total += product.price
    }
}
```

**4. Pass Data Down, Actions Up**
```swift
struct ParentView: View {
    @StateObject private var viewModel = CartViewModel()
    
    var body: some View {
        ProductList(
            products: viewModel.items,  // Data down (struct)
            onAdd: { product in          // Action up (closure)
                viewModel.addItem(product)
            }
        )
    }
}

struct ProductList: View {
    let products: [Product]
    let onAdd: (Product) -> Void
    
    var body: some View {
        List(products) { product in
            Button(product.name) {
                onAdd(product)
            }
        }
    }
}
```

**Decision Tree:**

```
Need mutable shared state?
├─ Yes → Class (ObservableObject)
└─ No  → Is it a view?
    ├─ Yes → Struct (always)
    └─ No  → Is data simple?
        ├─ Yes → Struct
        └─ No  → Consider class if complex relationships
```

**Key Takeaway:**

- **Views:** Always structs (SwiftUI requirement)
- **Models:** Structs for immutable data, classes for observable state
- **ViewModels:** Classes with `@Published` for reactive updates
- SwiftUI optimizes struct views with diffing and body caching

---

### Question 5: How do you implement protocol-oriented programming with structs?

**Answer:**

**Protocol-Oriented Programming (POP)** uses protocols to define behavior without inheritance. Structs conform to protocols to gain functionality.

**Basic Protocol Conformance:**

```swift
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

// Polymorphism without inheritance
let shapes: [Drawable] = [
    Circle(radius: 5),
    Rectangle(width: 10, height: 20)
]

shapes.forEach { $0.draw() }
/* Output:
Drawing circle with radius 5.0
Drawing rectangle 10.0x20.0
*/
```

**Protocol Extensions (Default Implementation):**

```swift
protocol Identifiable {
    var id: String { get }
}

protocol Entity: Identifiable {
    var createdAt: Date { get }
}

// Default implementation
extension Entity {
    func age() -> TimeInterval {
        return Date().timeIntervalSince(createdAt)
    }
    
    func description() -> String {
        return "Entity[\(id)] created \(age())s ago"
    }
}

struct User: Entity {
    let id: String
    let name: String
    let createdAt: Date
    
    // Gets age() and description() for free!
}

let user = User(id: "123", name: "Alice", createdAt: Date().addingTimeInterval(-3600))
print(user.age())  // ~3600.0
print(user.description())  // Entity[123] created 3600.0s ago
```

**Protocol Composition:**

```swift
protocol Named {
    var name: String { get }
}

protocol Aged {
    var age: Int { get }
}

// Combine protocols
typealias Person = Named & Aged

struct Employee: Person {
    let name: String
    let age: Int
    let department: String
}

func greet(_ person: Person) {
    print("Hello, \(person.name) (\(person.age))")
}

let employee = Employee(name: "Bob", age: 30, department: "Engineering")
greet(employee)  // Hello, Bob (30)
```

**Associated Types:**

```swift
protocol Container {
    associatedtype Item
    
    var count: Int { get }
    mutating func append(_ item: Item)
    subscript(index: Int) -> Item { get }
}

struct IntStack: Container {
    typealias Item = Int  // Can be inferred
    
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

var stack = IntStack()
stack.append(10)
stack.append(20)
print(stack[0])  // 10
```

**Protocol-Oriented Dependency Injection:**

```swift
// Protocol defines contract
protocol NetworkService {
    func fetchData() async throws -> Data
}

// Real implementation
struct URLSessionService: NetworkService {
    func fetchData() async throws -> Data {
        let url = URL(string: "https://api.example.com/data")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return data
    }
}

// Mock for testing
struct MockNetworkService: NetworkService {
    let mockData: Data
    
    func fetchData() async throws -> Data {
        return mockData
    }
}

// ViewModel depends on protocol, not concrete type
struct DataViewModel {
    let networkService: NetworkService
    
    func loadData() async throws -> String {
        let data = try await networkService.fetchData()
        return String(data: data, encoding: .utf8) ?? ""
    }
}

// Production
let prod = DataViewModel(networkService: URLSessionService())

// Testing
let mock = MockNetworkService(mockData: "Test".data(using: .utf8)!)
let test = DataViewModel(networkService: mock)
```

**Real-World iOS Example (2025):**

**Storage Protocol:**

```swift
protocol StorageService {
    func save<T: Encodable>(_ value: T, forKey key: String) throws
    func load<T: Decodable>(_ type: T.Type, forKey key: String) throws -> T?
    func delete(forKey key: String) throws
}

// UserDefaults implementation
struct UserDefaultsStorage: StorageService {
    func save<T: Encodable>(_ value: T, forKey key: String) throws {
        let data = try JSONEncoder().encode(value)
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func load<T: Decodable>(_ type: T.Type, forKey key: String) throws -> T? {
        guard let data = UserDefaults.standard.data(forKey: key) else {
            return nil
        }
        return try JSONDecoder().decode(type, from: data)
    }
    
    func delete(forKey key: String) throws {
        UserDefaults.standard.removeObject(forKey: key)
    }
}

// Keychain implementation
struct KeychainStorage: StorageService {
    func save<T: Encodable>(_ value: T, forKey key: String) throws {
        let data = try JSONEncoder().encode(value)
        // Keychain save logic
    }
    
    func load<T: Decodable>(_ type: T.Type, forKey key: String) throws -> T? {
        // Keychain load logic
        return nil
    }
    
    func delete(forKey key: String) throws {
        // Keychain delete logic
    }
}

// Usage
class AppSettings {
    private let storage: StorageService
    
    init(storage: StorageService) {
        self.storage = storage
    }
    
    func saveUser(_ user: User) throws {
        try storage.save(user, forKey: "currentUser")
    }
    
    func loadUser() throws -> User? {
        return try storage.load(User.self, forKey: "currentUser")
    }
}

// Use UserDefaults or Keychain interchangeably
let settings1 = AppSettings(storage: UserDefaultsStorage())
let settings2 = AppSettings(storage: KeychainStorage())
```

**Validation Protocol:**

```swift
protocol Validator {
    associatedtype Value
    func validate(_ value: Value) -> ValidationResult
}

enum ValidationResult {
    case valid
    case invalid(String)
}

struct EmailValidator: Validator {
    func validate(_ value: String) -> ValidationResult {
        guard value.contains("@"), value.contains(".") else {
            return .invalid("Invalid email format")
        }
        return .valid
    }
}

struct PasswordValidator: Validator {
    func validate(_ value: String) -> ValidationResult {
        guard value.count >= 8 else {
            return .invalid("Password must be at least 8 characters")
        }
        return .valid
    }
}

struct Form {
    func validate<V: Validator>(_ value: V.Value, using validator: V) -> Bool {
        switch validator.validate(value) {
        case .valid:
            return true
        case .invalid(let message):
            print(message)
            return false
        }
    }
}

let form = Form()
let isValid = form.validate("test@example.com", using: EmailValidator())
```

**Benefits Over Classes:**

1. **Composition over inheritance**
2. **No reference semantics issues**
3. **Can conform to multiple protocols**
4. **Value type safety**
5. **Better testability (easy mocking)**

**Key Pattern:**

```swift
// Define protocol
protocol Feature {
    func execute()
}

// Extend with default behavior
extension Feature {
    func log() {
        print("Executing \(Self.self)")
    }
}

// Conforming types get behavior for free
struct FeatureA: Feature {
    func execute() {
        log()
        print("Feature A")
    }
}

struct FeatureB: Feature {
    func execute() {
        log()
        print("Feature B")
    }
}
```

This is the **recommended Swift style** over class inheritance hierarchies.

---

## 📝 Practice Exercises

1. Create a `Vector2D` struct with add/subtract/multiply operations
2. Implement a `Stack` struct with push/pop/peek methods
3. Build a `ValidationEngine` using protocol-oriented design
4. Create a `Cache` struct with expiration logic
5. Implement copy-on-write for a custom collection type

---

## 🔗 Navigation
- [← Previous: Step 9 - Enumerations](../Step-9-Enumerations/README.md)
- [→ Next: Step 11 - Classes](../Step-11-Classes/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 10!** You now understand:
- ✅ Struct syntax and properties
- ✅ Stored vs computed properties
- ✅ Methods (instance and static)
- ✅ Mutating keyword
- ✅ Value semantics and copying
- ✅ Custom initializers
- ✅ Protocol conformance
- ✅ When to use structs vs classes
- ✅ Copy-on-write optimization

**Next:** Learn about **Classes** in Step 11!
