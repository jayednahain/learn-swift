# Step 13: Properties

## 📚 Description

**Properties** associate values with classes, structs, and enums. Swift provides powerful property features including **stored properties**, **computed properties**, **property observers**, **lazy properties**, **static/class properties**, and **property wrappers**. Understanding properties is essential for building clean, maintainable Swift code.

### What You'll Learn:
- Stored properties (constants and variables)
- Computed properties (getters and setters)
- Property observers (willSet and didSet)
- Lazy properties for deferred initialization
- Type properties (static and class)
- Property wrappers (@State, @Published, etc.)

### Key Concepts:
- **Stored Property**: Direct value storage
- **Computed Property**: Calculated on access
- **Property Observer**: Responds to value changes
- **Lazy Property**: Initialized on first access
- **Type Property**: Belongs to type, not instance
- **Property Wrapper**: Reusable property logic

---

## 💡 Clear Examples

### Example 1: Stored Properties and Computed Properties

```swift
// ========== STORED PROPERTIES ==========

struct Person {
    // Stored properties
    var firstName: String
    var lastName: String
    let birthYear: Int  // Constant stored property
    var age: Int = 0    // Default value
}

var john = Person(firstName: "John", lastName: "Doe", birthYear: 1990)
john.age = 33
// john.birthYear = 1991  // ❌ Error: birthYear is let

class BankAccount {
    // Stored properties
    var accountNumber: String
    var balance: Double = 0.0
    let currency: String = "USD"
    
    init(accountNumber: String) {
        self.accountNumber = accountNumber
    }
}

// ========== COMPUTED PROPERTIES ==========

struct Rectangle {
    var width: Double
    var height: Double
    
    // Read-only computed property
    var area: Double {
        return width * height
    }
    
    // Computed property with getter and setter
    var perimeter: Double {
        get {
            return 2 * (width + height)
        }
        set {
            // newValue is the new perimeter
            // Keep aspect ratio, adjust width and height
            let ratio = width / height
            height = newValue / (2 * (ratio + 1))
            width = height * ratio
        }
    }
}

var rect = Rectangle(width: 10, height: 5)
print(rect.area)       // 50.0 (computed)
print(rect.perimeter)  // 30.0 (computed)

rect.perimeter = 40    // Uses setter
print(rect.width)      // ~13.33
print(rect.height)     // ~6.67

// ========== SHORTHAND COMPUTED PROPERTIES ==========

struct Circle {
    var radius: Double
    
    // Shorthand for read-only computed property
    var diameter: Double {
        radius * 2  // No return keyword needed
    }
    
    var circumference: Double {
        2 * .pi * radius
    }
    
    var area: Double {
        .pi * radius * radius
    }
}

let circle = Circle(radius: 5)
print(circle.diameter)        // 10.0
print(circle.circumference)   // 31.42
print(circle.area)            // 78.54

// ========== COMPUTED PROPERTIES IN CLASSES ==========

class Temperature {
    var celsius: Double
    
    init(celsius: Double) {
        self.celsius = celsius
    }
    
    // Fahrenheit as computed property
    var fahrenheit: Double {
        get {
            return celsius * 9/5 + 32
        }
        set {
            celsius = (newValue - 32) * 5/9
        }
    }
    
    // Kelvin as computed property
    var kelvin: Double {
        get {
            return celsius + 273.15
        }
        set {
            celsius = newValue - 273.15
        }
    }
}

let temp = Temperature(celsius: 25)
print(temp.fahrenheit)  // 77.0

temp.fahrenheit = 100
print(temp.celsius)     // 37.78

temp.kelvin = 300
print(temp.celsius)     // 26.85

// ========== REAL-WORLD EXAMPLE: USER MODEL ==========

struct User {
    // Stored properties
    var firstName: String
    var lastName: String
    var email: String
    var profileImageURL: String?
    
    // Computed property
    var fullName: String {
        return "\(firstName) \(lastName)"
    }
    
    var initials: String {
        let first = firstName.prefix(1)
        let last = lastName.prefix(1)
        return "\(first)\(last)".uppercased()
    }
    
    var isEmailValid: Bool {
        return email.contains("@") && email.contains(".")
    }
}

var user = User(
    firstName: "Alice",
    lastName: "Johnson",
    email: "alice@example.com",
    profileImageURL: nil
)

print(user.fullName)       // "Alice Johnson"
print(user.initials)       // "AJ"
print(user.isEmailValid)   // true

// ========== COMPUTED PROPERTIES IN SWIFTUI ==========

import SwiftUI

struct ContentView: View {
    var message: String
    
    // Computed property for styling
    var textColor: Color {
        message.isEmpty ? .gray : .black
    }
    
    var fontSize: CGFloat {
        message.count > 50 ? 14 : 18
    }
    
    var body: some View {
        Text(message)
            .foregroundColor(textColor)
            .font(.system(size: fontSize))
    }
}

// ========== STORED VS COMPUTED ==========

struct Product {
    // Stored: Value is saved
    var price: Double
    var taxRate: Double = 0.08
    
    // Computed: Calculated on access
    var priceWithTax: Double {
        price * (1 + taxRate)
    }
    
    // Computed: No storage needed
    var formattedPrice: String {
        String(format: "$%.2f", priceWithTax)
    }
}

var product = Product(price: 99.99)
print(product.priceWithTax)     // 107.9892
print(product.formattedPrice)   // "$107.99"
```

**Explanation:**
- **Stored properties**: Hold values directly
- **Computed properties**: Calculate values dynamically
- Use computed for derived values
- Read-only computed properties are common
- Getters and setters provide controlled access

---

### Example 2: Property Observers (willSet and didSet)

```swift
// ========== PROPERTY OBSERVERS BASICS ==========

class StepCounter {
    var totalSteps: Int = 0 {
        willSet {
            print("About to set totalSteps to \(newValue)")
        }
        didSet {
            print("Changed from \(oldValue) to \(totalSteps)")
            
            if totalSteps > oldValue {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}

let counter = StepCounter()
counter.totalSteps = 100
// Output:
// About to set totalSteps to 100
// Changed from 0 to 100
// Added 100 steps

counter.totalSteps = 250
// Output:
// About to set totalSteps to 250
// Changed from 100 to 250
// Added 150 steps

// ========== VALIDATION WITH didSet ==========

struct Account {
    var balance: Double = 0 {
        didSet {
            // Prevent negative balance
            if balance < 0 {
                print("⚠️  Warning: Negative balance!")
                balance = oldValue  // Restore previous value
            }
        }
    }
}

var account = Account(balance: 100)
account.balance = 50   // OK
account.balance = -20  // Reverted to 50
print(account.balance) // 50

// ========== UI UPDATES WITH didSet ==========

import UIKit

class ProfileViewController: UIViewController {
    var user: User? {
        didSet {
            updateUI()
        }
    }
    
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var emailLabel: UILabel!
    
    func updateUI() {
        guard let user = user else { return }
        nameLabel.text = user.fullName
        emailLabel.text = user.email
    }
}

// When user is set, UI automatically updates

// ========== COMBINING COMPUTED AND OBSERVERS ==========

class TemperatureMonitor {
    // Stored property with observer
    var celsius: Double = 0 {
        didSet {
            checkThreshold()
        }
    }
    
    // Computed property
    var fahrenheit: Double {
        get {
            return celsius * 9/5 + 32
        }
        set {
            celsius = (newValue - 32) * 5/9
            // didSet is NOT called when setting computed property
        }
    }
    
    func checkThreshold() {
        if celsius > 30 {
            print("🔥 Hot! \(celsius)°C")
        } else if celsius < 0 {
            print("❄️  Cold! \(celsius)°C")
        }
    }
}

let monitor = TemperatureMonitor()
monitor.celsius = 35  // Triggers didSet
// Output: 🔥 Hot! 35.0°C

monitor.fahrenheit = 32  // Sets celsius, triggers didSet
// Output: ❄️  Cold! 0.0°C

// ========== REAL-WORLD: FORM VALIDATION ==========

class RegistrationForm {
    var username: String = "" {
        didSet {
            validateUsername()
        }
    }
    
    var email: String = "" {
        didSet {
            validateEmail()
        }
    }
    
    var password: String = "" {
        didSet {
            validatePassword()
        }
    }
    
    private(set) var isValid: Bool = false
    
    func validateUsername() {
        let valid = username.count >= 3
        print("Username valid: \(valid)")
        updateValidity()
    }
    
    func validateEmail() {
        let valid = email.contains("@")
        print("Email valid: \(valid)")
        updateValidity()
    }
    
    func validatePassword() {
        let valid = password.count >= 8
        print("Password valid: \(valid)")
        updateValidity()
    }
    
    func updateValidity() {
        isValid = username.count >= 3 &&
                  email.contains("@") &&
                  password.count >= 8
        print("Form valid: \(isValid)")
    }
}

let form = RegistrationForm()
form.username = "alice"      // Validates on each change
form.email = "alice@test.com"
form.password = "secret123"
// Auto-validates after each property change

// ========== SWIFTUI: PUBLISH CHANGES ==========

import Combine
import SwiftUI

class ViewModel: ObservableObject {
    // Using @Published (property wrapper with observer-like behavior)
    @Published var searchText: String = "" {
        didSet {
            performSearch()
        }
    }
    
    @Published var results: [String] = []
    
    func performSearch() {
        // Debounce or filter results
        results = ["Result 1", "Result 2"]
    }
}

// ========== NESTED PROPERTIES ==========

class GameCharacter {
    var health: Int = 100 {
        didSet {
            if health <= 0 {
                health = 0
                print("💀 Character died!")
            } else if health > 100 {
                health = 100
            }
            print("Health: \(health)/100")
        }
    }
    
    var inventory: [String] = [] {
        didSet {
            print("Inventory: \(inventory)")
        }
    }
}

let character = GameCharacter()
character.health -= 30   // Health: 70/100
character.health += 50   // Health: 100/100 (clamped)
character.inventory.append("Sword")  // Inventory: ["Sword"]

// ========== PERFORMANCE NOTE ==========

struct HeavyStruct {
    var data: [Int] = Array(repeating: 0, count: 10000) {
        didSet {
            // ⚠️  Careful: didSet can be expensive
            // Runs on every modification
            print("Data changed")
        }
    }
}

// Better approach for expensive operations:
struct BetterStruct {
    private var _data: [Int] = []
    
    var data: [Int] {
        get { _data }
        set {
            _data = newValue
            // Only do expensive work when needed
            if needsProcessing {
                processData()
            }
        }
    }
    
    var needsProcessing = false
    
    func processData() {
        // Expensive operation
    }
}
```

**Explanation:**
- **willSet**: Called before value changes
- **didSet**: Called after value changes
- Use for validation, UI updates, side effects
- `oldValue` in didSet, `newValue` in willSet
- Can prevent changes by restoring oldValue

---

### Example 3: Lazy Properties, Type Properties, and Property Wrappers

```swift
// ========== LAZY PROPERTIES ==========

class DataManager {
    // Expensive to create
    lazy var database: Database = {
        print("Creating database...")
        return Database()
    }()
    
    // Only created if accessed
    lazy var cache: [String: Any] = {
        print("Creating cache...")
        return [:]
    }()
    
    init() {
        print("DataManager initialized")
    }
}

class Database {
    init() {
        print("Database connected")
    }
}

let manager = DataManager()
// Output: DataManager initialized
// database not created yet

let db = manager.database
// Output: Creating database...
//         Database connected

let db2 = manager.database
// No output - already created

// ========== LAZY WITH CLOSURE ==========

class ImageLoader {
    var imageURL: URL
    
    // Lazy property with complex initialization
    lazy var image: UIImage? = {
        print("Loading image from \(imageURL)")
        guard let data = try? Data(contentsOf: imageURL) else {
            return nil
        }
        return UIImage(data: data)
    }()
    
    init(imageURL: URL) {
        self.imageURL = imageURL
    }
}

// ========== LAZY FOR PERFORMANCE ==========

struct Document {
    var title: String
    var content: String
    
    // Expensive computation, only done if needed
    lazy var wordCount: Int = {
        print("Counting words...")
        return content.split(separator: " ").count
    }()
    
    lazy var characterCount: Int = {
        print("Counting characters...")
        return content.count
    }()
}

var doc = Document(title: "Report", content: "Lorem ipsum dolor sit amet")
print(doc.title)  // No lazy properties initialized yet

// Only now is wordCount calculated
print(doc.wordCount)
// Output: Counting words...
//         5

// ========== TYPE PROPERTIES (static) ==========

struct Math {
    // Type property (belongs to type, not instance)
    static let pi = 3.14159
    static let e = 2.71828
    
    // Computed type property
    static var randomDouble: Double {
        return Double.random(in: 0...1)
    }
}

print(Math.pi)            // Access via type name
print(Math.randomDouble)

// let m = Math()
// m.pi  // ❌ Error: pi is a type property

// ========== CLASS TYPE PROPERTIES ==========

class GameSettings {
    // Static: Cannot be overridden
    static var maxPlayers = 4
    
    // Class: Can be overridden by subclasses
    class var defaultDifficulty: String {
        return "Normal"
    }
}

class AdvancedSettings: GameSettings {
    override class var defaultDifficulty: String {
        return "Hard"
    }
}

print(GameSettings.maxPlayers)            // 4
print(GameSettings.defaultDifficulty)     // "Normal"
print(AdvancedSettings.defaultDifficulty) // "Hard"

// ========== SINGLETON WITH TYPE PROPERTY ==========

class NetworkManager {
    static let shared = NetworkManager()
    
    private init() {
        print("NetworkManager created")
    }
    
    func makeRequest() {
        print("Making request...")
    }
}

NetworkManager.shared.makeRequest()
// Only one instance exists

// ========== PROPERTY WRAPPERS ==========

// Define a property wrapper
@propertyWrapper
struct Capitalized {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.capitalized }
    }
}

struct Article {
    @Capitalized var title: String
    @Capitalized var author: String
}

var article = Article()
article.title = "hello world"
article.author = "john doe"

print(article.title)   // "Hello World"
print(article.author)  // "John Doe"

// ========== CLAMPING PROPERTY WRAPPER ==========

@propertyWrapper
struct Clamped<Value: Comparable> {
    private var value: Value
    let range: ClosedRange<Value>
    
    init(wrappedValue: Value, _ range: ClosedRange<Value>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
    
    var wrappedValue: Value {
        get { value }
        set { value = min(max(newValue, range.lowerBound), range.upperBound) }
    }
}

struct Game {
    @Clamped(0...100) var health: Int = 100
    @Clamped(0...10) var level: Int = 1
}

var game = Game()
game.health = 150  // Clamped to 100
game.health = -10  // Clamped to 0
print(game.health) // 0

// ========== SWIFTUI PROPERTY WRAPPERS ==========

import SwiftUI

struct CounterView: View {
    // @State: Property wrapper for local state
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1  // Triggers view update
            }
        }
    }
}

class TimerViewModel: ObservableObject {
    // @Published: Property wrapper that publishes changes
    @Published var seconds = 0
    
    func start() {
        Timer.scheduledTimer(withTimeInterval: 1, repeats: true) { _ in
            self.seconds += 1
        }
    }
}

struct TimerView: View {
    // @StateObject: Property wrapper for object lifecycle
    @StateObject var viewModel = TimerViewModel()
    
    var body: some View {
        VStack {
            Text("Seconds: \(viewModel.seconds)")
            Button("Start") {
                viewModel.start()
            }
        }
    }
}

// ========== USERDEFAULTS PROPERTY WRAPPER ==========

@propertyWrapper
struct UserDefault<Value> {
    let key: String
    let defaultValue: Value
    
    var wrappedValue: Value {
        get {
            return UserDefaults.standard.object(forKey: key) as? Value ?? defaultValue
        }
        set {
            UserDefaults.standard.set(newValue, forKey: key)
        }
    }
}

struct Settings {
    @UserDefault(key: "username", defaultValue: "Guest")
    var username: String
    
    @UserDefault(key: "isDarkMode", defaultValue: false)
    var isDarkMode: Bool
}

var settings = Settings()
print(settings.username)  // "Guest"
settings.username = "Alice"
print(settings.username)  // "Alice" (persisted)

// ========== VALIDATION PROPERTY WRAPPER ==========

@propertyWrapper
struct Email {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set {
            if newValue.contains("@") && newValue.contains(".") {
                value = newValue
            } else {
                print("⚠️  Invalid email: \(newValue)")
            }
        }
    }
}

struct UserProfile {
    @Email var email: String
}

var profile = UserProfile()
profile.email = "invalid"        // Warning printed
profile.email = "test@test.com"  // Accepted
print(profile.email)             // "test@test.com"

// ========== PROJECTED VALUE ==========

@propertyWrapper
struct Logged<Value> {
    private var value: Value
    private(set) var projectedValue: [Value] = []
    
    init(wrappedValue: Value) {
        self.value = wrappedValue
        self.projectedValue = [wrappedValue]
    }
    
    var wrappedValue: Value {
        get { value }
        set {
            value = newValue
            projectedValue.append(newValue)
        }
    }
}

struct Counter {
    @Logged var count: Int = 0
}

var counter = Counter()
counter.count = 1
counter.count = 2
counter.count = 3

print(counter.count)   // 3
print(counter.$count)  // [0, 1, 2, 3] (history)
```

**Explanation:**
- **Lazy**: Deferred initialization for expensive properties
- **Static**: Type properties shared across all instances
- **Property Wrappers**: Reusable property logic (@State, @Published, etc.)
- Lazy properties must be `var`, not `let`
- Property wrappers reduce boilerplate

---

## 🎯 2025 Interview Questions

### Question 1: What's the difference between stored and computed properties? When would you use each?

**Answer:**

**Stored Properties:**
- **Store actual values** in memory
- **Consume memory** (one value per instance)
- Can have initial values
- Can use property observers (willSet/didSet)

**Computed Properties:**
- **Calculate values on access** (don't store)
- **Don't consume memory** for the value itself
- Must have explicit type
- Cannot use property observers (no stored value to observe)

**Examples:**

```swift
struct Rectangle {
    // STORED PROPERTIES
    var width: Double    // Stored in memory
    var height: Double   // Stored in memory
    
    // COMPUTED PROPERTY
    var area: Double {   // Calculated, not stored
        return width * height
    }
    
    var perimeter: Double {  // Calculated
        return 2 * (width + height)
    }
}

let rect = Rectangle(width: 10, height: 5)
// Memory: [width: 10, height: 5]
// area and perimeter are NOT stored

print(rect.area)       // Calculates: 10 * 5 = 50
print(rect.perimeter)  // Calculates: 2 * (10 + 5) = 30
```

**When to Use Stored:**

**1. Value Needs Persistence:**
```swift
class User {
    var name: String = ""        // ✅ Stored: needs to persist
    var email: String = ""       // ✅ Stored: needs to persist
    var createdAt: Date = Date() // ✅ Stored: timestamp
}
```

**2. Value Changes Independently:**
```swift
struct Point {
    var x: Double  // ✅ Stored: can change independently
    var y: Double  // ✅ Stored: can change independently
}
```

**3. Need Property Observers:**
```swift
class ViewController {
    var data: [Item] = [] {
        didSet {  // ✅ Only works with stored properties
            tableView.reloadData()
        }
    }
}
```

**When to Use Computed:**

**1. Value Derived from Other Properties:**
```swift
struct Person {
    var firstName: String
    var lastName: String
    
    // ✅ Computed: derived from other properties
    var fullName: String {
        return "\(firstName) \(lastName)"
    }
}
```

**2. Value Always Up-to-Date:**
```swift
struct ShoppingCart {
    var items: [Item]
    
    // ✅ Computed: always current total
    var total: Double {
        return items.reduce(0) { $0 + $1.price }
    }
}

var cart = ShoppingCart(items: [])
cart.items.append(Item(price: 10))
print(cart.total)  // 10 (automatically updated)
```

**3. Reduce Memory:**
```swift
struct Circle {
    var radius: Double
    
    // ✅ Computed: saves memory
    var diameter: Double {
        return radius * 2
    }
    
    var area: Double {
        return .pi * radius * radius
    }
    
    // If these were stored, we'd waste memory
}
```

**4. Read-Only Access:**
```swift
class Task {
    var isComplete: Bool = false
    var dueDate: Date
    
    // ✅ Computed: read-only derived value
    var isOverdue: Bool {
        return !isComplete && Date() > dueDate
    }
    
    init(dueDate: Date) {
        self.dueDate = dueDate
    }
}
```

**Performance Considerations:**

```swift
struct ExpensiveComputation {
    var data: [Int]
    
    // ❌ Bad: Recomputes every access
    var sum: Int {
        return data.reduce(0, +)  // O(n) each time
    }
}

let expensive = ExpensiveComputation(data: Array(0..<1_000_000))
for _ in 0..<100 {
    _ = expensive.sum  // Computes 100 times!
}

// ✅ Better: Lazy stored property
struct Optimized {
    var data: [Int]
    
    lazy var sum: Int = {
        return data.reduce(0, +)  // Computed once
    }()
}

var optimized = Optimized(data: Array(0..<1_000_000))
for _ in 0..<100 {
    _ = optimized.sum  // Uses cached value
}
```

**Real-World Decision Matrix:**

| Scenario | Stored | Computed |
|----------|--------|----------|
| User input (name, email) | ✅ | ❌ |
| Derived value (fullName) | ❌ | ✅ |
| Need observers (didSet) | ✅ | ❌ |
| Save memory | ❌ | ✅ |
| Expensive calculation (cache) | ✅ (lazy) | ❌ |
| Always in sync | ❌ | ✅ |
| Independent value | ✅ | ❌ |

**Key Takeaway:** Use stored for independent values that need persistence. Use computed for derived, always-current values.

---

### Question 2: Explain lazy properties. Why can't they be constants (let)?

**Answer:**

**Lazy Properties** are stored properties that are initialized only when first accessed.

**Basic Example:**

```swift
class DataLoader {
    lazy var data: [String] = {
        print("Loading data...")
        return ["Item 1", "Item 2", "Item 3"]
    }()
    
    init() {
        print("DataLoader initialized")
    }
}

let loader = DataLoader()
// Output: DataLoader initialized
// data is NOT loaded yet

print(loader.data)
// Output: Loading data...
//         ["Item 1", "Item 2", "Item 3"]

print(loader.data)
// No "Loading data..." - already initialized
```

**Why Lazy Properties Must Be `var`:**

**1. Initial Value is Nil:**

Conceptually, lazy properties work like this:

```swift
// How lazy works under the hood (simplified)
class Example {
    private var _data: [String]? = nil  // Initially nil
    
    var data: [String] {
        get {
            if _data == nil {
                _data = loadData()  // Mutation happens here
            }
            return _data!
        }
    }
    
    func loadData() -> [String] {
        return ["Data"]
    }
}
```

The property **mutates** from `nil` to the actual value on first access.

**2. Mutation on Access:**

```swift
class Logger {
    lazy var logFile: FileHandle = {
        return FileHandle(forWritingAtPath: "/tmp/log.txt")!
    }()
}

let logger = Logger()
// logFile starts uninitialized

let file = logger.logFile  // ⚠️  Mutates logger.logFile
// logFile is now initialized
```

Since accessing `logFile` **changes** its value (from uninitialized to initialized), it must be `var`.

**3. Let Would Prevent Initialization:**

```swift
class Broken {
    // ❌ Error: Lazy properties must be mutable
    // lazy let data: [String] = ["Data"]
}

// Why? Because initialization is a mutation:
// Uninitialized → Initialized
// 'let' prevents this mutation
```

**Real-World Examples:**

**Heavy Initialization:**

```swift
class ImageViewController {
    var imageURL: URL
    
    // Lazy: Only load if user scrolls to this image
    lazy var image: UIImage? = {
        print("Loading image...")
        guard let data = try? Data(contentsOf: imageURL) else {
            return nil
        }
        return UIImage(data: data)
    }()
    
    init(imageURL: URL) {
        self.imageURL = imageURL
    }
    
    func display() {
        imageView.image = image  // Loads here if needed
    }
}
```

**Circular Dependencies:**

```swift
class Person {
    var name: String
    
    // Lazy breaks circular dependency
    lazy var bestFriend: Person? = {
        // Can reference self because init is complete
        return Person(name: "\(self.name)'s friend")
    }()
    
    init(name: String) {
        self.name = name
    }
}

let person = Person(name: "Alice")
print(person.bestFriend?.name)  // "Alice's friend"
```

**Computed Properties (Can Use `self`):**

```swift
class ViewModel {
    var title: String
    
    // Lazy allows using self
    lazy var formattedTitle: String = {
        return "Title: \(self.title.uppercased())"
    }()
    
    init(title: String) {
        self.title = title
    }
}
```

**Performance:**

```swift
struct Document {
    var content: String
    
    // Expensive: Computed each access
    var wordCount: Int {
        return content.split(separator: " ").count
    }
    
    // Better: Lazy computed once
    lazy var cachedWordCount: Int = {
        return content.split(separator: " ").count
    }()
}

var doc = Document(content: String(repeating: "word ", count: 1_000_000))

// Computed property
let start1 = Date()
for _ in 0..<100 {
    _ = doc.wordCount  // Recomputes 100 times!
}
print("Computed: \(Date().timeIntervalSince(start1))s")

// Lazy property
let start2 = Date()
for _ in 0..<100 {
    _ = doc.cachedWordCount  // Computed once
}
print("Lazy: \(Date().timeIntervalSince(start2))s")
// Lazy is 100x faster
```

**Important Caveats:**

**1. Not Thread-Safe:**

```swift
class NotThreadSafe {
    lazy var data: [Int] = Array(0..<1000)
}

let instance = NotThreadSafe()

// ⚠️  Race condition if accessed from multiple threads
DispatchQueue.concurrentPerform(iterations: 10) { _ in
    _ = instance.data  // May initialize multiple times!
}
```

**2. Structs and Mutability:**

```swift
struct Container {
    lazy var items: [String] = ["Item"]
}

let immutableContainer = Container()
// ❌ Error: Cannot access lazy property on immutable struct
// let items = immutableContainer.items

var mutableContainer = Container()
let items = mutableContainer.items  // ✅ OK
```

**Key Takeaway:** Lazy properties must be `var` because initialization is a mutation. Use for expensive/deferred initialization.

---

### Question 3: How do property wrappers work? Implement a custom @Clamped wrapper.

**Answer:**

**Property Wrappers** encapsulate common property logic into reusable components.

**Anatomy of a Property Wrapper:**

```swift
@propertyWrapper
struct Wrapper<T> {
    private var value: T
    
    // Required: wrappedValue
    var wrappedValue: T {
        get { value }
        set { value = newValue }
    }
    
    // Optional: projectedValue (accessed with $)
    var projectedValue: SomeType {
        // Additional functionality
    }
    
    init(wrappedValue: T) {
        self.value = wrappedValue
    }
}
```

**@Clamped Implementation:**

```swift
@propertyWrapper
struct Clamped<Value: Comparable> {
    private var value: Value
    private let range: ClosedRange<Value>
    
    var wrappedValue: Value {
        get {
            return value
        }
        set {
            // Clamp to range
            value = min(max(newValue, range.lowerBound), range.upperBound)
        }
    }
    
    // Projected value: did the value get clamped?
    var projectedValue: Bool {
        return value != wrappedValue
    }
    
    init(wrappedValue: Value, _ range: ClosedRange<Value>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
}

// Usage
struct Character {
    @Clamped(0...100) var health: Int = 100
    @Clamped(1...50) var level: Int = 1
}

var character = Character()

character.health = 150
print(character.health)   // 100 (clamped)
print(character.$health)  // true (was clamped)

character.health = 50
print(character.health)   // 50
print(character.$health)  // false (not clamped)

character.level = 0
print(character.level)    // 1 (clamped to minimum)
```

**How It Works:**

```swift
// What you write:
struct Game {
    @Clamped(0...100) var health: Int = 100
}

// What the compiler generates:
struct Game {
    private var _health = Clamped(wrappedValue: 100, 0...100)
    
    var health: Int {
        get { _health.wrappedValue }
        set { _health.wrappedValue = newValue }
    }
    
    var $health: Bool {
        _health.projectedValue
    }
}
```

**More Examples:**

**@Capitalized:**

```swift
@propertyWrapper
struct Capitalized {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.capitalized }
    }
}

struct Person {
    @Capitalized var firstName: String
    @Capitalized var lastName: String
}

var person = Person()
person.firstName = "john"
person.lastName = "doe"

print(person.firstName)  // "John"
print(person.lastName)   // "Doe"
```

**@Trimmed:**

```swift
@propertyWrapper
struct Trimmed {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.trimmingCharacters(in: .whitespacesAndNewlines) }
    }
}

struct Form {
    @Trimmed var username: String
    @Trimmed var email: String
}

var form = Form()
form.username = "  alice  "
form.email = "  alice@test.com  "

print("'\(form.username)'")  // "alice"
print("'\(form.email)'")     // "alice@test.com"
```

**@UserDefault:**

```swift
@propertyWrapper
struct UserDefault<T> {
    let key: String
    let defaultValue: T
    
    var wrappedValue: T {
        get {
            return UserDefaults.standard.object(forKey: key) as? T ?? defaultValue
        }
        set {
            UserDefaults.standard.set(newValue, forKey: key)
        }
    }
}

struct AppSettings {
    @UserDefault(key: "theme", defaultValue: "light")
    var theme: String
    
    @UserDefault(key: "notifications", defaultValue: true)
    var notificationsEnabled: Bool
}

var settings = AppSettings()
print(settings.theme)  // "light" (default)
settings.theme = "dark"
print(settings.theme)  // "dark" (persisted)
```

**@Published (Simplified):**

```swift
import Combine

@propertyWrapper
class Published<Value> {
    private var value: Value
    var projectedValue: AnyPublisher<Value, Never> {
        subject.eraseToAnyPublisher()
    }
    
    private let subject = PassthroughSubject<Value, Never>()
    
    var wrappedValue: Value {
        get { value }
        set {
            value = newValue
            subject.send(newValue)
        }
    }
    
    init(wrappedValue: Value) {
        self.value = wrappedValue
    }
}

class ViewModel {
    @Published var count: Int = 0
}

let vm = ViewModel()
vm.$count.sink { newCount in
    print("Count changed: \(newCount)")
}

vm.count = 1  // Triggers sink
vm.count = 2  // Triggers sink
```

**Advanced: @Atomic (Thread-Safe):**

```swift
@propertyWrapper
class Atomic<Value> {
    private var value: Value
    private let lock = NSLock()
    
    var wrappedValue: Value {
        get {
            lock.lock()
            defer { lock.unlock() }
            return value
        }
        set {
            lock.lock()
            defer { lock.unlock() }
            value = newValue
        }
    }
    
    init(wrappedValue: Value) {
        self.value = wrappedValue
    }
}

class Counter {
    @Atomic var count: Int = 0
}

let counter = Counter()

// Thread-safe
DispatchQueue.concurrentPerform(iterations: 1000) { _ in
    counter.count += 1
}

print(counter.count)  // Always 1000 (thread-safe)
```

**SwiftUI Property Wrappers:**

```swift
import SwiftUI

struct ContentView: View {
    @State private var count = 0        // Local state
    @StateObject var vm = ViewModel()   // Object lifecycle
    @ObservedObject var sharedVM: ViewModel  // Observed object
    @EnvironmentObject var settings: Settings  // From environment
    @Binding var text: String           // Two-way binding
    @Environment(\.colorScheme) var colorScheme  // System value
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1  // @State updates view
            }
        }
    }
}
```

**Key Takeaway:** Property wrappers reduce boilerplate and centralize common property logic.

---

### Question 4: Explain property observers (willSet/didSet). Can you use them with computed properties?

**Answer:**

**Property Observers** respond to changes in property values.

**willSet and didSet:**

```swift
class BankAccount {
    var balance: Double = 0 {
        willSet {
            print("Balance will change from \(balance) to \(newValue)")
        }
        didSet {
            print("Balance changed from \(oldValue) to \(balance)")
            
            if balance < 0 {
                print("⚠️  Overdraft!")
            }
        }
    }
}

let account = BankAccount()
account.balance = 100
// Output:
// Balance will change from 0.0 to 100.0
// Balance changed from 0.0 to 100.0

account.balance = -50
// Output:
// Balance will change from 100.0 to -50.0
// Balance changed from 100.0 to -50.0
// ⚠️  Overdraft!
```

**Special Variables:**

- **willSet**: `newValue` (the incoming value)
- **didSet**: `oldValue` (the previous value)

```swift
class Counter {
    var count: Int = 0 {
        willSet(nextCount) {  // Custom name
            print("Setting to \(nextCount)")
        }
        didSet(previousCount) {  // Custom name
            print("Was \(previousCount)")
        }
    }
}
```

**Can You Use Them with Computed Properties? NO.**

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // ❌ Error: Computed properties can't have observers
    var area: Double {
        willSet { }  // Compilation error
        didSet { }   // Compilation error
        
        return width * height
    }
}
```

**Why Not?**

Computed properties **don't store values**, they calculate them. There's no "change" to observe.

**Workaround: Observe the Underlying Properties:**

```swift
struct Rectangle {
    var width: Double {
        didSet {
            print("Area changed to \(area)")
        }
    }
    
    var height: Double {
        didSet {
            print("Area changed to \(area)")
        }
    }
    
    var area: Double {
        return width * height
    }
}

var rect = Rectangle(width: 10, height: 5)
rect.width = 20  // Triggers didSet
// Output: Area changed to 100.0
```

**Common Use Cases:**

**1. UI Updates:**

```swift
class ProfileViewController: UIViewController {
    var user: User? {
        didSet {
            updateUI()
        }
    }
    
    func updateUI() {
        guard let user = user else { return }
        nameLabel.text = user.name
        emailLabel.text = user.email
    }
}

// Setting user automatically updates UI
viewController.user = User(name: "Alice", email: "alice@test.com")
```

**2. Validation:**

```swift
struct Password {
    var value: String = "" {
        didSet {
            validate()
        }
    }
    
    private(set) var isValid: Bool = false
    
    mutating func validate() {
        isValid = value.count >= 8
        print("Password valid: \(isValid)")
    }
}

var password = Password()
password.value = "short"      // Valid: false
password.value = "longpassword"  // Valid: true
```

**3. Synchronization:**

```swift
class SyncManager {
    var localData: [String] = [] {
        didSet {
            syncToServer()
        }
    }
    
    func syncToServer() {
        print("Syncing \(localData.count) items...")
    }
}
```

**4. Caching:**

```swift
class ImageCache {
    var images: [String: UIImage] = [:] {
        didSet {
            if images.count > 100 {
                // Evict oldest images
                print("Cache limit exceeded, evicting...")
            }
        }
    }
}
```

**Important Behaviors:**

**1. Init Doesn't Trigger Observers:**

```swift
class Test {
    var value: Int = 0 {
        didSet {
            print("Value changed")
        }
    }
    
    init(value: Int) {
        self.value = value  // No didSet called
    }
}

let test = Test(value: 42)  // No output
test.value = 43  // Output: Value changed
```

**2. Setting to Same Value Still Triggers:**

```swift
var count: Int = 5 {
    didSet {
        print("didSet called")
    }
}

count = 5  // Output: didSet called
// Even though value didn't change
```

**3. willSet Runs Before Change:**

```swift
var value: Int = 0 {
    willSet {
        print("Before: \(value)")    // Old value
        print("After:  \(newValue)")  // New value
    }
    didSet {
        print("Current: \(value)")    // New value
        print("Previous: \(oldValue)")  // Old value
    }
}

value = 42
// Output:
// Before: 0
// After:  42
// Current: 42
// Previous: 0
```

**Key Takeaway:** Use property observers for side effects when values change. Cannot be used with computed properties.

---

### Question 5: What are type properties (static/class)? When would you use them?

**Answer:**

**Type Properties** belong to the **type itself**, not to instances.

**Static vs Class:**

```swift
class Example {
    // Static: Cannot be overridden
    static var staticProperty = "Static"
    
    static func staticMethod() {
        print("Static method")
    }
    
    // Class: Can be overridden by subclasses
    class var classProperty: String {
        return "Class"
    }
    
    class func classMethod() {
        print("Class method")
    }
}

class Subclass: Example {
    // ❌ Can't override static
    // override static var staticProperty: String { "Overridden" }
    
    // ✅ Can override class
    override class var classProperty: String {
        return "Subclass"
    }
    
    override class func classMethod() {
        print("Subclass method")
    }
}

print(Example.staticProperty)     // "Static"
print(Example.classProperty)      // "Class"
print(Subclass.classProperty)     // "Subclass"
```

**When to Use Type Properties:**

**1. Constants:**

```swift
struct Math {
    static let pi = 3.14159265359
    static let e = 2.71828182846
    static let goldenRatio = 1.61803398875
}

let circumference = 2 * Math.pi * radius
```

**2. Singletons:**

```swift
class NetworkManager {
    static let shared = NetworkManager()
    
    private init() {
        // Private init prevents multiple instances
    }
    
    func makeRequest() {
        print("Making request...")
    }
}

NetworkManager.shared.makeRequest()
```

**3. Factory Methods:**

```swift
struct Color {
    let red: Double
    let green: Double
    let blue: Double
    
    // Type methods for common colors
    static var white: Color {
        return Color(red: 1, green: 1, blue: 1)
    }
    
    static var black: Color {
        return Color(red: 0, green: 0, blue: 0)
    }
    
    static func random() -> Color {
        return Color(
            red: Double.random(in: 0...1),
            green: Double.random(in: 0...1),
            blue: Double.random(in: 0...1)
        )
    }
}

let white = Color.white
let randomColor = Color.random()
```

**4. Configuration:**

```swift
class AppConfig {
    static var apiBaseURL = "https://api.example.com"
    static var timeout: TimeInterval = 30
    static var maxRetries = 3
}

// Access from anywhere
let url = "\(AppConfig.apiBaseURL)/users"
```

**5. Shared State (Counter):**

```swift
class RequestCounter {
    static var totalRequests = 0
    
    static func increment() {
        totalRequests += 1
        print("Total requests: \(totalRequests)")
    }
}

RequestCounter.increment()  // Total requests: 1
RequestCounter.increment()  // Total requests: 2
```

**Real-World Examples:**

**UserDefaults Keys:**

```swift
extension UserDefaults {
    enum Keys {
        static let username = "username"
        static let theme = "theme"
        static let notifications = "notifications"
    }
}

// Usage
UserDefaults.standard.set("Alice", forKey: UserDefaults.Keys.username)
```

**Notification Names:**

```swift
extension Notification.Name {
    static let userDidLogin = Notification.Name("userDidLogin")
    static let userDidLogout = Notification.Name("userDidLogout")
}

// Usage
NotificationCenter.default.post(name: .userDidLogin, object: nil)
```

**API Endpoints:**

```swift
struct API {
    static let baseURL = "https://api.example.com"
    
    enum Endpoints {
        static let login = "\(baseURL)/auth/login"
        static let register = "\(baseURL)/auth/register"
        static let profile = "\(baseURL)/user/profile"
    }
}

let loginURL = URL(string: API.Endpoints.login)
```

**Key Takeaway:** Use type properties for shared constants, singletons, and type-level functionality.

---

## 📝 Practice Exercises

1. Create a `Temperature` class with computed properties for Celsius, Fahrenheit, and Kelvin
2. Implement a `User` struct with property observers that validate email and username
3. Build a custom `@Validated` property wrapper that ensures strings meet certain criteria
4. Create a `Settings` class using `@UserDefault` property wrappers for persistence
5. Implement a lazy property that loads data from a file only when accessed

---

## 🔗 Navigation
- [← Previous: Step 12 - Structs vs Classes](../Step-12-Structs-vs-Classes/README.md)
- [→ Next: Step 14 - Methods](../Step-14-Methods/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 13!** You now understand:
- ✅ Stored properties and computed properties
- ✅ Property observers (willSet/didSet)
- ✅ Lazy properties and their benefits
- ✅ Type properties (static/class)
- ✅ Property wrappers (@State, @Published, custom)
- ✅ When to use each property type
- ✅ Real-world patterns and best practices

**Next:** Learn about **Methods** in Step 14!
