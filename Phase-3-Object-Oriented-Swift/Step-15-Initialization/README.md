# Step 15: Initialization

## 📚 Description

**Initialization** is the process of preparing an instance for use by setting initial values for stored properties. Swift provides powerful initialization features including **memberwise initializers**, **custom initializers**, **designated vs convenience initializers**, **failable initializers**, **required initializers**, and **two-phase initialization**. Understanding initialization is critical for proper object lifecycle management.

### What You'll Learn:
- Default and memberwise initializers
- Custom initializers
- Designated vs convenience initializers
- Failable initializers (init?)
- Required initializers
- Two-phase initialization
- Initialization inheritance and overriding

### Key Concepts:
- **Default Initializer**: Automatically provided for types with default values
- **Memberwise Initializer**: Auto-generated for structs
- **Designated Initializer**: Primary initializer
- **Convenience Initializer**: Secondary, delegates to designated
- **Failable Initializer**: Returns `nil` on failure
- **Required Initializer**: Must be implemented by subclasses

---

## 💡 Clear Examples

### Example 1: Default, Memberwise, and Custom Initializers

```swift
// ========== DEFAULT INITIALIZER ==========

struct Point {
    var x: Double = 0.0
    var y: Double = 0.0
}

// Default initializer automatically provided
let origin = Point()  // x: 0.0, y: 0.0
print(origin.x, origin.y)

// ========== MEMBERWISE INITIALIZER (STRUCTS ONLY) ==========

struct Rectangle {
    var width: Double
    var height: Double
    // Swift generates: init(width: Double, height: Double)
}

let rect = Rectangle(width: 10, height: 20)
print(rect.width, rect.height)  // 10.0, 20.0

// ========== CUSTOM INITIALIZER ==========

struct Circle {
    var radius: Double
    var color: String
    
    // Custom initializer
    init(radius: Double, color: String) {
        self.radius = radius
        self.color = color
    }
    
    // Convenience: Create circle from diameter
    init(diameter: Double, color: String) {
        self.radius = diameter / 2
        self.color = color
    }
}

let circle1 = Circle(radius: 5, color: "red")
let circle2 = Circle(diameter: 10, color: "blue")

print(circle1.radius)  // 5.0
print(circle2.radius)  // 5.0

// ========== LOSING MEMBERWISE INITIALIZER ==========

struct Size {
    var width: Double
    var height: Double
    
    // Custom init: Memberwise is now LOST
    init(side: Double) {
        self.width = side
        self.height = side
    }
}

let square = Size(side: 10)
// let rect = Size(width: 10, height: 20)  // ❌ Error: No memberwise init

// ========== KEEPING MEMBERWISE WITH EXTENSION ==========

struct BetterSize {
    var width: Double
    var height: Double
}

extension BetterSize {
    // Custom init in extension: Memberwise KEPT
    init(side: Double) {
        self.width = side
        self.height = side
    }
}

let square2 = BetterSize(side: 10)
let rect2 = BetterSize(width: 10, height: 20)  // ✅ Still available

// ========== DEFAULT PARAMETER VALUES ==========

struct User {
    var name: String
    var age: Int
    var isActive: Bool
    
    init(name: String, age: Int, isActive: Bool = true) {
        self.name = name
        self.age = age
        self.isActive = isActive
    }
}

let user1 = User(name: "Alice", age: 30)              // isActive defaults to true
let user2 = User(name: "Bob", age: 25, isActive: false)

// ========== COMPUTED PROPERTIES IN INIT ==========

struct Temperature {
    var celsius: Double
    
    // Can't compute during init
    var fahrenheit: Double {
        return celsius * 9/5 + 32
    }
    
    init(celsius: Double) {
        self.celsius = celsius
    }
    
    init(fahrenheit: Double) {
        self.celsius = (fahrenheit - 32) * 5/9
    }
}

let temp1 = Temperature(celsius: 25)
let temp2 = Temperature(fahrenheit: 77)
print(temp1.fahrenheit)  // 77.0
print(temp2.celsius)     // 25.0

// ========== VALIDATION IN INIT ==========

struct Password {
    var value: String
    
    init(value: String) {
        // Validation during initialization
        if value.count < 8 {
            print("⚠️  Warning: Weak password")
        }
        self.value = value
    }
}

let password = Password(value: "short")  // Warning printed

// ========== CLASSES NEED EXPLICIT INIT ==========

class Person {
    var name: String
    var age: Int
    
    // Must provide initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let person = Person(name: "Alice", age: 30)

// ========== OPTIONAL PROPERTIES ==========

struct Profile {
    var username: String
    var email: String
    var avatar: URL?  // Optional: no need to initialize
    
    init(username: String, email: String) {
        self.username = username
        self.email = email
        // avatar is nil by default
    }
}

let profile = Profile(username: "alice", email: "alice@example.com")
print(profile.avatar as Any)  // nil

// ========== CONSTANT PROPERTIES ==========

struct ImmutablePoint {
    let x: Double
    let y: Double
    
    init(x: Double, y: Double) {
        // Can set 'let' properties in init
        self.x = x
        self.y = y
        // After init, they're immutable
    }
}

let point = ImmutablePoint(x: 10, y: 20)
// point.x = 30  // ❌ Error: Cannot assign to 'let'
```

**Explanation:**
- **Default init**: Provided when all properties have defaults
- **Memberwise init**: Auto-generated for structs
- **Custom init**: Loses memberwise unless in extension
- Classes always need explicit initializers
- Optional properties don't need initialization

---

### Example 2: Designated vs Convenience Initializers

```swift
// ========== DESIGNATED INITIALIZERS ==========

class Vehicle {
    var numberOfWheels: Int
    var color: String
    
    // Designated initializer
    init(numberOfWheels: Int, color: String) {
        self.numberOfWheels = numberOfWheels
        self.color = color
    }
    
    // Convenience initializer
    convenience init(color: String) {
        // Must call designated init
        self.init(numberOfWheels: 4, color: color)
    }
    
    // Another convenience
    convenience init() {
        // Calls other convenience
        self.init(color: "white")
    }
}

let vehicle1 = Vehicle(numberOfWheels: 2, color: "red")
let vehicle2 = Vehicle(color: "blue")  // 4 wheels
let vehicle3 = Vehicle()               // 4 wheels, white

print(vehicle1.numberOfWheels)  // 2
print(vehicle2.numberOfWheels)  // 4
print(vehicle3.color)           // white

// ========== INITIALIZATION CHAIN ==========

class Bicycle: Vehicle {
    var hasBasket: Bool
    
    // Designated initializer
    init(color: String, hasBasket: Bool) {
        // Phase 1: Initialize own properties
        self.hasBasket = hasBasket
        
        // Phase 2: Call superclass designated init
        super.init(numberOfWheels: 2, color: color)
        
        // Phase 3: Can now modify inherited properties
        self.numberOfWheels = 2  // Already set, but can modify
    }
    
    // Convenience initializer
    convenience init(hasBasket: Bool) {
        // Must call designated init (self)
        self.init(color: "silver", hasBasket: hasBasket)
    }
}

let bike1 = Bicycle(color: "green", hasBasket: true)
let bike2 = Bicycle(hasBasket: false)

// ========== RULES VISUALIZATION ==========

/*
INITIALIZATION RULES:
1. Designated initializer must call designated initializer from superclass
2. Convenience initializer must call another initializer from same class
3. Convenience initializer must ultimately call designated initializer

Chain example:
convenience init() → convenience init(color:) → init(numberOfWheels:color:)
                                                           ↑
Bicycle.init(hasBasket:) → Bicycle.init(color:hasBasket:) ┘
*/

// ========== REAL-WORLD: VIEW CONTROLLER ==========

import UIKit

class CustomViewController: UIViewController {
    var viewModel: ViewModel
    
    // Designated initializer
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
        // Setup after super.init
        configure()
    }
    
    // Required by UIViewController
    required init?(coder: NSCoder) {
        // Provide default viewModel
        self.viewModel = ViewModel()
        super.init(coder: coder)
    }
    
    func configure() {
        // Additional setup
    }
}

class ViewModel { }

// ========== COMPLEX INITIALIZATION ==========

class NetworkManager {
    var baseURL: String
    var timeout: TimeInterval
    var headers: [String: String]
    
    // Designated: Full initialization
    init(baseURL: String, timeout: TimeInterval, headers: [String: String]) {
        self.baseURL = baseURL
        self.timeout = timeout
        self.headers = headers
    }
    
    // Convenience: Common case
    convenience init(baseURL: String) {
        self.init(baseURL: baseURL, timeout: 30, headers: [:])
    }
    
    // Convenience: With headers
    convenience init(baseURL: String, headers: [String: String]) {
        self.init(baseURL: baseURL, timeout: 30, headers: headers)
    }
}

let manager1 = NetworkManager(baseURL: "https://api.example.com",
                              timeout: 60,
                              headers: ["Auth": "token"])

let manager2 = NetworkManager(baseURL: "https://api.example.com")

// ========== AUTOMATIC INITIALIZER INHERITANCE ==========

class Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    convenience init() {
        self.init(name: "Unknown")
    }
}

class Dog: Animal {
    // No initializers defined
    // Inherits all initializers from Animal
}

let dog1 = Dog(name: "Buddy")  // Inherited
let dog2 = Dog()               // Inherited convenience

// ========== AUTOMATIC INHERITANCE RULES ==========

/*
Rule 1: If subclass doesn't define any designated initializers,
        it inherits all superclass designated initializers.

Rule 2: If subclass implements all superclass designated initializers
        (either inherited or custom), it inherits all superclass
        convenience initializers.
*/

class Cat: Animal {
    var breed: String
    
    // Implements superclass designated init
    init(name: String, breed: String) {
        self.breed = breed
        super.init(name: name)
    }
    
    // Override superclass designated init
    override init(name: String) {
        self.breed = "Unknown"
        super.init(name: name)
    }
    
    // Now inherits convenience init() from Animal
}

let cat1 = Cat(name: "Whiskers", breed: "Siamese")
let cat2 = Cat(name: "Fluffy")
let cat3 = Cat()  // Inherited convenience
```

**Explanation:**
- **Designated**: Primary initializer, does actual setup
- **Convenience**: Secondary, delegates to designated
- Must follow delegation rules (up for designated, across for convenience)
- Subclasses inherit initializers under specific conditions

---

### Example 3: Failable Initializers, Required Initializers, and Two-Phase Initialization

```swift
// ========== FAILABLE INITIALIZERS (init?) ==========

struct IPAddress {
    var value: String
    
    // Failable initializer
    init?(value: String) {
        // Validate IP address format
        let components = value.split(separator: ".")
        guard components.count == 4 else {
            return nil  // Initialization fails
        }
        
        for component in components {
            guard let number = Int(component), number >= 0 && number <= 255 else {
                return nil
            }
        }
        
        self.value = value
    }
}

if let ip = IPAddress(value: "192.168.1.1") {
    print("Valid IP: \(ip.value)")
} else {
    print("Invalid IP")
}

let invalidIP = IPAddress(value: "999.999.999.999")
print(invalidIP as Any)  // nil

// ========== FAILABLE ENUM INITIALIZATION ==========

enum TemperatureUnit {
    case celsius, fahrenheit, kelvin
    
    init?(symbol: String) {
        switch symbol {
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        case "K":
            self = .kelvin
        default:
            return nil
        }
    }
}

if let unit = TemperatureUnit(symbol: "C") {
    print(unit)  // celsius
}

print(TemperatureUnit(symbol: "X") as Any)  // nil

// ========== PROPAGATING FAILURE ==========

class Document {
    var content: String
    
    init?(path: String) {
        // Simulate file loading
        guard !path.isEmpty else {
            return nil
        }
        self.content = "File contents"
    }
}

class TextDocument: Document {
    var encoding: String
    
    init?(path: String, encoding: String) {
        self.encoding = encoding
        
        // Call superclass failable init
        super.init(path: path)
        
        // Can fail after super.init
        guard encoding == "UTF-8" else {
            return nil
        }
    }
}

let doc = TextDocument(path: "/file.txt", encoding: "UTF-8")
print(doc?.content as Any)  // Optional("File contents")

// ========== REQUIRED INITIALIZERS ==========

class SomeClass {
    required init() {
        // Must be implemented by subclasses
    }
}

class SubClass: SomeClass {
    var value: Int
    
    // Must implement required init
    required init() {
        self.value = 0
        super.init()
    }
    
    // Can have additional inits
    init(value: Int) {
        self.value = value
        super.init()
    }
}

// ========== REQUIRED WITH PROTOCOLS ==========

protocol Copyable {
    init(copying: Self)
}

class CopyableClass: Copyable {
    var value: Int
    
    init(value: Int) {
        self.value = value
    }
    
    // Required by protocol
    required init(copying other: CopyableClass) {
        self.value = other.value
    }
}

class SubCopyable: CopyableClass {
    var name: String
    
    init(value: Int, name: String) {
        self.name = name
        super.init(value: value)
    }
    
    // Must implement required init
    required init(copying other: CopyableClass) {
        if let other = other as? SubCopyable {
            self.name = other.name
        } else {
            self.name = ""
        }
        super.init(copying: other)
    }
}

// ========== TWO-PHASE INITIALIZATION ==========

class Food {
    var name: String
    
    init(name: String) {
        self.name = name
        print("Food initialized: \(name)")
    }
}

class RecipeIngredient: Food {
    var quantity: Int
    
    init(name: String, quantity: Int) {
        // PHASE 1: Initialize own properties
        self.quantity = quantity
        print("Phase 1: Initialized quantity")
        
        // Must call super.init before accessing inherited properties
        super.init(name: name)
        print("Phase 1 complete: Called super.init")
        
        // PHASE 2: Can now customize inherited properties
        self.name = "\(quantity)x \(name)"
        print("Phase 2: Modified inherited property")
    }
}

let ingredient = RecipeIngredient(name: "Eggs", quantity: 6)
print(ingredient.name)  // "6x Eggs"

/*
Output:
Phase 1: Initialized quantity
Food initialized: Eggs
Phase 1 complete: Called super.init
Phase 2: Modified inherited property
6x Eggs
*/

// ========== SAFETY CHECKS ==========

class SafetyExample {
    var property1: String
    var property2: Int
    
    init(property1: String, property2: Int) {
        // ✅ Phase 1: Initialize ALL properties first
        self.property1 = property1
        self.property2 = property2
        
        // ✅ Now can call methods
        self.doSomething()
    }
    
    func doSomething() {
        print("\(property1): \(property2)")
    }
}

class BrokenExample {
    var property: String
    
    init(property: String) {
        // ❌ Error: Cannot call methods before all properties initialized
        // self.doSomething()
        
        self.property = property
        
        // ✅ Now OK
        self.doSomething()
    }
    
    func doSomething() {
        print(property)
    }
}

// ========== DEINITIALIZATION ==========

class ResourceManager {
    var resource: String
    
    init(resource: String) {
        self.resource = resource
        print("✅ Acquired resource: \(resource)")
    }
    
    deinit {
        print("🗑  Released resource: \(resource)")
    }
}

func testDeinit() {
    let manager = ResourceManager(resource: "File Handle")
    // Use manager
}  // deinit called here

testDeinit()
/*
Output:
✅ Acquired resource: File Handle
🗑  Released resource: File Handle
*/

// ========== REAL-WORLD: USER INITIALIZATION ==========

struct User {
    let id: UUID
    var username: String
    var email: String
    var createdAt: Date
    
    // Primary initializer
    init(username: String, email: String) {
        self.id = UUID()
        self.username = username
        self.email = email
        self.createdAt = Date()
    }
    
    // Failable initializer with validation
    init?(username: String, email: String, validate: Bool) {
        guard validate else {
            return nil
        }
        
        guard username.count >= 3 else {
            print("Username too short")
            return nil
        }
        
        guard email.contains("@") else {
            print("Invalid email")
            return nil
        }
        
        self.id = UUID()
        self.username = username
        self.email = email
        self.createdAt = Date()
    }
}

let user1 = User(username: "alice", email: "alice@example.com")
print(user1.username)

if let user2 = User(username: "ab", email: "alice@example.com", validate: true) {
    print(user2.username)
} else {
    print("Failed to create user")  // Username too short
}

// ========== CODABLE INITIALIZATION ==========

import Foundation

struct Product: Codable {
    let id: Int
    let name: String
    let price: Double
    
    // Codable generates init(from:) and encode(to:)
}

let json = """
{
    "id": 1,
    "name": "iPhone",
    "price": 999.99
}
""".data(using: .utf8)!

let decoder = JSONDecoder()
if let product = try? decoder.decode(Product.self, from: json) {
    print(product.name)  // iPhone
}

// ========== MEMBERWISE WITH DEFAULTS ==========

struct Configuration {
    var apiURL: String = "https://api.example.com"
    var timeout: TimeInterval = 30
    var retries: Int = 3
    var enableLogging: Bool = false
}

// Can use memberwise with all or some parameters
let config1 = Configuration()
let config2 = Configuration(apiURL: "https://custom.api.com")
let config3 = Configuration(timeout: 60, retries: 5)
let config4 = Configuration(apiURL: "https://api.com", 
                           timeout: 45, 
                           retries: 2, 
                           enableLogging: true)
```

**Explanation:**
- **Failable init**: Returns `nil` if initialization fails
- **Required init**: Must be implemented by subclasses
- **Two-phase init**: First initialize own properties, then call super, then customize
- Safety checks prevent using `self` before full initialization
- `deinit` for cleanup when instance is deallocated

---

## 🎯 2025 Interview Questions

### Question 1: Explain the difference between designated and convenience initializers.

**Answer:**

**Designated Initializers:**
- **Primary initializers** for a class
- Fully initialize all properties
- Call designated initializer of superclass

**Convenience Initializers:**
- **Secondary initializers**
- Provide shortcuts or default values
- Must call designated initializer from **same class**

**Example:**

```swift
class Person {
    var name: String
    var age: Int
    
    // Designated initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // Convenience initializer
    convenience init(name: String) {
        // Must call designated init
        self.init(name: name, age: 0)
    }
    
    // Another convenience
    convenience init() {
        self.init(name: "Unknown")
        // Calls convenience which calls designated
    }
}

let person1 = Person(name: "Alice", age: 30)  // Designated
let person2 = Person(name: "Bob")             // Convenience
let person3 = Person()                        // Convenience
```

**Delegation Rules:**

```swift
/*
1. Designated → Designated (Up)
   Subclass designated must call superclass designated

2. Convenience → Designated (Across)
   Convenience must call designated from same class

3. Convenience → Convenience (Across)
   Convenience can call another convenience from same class
*/

class Vehicle {
    var wheels: Int
    
    // Designated
    init(wheels: Int) {
        self.wheels = wheels
    }
    
    // Convenience
    convenience init() {
        self.init(wheels: 4)  // Calls designated
    }
}

class Bicycle: Vehicle {
    var hasBasket: Bool
    
    // Designated
    init(hasBasket: Bool) {
        self.hasBasket = hasBasket
        super.init(wheels: 2)  // Must call superclass designated
    }
    
    // Convenience
    convenience init() {
        self.init(hasBasket: false)  // Calls designated from same class
    }
}
```

**Visual Delegation:**

```
Convenience init() → Convenience init(name:) → Designated init(name:age:)
                                                        ↓
Subclass Convenience init() → Subclass Designated init → Superclass Designated init
```

**Real-World Example:**

```swift
class NetworkRequest {
    var url: URL
    var method: String
    var headers: [String: String]
    
    // Designated: Full control
    init(url: URL, method: String, headers: [String: String]) {
        self.url = url
        self.method = method
        self.headers = headers
    }
    
    // Convenience: GET request
    convenience init(url: URL) {
        self.init(url: url, method: "GET", headers: [:])
    }
    
    // Convenience: POST with content type
    convenience init(url: URL, json: Bool) {
        let headers = json ? ["Content-Type": "application/json"] : [:]
        self.init(url: url, method: "POST", headers: headers)
    }
}

let url = URL(string: "https://api.example.com")!
let req1 = NetworkRequest(url: url, method: "PUT", headers: ["Auth": "token"])
let req2 = NetworkRequest(url: url)  // GET
let req3 = NetworkRequest(url: url, json: true)  // POST with JSON
```

**Key Takeaway:** Designated = primary, full init. Convenience = secondary, delegates to designated.

---

### Question 2: What is two-phase initialization? Why is it important?

**Answer:**

**Two-Phase Initialization** ensures instances are fully initialized before being used, preventing access to uninitialized properties.

**Phase 1: Bottom-Up**
- Initialize **own properties** first
- Call **superclass designated init**
- Propagates up inheritance chain

**Phase 2: Top-Down**
- Can **customize** inherited properties
- Can call **instance methods**
- Propagates down inheritance chain

**Example:**

```swift
class Food {
    var name: String
    
    init(name: String) {
        // Phase 1: Initialize own properties
        self.name = name
        print("Food.init: Phase 1 complete")
        
        // Phase 2: Can customize/call methods
        self.printInfo()
    }
    
    func printInfo() {
        print("Food: \(name)")
    }
}

class Ingredient: Food {
    var quantity: Int
    
    init(name: String, quantity: Int) {
        // PHASE 1: Bottom-Up
        print("Ingredient.init: Phase 1 start")
        
        // 1a. Initialize own properties FIRST
        self.quantity = quantity
        print("Ingredient.init: quantity initialized")
        
        // ❌ Cannot call methods yet
        // self.printInfo()  // Error: self used before super.init
        
        // 1b. Call superclass init
        super.init(name: name)
        print("Ingredient.init: super.init called")
        
        // PHASE 2: Top-Down
        print("Ingredient.init: Phase 2 start")
        
        // ✅ Now can customize inherited properties
        self.name = "\(quantity)x \(name)"
        
        // ✅ Now can call methods
        self.printInfo()
        
        print("Ingredient.init: Phase 2 complete")
    }
    
    override func printInfo() {
        print("Ingredient: \(quantity)x \(name)")
    }
}

let ingredient = Ingredient(name: "Eggs", quantity: 6)

/*
Output:
Ingredient.init: Phase 1 start
Ingredient.init: quantity initialized
Food.init: Phase 1 complete
Food: Eggs
Ingredient.init: super.init called
Ingredient.init: Phase 2 start
Ingredient: 6x 6x Eggs
Ingredient.init: Phase 2 complete
*/
```

**Safety Checks:**

**Check 1: Initialize Before Delegating Up**

```swift
class Child: Parent {
    var childProperty: String
    
    init(parentProperty: Int, childProperty: String) {
        // ❌ Error: Must initialize childProperty first
        // super.init(parentProperty: parentProperty)
        
        // ✅ Correct: Initialize own properties first
        self.childProperty = childProperty
        super.init(parentProperty: parentProperty)
    }
}

class Parent {
    var parentProperty: Int
    
    init(parentProperty: Int) {
        self.parentProperty = parentProperty
    }
}
```

**Check 2: Can't Use `self` Before Super.init**

```swift
class Example: Parent {
    var value: String
    
    init(value: String) {
        self.value = value
        
        // ❌ Error: Cannot use self before super.init
        // self.doSomething()
        
        super.init(parentProperty: 0)
        
        // ✅ Now OK
        self.doSomething()
    }
    
    func doSomething() {
        print(value)
    }
}
```

**Check 3: Can't Modify Inherited Properties Before Super.init**

```swift
class SubClass: SuperClass {
    var subProperty: String
    
    init(subProperty: String, superProperty: Int) {
        self.subProperty = subProperty
        
        // ❌ Error: Cannot modify inherited property before super.init
        // self.superProperty = superProperty
        
        super.init(superProperty: superProperty)
        
        // ✅ Now OK (Phase 2)
        self.superProperty = 999
    }
}

class SuperClass {
    var superProperty: Int
    
    init(superProperty: Int) {
        self.superProperty = superProperty
    }
}
```

**Why Important:**

**1. Memory Safety:**
- Prevents accessing uninitialized memory
- All properties have values before use

**2. Inheritance Safety:**
- Subclass can't accidentally use superclass properties before they're initialized

**3. Method Safety:**
- Can't call methods until fully initialized

**Real-World Example:**

```swift
class UIViewController {
    var view: UIView
    
    init() {
        // Phase 1: Initialize view
        self.view = UIView()
        
        // Phase 2: Can configure
        configureView()
    }
    
    func configureView() {
        view.backgroundColor = .white
    }
}

class CustomViewController: UIViewController {
    var label: UILabel
    
    override init() {
        // Phase 1: Initialize own properties
        self.label = UILabel()
        
        // Call super
        super.init()
        
        // Phase 2: Add to view (view is now initialized)
        view.addSubview(label)
    }
}
```

**Key Takeaway:** Two-phase initialization ensures safety by requiring properties initialized before use.

---

### Question 3: What are failable initializers? Provide a real-world example.

**Answer:**

**Failable Initializers** (`init?`) can return `nil` if initialization cannot succeed.

**Syntax:**

```swift
struct Example {
    var value: Int
    
    init?(value: Int) {
        guard value >= 0 else {
            return nil  // Initialization fails
        }
        self.value = value
    }
}

if let example = Example(value: 10) {
    print(example.value)  // 10
} else {
    print("Failed")
}

print(Example(value: -5) as Any)  // nil
```

**Real-World Example 1: URL Parsing**

```swift
struct SafeURL {
    let url: URL
    
    init?(string: String) {
        guard let url = URL(string: string) else {
            return nil  // Invalid URL
        }
        self.url = url
    }
}

if let safeURL = SafeURL(string: "https://example.com") {
    print(safeURL.url)
} else {
    print("Invalid URL")
}

print(SafeURL(string: "not a url") as Any)  // nil
```

**Real-World Example 2: Email Validation**

```swift
struct Email {
    let address: String
    
    init?(address: String) {
        // Validate email format
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let predicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        
        guard predicate.evaluate(with: address) else {
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

print(Email(address: "not-an-email") as Any)  // nil
```

**Real-World Example 3: Image Loading**

```swift
import UIKit

struct AppIcon {
    let image: UIImage
    
    init?(named: String) {
        guard let image = UIImage(named: named) else {
            print("⚠️  Image not found: \(named)")
            return nil
        }
        self.image = image
    }
}

if let icon = AppIcon(named: "app-icon") {
    // Use icon.image
    print("Icon loaded")
} else {
    print("Using default icon")
}
```

**Real-World Example 4: JSON Parsing**

```swift
import Foundation

struct User {
    let id: Int
    let name: String
    let email: String
    
    init?(json: [String: Any]) {
        guard let id = json["id"] as? Int,
              let name = json["name"] as? String,
              let email = json["email"] as? String else {
            print("Invalid JSON structure")
            return nil
        }
        
        self.id = id
        self.name = name
        self.email = email
    }
}

let validJSON: [String: Any] = [
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
]

let invalidJSON: [String: Any] = [
    "id": "not a number",  // Wrong type
    "name": "Bob"
]

if let user = User(json: validJSON) {
    print("User: \(user.name)")  // User: Alice
}

print(User(json: invalidJSON) as Any)  // nil
```

**Real-World Example 5: Date Parsing**

```swift
struct CustomDate {
    let date: Date
    
    init?(string: String, format: String = "yyyy-MM-dd") {
        let formatter = DateFormatter()
        formatter.dateFormat = format
        
        guard let date = formatter.date(from: string) else {
            return nil
        }
        
        self.date = date
    }
}

if let customDate = CustomDate(string: "2025-01-15") {
    print(customDate.date)
}

print(CustomDate(string: "invalid-date") as Any)  // nil
```

**Propagating Failure:**

```swift
class Document {
    var title: String
    
    init?(title: String) {
        guard !title.isEmpty else {
            return nil
        }
        self.title = title
    }
}

class PDFDocument: Document {
    var pageCount: Int
    
    init?(title: String, pageCount: Int) {
        guard pageCount > 0 else {
            return nil  // Fail before super.init
        }
        
        self.pageCount = pageCount
        
        super.init(title: title)  // Can fail
        
        // Can also fail after super.init
        guard pageCount <= 1000 else {
            return nil  // Too many pages
        }
    }
}

let pdf1 = PDFDocument(title: "Report", pageCount: 10)
print(pdf1?.title as Any)  // Optional("Report")

let pdf2 = PDFDocument(title: "", pageCount: 10)
print(pdf2 as Any)  // nil (empty title)

let pdf3 = PDFDocument(title: "Huge", pageCount: 0)
print(pdf3 as Any)  // nil (invalid page count)
```

**Init! (Implicitly Unwrapped)**

```swift
struct ImplicitlyUnwrappedExample {
    var value: String
    
    init!(value: String?) {
        guard let value = value else {
            return nil
        }
        self.value = value
    }
}

let example = ImplicitlyUnwrappedExample(value: "Hello")
print(example.value)  // No optional binding needed

// let failed = ImplicitlyUnwrappedExample(value: nil)  // Runtime crash
```

**Key Takeaway:** Use failable initializers when initialization can logically fail. Safer than force unwrapping or assertions.

---

### Question 4: When do subclasses inherit initializers from their superclass?

**Answer:**

Swift has **automatic initializer inheritance** rules that determine when subclasses inherit superclass initializers.

**Rule 1: No Designated Initializers Defined**

If subclass doesn't define **any** designated initializers, it inherits **all** designated initializers from superclass.

```swift
class Animal {
    var name: String
    
    // Designated initializer
    init(name: String) {
        self.name = name
    }
    
    // Convenience initializer
    convenience init() {
        self.init(name: "Unknown")
    }
}

class Dog: Animal {
    // No designated initializers defined
    // → Inherits all initializers from Animal
}

let dog1 = Dog(name: "Buddy")  // ✅ Inherited designated
let dog2 = Dog()               // ✅ Inherited convenience

print(dog1.name)  // Buddy
print(dog2.name)  // Unknown
```

**Rule 2: Override All Designated Initializers**

If subclass **overrides** all superclass designated initializers, it inherits all superclass convenience initializers.

```swift
class Vehicle {
    var wheels: Int
    
    // Designated
    init(wheels: Int) {
        self.wheels = wheels
    }
    
    // Convenience
    convenience init() {
        self.init(wheels: 4)
    }
}

class Car: Vehicle {
    var brand: String
    
    // Override superclass designated init
    override init(wheels: Int) {
        self.brand = "Unknown"
        super.init(wheels: wheels)
    }
    
    // New designated init
    init(brand: String, wheels: Int) {
        self.brand = brand
        super.init(wheels: wheels)
    }
    
    // Because we override all designated inits,
    // we inherit convenience init()
}

let car1 = Car(wheels: 4)                // Overridden designated
let car2 = Car(brand: "Tesla", wheels: 4) // New designated
let car3 = Car()                         // ✅ Inherited convenience

print(car3.wheels)  // 4 (from convenience init)
```

**Example: No Inheritance**

```swift
class Base {
    var value: Int
    
    init(value: Int) {
        self.value = value
    }
    
    convenience init() {
        self.init(value: 0)
    }
}

class Derived: Base {
    var name: String
    
    // New designated init (doesn't override Base.init)
    init(name: String, value: Int) {
        self.name = name
        super.init(value: value)
    }
    
    // ❌ Does NOT inherit Base.init(value:)
    // ❌ Does NOT inherit Base.init()
}

let derived1 = Derived(name: "Test", value: 10)  // ✅ New init
// let derived2 = Derived(value: 10)  // ❌ Error: not inherited
// let derived3 = Derived()           // ❌ Error: not inherited
```

**Example: Inherit With Override**

```swift
class Person {
    var name: String
    var age: Int
    
    // Designated
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // Convenience
    convenience init(name: String) {
        self.init(name: name, age: 0)
    }
}

class Employee: Person {
    var employeeID: String
    
    // Override designated init
    override init(name: String, age: Int) {
        self.employeeID = UUID().uuidString
        super.init(name: name, age: age)
    }
    
    // Inherits convenience init(name:)
}

let emp1 = Employee(name: "Alice", age: 30)  // Overridden designated
let emp2 = Employee(name: "Bob")             // ✅ Inherited convenience

print(emp2.age)  // 0 (from convenience)
```

**Example: Default Property Values**

```swift
class Item {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class PriceItem: Item {
    var price: Double = 0.0  // Default value
    
    // No designated inits defined
    // All properties have defaults or inherited
    // → Inherits all initializers
}

let item = PriceItem(name: "Widget")
print(item.name, item.price)  // Widget 0.0
```

**Summary Table:**

| Scenario | Inherits Designated? | Inherits Convenience? |
|----------|---------------------|----------------------|
| No designated inits defined | ✅ All | ✅ All |
| Override ALL designated | ❌ No | ✅ All |
| Define NEW designated (no override) | ❌ No | ❌ No |
| Override SOME designated | ❌ No | ❌ No |

**Key Takeaway:** Subclasses inherit initializers only under specific conditions to ensure safety.

---

### Question 5: What is a required initializer? When would you use it?

**Answer:**

**Required Initializers** must be implemented by every subclass.

**Syntax:**

```swift
class SomeClass {
    required init() {
        // Implementation
    }
}

class SubClass: SomeClass {
    var value: Int
    
    // Must implement required init
    required init() {
        self.value = 0
        super.init()
    }
}
```

**When to Use:**

**1. Protocol Requirements:**

```swift
protocol Copyable {
    init(copying: Self)
}

class CopyableClass: Copyable {
    var value: Int
    
    init(value: Int) {
        self.value = value
    }
    
    // Required by protocol
    required init(copying other: CopyableClass) {
        self.value = other.value
    }
}

class SubCopyable: CopyableClass {
    var name: String
    
    init(value: Int, name: String) {
        self.name = name
        super.init(value: value)
    }
    
    // Must implement required init
    required init(copying other: CopyableClass) {
        if let other = other as? SubCopyable {
            self.name = other.name
        } else {
            self.name = ""
        }
        super.init(copying: other)
    }
}

let original = SubCopyable(value: 42, name: "Test")
let copy = SubCopyable(copying: original)
print(copy.name)  // Test
```

**2. NSCoding (UIKit):**

```swift
import UIKit

class CustomView: UIView {
    var customProperty: String = ""
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setup()
    }
    
    // Required by NSCoding (for Interface Builder)
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        setup()
    }
    
    func setup() {
        backgroundColor = .blue
    }
}

class SpecialView: CustomView {
    // Must implement required init
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        // Additional setup
    }
    
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
}
```

**3. Factory Pattern:**

```swift
protocol Creatable {
    init(configuration: [String: Any])
}

class BaseWidget: Creatable {
    var config: [String: Any]
    
    required init(configuration: [String: Any]) {
        self.config = configuration
    }
}

class Button: BaseWidget {
    var title: String
    
    required init(configuration: [String: Any]) {
        self.title = configuration["title"] as? String ?? "Button"
        super.init(configuration: configuration)
    }
}

// Factory function
func createWidget<T: BaseWidget>(type: T.Type, config: [String: Any]) -> T {
    return T(configuration: config)
}

let button = createWidget(type: Button.self, config: ["title": "Click Me"])
print(button.title)  // Click Me
```

**4. JSON Deserialization:**

```swift
protocol JSONDecodable {
    init?(json: [String: Any])
}

class User: JSONDecodable {
    var name: String
    var email: String
    
    required init?(json: [String: Any]) {
        guard let name = json["name"] as? String,
              let email = json["email"] as? String else {
            return nil
        }
        self.name = name
        self.email = email
    }
}

class Admin: User {
    var permissions: [String]
    
    required init?(json: [String: Any]) {
        guard let permissions = json["permissions"] as? [String] else {
            return nil
        }
        self.permissions = permissions
        super.init(json: json)
    }
}

let adminJSON: [String: Any] = [
    "name": "Alice",
    "email": "alice@example.com",
    "permissions": ["read", "write", "delete"]
]

if let admin = Admin(json: adminJSON) {
    print(admin.permissions)  // ["read", "write", "delete"]
}
```

**Required with Override:**

```swift
class Base {
    required init() {
        // Base implementation
    }
}

class Middle: Base {
    override required init() {
        // Both 'override' and 'required'
        super.init()
    }
}

class Final: Middle {
    required init() {
        // Just 'required' (not override in this context)
        super.init()
    }
}
```

**Key Takeaway:** Use `required` when all subclasses must provide a specific initializer, commonly for protocols and frameworks.

---

## 📝 Practice Exercises

1. Create a `Temperature` struct with multiple initializers (Celsius, Fahrenheit, Kelvin)
2. Implement a failable initializer for a `CreditCard` struct that validates card numbers
3. Build a class hierarchy with designated and convenience initializers
4. Create a protocol with a required initializer and conform multiple types to it
5. Implement two-phase initialization in a complex class hierarchy

---

## 🔗 Navigation
- [← Previous: Step 14 - Methods](../Step-14-Methods/README.md)
- [→ Next: Phase 4 - Advanced Swift Concepts](../../Phase-4-Advanced-Swift-Concepts/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**🎉 Congratulations! You've completed Phase 3: Object-Oriented Swift!**

You now understand:
- ✅ Enumerations (Step 9)
- ✅ Structures (Step 10)
- ✅ Classes (Step 11)
- ✅ Structs vs Classes (Step 12) ⭐
- ✅ Properties (Step 13)
- ✅ Methods (Step 14)
- ✅ Initialization (Step 15)

**Phase 3 Complete!** You're now ready for **Advanced Swift Concepts** in Phase 4!
