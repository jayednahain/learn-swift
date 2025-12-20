# Step 14: Methods

## 📚 Description

**Methods** are functions associated with types (structs, classes, enums). Swift provides **instance methods** (work on specific instances), **type methods** (work on the type itself), **mutating methods** for value types, and **subscripts** for collection-like access. Understanding methods is crucial for building object-oriented Swift code.

### What You'll Learn:
- Instance methods and self
- Type methods (static and class)
- Mutating methods for structs
- Method parameters and argument labels
- Subscripts for custom collection access
- Method dispatch and performance

### Key Concepts:
- **Instance Method**: Operates on specific instance
- **Type Method**: Operates on type itself
- **Mutating**: Allows struct/enum modification
- **Subscript**: Collection-style access (`array[index]`)
- **Self**: Refers to type (capital S), `self` refers to instance

---

## 💡 Clear Examples

### Example 1: Instance Methods and the `self` Keyword

```swift
// ========== BASIC INSTANCE METHODS ==========

struct Counter {
    var count: Int = 0
    
    // Instance method
    func increment() {
        // ❌ Error: Cannot modify struct property
        // count += 1
    }
    
    // Mutating method (required for structs)
    mutating func incrementMutating() {
        count += 1
    }
    
    func printCount() {
        print("Count: \(count)")
    }
}

var counter = Counter()
counter.incrementMutating()
counter.printCount()  // Count: 1

// ========== SELF KEYWORD ==========

struct Point {
    var x: Double
    var y: Double
    
    // self refers to the instance
    func distance(to point: Point) -> Double {
        let dx = self.x - point.x
        let dy = self.y - point.y
        return sqrt(dx * dx + dy * dy)
    }
    
    // self disambiguates parameter from property
    init(x: Double, y: Double) {
        self.x = x  // self.x is property, x is parameter
        self.y = y
    }
}

let point1 = Point(x: 0, y: 0)
let point2 = Point(x: 3, y: 4)
print(point1.distance(to: point2))  // 5.0

// ========== CLASSES DON'T NEED MUTATING ==========

class BankAccount {
    var balance: Double
    
    init(balance: Double) {
        self.balance = balance
    }
    
    // No 'mutating' needed for classes
    func deposit(_ amount: Double) {
        balance += amount
        print("Deposited \(amount). New balance: \(balance)")
    }
    
    func withdraw(_ amount: Double) -> Bool {
        if amount <= balance {
            balance -= amount
            print("Withdrew \(amount). New balance: \(balance)")
            return true
        } else {
            print("Insufficient funds")
            return false
        }
    }
}

let account = BankAccount(balance: 1000)
account.deposit(500)   // Deposited 500. New balance: 1500
account.withdraw(200)  // Withdrew 200. New balance: 1300

// ========== MULTIPLE PARAMETERS ==========

struct Rectangle {
    var width: Double
    var height: Double
    
    func area() -> Double {
        return width * height
    }
    
    func aspectRatio() -> Double {
        return width / height
    }
    
    func scale(by factor: Double) -> Rectangle {
        return Rectangle(
            width: width * factor,
            height: height * factor
        )
    }
    
    func fits(in container: Rectangle) -> Bool {
        return width <= container.width && height <= container.height
    }
}

let rect = Rectangle(width: 10, height: 5)
print(rect.area())              // 50.0
print(rect.aspectRatio())       // 2.0

let scaled = rect.scale(by: 2)
print(scaled.width)             // 20.0

let container = Rectangle(width: 100, height: 100)
print(rect.fits(in: container))  // true

// ========== RETURNING SELF ==========

struct Builder {
    var value: String = ""
    
    mutating func add(_ text: String) -> Builder {
        value += text
        return self
    }
    
    mutating func addLine(_ text: String) -> Builder {
        value += text + "\n"
        return self
    }
}

var builder = Builder()
builder.add("Hello").addLine(" World").add("!")
print(builder.value)
// Output:
// Hello World
// !

// ========== REAL-WORLD: API CLIENT ==========

struct APIRequest {
    var url: String
    var method: String = "GET"
    var headers: [String: String] = [:]
    var body: Data?
    
    mutating func setMethod(_ method: String) -> APIRequest {
        self.method = method
        return self
    }
    
    mutating func addHeader(key: String, value: String) -> APIRequest {
        headers[key] = value
        return self
    }
    
    func execute() async throws -> Data {
        // Execute request
        print("Executing \(method) \(url)")
        return Data()
    }
}

var request = APIRequest(url: "https://api.example.com/users")
request.setMethod("POST")
       .addHeader(key: "Content-Type", value: "application/json")
       .addHeader(key: "Authorization", value: "Bearer token")

// ========== INSTANCE METHODS IN ENUMS ==========

enum TrafficLight {
    case red, yellow, green
    
    func duration() -> TimeInterval {
        switch self {
        case .red:
            return 60
        case .yellow:
            return 5
        case .green:
            return 45
        }
    }
    
    func next() -> TrafficLight {
        switch self {
        case .red:
            return .green
        case .yellow:
            return .red
        case .green:
            return .yellow
        }
    }
}

var light = TrafficLight.red
print(light.duration())  // 60
light = light.next()
print(light)  // green
```

**Explanation:**
- Instance methods work on specific instances
- Structs/enums need `mutating` to modify properties
- Classes don't need `mutating`
- `self` refers to current instance
- Methods can return `Self` for chaining

---

### Example 2: Type Methods (Static and Class)

```swift
// ========== STATIC METHODS ==========

struct Math {
    // Type method (static)
    static func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
    
    static func multiply(_ a: Int, _ b: Int) -> Int {
        return a * b
    }
    
    static func power(_ base: Double, _ exponent: Double) -> Double {
        return pow(base, exponent)
    }
}

// Call on type, not instance
print(Math.add(5, 3))          // 8
print(Math.multiply(4, 7))     // 28
print(Math.power(2, 8))        // 256.0

// ========== STATIC VS CLASS ==========

class Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    // Static: Cannot be overridden
    static func kingdom() -> String {
        return "Animalia"
    }
    
    // Class: Can be overridden
    class func sound() -> String {
        return "Some sound"
    }
}

class Dog: Animal {
    // ❌ Cannot override static
    // override static func kingdom() -> String { }
    
    // ✅ Can override class
    override class func sound() -> String {
        return "Woof!"
    }
}

print(Animal.kingdom())  // "Animalia"
print(Animal.sound())    // "Some sound"
print(Dog.sound())       // "Woof!"

// ========== FACTORY METHODS ==========

struct Color {
    let red: Double
    let green: Double
    let blue: Double
    
    // Factory type methods
    static func white() -> Color {
        return Color(red: 1, green: 1, blue: 1)
    }
    
    static func black() -> Color {
        return Color(red: 0, green: 0, blue: 0)
    }
    
    static func red() -> Color {
        return Color(red: 1, green: 0, blue: 0)
    }
    
    static func random() -> Color {
        return Color(
            red: Double.random(in: 0...1),
            green: Double.random(in: 0...1),
            blue: Double.random(in: 0...1)
        )
    }
}

let white = Color.white()
let randomColor = Color.random()

// ========== ACCESSING TYPE PROPERTIES ==========

class AppConfig {
    static var version = "1.0.0"
    static var apiURL = "https://api.example.com"
    
    static func configure() {
        print("Configuring app version \(version)")
        print("API URL: \(apiURL)")
    }
    
    static func updateVersion(_ newVersion: String) {
        version = newVersion
        print("Version updated to \(version)")
    }
}

AppConfig.configure()
AppConfig.updateVersion("1.1.0")

// ========== VALIDATION ==========

struct Email {
    let address: String
    
    // Type method for validation
    static func isValid(_ email: String) -> Bool {
        return email.contains("@") && email.contains(".")
    }
    
    init?(_ address: String) {
        guard Email.isValid(address) else {
            return nil
        }
        self.address = address
    }
}

if let email = Email("user@example.com") {
    print("Valid email: \(email.address)")
} else {
    print("Invalid email")
}

// Can also use type method directly
print(Email.isValid("test@test.com"))  // true
print(Email.isValid("invalid"))        // false

// ========== COUNTERS ==========

class IDGenerator {
    private static var currentID = 0
    
    static func nextID() -> Int {
        currentID += 1
        return currentID
    }
    
    static func reset() {
        currentID = 0
    }
}

print(IDGenerator.nextID())  // 1
print(IDGenerator.nextID())  // 2
print(IDGenerator.nextID())  // 3
IDGenerator.reset()
print(IDGenerator.nextID())  // 1

// ========== REAL-WORLD: DATE UTILITIES ==========

import Foundation

extension Date {
    static func from(year: Int, month: Int, day: Int) -> Date? {
        var components = DateComponents()
        components.year = year
        components.month = month
        components.day = day
        return Calendar.current.date(from: components)
    }
    
    static var yesterday: Date {
        return Calendar.current.date(byAdding: .day, value: -1, to: Date())!
    }
    
    static var tomorrow: Date {
        return Calendar.current.date(byAdding: .day, value: 1, to: Date())!
    }
}

if let date = Date.from(year: 2025, month: 1, day: 1) {
    print(date)
}

print(Date.yesterday)
print(Date.tomorrow)

// ========== CONVERTING UNITS ==========

struct Temperature {
    let celsius: Double
    
    init(celsius: Double) {
        self.celsius = celsius
    }
    
    // Type methods for conversion
    static func celsiusToFahrenheit(_ celsius: Double) -> Double {
        return celsius * 9/5 + 32
    }
    
    static func fahrenheitToCelsius(_ fahrenheit: Double) -> Double {
        return (fahrenheit - 32) * 5/9
    }
    
    static func celsiusToKelvin(_ celsius: Double) -> Double {
        return celsius + 273.15
    }
}

let fahrenheit = Temperature.celsiusToFahrenheit(25)  // 77.0
let celsius = Temperature.fahrenheitToCelsius(77)     // 25.0
```

**Explanation:**
- Type methods belong to the type, not instances
- `static` cannot be overridden
- `class` can be overridden in subclasses
- Use for factories, utilities, shared functionality
- Access type properties from type methods

---

### Example 3: Mutating Methods, Subscripts, and Advanced Patterns

```swift
// ========== MUTATING METHODS FOR STRUCTS ==========

struct Stack<Element> {
    private var items: [Element] = []
    
    // Mutating: Modifies the struct
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.isEmpty ? nil : items.removeLast()
    }
    
    // Non-mutating: Just reads
    func peek() -> Element? {
        return items.last
    }
    
    var count: Int {
        return items.count
    }
}

var stack = Stack<Int>()
stack.push(1)
stack.push(2)
stack.push(3)
print(stack.pop())   // Optional(3)
print(stack.peek())  // Optional(2)

// ========== MUTATING SELF ==========

enum TriStateSwitch {
    case off, low, high
    
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}

var lightSwitch = TriStateSwitch.off
lightSwitch.next()
print(lightSwitch)  // low
lightSwitch.next()
print(lightSwitch)  // high

// ========== SUBSCRIPTS (BASIC) ==========

struct TimesTable {
    let multiplier: Int
    
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}

let threeTimesTable = TimesTable(multiplier: 3)
print(threeTimesTable[5])   // 15
print(threeTimesTable[10])  // 30

// ========== SUBSCRIPTS WITH GET/SET ==========

struct Matrix {
    var grid: [[Double]]
    let rows: Int
    let columns: Int
    
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: Array(repeating: 0.0, count: columns), count: rows)
    }
    
    subscript(row: Int, column: Int) -> Double {
        get {
            return grid[row][column]
        }
        set {
            grid[row][column] = newValue
        }
    }
}

var matrix = Matrix(rows: 2, columns: 2)
matrix[0, 0] = 1.0
matrix[0, 1] = 2.0
matrix[1, 0] = 3.0
matrix[1, 1] = 4.0

print(matrix[0, 0])  // 1.0
print(matrix[1, 1])  // 4.0

// ========== SUBSCRIPT WITH MULTIPLE PARAMETERS ==========

struct ChessBoard {
    var pieces: [String: String] = [:]
    
    subscript(file: Character, rank: Int) -> String? {
        get {
            return pieces["\(file)\(rank)"]
        }
        set {
            pieces["\(file)\(rank)"] = newValue
        }
    }
}

var board = ChessBoard()
board["a", 1] = "♜"  // Rook
board["e", 1] = "♚"  // King

print(board["a", 1] ?? "Empty")  // ♜

// ========== DICTIONARY-LIKE SUBSCRIPT ==========

struct PhoneBook {
    private var contacts: [String: String] = [:]
    
    subscript(name: String) -> String? {
        get {
            return contacts[name]
        }
        set {
            contacts[name] = newValue
        }
    }
    
    var count: Int {
        return contacts.count
    }
}

var phoneBook = PhoneBook()
phoneBook["Alice"] = "123-456-7890"
phoneBook["Bob"] = "098-765-4321"

print(phoneBook["Alice"] ?? "Not found")  // 123-456-7890
phoneBook["Alice"] = nil  // Remove
print(phoneBook.count)  // 1

// ========== SUBSCRIPT WITH RANGE ==========

struct Playlist {
    var songs: [String]
    
    subscript(range: Range<Int>) -> [String] {
        return Array(songs[range])
    }
}

let playlist = Playlist(songs: ["Song1", "Song2", "Song3", "Song4", "Song5"])
print(playlist[1..<4])  // ["Song2", "Song3", "Song4"]

// ========== TYPE SUBSCRIPTS ==========

struct Planet {
    static var planets = ["Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune"]
    
    static subscript(index: Int) -> String {
        return planets[index]
    }
}

print(Planet[2])  // Earth
print(Planet[4])  // Jupiter

// ========== REAL-WORLD: SAFE ARRAY ACCESS ==========

extension Array {
    subscript(safe index: Int) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}

let numbers = [1, 2, 3, 4, 5]
print(numbers[safe: 2] ?? 0)   // 3
print(numbers[safe: 10] ?? 0)  // 0 (safe, no crash)

// ========== SWIFTUI-STYLE SUBSCRIPT ==========

struct Storage {
    private var data: [String: Any] = [:]
    
    subscript<T>(key: String, as type: T.Type = T.self) -> T? {
        get {
            return data[key] as? T
        }
        set {
            data[key] = newValue
        }
    }
}

var storage = Storage()
storage["count", as: Int.self] = 42
storage["name", as: String.self] = "Alice"

if let count: Int = storage["count", as: Int.self] {
    print("Count: \(count)")  // Count: 42
}

// ========== PERFORMANCE: INLINING ==========

struct Point3D {
    var x, y, z: Double
    
    // @inline(__always) forces inlining
    @inline(__always)
    func distance(to point: Point3D) -> Double {
        let dx = x - point.x
        let dy = y - point.y
        let dz = z - point.z
        return sqrt(dx*dx + dy*dy + dz*dz)
    }
}

// ========== METHOD PARAMETERS ==========

struct Formatter {
    // External and internal parameter names
    func format(number: Int, withThousandsSeparator separator: Bool) -> String {
        let formatter = NumberFormatter()
        formatter.numberStyle = .decimal
        formatter.usesGroupingSeparator = separator
        return formatter.string(from: NSNumber(value: number)) ?? "\(number)"
    }
    
    // Omit external label with _
    func format(_ number: Double, decimals: Int) -> String {
        return String(format: "%.\(decimals)f", number)
    }
}

let formatter = Formatter()
print(formatter.format(number: 1000000, withThousandsSeparator: true))  // "1,000,000"
print(formatter.format(3.14159, decimals: 2))  // "3.14"

// ========== VARIADIC PARAMETERS ==========

struct Logger {
    func log(_ items: String...) {
        for item in items {
            print("📝 \(item)")
        }
    }
}

let logger = Logger()
logger.log("Starting app")
logger.log("User logged in", "Session started", "Data loaded")

// ========== INOUT PARAMETERS ==========

struct Swapper {
    func swap(_ a: inout Int, _ b: inout Int) {
        let temp = a
        a = b
        b = temp
    }
}

var x = 5
var y = 10
let swapper = Swapper()
swapper.swap(&x, &y)
print(x, y)  // 10, 5

// ========== THROWING METHODS ==========

enum FileError: Error {
    case notFound
    case permissionDenied
}

struct FileManager {
    func readFile(at path: String) throws -> String {
        guard path != "" else {
            throw FileError.notFound
        }
        return "File contents"
    }
    
    func deleteFile(at path: String) throws {
        // Implementation
    }
}

let fm = FileManager()
do {
    let contents = try fm.readFile(at: "/path/to/file")
    print(contents)
} catch {
    print("Error: \(error)")
}

// ========== ASYNC METHODS ==========

struct APIClient {
    func fetchUser(id: Int) async throws -> User {
        // Simulate network request
        try await Task.sleep(nanoseconds: 1_000_000_000)
        return User(firstName: "Alice", lastName: "Johnson", email: "alice@example.com", profileImageURL: nil)
    }
    
    func fetchPosts() async throws -> [Post] {
        try await Task.sleep(nanoseconds: 500_000_000)
        return []
    }
}

struct Post { }

// Usage in async context:
// let client = APIClient()
// let user = try await client.fetchUser(id: 123)
```

**Explanation:**
- **Mutating**: Required for structs/enums to modify `self`
- **Subscripts**: Custom `[]` access syntax
- **Type subscripts**: Work on type, not instance
- **Multiple parameters**: Subscripts can take many params
- **Performance**: Use `@inline` for hot paths

---

## 🎯 2025 Interview Questions

### Question 1: Why do structs need the `mutating` keyword for methods that modify properties?

**Answer:**

**Short Answer:** Structs are **value types** with immutable instances by default. `mutating` explicitly marks methods that change the struct.

**The Reason:**

```swift
struct Counter {
    var count: Int = 0
    
    // ❌ Error: Cannot assign to property: 'self' is immutable
    func increment() {
        count += 1  // Trying to mutate
    }
    
    // ✅ Correct: Mark as mutating
    mutating func incrementCorrect() {
        count += 1
    }
}

let immutableCounter = Counter()
// immutableCounter.incrementCorrect()  // ❌ Cannot mutate 'let' constant

var mutableCounter = Counter()
mutableCounter.incrementCorrect()  // ✅ OK
```

**What `mutating` Does:**

Under the hood, `mutating` replaces `self` with a new copy:

```swift
// What you write:
struct Point {
    var x, y: Double
    
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}

// What the compiler does (conceptually):
struct Point {
    var x, y: Double
    
    func moveBy(x deltaX: Double, y deltaY: Double) -> Point {
        var newSelf = self
        newSelf.x += deltaX
        newSelf.y += deltaY
        return newSelf  // Replaces original
    }
}
```

**Classes Don't Need `mutating`:**

```swift
class BankAccount {
    var balance: Double
    
    init(balance: Double) {
        self.balance = balance
    }
    
    // No 'mutating' needed
    func deposit(_ amount: Double) {
        balance += amount  // OK - reference type
    }
}

let account = BankAccount(balance: 1000)
account.deposit(500)  // Works even with 'let'
// 'let' prevents reassignment, not mutation
```

**Mutating `self` Entirely:**

```swift
enum State {
    case loading
    case loaded(data: String)
    case error(message: String)
    
    mutating func load(_ data: String) {
        self = .loaded(data: data)  // Replace entire enum
    }
    
    mutating func fail(_ message: String) {
        self = .error(message: message)
    }
}

var state = State.loading
state.load("Data")
print(state)  // loaded("Data")
```

**Nested Mutating:**

```swift
struct Game {
    var score: Int = 0
    
    mutating func addPoints(_ points: Int) {
        score += points
    }
}

struct Player {
    var name: String
    var game: Game
    
    // Mutating because it mutates nested struct
    mutating func increaseScore(_ points: Int) {
        game.addPoints(points)  // Mutates game
    }
}

var player = Player(name: "Alice", game: Game())
player.increaseScore(10)
print(player.game.score)  // 10
```

**Performance Implications:**

```swift
struct LargeStruct {
    var data: [Int] = Array(repeating: 0, count: 10000)
    
    mutating func modify() {
        data[0] = 42
        // Copy-on-write prevents full copy
    }
}

var large = LargeStruct()
large.modify()  // Efficient with COW
```

**Key Takeaway:** `mutating` is required for value types (structs/enums) to modify themselves, ensuring immutability by default.

---

### Question 2: What's the difference between static and class methods? When would you use each?

**Answer:**

**Static Methods:**
- **Cannot be overridden** by subclasses
- Used in structs, enums, classes
- Final by default

**Class Methods:**
- **Can be overridden** by subclasses
- Only in classes, not structs/enums
- Allow polymorphism

**Examples:**

```swift
class Animal {
    // Static: Cannot override
    static func kingdom() -> String {
        return "Animalia"
    }
    
    // Class: Can override
    class func sound() -> String {
        return "Some sound"
    }
}

class Dog: Animal {
    // ❌ Error: Cannot override static
    // override static func kingdom() -> String { }
    
    // ✅ Can override class
    override class func sound() -> String {
        return "Woof!"
    }
}

class Cat: Animal {
    override class func sound() -> String {
        return "Meow!"
    }
}

print(Animal.kingdom())  // "Animalia"
print(Dog.kingdom())     // "Animalia" (inherited)

print(Animal.sound())    // "Some sound"
print(Dog.sound())       // "Woof!" (overridden)
print(Cat.sound())       // "Meow!" (overridden)
```

**When to Use Static:**

**1. Utility Functions:**

```swift
struct Math {
    static func square(_ x: Double) -> Double {
        return x * x
    }
    
    static func cube(_ x: Double) -> Double {
        return x * x * x
    }
}

// No need to override math functions
print(Math.square(5))  // 25.0
```

**2. Constants:**

```swift
struct Config {
    static let apiURL = "https://api.example.com"
    static let timeout: TimeInterval = 30
}
```

**3. Singleton:**

```swift
class NetworkManager {
    static let shared = NetworkManager()
    private init() { }
}
```

**4. Factory Methods (Struct):**

```swift
struct Color {
    static func random() -> Color {
        return Color(red: .random(in: 0...1),
                     green: .random(in: 0...1),
                     blue: .random(in: 0...1))
    }
    
    let red, green, blue: Double
}
```

**When to Use Class:**

**1. Polymorphic Behavior:**

```swift
class Logger {
    class func log(_ message: String) {
        print("[\(type(of: self))] \(message)")
    }
}

class DebugLogger: Logger {
    override class func log(_ message: String) {
        print("🐛 [DEBUG] \(message)")
    }
}

class ErrorLogger: Logger {
    override class func log(_ message: String) {
        print("❌ [ERROR] \(message)")
    }
}

Logger.log("Message")       // [Logger] Message
DebugLogger.log("Message")  // 🐛 [DEBUG] Message
ErrorLogger.log("Message")  // ❌ [ERROR] Message
```

**2. Factory with Overrides:**

```swift
class View {
    class func create() -> View {
        return View()
    }
}

class Button: View {
    override class func create() -> View {
        let button = Button()
        button.setup()
        return button
    }
    
    func setup() {
        print("Setting up button")
    }
}

let view = View.create()      // View
let button = Button.create()  // Button (with setup)
```

**3. Default Implementations:**

```swift
class DatabaseManager {
    class func connectionString() -> String {
        return "default://localhost"
    }
}

class ProductionDBManager: DatabaseManager {
    override class func connectionString() -> String {
        return "production://prod.db.com"
    }
}

class TestDBManager: DatabaseManager {
    override class func connectionString() -> String {
        return "test://localhost:5432"
    }
}
```

**Structs: Only Static:**

```swift
struct Point {
    // ✅ Static works
    static func zero() -> Point {
        return Point(x: 0, y: 0)
    }
    
    // ❌ Error: 'class' method can only be used in classes
    // class func origin() -> Point { }
    
    var x, y: Double
}
```

**Decision Matrix:**

| Need | Static | Class |
|------|--------|-------|
| Structs/Enums | ✅ | ❌ |
| Cannot override | ✅ | ❌ |
| Can override | ❌ | ✅ |
| Utility functions | ✅ | ❌ |
| Polymorphic behavior | ❌ | ✅ |
| Constants | ✅ | ❌ |
| Performance (slight edge) | ✅ | ⚠️  |

**Key Takeaway:** Use `static` by default. Use `class` only when you need override capability.

---

### Question 3: Explain subscripts. Implement a custom subscript for safe array access.

**Answer:**

**Subscripts** provide shorthand syntax for accessing elements in a collection using `[]`.

**Basic Subscript:**

```swift
struct TimesTable {
    let multiplier: Int
    
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}

let fiveTimesTable = TimesTable(multiplier: 5)
print(fiveTimesTable[3])   // 15
print(fiveTimesTable[6])   // 30
```

**Subscript with Getter and Setter:**

```swift
struct Matrix {
    var grid: [[Int]]
    
    init(rows: Int, columns: Int) {
        grid = Array(repeating: Array(repeating: 0, count: columns), count: rows)
    }
    
    subscript(row: Int, column: Int) -> Int {
        get {
            return grid[row][column]
        }
        set {
            grid[row][column] = newValue
        }
    }
}

var matrix = Matrix(rows: 3, columns: 3)
matrix[0, 0] = 1
matrix[1, 1] = 5
matrix[2, 2] = 9

print(matrix[1, 1])  // 5
```

**Safe Array Access Implementation:**

```swift
extension Array {
    // Read-only safe subscript
    subscript(safe index: Int) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}

let numbers = [1, 2, 3, 4, 5]

// Normal subscript (crashes on out-of-bounds)
// print(numbers[10])  // ❌ Crash

// Safe subscript (returns nil)
print(numbers[safe: 2])   // Optional(3)
print(numbers[safe: 10])  // nil

// With nil coalescing
print(numbers[safe: 10] ?? 0)  // 0
```

**Safe Subscript with Get/Set:**

```swift
extension Array {
    subscript(safe index: Int) -> Element? {
        get {
            return indices.contains(index) ? self[index] : nil
        }
        set {
            guard let newValue = newValue, indices.contains(index) else {
                return
            }
            self[index] = newValue
        }
    }
}

var mutableNumbers = [1, 2, 3, 4, 5]

// Safe get
print(mutableNumbers[safe: 2] ?? 0)  // 3

// Safe set
mutableNumbers[safe: 2] = 99
print(mutableNumbers)  // [1, 2, 99, 4, 5]

// Safe set out-of-bounds (no-op)
mutableNumbers[safe: 10] = 100
print(mutableNumbers)  // [1, 2, 99, 4, 5] (unchanged)
```

**Multiple Parameters:**

```swift
struct Coordinate {
    var grid: [[String]]
    
    init(rows: Int, cols: Int) {
        grid = Array(repeating: Array(repeating: ".", count: cols), count: rows)
    }
    
    subscript(row: Int, col: Int) -> String {
        get {
            guard row >= 0 && row < grid.count &&
                  col >= 0 && col < grid[0].count else {
                return "?"
            }
            return grid[row][col]
        }
        set {
            guard row >= 0 && row < grid.count &&
                  col >= 0 && col < grid[0].count else {
                return
            }
            grid[row][col] = newValue
        }
    }
}

var coords = Coordinate(rows: 3, cols: 3)
coords[1, 1] = "X"
print(coords[1, 1])   // "X"
print(coords[10, 10]) // "?" (out of bounds)
```

**Range Subscript:**

```swift
extension Array {
    subscript(safe range: Range<Int>) -> ArraySlice<Element> {
        let lowerBound = Swift.max(0, range.lowerBound)
        let upperBound = Swift.min(count, range.upperBound)
        
        guard lowerBound < upperBound else {
            return []
        }
        
        return self[lowerBound..<upperBound]
    }
}

let items = [1, 2, 3, 4, 5]
print(Array(items[safe: 1..<4]))   // [2, 3, 4]
print(Array(items[safe: 3..<10]))  // [4, 5] (clamped)
print(Array(items[safe: 10..<20])) // [] (empty)
```

**Key-Path Subscript:**

```swift
struct Storage {
    private var data: [String: Any] = [:]
    
    subscript<T>(key: String, as type: T.Type = T.self) -> T? {
        get {
            return data[key] as? T
        }
        set {
            data[key] = newValue
        }
    }
}

var storage = Storage()
storage["age", as: Int.self] = 30
storage["name", as: String.self] = "Alice"

if let age: Int = storage["age", as: Int.self] {
    print("Age: \(age)")  // Age: 30
}
```

**Key Takeaway:** Subscripts provide collection-like syntax. Always validate bounds for safety.

---

### Question 4: What are the performance implications of method dispatch in Swift?

**Answer:**

Swift uses **different dispatch mechanisms** depending on the type and method declaration.

**1. Static Dispatch (Fastest):**

Compiler knows exact method at compile time.

```swift
struct Point {
    var x, y: Double
    
    func distance() -> Double {
        return sqrt(x*x + y*y)
    }
}

// Static dispatch: Compiler directly calls Point.distance
let point = Point(x: 3, y: 4)
point.distance()  // Direct function call (fastest)
```

**2. Dynamic Dispatch (Slower):**

Runtime determines which method to call (for inheritance).

```swift
class Animal {
    func makeSound() {
        print("Some sound")
    }
}

class Dog: Animal {
    override func makeSound() {
        print("Woof!")
    }
}

let animal: Animal = Dog()
animal.makeSound()  // Dynamic dispatch (v-table lookup)
```

**3. Message Dispatch (Slowest):**

Objective-C runtime messaging.

```swift
@objc class ObjCClass: NSObject {
    @objc dynamic func method() {
        print("Method")
    }
}

// Uses obj_msgSend (slowest)
```

**Performance Comparison:**

```swift
// Benchmark
import Foundation

struct StructType {
    var value: Int
    func calculate() -> Int { return value * 2 }
}

class ClassType {
    var value: Int
    init(value: Int) { self.value = value }
    func calculate() -> Int { return value * 2 }
}

final class FinalClassType {
    var value: Int
    init(value: Int) { self.value = value }
    func calculate() -> Int { return value * 2 }
}

// Struct (static dispatch)
let start1 = Date()
let s = StructType(value: 42)
for _ in 0..<10_000_000 {
    _ = s.calculate()
}
let time1 = Date().timeIntervalSince(start1)

// Class (dynamic dispatch)
let start2 = Date()
let c = ClassType(value: 42)
for _ in 0..<10_000_000 {
    _ = c.calculate()
}
let time2 = Date().timeIntervalSince(start2)

// Final class (static dispatch)
let start3 = Date()
let f = FinalClassType(value: 42)
for _ in 0..<10_000_000 {
    _ = f.calculate()
}
let time3 = Date().timeIntervalSince(start3)

print("Struct:       \(time1)s")  // Fastest
print("Class:        \(time2)s")  // ~2x slower
print("Final Class:  \(time3)s")  // Similar to struct
```

**Optimizing with `final`:**

```swift
class BaseClass {
    func method() { }  // Dynamic dispatch
}

final class OptimizedClass {
    func method() { }  // Static dispatch
}

// Or mark individual methods
class PartiallyOptimized {
    final func method() { }  // Static dispatch
    func overridable() { }   // Dynamic dispatch
}
```

**Protocol Witness Table (PWT):**

```swift
protocol Drawable {
    func draw()
}

struct Circle: Drawable {
    func draw() { print("Circle") }
}

struct Rectangle: Drawable {
    func draw() { print("Rectangle") }
}

let shapes: [Drawable] = [Circle(), Rectangle()]

// Uses Protocol Witness Table (slower than static, faster than dynamic)
shapes.forEach { $0.draw() }
```

**Whole Module Optimization:**

```swift
// With WMO, private/internal methods can be devirtualized
class InternalClass {
    internal func method() {
        // Compiler can inline if WMO enabled
    }
}
```

**@inline Attribute:**

```swift
struct Math {
    @inline(__always)
    static func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
}

// Inlined: No function call overhead
let result = Math.add(5, 3)
```

**Performance Summary:**

| Dispatch Type | Speed | Example |
|---------------|-------|---------|
| Static (struct) | Fastest | `struct Point` |
| Static (final class) | Fastest | `final class` |
| Dynamic (v-table) | ~2x slower | `class Animal` |
| Protocol witness | Medium | `protocol Drawable` |
| Message (Obj-C) | Slowest | `@objc dynamic` |

**Key Takeaway:** Prefer structs and final classes for performance-critical code. Use dynamic dispatch only when needed.

---

### Question 5: How do you implement method chaining in Swift?

**Answer:**

**Method Chaining** allows calling multiple methods in sequence by returning `self` or a modified copy.

**Pattern 1: Mutating Methods (Structs):**

```swift
struct Builder {
    var text: String = ""
    var fontSize: CGFloat = 12
    var color: String = "black"
    
    mutating func setText(_ text: String) -> Builder {
        self.text = text
        return self
    }
    
    mutating func setFontSize(_ size: CGFloat) -> Builder {
        self.fontSize = size
        return self
    }
    
    mutating func setColor(_ color: String) -> Builder {
        self.color = color
        return self
    }
    
    func build() -> String {
        return "Text: \(text), Size: \(fontSize), Color: \(color)"
    }
}

var builder = Builder()
let result = builder
    .setText("Hello")
    .setFontSize(18)
    .setColor("blue")
    .build()

print(result)
// Text: Hello, Size: 18.0, Color: blue
```

**Pattern 2: Non-Mutating (Return New Instance):**

```swift
struct ImmutableBuilder {
    let text: String
    let fontSize: CGFloat
    let color: String
    
    init(text: String = "", fontSize: CGFloat = 12, color: String = "black") {
        self.text = text
        self.fontSize = fontSize
        self.color = color
    }
    
    func text(_ text: String) -> ImmutableBuilder {
        return ImmutableBuilder(text: text, fontSize: fontSize, color: color)
    }
    
    func fontSize(_ size: CGFloat) -> ImmutableBuilder {
        return ImmutableBuilder(text: text, fontSize: size, color: color)
    }
    
    func color(_ color: String) -> ImmutableBuilder {
        return ImmutableBuilder(text: text, fontSize: fontSize, color: color)
    }
}

let result2 = ImmutableBuilder()
    .text("Hello")
    .fontSize(18)
    .color("blue")

print(result2.text, result2.fontSize, result2.color)
```

**Pattern 3: Classes (Reference Types):**

```swift
class RequestBuilder {
    private var url: String = ""
    private var method: String = "GET"
    private var headers: [String: String] = [:]
    
    @discardableResult
    func setURL(_ url: String) -> RequestBuilder {
        self.url = url
        return self
    }
    
    @discardableResult
    func setMethod(_ method: String) -> RequestBuilder {
        self.method = method
        return self
    }
    
    @discardableResult
    func addHeader(key: String, value: String) -> RequestBuilder {
        headers[key] = value
        return self
    }
    
    func build() -> String {
        return "\(method) \(url) Headers: \(headers)"
    }
}

let request = RequestBuilder()
    .setURL("https://api.example.com/users")
    .setMethod("POST")
    .addHeader(key: "Content-Type", value: "application/json")
    .addHeader(key: "Authorization", value: "Bearer token")
    .build()

print(request)
```

**@discardableResult:**

Suppresses warnings when return value is ignored:

```swift
class Logger {
    @discardableResult
    func log(_ message: String) -> Logger {
        print(message)
        return self
    }
}

let logger = Logger()

// Can ignore return value
logger.log("Message 1")  // No warning

// Or chain
logger.log("Message 1").log("Message 2")
```

**Real-World: SwiftUI-Style:**

```swift
struct ViewModifier {
    var padding: CGFloat = 0
    var backgroundColor: String = "white"
    var cornerRadius: CGFloat = 0
    
    func padding(_ padding: CGFloat) -> ViewModifier {
        var copy = self
        copy.padding = padding
        return copy
    }
    
    func background(_ color: String) -> ViewModifier {
        var copy = self
        copy.backgroundColor = color
        return copy
    }
    
    func cornerRadius(_ radius: CGFloat) -> ViewModifier {
        var copy = self
        copy.cornerRadius = radius
        return copy
    }
}

let view = ViewModifier()
    .padding(20)
    .background("blue")
    .cornerRadius(10)

print(view.padding, view.backgroundColor, view.cornerRadius)
```

**Key Takeaway:** Return `self` (classes) or modified copy (structs) to enable chaining.

---

## 📝 Practice Exercises

1. Create a `Calculator` struct with chainable methods for arithmetic operations
2. Implement a `Stack` with mutating `push`/`pop` methods
3. Build a custom subscript that supports negative indexing (Python-style)
4. Create a `Matrix` struct with subscript access and arithmetic operations
5. Implement a builder pattern for constructing complex objects with method chaining

---

## 🔗 Navigation
- [← Previous: Step 13 - Properties](../Step-13-Properties/README.md)
- [→ Next: Step 15 - Initialization](../Step-15-Initialization/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 14!** You now understand:
- ✅ Instance methods and the `self` keyword
- ✅ Type methods (static vs class)
- ✅ Mutating methods for value types
- ✅ Subscripts for custom collection access
- ✅ Method parameters and argument labels
- ✅ Performance implications of dispatch
- ✅ Method chaining patterns

**Next:** Master **Initialization** in Step 15!
