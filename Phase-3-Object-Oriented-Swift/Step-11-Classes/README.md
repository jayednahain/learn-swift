# Step 11: Classes

## 📚 Description

Classes are **reference types** that allow you to create complex, object-oriented structures with inheritance, shared state, and identity. Unlike structs (value types), classes are passed by reference, enabling multiple variables to point to the same instance.

### What You'll Learn:
- Class syntax and definition
- Reference semantics (vs value semantics)
- Inheritance and method overriding
- The `super` keyword
- Identity operators (`===`, `!==`)
- Deinitializers (`deinit`)
- Final classes (preventing inheritance)

### Key Concepts:
- **Reference Types**: Classes are stored in heap, passed by reference
- **Inheritance**: Classes can inherit from other classes
- **Identity**: Two instances can have same data but different identity
- **Deinitializers**: Cleanup code when instance is deallocated
- **ARC**: Automatic Reference Counting manages memory

---

## 💡 Clear Examples

### Example 1: Basic Class Syntax and Reference Semantics

```swift
// BASIC CLASS DEFINITION
class Person {
    var name: String
    var age: Int
    
    // Classes require explicit initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func introduce() {
        print("Hi, I'm \(name), \(age) years old")
    }
}

// Create instance
let person1 = Person(name: "Alice", age: 30)
person1.introduce()  // Hi, I'm Alice, 30 years old

// REFERENCE SEMANTICS
var person2 = person1  // Both reference SAME instance
person2.name = "Alice Smith"

print(person1.name)  // Alice Smith (changed!)
print(person2.name)  // Alice Smith (same instance)

// Compare with struct (value semantics)
struct PersonStruct {
    var name: String
    var age: Int
}

var struct1 = PersonStruct(name: "Bob", age: 25)
var struct2 = struct1  // Creates COPY
struct2.name = "Bob Jones"

print(struct1.name)  // Bob (unchanged)
print(struct2.name)  // Bob Jones (independent copy)

// IDENTITY OPERATORS (=== and !==)
// Check if two references point to SAME instance

let person3 = Person(name: "Charlie", age: 35)
let person4 = person3  // Same instance
let person5 = Person(name: "Charlie", age: 35)  // Different instance

print(person3 === person4)  // true (same instance)
print(person3 === person5)  // false (different instances)
print(person3 !== person5)  // true (not same instance)

// Equality (==) vs Identity (===)
// == compares VALUES (requires Equatable protocol)
// === compares REFERENCES (built-in for classes)

// PROPERTIES IN CLASSES
class Rectangle {
    var width: Double
    var height: Double
    
    // Computed property
    var area: Double {
        return width * height
    }
    
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
}

let rect = Rectangle(width: 10, height: 20)
print(rect.area)  // 200.0

rect.width = 15  // Can modify even with 'let'
print(rect.area)  // 300.0

// 'let' prevents reassignment, not property changes
// rect = Rectangle(width: 5, height: 5)  // ❌ Error

// METHODS IN CLASSES
class BankAccount {
    var balance: Double
    let accountNumber: String
    
    init(accountNumber: String, balance: Double = 0) {
        self.accountNumber = accountNumber
        self.balance = balance
    }
    
    func deposit(_ amount: Double) {
        balance += amount
        print("Deposited $\(amount). New balance: $\(balance)")
    }
    
    func withdraw(_ amount: Double) -> Bool {
        if balance >= amount {
            balance -= amount
            print("Withdrew $\(amount). New balance: $\(balance)")
            return true
        } else {
            print("Insufficient funds")
            return false
        }
    }
    
    func transfer(to account: BankAccount, amount: Double) -> Bool {
        if withdraw(amount) {
            account.deposit(amount)
            return true
        }
        return false
    }
}

let account1 = BankAccount(accountNumber: "123", balance: 1000)
let account2 = BankAccount(accountNumber: "456", balance: 500)

account1.transfer(to: account2, amount: 300)
// Withdrew $300.0. New balance: $700.0
// Deposited $300.0. New balance: $800.0

// SHARED STATE (KEY DIFFERENCE FROM STRUCTS)
class Counter {
    var count = 0
    
    func increment() {
        count += 1
    }
}

let sharedCounter = Counter()

func incrementGlobal() {
    sharedCounter.increment()
}

sharedCounter.increment()
print(sharedCounter.count)  // 1

incrementGlobal()
print(sharedCounter.count)  // 2

// Same instance modified from different places

// iOS EXAMPLE: SINGLETON PATTERN
class NetworkManager {
    static let shared = NetworkManager()  // Singleton
    
    private init() {
        // Private init prevents creating multiple instances
    }
    
    var isConnected = false
    
    func fetchData() {
        print("Fetching data... (connected: \(isConnected))")
    }
}

// Use singleton
NetworkManager.shared.isConnected = true
NetworkManager.shared.fetchData()

// Can't create new instance:
// let manager = NetworkManager()  // ❌ Error: init is private

// CONSTANT CLASS INSTANCES
class MutableData {
    var value: Int
    
    init(value: Int) {
        self.value = value
    }
}

let data = MutableData(value: 10)
data.value = 20  // ✅ Allowed! 'let' only prevents reassignment

print(data.value)  // 20

// data = MutableData(value: 30)  // ❌ Error: can't reassign
```

**Explanation:**
- Classes use reference semantics (passed by reference)
- Multiple variables can reference the same instance
- Identity operators (`===`, `!==`) check if references point to same instance
- `let` constant prevents reassignment but allows property changes
- Singletons ensure only one instance exists

---

### Example 2: Inheritance and Method Overriding

```swift
// BASE CLASS (SUPERCLASS)
class Vehicle {
    var brand: String
    var year: Int
    
    init(brand: String, year: Int) {
        self.brand = brand
        self.year = year
    }
    
    func start() {
        print("\(brand) is starting...")
    }
    
    func stop() {
        print("\(brand) stopped")
    }
    
    func description() -> String {
        return "\(year) \(brand)"
    }
}

// DERIVED CLASS (SUBCLASS)
class Car: Vehicle {
    var numberOfDoors: Int
    
    // Initialize subclass-specific properties + call super.init
    init(brand: String, year: Int, numberOfDoors: Int) {
        self.numberOfDoors = numberOfDoors
        super.init(brand: brand, year: year)  // Must call super
    }
    
    // Override method
    override func start() {
        super.start()  // Call parent implementation
        print("Car engine started")
    }
    
    // Add new method
    func openTrunk() {
        print("Trunk opened")
    }
}

let vehicle = Vehicle(brand: "Generic", year: 2020)
vehicle.start()
// Generic is starting...

let car = Car(brand: "Toyota", year: 2023, numberOfDoors: 4)
car.start()
// Toyota is starting...
// Car engine started

car.openTrunk()  // Trunk opened

// MULTIPLE LEVELS OF INHERITANCE
class ElectricCar: Car {
    var batteryCapacity: Double
    
    init(brand: String, year: Int, numberOfDoors: Int, batteryCapacity: Double) {
        self.batteryCapacity = batteryCapacity
        super.init(brand: brand, year: year, numberOfDoors: numberOfDoors)
    }
    
    override func start() {
        print("Electric motor activating...")
        print("Battery: \(batteryCapacity)%")
    }
    
    func charge() {
        print("Charging battery...")
        batteryCapacity = 100
    }
}

let tesla = ElectricCar(brand: "Tesla", year: 2024, numberOfDoors: 4, batteryCapacity: 85)
tesla.start()
// Electric motor activating...
// Battery: 85.0%

tesla.charge()

// OVERRIDING PROPERTIES
class Animal {
    var name: String
    
    // Computed property
    var description: String {
        return "An animal named \(name)"
    }
    
    init(name: String) {
        self.name = name
    }
    
    func makeSound() {
        print("Some generic sound")
    }
}

class Dog: Animal {
    var breed: String
    
    // Override computed property
    override var description: String {
        return "A \(breed) dog named \(name)"
    }
    
    init(name: String, breed: String) {
        self.breed = breed
        super.init(name: name)
    }
    
    override func makeSound() {
        print("Woof! Woof!")
    }
    
    func fetch() {
        print("\(name) is fetching")
    }
}

let animal = Animal(name: "Generic")
print(animal.description)  // An animal named Generic
animal.makeSound()  // Some generic sound

let dog = Dog(name: "Buddy", breed: "Golden Retriever")
print(dog.description)  // A Golden Retriever dog named Buddy
dog.makeSound()  // Woof! Woof!
dog.fetch()  // Buddy is fetching

// PREVENTING OVERRIDES WITH 'final'
class FinalVehicle {
    final func start() {  // Can't be overridden
        print("Starting...")
    }
    
    func stop() {
        print("Stopping...")
    }
}

// Can't override final method:
// class FinalCar: FinalVehicle {
//     override func start() { }  // ❌ Error: can't override final
// }

// FINAL CLASS (can't be subclassed)
final class UtilityClass {
    static func calculate() -> Int {
        return 42
    }
}

// Can't subclass:
// class SubClass: UtilityClass { }  // ❌ Error: can't subclass final class

// TYPE CHECKING AND CASTING
let vehicles: [Vehicle] = [
    Vehicle(brand: "Generic", year: 2020),
    Car(brand: "Honda", year: 2021, numberOfDoors: 4),
    ElectricCar(brand: "Tesla", year: 2024, numberOfDoors: 4, batteryCapacity: 90)
]

for vehicle in vehicles {
    // Type checking
    if vehicle is ElectricCar {
        print("\(vehicle.brand) is an electric car")
    } else if vehicle is Car {
        print("\(vehicle.brand) is a car")
    } else {
        print("\(vehicle.brand) is a vehicle")
    }
}

// Type casting
for vehicle in vehicles {
    if let electricCar = vehicle as? ElectricCar {
        print("\(electricCar.brand) - Battery: \(electricCar.batteryCapacity)%")
    } else if let car = vehicle as? Car {
        print("\(car.brand) - Doors: \(car.numberOfDoors)")
    }
}

// iOS EXAMPLE: UIViewController Hierarchy
class BaseViewController {
    var title: String?
    
    init(title: String? = nil) {
        self.title = title
    }
    
    func viewDidLoad() {
        print("View loaded: \(title ?? "No title")")
    }
    
    func viewWillAppear() {
        print("View will appear")
    }
}

class ProfileViewController: BaseViewController {
    var userId: String
    
    init(userId: String) {
        self.userId = userId
        super.init(title: "Profile")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        print("Loading profile for user: \(userId)")
    }
    
    func loadUserData() {
        print("Fetching user data...")
    }
}

let profileVC = ProfileViewController(userId: "user123")
profileVC.viewDidLoad()
// View loaded: Profile
// Loading profile for user: user123
```

**Explanation:**
- Inheritance creates "is-a" relationships (Car is-a Vehicle)
- Subclasses inherit properties and methods from superclass
- `override` keyword required to override methods/properties
- `super` accesses parent class implementation
- `final` prevents overriding or subclassing
- Type checking (`is`) and casting (`as?`, `as!`)

---

### Example 3: Deinitializers and Memory Management

```swift
// DEINITIALIZERS
// Called automatically when instance is deallocated

class FileHandler {
    let filename: String
    
    init(filename: String) {
        self.filename = filename
        print("📄 Opening file: \(filename)")
    }
    
    deinit {
        print("🗑️  Closing file: \(filename)")
        // Cleanup code: close file handles, release resources
    }
    
    func writeData(_ data: String) {
        print("Writing to \(filename): \(data)")
    }
}

func processFile() {
    let file = FileHandler(filename: "data.txt")
    file.writeData("Hello, World!")
    // file deallocates when function ends
}

processFile()
// 📄 Opening file: data.txt
// Writing to data.txt: Hello, World!
// 🗑️  Closing file: data.txt

// REFERENCE COUNTING
class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("👤 \(name) is initialized")
    }
    
    deinit {
        print("💀 \(name) is deinitialized")
    }
}

var reference1: Person?
var reference2: Person?
var reference3: Person?

reference1 = Person(name: "Alice")
// 👤 Alice is initialized

reference2 = reference1  // Reference count: 2
reference3 = reference1  // Reference count: 3

reference1 = nil  // Reference count: 2
reference2 = nil  // Reference count: 1
reference3 = nil  // Reference count: 0 → deallocates
// 💀 Alice is deinitialized

// STRONG REFERENCE CYCLES (MEMORY LEAKS)
class Apartment {
    let unit: String
    var tenant: Person2?
    
    init(unit: String) {
        self.unit = unit
        print("🏠 Apartment \(unit) allocated")
    }
    
    deinit {
        print("🏚️  Apartment \(unit) deallocated")
    }
}

class Person2 {
    let name: String
    var apartment: Apartment?
    
    init(name: String) {
        self.name = name
        print("👤 \(name) allocated")
    }
    
    deinit {
        print("💀 \(name) deallocated")
    }
}

var alice: Person2?
var apt: Apartment?

alice = Person2(name: "Alice")
apt = Apartment(unit: "4A")

// Create strong reference cycle
alice!.apartment = apt  // Person → Apartment
apt!.tenant = alice     // Apartment → Person

alice = nil
apt = nil
// ❌ Neither deallocates! Memory leak!

// BREAKING CYCLES WITH WEAK REFERENCES
class Apartment2 {
    let unit: String
    weak var tenant: Person3?  // weak breaks cycle
    
    init(unit: String) {
        self.unit = unit
        print("🏠 Apartment \(unit) allocated")
    }
    
    deinit {
        print("🏚️  Apartment \(unit) deallocated")
    }
}

class Person3 {
    let name: String
    var apartment: Apartment2?
    
    init(name: String) {
        self.name = name
        print("👤 \(name) allocated")
    }
    
    deinit {
        print("💀 \(name) deallocated")
    }
}

var bob: Person3?
var apt2: Apartment2?

bob = Person3(name: "Bob")
apt2 = Apartment2(unit: "5B")

bob!.apartment = apt2   // Strong reference
apt2!.tenant = bob      // Weak reference (doesn't increase count)

bob = nil
// 💀 Bob deallocated

apt2 = nil
// 🏚️  Apartment 5B deallocated

// iOS EXAMPLE: DELEGATE PATTERN
protocol ViewControllerDelegate: AnyObject {
    func didFinish()
}

class ChildViewController {
    weak var delegate: ViewControllerDelegate?  // weak to avoid cycle
    
    func complete() {
        print("Task completed")
        delegate?.didFinish()
    }
    
    deinit {
        print("🗑️  ChildViewController deallocated")
    }
}

class ParentViewController: ViewControllerDelegate {
    var childVC: ChildViewController?
    
    func presentChild() {
        childVC = ChildViewController()
        childVC?.delegate = self  // self is strong, delegate is weak
    }
    
    func didFinish() {
        print("Child finished, dismissing...")
        childVC = nil  // Releases child
    }
    
    deinit {
        print("🗑️  ParentViewController deallocated")
    }
}

var parent: ParentViewController? = ParentViewController()
parent?.presentChild()
parent?.childVC?.complete()
// Task completed
// Child finished, dismissing...
// 🗑️  ChildViewController deallocated

parent = nil
// 🗑️  ParentViewController deallocated

// UNOWNED REFERENCES (for non-optional references)
class Customer {
    let name: String
    var card: CreditCard?
    
    init(name: String) {
        self.name = name
        print("👤 Customer \(name) initialized")
    }
    
    deinit {
        print("💀 Customer \(name) deinitialized")
    }
}

class CreditCard {
    let number: String
    unowned let customer: Customer  // unowned (card can't exist without customer)
    
    init(number: String, customer: Customer) {
        self.number = number
        self.customer = customer
        print("💳 Card \(number) initialized")
    }
    
    deinit {
        print("💳 Card \(number) deinitialized")
    }
}

var charlie: Customer?
charlie = Customer(name: "Charlie")
charlie!.card = CreditCard(number: "1234-5678-9012-3456", customer: charlie!)

charlie = nil
// 💀 Customer Charlie deinitialized
// 💳 Card 1234-5678-9012-3456 deinitialized

// CLOSURE CAPTURE LISTS
class ViewController {
    var name = "ViewController"
    var closure: (() -> Void)?
    
    func setupClosure() {
        // ❌ Strong reference cycle
        // closure = {
        //     print(self.name)  // Captures self strongly
        // }
        
        // ✅ Use weak self
        closure = { [weak self] in
            guard let self = self else { return }
            print(self.name)
        }
        
        // ✅ Or unowned self (if you're sure self exists)
        closure = { [unowned self] in
            print(self.name)
        }
    }
    
    deinit {
        print("🗑️  ViewController deinitialized")
    }
}

var vc: ViewController? = ViewController()
vc?.setupClosure()
vc?.closure?()
vc = nil
// 🗑️  ViewController deinitialized
```

**Explanation:**
- `deinit` runs when class instance is deallocated
- ARC (Automatic Reference Counting) manages memory
- Strong reference cycles cause memory leaks
- Use `weak` for optional references that can be nil
- Use `unowned` for non-optional references (risky if deallocated)
- Always use `[weak self]` in closures to avoid cycles

---

## 🎯 2025 Interview Questions

### Question 1: What's the difference between structs and classes? When should you use each?

**Answer:**

**Key Differences:**

| Feature | Struct | Class |
|---------|--------|-------|
| **Type** | Value type | Reference type |
| **Memory** | Stack (usually) | Heap |
| **Copy Behavior** | Copied on assignment | Referenced on assignment |
| **Inheritance** | ❌ No | ✅ Yes |
| **Deinitializers** | ❌ No | ✅ Yes (`deinit`) |
| **Identity** | ❌ No identity | ✅ Identity (`===`) |
| **Mutability** | `mutating` needed | No `mutating` needed |
| **Thread Safety** | Safer (no shared state) | Requires synchronization |
| **Performance** | Faster (stack allocation) | Slightly slower (heap + ARC) |

**Value Semantics (Struct):**
```swift
struct Point {
    var x: Int
    var y: Int
}

var point1 = Point(x: 10, y: 20)
var point2 = point1  // COPY created

point2.x = 100

print(point1.x)  // 10 (unchanged)
print(point2.x)  // 100 (independent copy)
```

**Reference Semantics (Class):**
```swift
class PointClass {
    var x: Int
    var y: Int
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

var point3 = PointClass(x: 10, y: 20)
var point4 = point3  // SAME instance

point4.x = 100

print(point3.x)  // 100 (changed!)
print(point4.x)  // 100 (same instance)
```

**When to Use Structs:**

1. **Simple data models**
```swift
struct User {
    let id: String
    var name: String
    var email: String
}
```

2. **Independent copies desired**
```swift
var originalConfig = AppConfig(theme: .light)
var modifiedConfig = originalConfig
modifiedConfig.theme = .dark
// originalConfig unchanged
```

3. **No inheritance needed**
```swift
struct Coordinates {
    let latitude: Double
    let longitude: Double
}
```

4. **Small, lightweight types**
```swift
struct Color {
    let red, green, blue: Double
}
```

5. **Thread safety important**
```swift
// Structs are inherently thread-safe
let settings = AppSettings()
// Can safely pass to multiple threads
```

**When to Use Classes:**

1. **Identity matters**
```swift
class UIViewController {
    // Each screen is a unique object
}
```

2. **Shared mutable state**
```swift
class NetworkManager {
    static let shared = NetworkManager()
    var authToken: String?
}
```

3. **Inheritance needed**
```swift
class Animal { }
class Dog: Animal { }
```

4. **Objective-C interoperability**
```swift
class MyView: UIView {
    // Must be class
}
```

5. **Deinitialization needed**
```swift
class DatabaseConnection {
    deinit {
        // Close connection
    }
}
```

**Apple's Guidance (2025):**
> "Use structs by default. Use classes when you need inheritance, reference semantics, or Objective-C interoperability."

**Real-World Example:**

```swift
// ✅ Struct for data
struct Article {
    let id: UUID
    var title: String
    var content: String
    let publishedAt: Date
}

// ✅ Class for view controller
class ArticleViewController: UIViewController {
    var article: Article  // Struct stored in class
    
    init(article: Article) {
        self.article = article
        super.init(nibName: nil, bundle: nil)
    }
}

// ✅ Class for service
class ArticleService {
    static let shared = ArticleService()
    
    func fetchArticles() async -> [Article] {
        // Network call
        return []
    }
}
```

**Key Takeaway:** Prefer structs unless you specifically need reference semantics, inheritance, or identity.

---

### Question 2: Explain the class initialization process in Swift. What are designated vs convenience initializers?

**Answer:**

**Initialization Rules:**

Swift ensures **all properties are initialized** before use. Classes have two types of initializers:

**1. Designated Initializers (Primary)**

The main initializers. Every class must have at least one.

```swift
class Person {
    let name: String
    var age: Int
    
    // Designated initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}
```

**2. Convenience Initializers (Secondary)**

Helpers that call designated initializers.

```swift
class Person {
    let name: String
    var age: Int
    
    // Designated
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // Convenience (calls designated)
    convenience init(name: String) {
        self.init(name: name, age: 0)
    }
}

let person1 = Person(name: "Alice", age: 30)
let person2 = Person(name: "Bob")  // age = 0
```

**Initialization Rules:**

**Rule 1:** Designated initializer must call designated initializer from immediate superclass
**Rule 2:** Convenience initializer must call another initializer from same class
**Rule 3:** Convenience initializer must ultimately call a designated initializer

```swift
class Vehicle {
    let brand: String
    
    // Designated
    init(brand: String) {
        self.brand = brand
        print("Vehicle init")
    }
    
    // Convenience
    convenience init() {
        self.init(brand: "Unknown")
    }
}

class Car: Vehicle {
    let model: String
    
    // Designated (must call super designated)
    init(brand: String, model: String) {
        self.model = model          // 1. Initialize own properties
        super.init(brand: brand)    // 2. Call super designated
        print("Car init")
    }
    
    // Convenience (must call designated in same class)
    convenience init(model: String) {
        self.init(brand: "Generic", model: model)
    }
}

let car = Car(model: "Sedan")
// Vehicle init
// Car init
```

**Two-Phase Initialization:**

**Phase 1:** Initialize all stored properties (bottom-up)
**Phase 2:** Customize properties (top-down)

```swift
class Base {
    var baseProperty: String
    
    init(baseProperty: String) {
        // Phase 1: Set own properties
        self.baseProperty = baseProperty
        
        // Phase 2: Can now call methods
        self.setup()
    }
    
    func setup() {
        print("Base setup")
    }
}

class Derived: Base {
    var derivedProperty: Int
    
    init(baseProperty: String, derivedProperty: Int) {
        // Phase 1: Initialize own properties FIRST
        self.derivedProperty = derivedProperty
        
        // Then call super
        super.init(baseProperty: baseProperty)
        
        // Phase 2: Now can customize
        self.derivedProperty *= 2
    }
    
    override func setup() {
        super.setup()
        print("Derived setup")
    }
}
```

**Automatic Initializer Inheritance:**

Subclass automatically inherits initializers if:
1. No designated initializers defined, OR
2. All designated initializers overridden

```swift
class Food {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    convenience init() {
        self.init(name: "Unnamed")
    }
}

// No custom initializers → inherits all
class RecipeIngredient: Food {
    var quantity: Int = 1
    
    // Automatically gets:
    // - init(name: String)
    // - convenience init()
}

let ingredient = RecipeIngredient(name: "Eggs")
let unnamed = RecipeIngredient()  // Uses convenience
```

**Failable Initializers:**

Return `nil` if initialization fails.

```swift
class Product {
    let name: String
    let price: Double
    
    init?(name: String, price: Double) {
        guard !name.isEmpty, price > 0 else {
            return nil  // Initialization failed
        }
        
        self.name = name
        self.price = price
    }
}

if let product = Product(name: "iPhone", price: 999) {
    print("Created: \(product.name)")
} else {
    print("Invalid product")
}

let invalid = Product(name: "", price: -10)
print(invalid)  // nil
```

**Required Initializers:**

Force subclasses to implement.

```swift
class Base {
    required init() {
        // Must be implemented by subclasses
    }
}

class Derived: Base {
    required init() {
        super.init()
        // Custom setup
    }
}
```

**iOS Example (2025):**

```swift
class ViewController: UIViewController {
    let viewModel: ViewModel
    
    // Designated
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    // Required (for storyboard support)
    required init?(coder: NSCoder) {
        fatalError("init(coder:) not implemented")
    }
    
    // Convenience
    convenience init() {
        self.init(viewModel: ViewModel())
    }
}
```

**Best Practices:**

1. Keep designated initializers minimal
2. Use convenience initializers for common cases
3. Always initialize properties before calling super
4. Use failable initializers for validation

---

### Question 3: What are strong reference cycles, and how do you prevent them?

**Answer:**

**Strong Reference Cycle (Retain Cycle):**

When two objects hold strong references to each other, preventing deallocation (memory leak).

**The Problem:**

```swift
class Person {
    let name: String
    var apartment: Apartment?
    
    init(name: String) {
        self.name = name
        print("👤 \(name) allocated")
    }
    
    deinit {
        print("💀 \(name) deallocated")
    }
}

class Apartment {
    let unit: String
    var tenant: Person?
    
    init(unit: String) {
        self.unit = unit
        print("🏠 \(unit) allocated")
    }
    
    deinit {
        print("🏚️  \(unit) deallocated")
    }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A  // Person → Apartment (strong)
unit4A!.tenant = john     // Apartment → Person (strong)

// Strong reference cycle created!
// Person retains Apartment
// Apartment retains Person

john = nil
unit4A = nil

// ❌ Nothing printed! Both leaked!
```

**Solution 1: Weak References**

Use `weak` for optional references that can become `nil`.

```swift
class Apartment {
    let unit: String
    weak var tenant: Person?  // weak breaks cycle
    
    init(unit: String) {
        self.unit = unit
        print("🏠 \(unit) allocated")
    }
    
    deinit {
        print("🏚️  \(unit) deallocated")
    }
}

var john2: Person?
var unit4B: Apartment?

john2 = Person(name: "John")
unit4B = Apartment(unit: "4B")

john2!.apartment = unit4B  // Strong
unit4B!.tenant = john2     // Weak (doesn't increase count)

john2 = nil
// 💀 John deallocated (weak reference doesn't prevent)

unit4B = nil
// 🏚️  4B deallocated
```

**Solution 2: Unowned References**

Use `unowned` for non-optional references that will never be `nil` after initialization.

```swift
class Customer {
    let name: String
    var card: CreditCard?
    
    init(name: String) {
        self.name = name
        print("👤 \(name) allocated")
    }
    
    deinit {
        print("💀 \(name) deallocated")
    }
}

class CreditCard {
    let number: String
    unowned let customer: Customer  // unowned (card can't outlive customer)
    
    init(number: String, customer: Customer) {
        self.number = number
        self.customer = customer
        print("💳 \(number) allocated")
    }
    
    deinit {
        print("💳 \(number) deallocated")
    }
}

var alice: Customer?
alice = Customer(name: "Alice")
alice!.card = CreditCard(number: "1234", customer: alice!)

alice = nil
// 💀 Alice deallocated
// 💳 1234 deallocated
```

**Closure Capture Lists:**

Closures create strong references to captured values.

```swift
class HTMLElement {
    let name: String
    let text: String?
    
    // ❌ Strong reference cycle
    lazy var asHTML: () -> String = {
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
        print("💀 \(name) deallocated")
    }
}

var heading: HTMLElement? = HTMLElement(name: "h1", text: "Hello")
print(heading!.asHTML())
heading = nil
// ❌ Not deallocated! Closure captures self strongly
```

**Solution: Capture Lists**

```swift
class HTMLElement {
    let name: String
    let text: String?
    
    // ✅ Use capture list
    lazy var asHTML: () -> String = { [weak self] in
        guard let self = self else { return "" }
        
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    
    // Or unowned if self is guaranteed to exist
    lazy var asHTML2: () -> String = { [unowned self] in
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
        print("💀 \(name) deallocated")
    }
}

var paragraph: HTMLElement? = HTMLElement(name: "p", text: "Content")
print(paragraph!.asHTML())
paragraph = nil
// 💀 p deallocated ✅
```

**iOS Examples (2025):**

**1. Delegate Pattern:**
```swift
protocol ViewControllerDelegate: AnyObject {
    func didComplete()
}

class ChildViewController {
    weak var delegate: ViewControllerDelegate?  // Always weak!
    
    func finish() {
        delegate?.didComplete()
    }
}

class ParentViewController: ViewControllerDelegate {
    var child: ChildViewController?
    
    func setup() {
        child = ChildViewController()
        child?.delegate = self  // No cycle (delegate is weak)
    }
    
    func didComplete() {
        child = nil
    }
}
```

**2. Combine Subscriptions:**
```swift
import Combine

class ViewModel {
    var cancellables = Set<AnyCancellable>()
    
    func subscribe() {
        publisher
            .sink { [weak self] value in
                self?.handleValue(value)  // Weak self!
            }
            .store(in: &cancellables)
    }
}
```

**3. GCD Closures:**
```swift
class DataManager {
    func loadData() {
        DispatchQueue.global().async { [weak self] in
            let data = self?.fetchData()
            
            DispatchQueue.main.async { [weak self] in
                self?.updateUI(with: data)
            }
        }
    }
}
```

**Decision Tree:**

```
Does object A own object B?
├─ Yes → Does B need reference to A?
│   ├─ Yes → Use weak (B → A)
│   └─ No  → Strong is fine
└─ No  → Both independent? Strong is fine
```

**Weak vs Unowned:**

| Aspect | weak | unowned |
|--------|------|---------|
| Type | Optional | Non-optional |
| When nil | Safe (becomes nil) | Crashes if accessed |
| Use when | Object may be deallocated | Object guaranteed to exist |
| Safety | Safer | Risky |
| Performance | Slightly slower | Slightly faster |

**Best Practices:**

1. Default to `weak` in delegates
2. Use `[weak self]` in closures
3. Use `unowned` only when certain reference exists
4. Analyze with Instruments (Leaks tool)
5. Test deallocation with `deinit` logs

---

### Question 4: Explain the difference between `===` (identity) and `==` (equality) for classes.

**Answer:**

**Identity (`===`) vs Equality (`==`):**

**Identity (`===` and `!==`):**
- Checks if two references point to the **same instance** (same memory address)
- **Built-in** for classes
- About **reference** comparison

**Equality (`==` and `!=`):**
- Checks if two instances have the **same values**
- Requires **Equatable** protocol
- About **value** comparison

**Example:**

```swift
class Person {
    let name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let person1 = Person(name: "Alice", age: 30)
let person2 = person1  // Same instance
let person3 = Person(name: "Alice", age: 30)  // Different instance, same data

// IDENTITY (===)
print(person1 === person2)  // true (same instance)
print(person1 === person3)  // false (different instances)
print(person1 !== person3)  // true (not same instance)

// EQUALITY (==) - requires Equatable
// person1 == person3  // ❌ Error: Person doesn't conform to Equatable
```

**Implementing Equatable:**

```swift
class Person: Equatable {
    let name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // Required for Equatable
    static func == (lhs: Person, rhs: Person) -> Bool {
        return lhs.name == rhs.name && lhs.age == rhs.age
    }
}

let person1 = Person(name: "Alice", age: 30)
let person2 = person1
let person3 = Person(name: "Alice", age: 30)

// Identity
print(person1 === person2)  // true (same instance)
print(person1 === person3)  // false (different instances)

// Equality
print(person1 == person2)  // true (same values)
print(person1 == person3)  // true (same values!)
```

**Visual Explanation:**

```swift
// Memory representation
// person1 → [Memory: 0x1234] { name: "Alice", age: 30 }
// person2 → [Memory: 0x1234]  // Points to same address
// person3 → [Memory: 0x5678] { name: "Alice", age: 30 }  // Different address

person1 === person2  // true (0x1234 == 0x1234)
person1 === person3  // false (0x1234 != 0x5678)

person1 == person3   // true (both have name: "Alice", age: 30)
```

**When to Use Each:**

**Use `===` (Identity):**

```swift
// Check if updating same instance
class ViewController: UIViewController {
    var dataSource: DataSource?
    
    func updateDataSource(_ newDataSource: DataSource) {
        if self.dataSource === newDataSource {
            print("Same instance, no update needed")
            return
        }
        self.dataSource = newDataSource
    }
}

// Singleton check
if NetworkManager.shared === NetworkManager.shared {
    print("Same singleton instance")
}

// Check if object is in array
class Item { }
let item = Item()
let items = [item, Item(), Item()]

for element in items {
    if element === item {
        print("Found the specific instance")
    }
}
```

**Use `==` (Equality):**

```swift
// Check if data matches
struct User: Equatable {
    let id: String
    let name: String
}

let user1 = User(id: "123", name: "Alice")
let user2 = User(id: "123", name: "Alice")

if user1 == user2 {
    print("Same user data")
}

// Check if arrays contain same values
let array1 = [1, 2, 3]
let array2 = [1, 2, 3]

if array1 == array2 {
    print("Same contents")
}
```

**Real-World iOS Example (2025):**

```swift
// SwiftUI View Identity
class ViewModel: ObservableObject {
    @Published var data: String = ""
}

struct ContentView: View {
    @StateObject var viewModel1 = ViewModel()
    @StateObject var viewModel2 = ViewModel()
    
    var body: some View {
        VStack {
            // Check if same instance
            if viewModel1 === viewModel1 {
                Text("Same instance")
            }
            
            if viewModel1 === viewModel2 {
                Text("Different instances")
            }
        }
    }
}

// Cache Check
class ImageCache {
    private var cache: [String: UIImage] = [:]
    
    func image(for url: String) -> UIImage? {
        return cache[url]
    }
    
    func setImage(_ image: UIImage, for url: String) {
        // Check if updating with same instance
        if let existing = cache[url], existing === image {
            return  // Skip if same instance
        }
        cache[url] = image
    }
}
```

**Array Contains:**

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
}

let alice = Person(name: "Alice")
let people = [alice, Person(name: "Bob")]

// Check if array contains specific instance
let containsAlice = people.contains { $0 === alice }
print(containsAlice)  // true

// With Equatable, can use ==
extension Person: Equatable {
    static func == (lhs: Person, rhs: Person) -> Bool {
        return lhs.name == rhs.name
    }
}

let aliceCopy = Person(name: "Alice")
let containsSameName = people.contains(aliceCopy)  // Uses ==
print(containsSameName)  // true
```

**Performance:**

```swift
// === is O(1) - just compares memory addresses
person1 === person2

// == can be O(n) - compares all properties
person1 == person2  // Compares name, age, etc.
```

**Key Takeaways:**

- `===`: "Are these the **same object**?" (reference)
- `==`: "Do these have the **same value**?" (content)
- Classes use `===` for identity, requires `Equatable` for `==`
- Structs/Enums use `==` for equality (no identity)
- `===` is faster (just pointer comparison)

---

### Question 5: What is the `final` keyword, and when should you use it?

**Answer:**

The `final` keyword **prevents inheritance and overriding**, providing performance optimizations and design constraints.

**Usage:**

**1. Final Class (Cannot be Subclassed):**

```swift
final class Utility {
    static func formatDate(_ date: Date) -> String {
        // Implementation
        return ""
    }
}

// ❌ Error: Cannot inherit from final class
// class SubUtility: Utility { }
```

**2. Final Method (Cannot be Overridden):**

```swift
class Vehicle {
    final func start() {
        print("Starting engine...")
    }
    
    func stop() {
        print("Stopping...")
    }
}

class Car: Vehicle {
    // ❌ Error: Cannot override final method
    // override func start() { }
    
    // ✅ Can override non-final
    override func stop() {
        super.stop()
        print("Car stopped")
    }
}
```

**3. Final Property (Cannot be Overridden):**

```swift
class Base {
    final var immutableValue: Int = 10
    var mutableValue: Int = 20
}

class Derived: Base {
    // ❌ Error: Cannot override final property
    // override var immutableValue: Int { return 15 }
    
    // ✅ Can override non-final
    override var mutableValue: Int {
        get { return super.mutableValue * 2 }
        set { super.mutableValue = newValue }
    }
}
```

**Why Use `final`?**

**1. Performance Optimization:**

```swift
// Without final - dynamic dispatch (slower)
class Animal {
    func makeSound() {
        print("Generic sound")
    }
}

// With final - static dispatch (faster)
class Dog {
    final func makeSound() {
        print("Woof!")
    }
}

// Compiler can inline final methods
let dog = Dog()
dog.makeSound()  // Can be inlined at compile time
```

**2. Design Intent:**

```swift
// Prevent unintended subclassing
final class NetworkConfiguration {
    let apiKey: String
    let baseURL: String
    
    init(apiKey: String, baseURL: String) {
        self.apiKey = apiKey
        self.baseURL = baseURL
    }
}

// Clear message: This class should not be subclassed
```

**3. Safety:**

```swift
// Prevent method swizzling vulnerabilities
final class SecurityManager {
    final func authenticate(token: String) -> Bool {
        // Critical security logic
        return true
    }
}

// Can't be overridden to bypass security
```

**When to Use `final`:**

**Use `final` For:**

**1. Utility/Helper Classes:**
```swift
final class StringUtils {
    static func capitalize(_ string: String) -> String {
        return string.uppercased()
    }
}
```

**2. Singletons:**
```swift
final class AppManager {
    static let shared = AppManager()
    private init() { }
}
```

**3. Value-Like Classes:**
```swift
final class Point {
    let x: Double
    let y: Double
    
    init(x: Double, y: Double) {
        self.x = x
        self.y = y
    }
}
```

**4. Performance-Critical Code:**
```swift
final class FastCalculator {
    final func compute() -> Double {
        // Inlined for performance
        return 42.0
    }
}
```

**5. Security-Sensitive Code:**
```swift
final class CryptoManager {
    final func encrypt(_ data: Data) -> Data {
        // Can't be overridden/tampered
        return data
    }
}
```

**Don't Use `final` For:**

**1. Extension Points:**
```swift
// ❌ Don't finalize if subclassing is expected
class BaseViewController: UIViewController {
    // Leave open for subclasses to customize
    func setupUI() { }
}
```

**2. Public API:**
```swift
// ❌ Limits library users
public class CustomView: UIView {
    // Users might want to subclass
}

// ✅ Better: Document intention
public class CustomView: UIView {
    // Override this to customize
    open func configure() { }
}
```

**Performance Impact:**

```swift
// Dynamic dispatch (virtual method table lookup)
class Base {
    func method() { }  // Runtime lookup
}

// Static dispatch (compile-time resolution)
final class Derived {
    func method() { }  // Compile-time, can inline
}

// Benchmark (simplified)
// Dynamic: ~10ns per call
// Static:  ~1ns per call (10x faster)
```

**Real-World iOS Examples (2025):**

**1. Swift Standard Library:**
```swift
// Many Swift types are final
final class NSString { }
final class NSNumber { }
final class NSData { }
```

**2. UIKit:**
```swift
// Some UIKit classes are final
final class UIStackView: UIView { }

// Others are open
open class UIViewController { }
```

**3. SwiftUI:**
```swift
// Many SwiftUI types are structs (no inheritance)
// But some classes are final
@available(iOS 13.0, *)
final class UIHostingController<Content>: UIViewController where Content: View {
}
```

**4. App Code:**
```swift
// Configuration
final class AppConfig {
    static let shared = AppConfig()
    let apiURL = "https://api.example.com"
    let apiKey = "secret-key"
}

// Coordinator (no need for inheritance)
final class AppCoordinator {
    func start() {
        // Navigation logic
    }
}

// View Models (prefer composition over inheritance)
final class ProfileViewModel: ObservableObject {
    @Published var user: User?
}
```

**Access Control + Final:**

```swift
// Combine with access control
public final class PublicUtility {
    // Can't be subclassed outside module
    
    private final func privateMethod() {
        // Can't be overridden or accessed outside
    }
}
```

**Alternatives to Inheritance:**

Instead of subclassing, use:

**1. Protocols:**
```swift
protocol Drawable {
    func draw()
}

final class Circle: Drawable {
    func draw() { }
}

final class Rectangle: Drawable {
    func draw() { }
}
```

**2. Composition:**
```swift
final class Engine {
    func start() { }
}

final class Car {
    let engine = Engine()
    
    func start() {
        engine.start()
    }
}
```

**3. Extensions:**
```swift
protocol Vehicle { }

extension Vehicle {
    func start() {
        print("Starting...")
    }
}

final class Car: Vehicle { }
```

**Key Takeaways:**

- `final` prevents inheritance/overriding
- Improves performance (static dispatch)
- Communicates design intent
- Use for utilities, singletons, value types
- Don't use if extensibility needed
- Swift encourages composition over inheritance

**Apple's Recommendation:**
> "Mark classes as `final` by default unless they're explicitly designed for inheritance."

---

## 📝 Practice Exercises

1. Create an `Animal` class hierarchy with at least 3 subclasses
2. Implement a view controller system with base and derived controllers
3. Build a shape system using classes with area/perimeter calculations
4. Create a vehicle rental system with classes for different vehicle types
5. Implement a memory leak and then fix it using weak/unowned references

---

## 🔗 Navigation
- [← Previous: Step 10 - Structures](../Step-10-Structures/README.md)
- [→ Next: Step 12 - Structs vs Classes](../Step-12-Structs-vs-Classes/README.md)
- [↑ Back to Main README](../../Swift-Learning-Documentation-README.md)

---

**You've completed Step 11!** You now understand:
- ✅ Class syntax and definition
- ✅ Reference semantics
- ✅ Inheritance and method overriding
- ✅ Identity operators (`===`, `!==`)
- ✅ Deinitializers (`deinit`)
- ✅ Strong reference cycles and solutions
- ✅ The `final` keyword
- ✅ When to use classes vs structs

**Next:** Deep dive into **Structs vs Classes** comparison in Step 12!
