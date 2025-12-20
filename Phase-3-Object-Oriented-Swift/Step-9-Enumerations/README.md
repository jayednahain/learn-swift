# Step 9: Enumerations (Enums)

## 📚 Description

Enumerations (enums) define a common type for a group of related values, enabling you to work with those values in a type-safe way. Swift enums are incredibly powerful compared to enums in other languages—they can have associated values, raw values, methods, computed properties, and conform to protocols.

### What You'll Learn:
- Basic enum syntax and cases
- Raw values (Int, String, etc.)
- Associated values (storing data with each case)
- Computed properties and methods in enums
- Pattern matching with switch statements
- Recursive enumerations
- CaseIterable protocol

### Key Concepts:
- **Cases**: The different possible values of an enum
- **Raw Values**: Default values (like Int or String) for each case
- **Associated Values**: Custom data stored with each case instance
- **Pattern Matching**: Extracting values using switch/if-case
- **Type Safety**: Compiler ensures you handle all cases

---

## 💡 Clear Examples

### Example 1: Basic Enums, Raw Values, and Pattern Matching

```swift
// BASIC ENUM
enum Direction {
    case north
    case south
    case east
    case west
}

// Can declare multiple cases on one line
enum CompassPoint {
    case north, south, east, west
}

// Using enums
var heading = Direction.north
print(heading)  // north

// Type inference - Swift knows the type
heading = .south
print(heading)  // south

// Switch statement (must be exhaustive)
func describe(direction: Direction) -> String {
    switch direction {
    case .north:
        return "Going north ⬆️"
    case .south:
        return "Going south ⬇️"
    case .east:
        return "Going east ➡️"
    case .west:
        return "Going west ⬅️"
    }
    // No default needed - all cases covered
}

print(describe(direction: .north))  // Going north ⬆️

// RAW VALUES (Int)
enum Planet: Int {
    case mercury = 1
    case venus = 2
    case earth = 3
    case mars = 4
    case jupiter = 5
    case saturn = 6
    case uranus = 7
    case neptune = 8
}

let earth = Planet.earth
print(earth.rawValue)  // 3

// Create from raw value (returns optional)
if let planet = Planet(rawValue: 3) {
    print("Found planet: \(planet)")  // Found planet: earth
}

let unknown = Planet(rawValue: 99)
print(unknown)  // nil

// Auto-incrementing raw values
enum Month: Int {
    case january = 1  // Start at 1
    case february     // 2
    case march        // 3
    case april        // 4
    case may          // 5
    case june         // 6
    case july         // 7
    case august       // 8
    case september    // 9
    case october      // 10
    case november     // 11
    case december     // 12
}

print(Month.december.rawValue)  // 12

// RAW VALUES (String)
enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
}

let method = HTTPMethod.post
print(method.rawValue)  // POST

// String raw values default to case name
enum Weekday: String {
    case monday     // "monday"
    case tuesday    // "tuesday"
    case wednesday  // "wednesday"
    case thursday   // "thursday"
    case friday     // "friday"
}

print(Weekday.monday.rawValue)  // monday

// Custom string raw values
enum AppTheme: String {
    case light = "Light Mode"
    case dark = "Dark Mode"
    case auto = "System Default"
}

print(AppTheme.dark.rawValue)  // Dark Mode

// PATTERN MATCHING WITH IF-CASE
let currentDay = Weekday.friday

if case .friday = currentDay {
    print("It's Friday! 🎉")
}

// Multiple cases
if currentDay == .friday || currentDay == .monday {
    print("Extreme day")
}

// CASEITERABLE PROTOCOL
enum Beverage: CaseIterable {
    case coffee, tea, juice, water
}

print(Beverage.allCases.count)  // 4

for beverage in Beverage.allCases {
    print(beverage)
}
/* Output:
coffee
tea
juice
water
*/

// iOS Example: UI State
enum LoadingState {
    case idle
    case loading
    case success
    case failure
}

var state = LoadingState.idle

func updateUI(for state: LoadingState) {
    switch state {
    case .idle:
        print("Show initial view")
    case .loading:
        print("Show spinner")
    case .success:
        print("Show data")
    case .failure:
        print("Show error message")
    }
}

state = .loading
updateUI(for: state)  // Show spinner
```

**Explanation:**
- Enums group related values together
- Raw values provide default Int/String representations
- Switch statements must handle all cases (exhaustive)
- CaseIterable gives you all cases in an array
- Type safety prevents invalid values

---

### Example 2: Associated Values (The Power of Swift Enums)

```swift
// ASSOCIATED VALUES
// Store different types of data with each case

enum Barcode {
    case upc(Int, Int, Int, Int)  // UPC barcode with 4 numbers
    case qrCode(String)             // QR code with string
}

// Create instances with associated values
var productCode = Barcode.upc(8, 85909, 51226, 3)
print(productCode)  // upc(8, 85909, 51226, 3)

productCode = .qrCode("ABCDEFGHIJKLMNOP")
print(productCode)  // qrCode("ABCDEFGHIJKLMNOP")

// Extract associated values with switch
func printBarcode(_ barcode: Barcode) {
    switch barcode {
    case .upc(let numberSystem, let manufacturer, let product, let check):
        print("UPC: \(numberSystem) \(manufacturer) \(product) \(check)")
    case .qrCode(let code):
        print("QR Code: \(code)")
    }
}

printBarcode(productCode)  // QR Code: ABCDEFGHIJKLMNOP

// Shorthand - extract all at once
func processBarcode(_ barcode: Barcode) {
    switch barcode {
    case let .upc(system, manufacturer, product, check):
        print("Processing UPC...")
    case let .qrCode(code):
        print("Processing QR: \(code)")
    }
}

// REAL-WORLD: API Response
enum NetworkResponse {
    case success(data: Data, statusCode: Int)
    case failure(error: Error, statusCode: Int?)
    case loading
}

func handle(response: NetworkResponse) {
    switch response {
    case .success(let data, let statusCode):
        print("Success (\(statusCode)): \(data.count) bytes")
    case .failure(let error, let statusCode):
        if let code = statusCode {
            print("Error \(code): \(error.localizedDescription)")
        } else {
            print("Error: \(error.localizedDescription)")
        }
    case .loading:
        print("Loading...")
    }
}

// AUTHENTICATION STATE
enum AuthenticationState {
    case loggedOut
    case loggingIn
    case loggedIn(user: User)
    case error(message: String)
}

struct User {
    let id: String
    let name: String
    let email: String
}

let currentUser = User(id: "123", name: "Alice", email: "alice@example.com")
var authState = AuthenticationState.loggedIn(user: currentUser)

switch authState {
case .loggedOut:
    print("Show login screen")
case .loggingIn:
    print("Show loading")
case .loggedIn(let user):
    print("Welcome, \(user.name)!")
case .error(let message):
    print("Error: \(message)")
}
// Welcome, Alice!

// FORM VALIDATION
enum ValidationResult {
    case valid
    case invalid(reasons: [String])
}

func validate(email: String, password: String) -> ValidationResult {
    var errors: [String] = []
    
    if !email.contains("@") {
        errors.append("Invalid email format")
    }
    
    if password.count < 8 {
        errors.append("Password must be at least 8 characters")
    }
    
    return errors.isEmpty ? .valid : .invalid(reasons: errors)
}

let result = validate(email: "test", password: "123")

switch result {
case .valid:
    print("✅ Form is valid")
case .invalid(let reasons):
    print("❌ Validation failed:")
    reasons.forEach { print("  - \($0)") }
}
/* Output:
❌ Validation failed:
  - Invalid email format
  - Password must be at least 8 characters
*/

// MEDIA TYPE
enum Media {
    case image(url: String, width: Int, height: Int)
    case video(url: String, duration: TimeInterval)
    case audio(url: String, duration: TimeInterval, artist: String?)
}

let photo = Media.image(
    url: "https://example.com/photo.jpg",
    width: 1920,
    height: 1080
)

let song = Media.audio(
    url: "https://example.com/song.mp3",
    duration: 240,
    artist: "The Beatles"
)

func displayMedia(_ media: Media) {
    switch media {
    case .image(let url, let width, let height):
        print("📷 Image: \(width)x\(height) - \(url)")
    case .video(let url, let duration):
        print("🎥 Video: \(Int(duration))s - \(url)")
    case .audio(let url, let duration, let artist):
        let artistName = artist ?? "Unknown"
        print("🎵 Audio: \(Int(duration))s by \(artistName)")
    }
}

displayMedia(photo)  // 📷 Image: 1920x1080 - https://example.com/photo.jpg
displayMedia(song)   // 🎵 Audio: 240s by The Beatles

// PAYMENT METHOD
enum PaymentMethod {
    case cash
    case creditCard(number: String, cvv: String, expiry: String)
    case applePay(deviceAccountNumber: String)
    case paypal(email: String)
}

let payment = PaymentMethod.creditCard(
    number: "4111111111111111",
    cvv: "123",
    expiry: "12/25"
)

func processPayment(_ method: PaymentMethod, amount: Double) {
    switch method {
    case .cash:
        print("Collect $\(amount) in cash")
    case .creditCard(let number, _, _):
        let last4 = String(number.suffix(4))
        print("Charge $\(amount) to card ending in \(last4)")
    case .applePay:
        print("Process $\(amount) via Apple Pay")
    case .paypal(let email):
        print("Send $\(amount) PayPal invoice to \(email)")
    }
}

processPayment(payment, amount: 99.99)
// Charge $99.99 to card ending in 1111
```

**Explanation:**
- Associated values let you store different data with each case
- Each enum instance can have unique associated values
- Extract values using switch with `let` or `var`
- Perfect for representing states with different data
- More type-safe than using multiple optional properties

---

### Example 3: Methods, Computed Properties, and Advanced Patterns

```swift
// METHODS IN ENUMS
enum TrafficLight {
    case red, yellow, green
    
    // Method
    func duration() -> TimeInterval {
        switch self {
        case .red:
            return 60.0
        case .yellow:
            return 5.0
        case .green:
            return 45.0
        }
    }
    
    // Mutating method (changes self)
    mutating func next() {
        switch self {
        case .red:
            self = .green
        case .yellow:
            self = .red
        case .green:
            self = .yellow
        }
    }
}

var light = TrafficLight.red
print("Red light lasts \(light.duration())s")  // 60.0

light.next()
print("Now: \(light)")  // green

// COMPUTED PROPERTIES
enum Size {
    case small, medium, large, extraLarge
    
    var price: Double {
        switch self {
        case .small:
            return 9.99
        case .medium:
            return 12.99
        case .large:
            return 14.99
        case .extraLarge:
            return 16.99
        }
    }
    
    var abbreviation: String {
        switch self {
        case .small: return "S"
        case .medium: return "M"
        case .large: return "L"
        case .extraLarge: return "XL"
        }
    }
}

let size = Size.large
print("\(size.abbreviation): $\(size.price)")  // L: $14.99

// ENUM WITH BOTH RAW VALUES AND METHODS
enum HTTPStatus: Int {
    case ok = 200
    case created = 201
    case badRequest = 400
    case unauthorized = 401
    case notFound = 404
    case serverError = 500
    
    var isSuccess: Bool {
        return rawValue >= 200 && rawValue < 300
    }
    
    var isClientError: Bool {
        return rawValue >= 400 && rawValue < 500
    }
    
    var description: String {
        switch self {
        case .ok:
            return "OK"
        case .created:
            return "Created"
        case .badRequest:
            return "Bad Request"
        case .unauthorized:
            return "Unauthorized"
        case .notFound:
            return "Not Found"
        case .serverError:
            return "Internal Server Error"
        }
    }
}

let status = HTTPStatus.notFound
print("\(status.rawValue): \(status.description)")  // 404: Not Found
print("Is success: \(status.isSuccess)")             // false
print("Is client error: \(status.isClientError)")    // true

// RECURSIVE ENUMS (indirect keyword)
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}

// Build expression: (5 + 4) * 2
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

// Evaluate recursively
func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case .number(let value):
        return value
    case .addition(let left, let right):
        return evaluate(left) + evaluate(right)
    case .multiplication(let left, let right):
        return evaluate(left) * evaluate(right)
    }
}

print(evaluate(product))  // 18 (because (5 + 4) * 2 = 18)

// REAL-WORLD iOS: View State Management
enum ViewState<T> {
    case idle
    case loading
    case loaded(T)
    case error(Error)
    
    var isLoading: Bool {
        if case .loading = self {
            return true
        }
        return false
    }
    
    var data: T? {
        if case .loaded(let value) = self {
            return value
        }
        return nil
    }
}

// Usage in view controller
typealias UserListState = ViewState<[User]>

var state: UserListState = .idle

func loadUsers() {
    state = .loading
    
    // Simulate API call
    let users = [
        User(id: "1", name: "Alice", email: "alice@example.com"),
        User(id: "2", name: "Bob", email: "bob@example.com")
    ]
    
    state = .loaded(users)
}

func updateUI() {
    switch state {
    case .idle:
        print("Show placeholder")
    case .loading:
        print("Show loading spinner")
    case .loaded(let users):
        print("Display \(users.count) users")
        users.forEach { print("  - \(($0.name))") }
    case .error(let error):
        print("Show error: \(error.localizedDescription)")
    }
}

loadUsers()
updateUI()
/* Output:
Display 2 users
  - Alice
  - Bob
*/

// EQUATABLE ENUM
enum AppSection: Equatable {
    case home
    case profile(userId: String)
    case settings
    case chat(conversationId: String)
}

let section1 = AppSection.profile(userId: "123")
let section2 = AppSection.profile(userId: "123")
let section3 = AppSection.profile(userId: "456")

print(section1 == section2)  // true
print(section1 == section3)  // false

// CODABLE ENUM (JSON serialization)
enum Priority: String, Codable {
    case low, medium, high, urgent
}

struct Task: Codable {
    let title: String
    let priority: Priority
}

let task = Task(title: "Fix bug", priority: .high)

// Encode to JSON
let encoder = JSONEncoder()
if let jsonData = try? encoder.encode(task),
   let jsonString = String(data: jsonData, encoding: .utf8) {
    print(jsonString)
    // {"title":"Fix bug","priority":"high"}
}

// Decode from JSON
let jsonString = """
{"title":"Review code","priority":"medium"}
"""

if let data = jsonString.data(using: .utf8),
   let decoded = try? JSONDecoder().decode(Task.self, from: data) {
    print("\(decoded.title): \(decoded.priority)")
    // Review code: medium
}

// NAMESPACE USING ENUMS
enum Constants {
    enum UI {
        static let cornerRadius: CGFloat = 8.0
        static let padding: CGFloat = 16.0
        static let animationDuration: TimeInterval = 0.3
    }
    
    enum API {
        static let baseURL = "https://api.example.com"
        static let timeout: TimeInterval = 30.0
    }
    
    enum Colors {
        static let primary = "#007AFF"
        static let secondary = "#5856D6"
    }
}

// Usage
print(Constants.UI.cornerRadius)  // 8.0
print(Constants.API.baseURL)      // https://api.example.com

// OPTION SET (similar to enum, for bit flags)
struct Toppings: OptionSet {
    let rawValue: Int
    
    static let cheese = Toppings(rawValue: 1 << 0)  // 1
    static let pepperoni = Toppings(rawValue: 1 << 1)  // 2
    static let mushrooms = Toppings(rawValue: 1 << 2)  // 4
    static let olives = Toppings(rawValue: 1 << 3)  // 8
    
    static let vegetarian: Toppings = [.cheese, .mushrooms, .olives]
}

var myPizza: Toppings = [.cheese, .pepperoni]
myPizza.insert(.mushrooms)

if myPizza.contains(.pepperoni) {
    print("Has pepperoni")  // Has pepperoni
}

print("Is vegetarian: \(myPizza.isSubset(of: .vegetarian))")  // false
```

**Explanation:**
- Enums can have methods and computed properties
- `mutating` methods can change the enum's value
- `indirect` allows recursive enum definitions
- Enums work with protocols like Equatable, Codable
- Generic enums (like `ViewState<T>`) enable reusable patterns
- OptionSet provides bit flag functionality

---

## 🎯 2025 Interview Questions

### Question 1: What are associated values in Swift enums, and how do they differ from raw values?

**Answer:**

**Raw Values:**
- **Same type** for all cases (Int, String, etc.)
- **Same value** for all instances of a case
- Set at **compile time**
- Can initialize enum from raw value

```swift
enum Planet: Int {
    case mercury = 1
    case venus = 2
    case earth = 3
}

let planet1 = Planet.earth
let planet2 = Planet.earth

print(planet1.rawValue)  // 3
print(planet2.rawValue)  // 3 (always the same)

// Initialize from raw value
if let p = Planet(rawValue: 2) {
    print(p)  // venus
}
```

**Associated Values:**
- **Different types** for different cases
- **Different values** for each instance
- Set at **runtime**
- Cannot initialize from value (use switch to extract)

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

let code1 = Barcode.upc(1, 2, 3, 4)
let code2 = Barcode.upc(5, 6, 7, 8)

// Different instances have different values
switch code1 {
case .upc(let a, let b, let c, let d):
    print("\(a), \(b), \(c), \(d)")  // 1, 2, 3, 4
case .qrCode: break
}

switch code2 {
case .upc(let a, let b, let c, let d):
    print("\(a), \(b), \(c), \(d)")  // 5, 6, 7, 8
case .qrCode: break
}
```

**Key Differences:**

| Feature | Raw Values | Associated Values |
|---------|------------|-------------------|
| Type | Same for all cases | Different per case |
| Value | Fixed per case | Unique per instance |
| When set | Compile time | Runtime |
| Initialization | `enum(rawValue:)` | Constructor syntax |
| Use case | Constants, IDs | Dynamic data |

**Real-World iOS Example (2025):**

```swift
// ❌ Can't mix raw values and associated values
// enum Result: Int {
//     case success(Data) = 0  // ❌ Error!
//     case failure(Error) = 1
// }

// ✅ Use associated values for dynamic data
enum Result<T, E: Error> {
    case success(T)
    case failure(E)
}

func fetchUser() -> Result<User, NetworkError> {
    // Returns different data each time
    return .success(User(id: "123", name: "Alice", email: "alice@example.com"))
}

// ✅ Use raw values for constants
enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case delete = "DELETE"
}

let method = HTTPMethod.post
print(method.rawValue)  // POST (always the same)
```

**When to Use Each:**

**Raw Values:**
- API request types
- Status codes
- Configuration options
- Converting to/from strings or numbers

**Associated Values:**
- API responses (success/failure with data)
- UI state (loaded with data, error with message)
- Events with context
- Variant data structures

**Both in One App:**
```swift
enum APIEndpoint: String {
    case users = "/api/users"
    case posts = "/api/posts"
}

enum APIResponse {
    case success(data: Data, statusCode: Int)
    case failure(error: Error)
}

func fetch(endpoint: APIEndpoint) -> APIResponse {
    let url = "https://example.com" + endpoint.rawValue
    // ... make request
    return .success(data: Data(), statusCode: 200)
}
```

---

### Question 2: Explain the `indirect` keyword in Swift enums. Why is it needed?

**Answer:**

**The Problem: Infinite Size**

Without `indirect`, recursive enums would have **infinite size**:

```swift
// ❌ This won't compile
// enum BinaryTree {
//     case empty
//     case node(value: Int, left: BinaryTree, right: BinaryTree)
// }
// Error: Recursive enum 'BinaryTree' is not marked 'indirect'
```

**Why?** Swift needs to know the exact size of types at compile time:
- `BinaryTree` contains `BinaryTree`
- Which contains `BinaryTree`
- Which contains... (infinite recursion!)

**The Solution: `indirect`**

```swift
indirect enum BinaryTree {
    case empty
    case node(value: Int, left: BinaryTree, right: BinaryTree)
}
```

`indirect` tells Swift to store the enum **by reference** (like a class) instead of by value, breaking the infinite size problem.

**How It Works:**

```swift
// Without indirect (simplified):
// struct BinaryTree {
//     var value: Int
//     var left: BinaryTree   // ❌ Infinite size
//     var right: BinaryTree  // ❌ Infinite size
// }

// With indirect:
// Uses heap allocation and pointers internally
indirect enum BinaryTree {
    case empty
    case node(value: Int, left: BinaryTree, right: BinaryTree)
}
// Now Swift stores references, not full copies
```

**Building a Binary Tree:**

```swift
//       5
//      / \
//     3   7
//    /   / \
//   1   6   9

let tree = BinaryTree.node(
    value: 5,
    left: .node(
        value: 3,
        left: .node(value: 1, left: .empty, right: .empty),
        right: .empty
    ),
    right: .node(
        value: 7,
        left: .node(value: 6, left: .empty, right: .empty),
        right: .node(value: 9, left: .empty, right: .empty)
    )
)

// Traverse the tree
func inorderTraversal(_ tree: BinaryTree) {
    switch tree {
    case .empty:
        return
    case .node(let value, let left, let right):
        inorderTraversal(left)
        print(value, terminator: " ")
        inorderTraversal(right)
    }
}

inorderTraversal(tree)  // 1 3 5 6 7 9
```

**Per-Case `indirect`:**

```swift
// Only specific cases need indirect
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}

// Equivalent to:
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

**Real-World Use Cases (2025):**

**1. Expression Evaluator:**
```swift
indirect enum Expression {
    case value(Double)
    case variable(String)
    case binary(op: String, Expression, Expression)
    case unary(op: String, Expression)
}

// Represent: -(x + 3.0)
let expr = Expression.unary(
    op: "-",
    .binary(op: "+", .variable("x"), .value(3.0))
)

func evaluate(_ expr: Expression, variables: [String: Double]) -> Double {
    switch expr {
    case .value(let num):
        return num
    case .variable(let name):
        return variables[name] ?? 0
    case .binary(let op, let left, let right):
        let l = evaluate(left, variables: variables)
        let r = evaluate(right, variables: variables)
        switch op {
        case "+": return l + r
        case "-": return l - r
        case "*": return l * r
        case "/": return l / r
        default: return 0
        }
    case .unary(let op, let operand):
        let val = evaluate(operand, variables: variables)
        return op == "-" ? -val : val
    }
}

let result = evaluate(expr, variables: ["x": 5.0])
print(result)  // -8.0 (because -(5 + 3) = -8)
```

**2. File System Tree:**
```swift
indirect enum FileSystemItem {
    case file(name: String, size: Int)
    case directory(name: String, contents: [FileSystemItem])
}

let filesystem = FileSystemItem.directory(
    name: "root",
    contents: [
        .directory(name: "Documents", contents: [
            .file(name: "report.pdf", size: 1024),
            .file(name: "notes.txt", size: 256)
        ]),
        .file(name: "config.json", size: 128)
    ]
)

func totalSize(_ item: FileSystemItem) -> Int {
    switch item {
    case .file(_, let size):
        return size
    case .directory(_, let contents):
        return contents.reduce(0) { $0 + totalSize($1) }
    }
}

print(totalSize(filesystem))  // 1408 bytes
```

**3. Linked List:**
```swift
indirect enum LinkedList<T> {
    case empty
    case node(value: T, next: LinkedList<T>)
}

let list: LinkedList<Int> = .node(value: 1,
    next: .node(value: 2,
        next: .node(value: 3,
            next: .empty
        )
    )
)

func printList<T>(_ list: LinkedList<T>) {
    switch list {
    case .empty:
        print("End")
    case .node(let value, let next):
        print(value, terminator: " -> ")
        printList(next)
    }
}

printList(list)  // 1 -> 2 -> 3 -> End
```

**Performance Consideration:**

`indirect` uses **heap allocation** (slower) instead of **stack allocation** (faster):

```swift
// Regular enum: Stack allocated (fast)
enum Simple {
    case a, b, c
}

// Indirect enum: Heap allocated (slower, but enables recursion)
indirect enum Recursive {
    case empty
    case node(Recursive)
}
```

**Key Takeaways:**
- Use `indirect` for **recursive** enum definitions
- Enables tree structures, expressions, linked lists
- Small performance cost (heap vs stack)
- Swift compiler enforces when needed

---

### Question 3: How can you use enums for state management in iOS apps? Provide a SwiftUI example.

**Answer:**

Enums are **perfect for state management** because they:
- Represent **all possible states** explicitly
- Ensure **type safety** (can't have invalid states)
- Work great with **switch statements** (exhaustive)
- Can carry **associated data** for each state

**Basic State Management Pattern:**

```swift
enum LoadingState<T> {
    case idle
    case loading
    case loaded(T)
    case error(String)
}
```

**SwiftUI Example: User Profile View (2025)**

```swift
import SwiftUI

// 1. Define state enum
enum ProfileViewState {
    case loading
    case loaded(user: User)
    case error(message: String)
    case empty
}

struct User: Identifiable {
    let id: String
    let name: String
    let email: String
    let avatarURL: String?
}

// 2. ViewModel with enum state
class ProfileViewModel: ObservableObject {
    @Published var state: ProfileViewState = .loading
    
    func loadProfile(userId: String) {
        state = .loading
        
        // Simulate API call
        DispatchQueue.main.asyncAfter(deadline: .now() + 1.5) {
            if userId == "valid" {
                let user = User(
                    id: userId,
                    name: "Alice Johnson",
                    email: "alice@example.com",
                    avatarURL: "https://example.com/avatar.jpg"
                )
                self.state = .loaded(user: user)
            } else if userId == "empty" {
                self.state = .empty
            } else {
                self.state = .error(message: "Failed to load user profile")
            }
        }
    }
    
    func retry() {
        loadProfile(userId: "valid")
    }
}

// 3. SwiftUI View with state-based UI
struct ProfileView: View {
    @StateObject private var viewModel = ProfileViewModel()
    let userId: String
    
    var body: some View {
        ZStack {
            // Switch on enum state
            switch viewModel.state {
            case .loading:
                loadingView
                
            case .loaded(let user):
                loadedView(user: user)
                
            case .error(let message):
                errorView(message: message)
                
            case .empty:
                emptyView
            }
        }
        .onAppear {
            viewModel.loadProfile(userId: userId)
        }
    }
    
    // MARK: - State-Specific Views
    
    private var loadingView: some View {
        VStack {
            ProgressView()
            Text("Loading profile...")
                .foregroundColor(.secondary)
        }
    }
    
    private func loadedView(user: User) -> some View {
        VStack(spacing: 20) {
            // Avatar
            if let avatarURL = user.avatarURL {
                AsyncImage(url: URL(string: avatarURL)) { image in
                    image.resizable()
                } placeholder: {
                    Color.gray
                }
                .frame(width: 100, height: 100)
                .clipShape(Circle())
            }
            
            // User info
            Text(user.name)
                .font(.title)
            Text(user.email)
                .foregroundColor(.secondary)
            
            Button("Edit Profile") {
                // Edit action
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
    }
    
    private func errorView(message: String) -> some View {
        VStack(spacing: 16) {
            Image(systemName: "exclamationmark.triangle")
                .font(.system(size: 60))
                .foregroundColor(.red)
            
            Text("Error")
                .font(.title)
            
            Text(message)
                .foregroundColor(.secondary)
                .multilineTextAlignment(.center)
            
            Button("Retry") {
                viewModel.retry()
            }
            .buttonStyle(.bordered)
        }
        .padding()
    }
    
    private var emptyView: some View {
        VStack(spacing: 16) {
            Image(systemName: "person.slash")
                .font(.system(size: 60))
                .foregroundColor(.gray)
            
            Text("No Profile Found")
                .font(.title)
            
            Text("This user doesn't have a profile yet")
                .foregroundColor(.secondary)
        }
    }
}
```

**Advanced: Generic Loadable State**

```swift
// Reusable state enum
enum Loadable<T> {
    case idle
    case loading
    case loaded(T)
    case failed(Error)
    
    var data: T? {
        if case .loaded(let value) = self {
            return value
        }
        return nil
    }
    
    var isLoading: Bool {
        if case .loading = self { return true }
        return false
    }
    
    var error: Error? {
        if case .failed(let error) = self {
            return error
        }
        return nil
    }
}

// Use with any data type
class PostsViewModel: ObservableObject {
    @Published var state: Loadable<[Post]> = .idle
    
    func loadPosts() {
        state = .loading
        
        // API call
        fetchPosts { result in
            switch result {
            case .success(let posts):
                self.state = .loaded(posts)
            case .failure(let error):
                self.state = .failed(error)
            }
        }
    }
}

struct PostsView: View {
    @StateObject private var viewModel = PostsViewModel()
    
    var body: some View {
        Group {
            switch viewModel.state {
            case .idle:
                Text("Tap to load posts")
            case .loading:
                ProgressView()
            case .loaded(let posts):
                List(posts) { post in
                    Text(post.title)
                }
            case .failed(let error):
                VStack {
                    Text("Error: \(error.localizedDescription)")
                    Button("Retry") {
                        viewModel.loadPosts()
                    }
                }
            }
        }
        .onAppear {
            if case .idle = viewModel.state {
                viewModel.loadPosts()
            }
        }
    }
}
```

**Multi-Step Form State:**

```swift
enum FormState {
    case editing
    case validating
    case valid
    case invalid(errors: [String])
    case submitting
    case submitted
}

class FormViewModel: ObservableObject {
    @Published var state: FormState = .editing
    @Published var email = ""
    @Published var password = ""
    
    func validate() {
        state = .validating
        
        var errors: [String] = []
        if !email.contains("@") {
            errors.append("Invalid email")
        }
        if password.count < 8 {
            errors.append("Password too short")
        }
        
        state = errors.isEmpty ? .valid : .invalid(errors: errors)
    }
    
    func submit() {
        guard case .valid = state else { return }
        
        state = .submitting
        
        // API call
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            self.state = .submitted
        }
    }
}

struct FormView: View {
    @StateObject private var viewModel = FormViewModel()
    
    var body: some View {
        Form {
            TextField("Email", text: $viewModel.email)
            SecureField("Password", text: $viewModel.password)
            
            switch viewModel.state {
            case .editing:
                Button("Validate") {
                    viewModel.validate()
                }
                
            case .validating:
                ProgressView()
                
            case .valid:
                Button("Submit") {
                    viewModel.submit()
                }
                .tint(.green)
                
            case .invalid(let errors):
                ForEach(errors, id: \.self) { error in
                    Text(error)
                        .foregroundColor(.red)
                }
                Button("Try Again") {
                    viewModel.state = .editing
                }
                
            case .submitting:
                ProgressView("Submitting...")
                
            case .submitted:
                HStack {
                    Image(systemName: "checkmark.circle.fill")
                        .foregroundColor(.green)
                    Text("Success!")
                }
            }
        }
    }
}
```

**Benefits Over Booleans:**

```swift
// ❌ Error-prone: Multiple booleans
class BadViewModel {
    var isLoading = false
    var hasError = false
    var hasData = false
    // Can have invalid states: all true, or hasData && hasError
}

// ✅ Type-safe: One enum
class GoodViewModel {
    var state: LoadingState<Data> = .idle
    // Can only be in one state at a time
}
```

**Key Advantages:**
- **Impossible states** are prevented (can't be loading AND errored)
- **Exhaustive** switch ensures all cases handled
- **Self-documenting** code
- **Easy testing** (just check enum cases)
- **Associated values** carry relevant data

---

### Question 4: Can enums conform to protocols? How would you make an enum Codable, Equatable, and Hashable?

**Answer:**

**Yes!** Swift enums can conform to protocols just like structs and classes.

**Automatic Protocol Conformance:**

Many protocols are automatically synthesized by the compiler:

**1. Equatable:**
```swift
enum Status: Equatable {
    case active
    case inactive
    case pending(Date)
}

let status1 = Status.pending(Date())
let status2 = Status.active

print(status1 == status2)  // false

// Compiler generates:
// static func ==(lhs: Status, rhs: Status) -> Bool { ... }
```

**2. Hashable:**
```swift
enum Priority: Hashable {
    case low, medium, high
}

let dict: [Priority: String] = [
    .low: "Not urgent",
    .high: "Critical"
]

// Can use as dictionary key or in Set
let priorities: Set<Priority> = [.low, .medium]
```

**3. Codable (JSON Serialization):**

**Simple Enum:**
```swift
enum UserRole: String, Codable {
    case admin, member, guest
}

struct User: Codable {
    let name: String
    let role: UserRole
}

// Encode
let user = User(name: "Alice", role: .admin)
let encoder = JSONEncoder()
let jsonData = try! encoder.encode(user)
let jsonString = String(data: jsonData, encoding: .utf8)!
print(jsonString)
// {"name":"Alice","role":"admin"}

// Decode
let decoder = JSONDecoder()
let decoded = try! decoder.decode(User.self, from: jsonData)
print(decoded.role)  // admin
```

**Enum with Associated Values:**
```swift
enum Response: Codable {
    case success(message: String)
    case error(code: Int, message: String)
    
    // Codable keys
    enum CodingKeys: String, CodingKey {
        case success, error
    }
    
    enum SuccessKeys: String, CodingKey {
        case message
    }
    
    enum ErrorKeys: String, CodingKey {
        case code, message
    }
    
    // Manual encoding
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        
        switch self {
        case .success(let message):
            var nested = container.nestedContainer(keyedBy: SuccessKeys.self, forKey: .success)
            try nested.encode(message, forKey: .message)
            
        case .error(let code, let message):
            var nested = container.nestedContainer(keyedBy: ErrorKeys.self, forKey: .error)
            try nested.encode(code, forKey: .code)
            try nested.encode(message, forKey: .message)
        }
    }
    
    // Manual decoding
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        if let successContainer = try? container.nestedContainer(keyedBy: SuccessKeys.self, forKey: .success) {
            let message = try successContainer.decode(String.self, forKey: .message)
            self = .success(message: message)
        } else {
            let errorContainer = try container.nestedContainer(keyedBy: ErrorKeys.self, forKey: .error)
            let code = try errorContainer.decode(Int.self, forKey: .code)
            let message = try errorContainer.decode(String.self, forKey: .message)
            self = .error(code: code, message: message)
        }
    }
}

// Usage
let response = Response.error(code: 404, message: "Not found")
let data = try! JSONEncoder().encode(response)
print(String(data: data, encoding: .utf8)!)
// {"error":{"code":404,"message":"Not found"}}
```

**4. CaseIterable:**
```swift
enum Season: CaseIterable {
    case spring, summer, fall, winter
}

print(Season.allCases.count)  // 4

for season in Season.allCases {
    print(season)
}
// spring summer fall winter

// Works with raw values
enum Weekday: Int, CaseIterable {
    case monday = 1, tuesday, wednesday, thursday, friday
}

Weekday.allCases.forEach { day in
    print("\(day): \(day.rawValue)")
}
```

**Custom Protocol Conformance:**

```swift
protocol Describable {
    var description: String { get }
}

enum Animal: Describable {
    case dog(name: String)
    case cat(name: String)
    case bird(species: String)
    
    var description: String {
        switch self {
        case .dog(let name):
            return "Dog named \(name)"
        case .cat(let name):
            return "Cat named \(name)"
        case .bird(let species):
            return "\(species) bird"
        }
    }
}

let pet = Animal.dog(name: "Buddy")
print(pet.description)  // Dog named Buddy
```

**CustomStringConvertible:**
```swift
enum LogLevel: CustomStringConvertible {
    case debug, info, warning, error
    
    var description: String {
        switch self {
        case .debug: return "🐛 DEBUG"
        case .info: return "ℹ️ INFO"
        case .warning: return "⚠️ WARNING"
        case .error: return "❌ ERROR"
        }
    }
}

print(LogLevel.error)  // ❌ ERROR
```

**Comparable:**
```swift
enum Size: Comparable {
    case small, medium, large, extraLarge
    
    var rawValue: Int {
        switch self {
        case .small: return 1
        case .medium: return 2
        case .large: return 3
        case .extraLarge: return 4
        }
    }
    
    static func <(lhs: Size, rhs: Size) -> Bool {
        return lhs.rawValue < rhs.rawValue
    }
}

let sizes: [Size] = [.large, .small, .extraLarge, .medium]
let sorted = sizes.sorted()
print(sorted)  // [small, medium, large, extraLarge]

print(Size.small < Size.large)  // true
```

**Real-World iOS Example (2025):**

```swift
// API Error handling
enum APIError: Error, LocalizedError, Codable {
    case networkError
    case serverError(statusCode: Int)
    case decodingError
    case unauthorized
    case notFound
    
    var errorDescription: String? {
        switch self {
        case .networkError:
            return "Network connection failed"
        case .serverError(let code):
            return "Server error: \(code)"
        case .decodingError:
            return "Failed to decode response"
        case .unauthorized:
            return "Authentication required"
        case .notFound:
            return "Resource not found"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .networkError:
            return "Check your internet connection"
        case .unauthorized:
            return "Please log in again"
        default:
            return "Please try again later"
        }
    }
}

// SwiftUI Alert
struct ContentView: View {
    @State private var error: APIError?
    @State private var showError = false
    
    var body: some View {
        Button("Test Error") {
            error = .networkError
            showError = true
        }
        .alert("Error", isPresented: $showError, presenting: error) { _ in
            Button("OK") { }
        } message: { error in
            Text(error.errorDescription ?? "Unknown error")
        }
    }
}
```

**Multiple Protocol Conformance:**
```swift
enum TaskStatus: String, Codable, CaseIterable, Equatable, Hashable, CustomStringConvertible {
    case todo, inProgress, done, cancelled
    
    var description: String {
        switch self {
        case .todo: return "📝 To Do"
        case .inProgress: return "🔄 In Progress"
        case .done: return "✅ Done"
        case .cancelled: return "❌ Cancelled"
        }
    }
}

// All protocols work together
let statuses = TaskStatus.allCases  // CaseIterable
let set: Set<TaskStatus> = [.todo, .done]  // Hashable
let json = try! JSONEncoder().encode(TaskStatus.done)  // Codable
print(TaskStatus.inProgress)  // CustomStringConvertible
```

**Best Practices:**
- Use `Codable` for network/storage
- Use `Equatable`/`Hashable` for collections
- Use `CaseIterable` to enumerate all cases
- Implement `CustomStringConvertible` for debugging
- Compiler synthesizes most protocols automatically

---

### Question 5: How would you use enums to implement a type-safe API client in Swift?

**Answer:**

Enums provide **type safety, exhaustive handling, and clear structure** for API clients.

**Complete Type-Safe API Client Example:**

```swift
import Foundation

// 1. API Endpoints as Enum
enum APIEndpoint {
    case users
    case user(id: String)
    case posts
    case post(id: Int)
    case createPost(title: String, body: String)
    case deletePost(id: Int)
    
    var path: String {
        switch self {
        case .users:
            return "/users"
        case .user(let id):
            return "/users/\(id)"
        case .posts:
            return "/posts"
        case .post(let id):
            return "/posts/\(id)"
        case .createPost:
            return "/posts"
        case .deletePost(let id):
            return "/posts/\(id)"
        }
    }
    
    var method: HTTPMethod {
        switch self {
        case .users, .user, .posts, .post:
            return .get
        case .createPost:
            return .post
        case .deletePost:
            return .delete
        }
    }
    
    var body: Data? {
        switch self {
        case .createPost(let title, let body):
            let json: [String: Any] = ["title": title, "body": body]
            return try? JSONSerialization.data(withJSONObject: json)
        default:
            return nil
        }
    }
}

// 2. HTTP Methods
enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
    case patch = "PATCH"
}

// 3. API Result
enum APIResult<T> {
    case success(T)
    case failure(APIError)
}

// 4. API Errors
enum APIError: Error, LocalizedError {
    case networkError(Error)
    case httpError(statusCode: Int)
    case decodingError(Error)
    case invalidURL
    case noData
    
    var errorDescription: String? {
        switch self {
        case .networkError(let error):
            return "Network error: \(error.localizedDescription)"
        case .httpError(let code):
            return "HTTP error: \(code)"
        case .decodingError:
            return "Failed to decode response"
        case .invalidURL:
            return "Invalid URL"
        case .noData:
            return "No data received"
        }
    }
}

// 5. API Client
class APIClient {
    static let shared = APIClient()
    private let baseURL = "https://jsonplaceholder.typicode.com"
    
    private init() {}
    
    func request<T: Decodable>(
        _ endpoint: APIEndpoint,
        completion: @escaping (APIResult<T>) -> Void
    ) {
        // Build URL
        guard let url = URL(string: baseURL + endpoint.path) else {
            completion(.failure(.invalidURL))
            return
        }
        
        // Create request
        var request = URLRequest(url: url)
        request.httpMethod = endpoint.method.rawValue
        request.httpBody = endpoint.body
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        
        // Make request
        URLSession.shared.dataTask(with: request) { data, response, error in
            // Handle error
            if let error = error {
                completion(.failure(.networkError(error)))
                return
            }
            
            // Check HTTP status
            if let httpResponse = response as? HTTPURLResponse,
               !(200...299).contains(httpResponse.statusCode) {
                completion(.failure(.httpError(statusCode: httpResponse.statusCode)))
                return
            }
            
            // Check data
            guard let data = data else {
                completion(.failure(.noData))
                return
            }
            
            // Decode
            do {
                let decoded = try JSONDecoder().decode(T.self, from: data)
                completion(.success(decoded))
            } catch {
                completion(.failure(.decodingError(error)))
            }
        }.resume()
    }
}

// 6. Models
struct User: Codable {
    let id: Int
    let name: String
    let email: String
}

struct Post: Codable {
    let id: Int?
    let title: String
    let body: String
    let userId: Int?
}

// 7. Usage
// Fetch all users
APIClient.shared.request(.users) { (result: APIResult<[User]>) in
    switch result {
    case .success(let users):
        print("Fetched \(users.count) users")
        users.forEach { print("  - \($0.name)") }
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}

// Fetch specific user
APIClient.shared.request(.user(id: "1")) { (result: APIResult<User>) in
    switch result {
    case .success(let user):
        print("User: \(user.name) (\(user.email))")
    case .failure(let error):
        print("Error: \(error)")
    }
}

// Create post
APIClient.shared.request(.createPost(title: "New Post", body: "Content")) { (result: APIResult<Post>) in
    switch result {
    case .success(let post):
        print("Created post: \(post.title)")
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

**Modern Async/Await Version (2025):**

```swift
extension APIClient {
    func request<T: Decodable>(_ endpoint: APIEndpoint) async throws -> T {
        guard let url = URL(string: baseURL + endpoint.path) else {
            throw APIError.invalidURL
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = endpoint.method.rawValue
        request.httpBody = endpoint.body
        
        do {
            let (data, response) = try await URLSession.shared.data(for: request)
            
            guard let httpResponse = response as? HTTPURLResponse,
                  (200...299).contains(httpResponse.statusCode) else {
                let statusCode = (response as? HTTPURLResponse)?.statusCode ?? 0
                throw APIError.httpError(statusCode: statusCode)
            }
            
            return try JSONDecoder().decode(T.self, from: data)
        } catch let error as APIError {
            throw error
        } catch {
            throw APIError.networkError(error)
        }
    }
}

// Usage with async/await
Task {
    do {
        let users: [User] = try await APIClient.shared.request(.users)
        print("Fetched \(users.count) users")
    } catch {
        print("Error: \(error)")
    }
}
```

**Advanced: Request Configuration**

```swift
enum APIConfiguration {
    case production
    case staging
    case development
    
    var baseURL: String {
        switch self {
        case .production:
            return "https://api.myapp.com"
        case .staging:
            return "https://staging-api.myapp.com"
        case .development:
            return "http://localhost:3000"
        }
    }
    
    var timeout: TimeInterval {
        switch self {
        case .production:
            return 30.0
        case .staging, .development:
            return 60.0
        }
    }
    
    var shouldLogRequests: Bool {
        switch self {
        case .production:
            return false
        case .staging, .development:
            return true
        }
    }
}

class ConfigurableAPIClient {
    static let shared = ConfigurableAPIClient()
    var configuration: APIConfiguration = .production
    
    private var baseURL: String {
        configuration.baseURL
    }
}
```

**Benefits of Enum-Based API:**

1. **Type Safety**: Compiler catches endpoint typos
2. **Exhaustive**: Switch ensures all cases handled
3. **Self-Documenting**: Clear what endpoints exist
4. **Centralized**: All API logic in one place
5. **Easy Testing**: Mock specific endpoints
6. **Refactoring**: Rename endpoints safely

**Testing:**
```swift
class MockAPIClient: APIClient {
    var mockResponse: Any?
    var shouldFail = false
    
    override func request<T: Decodable>(
        _ endpoint: APIEndpoint,
        completion: @escaping (APIResult<T>) -> Void
    ) {
        if shouldFail {
            completion(.failure(.networkError(NSError())))
        } else if let response = mockResponse as? T {
            completion(.success(response))
        } else {
            completion(.failure(.noData))
        }
    }
}

// Test
let mockClient = MockAPIClient()
mockClient.mockResponse = [User(id: 1, name: "Test", email: "test@test.com")]

mockClient.request(.users) { (result: APIResult<[User]>) in
    // Assert result
}
```

This pattern is used in production iOS apps for robust, maintainable API layers.

---

## 📝 Practice Exercises

1. Create an enum representing a calculator operation with associated values and implement an `evaluate()` method
2. Build a file system enum with directories and files, including a `size()` method
3. Implement a state machine using enums for a traffic light system
4. Create a `Result<T, E>` enum (like Swift's standard library) with map/flatMap methods
5. Design an enum-based router for a multi-screen iOS app

---

## 🔗 Navigation
- [← Previous: Step 8 - Closures](../../Phase-2-Functions-and-Closures/Step-8-Closures/README.md)
- [→ Next: Step 10 - Structures](../Step-10-Structures/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 9!** You now understand:
- ✅ Basic enum syntax and cases
- ✅ Raw values (Int, String)
- ✅ Associated values for storing data
- ✅ Methods and computed properties in enums
- ✅ Pattern matching with switch
- ✅ Recursive enums with `indirect`
- ✅ Protocol conformance (Codable, Equatable, etc.)
- ✅ Real-world patterns for state management and API clients

**Next:** Learn about **Structures** in Step 10!
