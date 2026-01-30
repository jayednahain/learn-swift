# Step 16: Protocols

## 📚 Description

**Protocols** define a blueprint of methods, properties, and requirements that suit a particular task or piece of functionality. They are the foundation of **Protocol-Oriented Programming (POP)**, Swift's preferred programming paradigm. Protocols enable polymorphism, code reuse, and flexible architecture without the constraints of inheritance.

### What You'll Learn:
- Defining and conforming to protocols
- Protocol properties and methods
- Protocol inheritance and composition
- Protocol extensions with default implementations
- Protocol-oriented programming (POP)
- Associated types in protocols
- Protocol as types and existential types

### Key Concepts:
- **Protocol**: A contract that defines required methods and properties
- **Conformance**: A type adopting and implementing a protocol
- **Protocol Extension**: Adding default implementations to protocols
- **Protocol Composition**: Combining multiple protocols (`&`)
- **Associated Type**: A placeholder type in a protocol
- **Protocol-Oriented Programming**: Designing with protocols first, not classes

### Benefits:
- **Flexibility**: Types can conform to multiple protocols (unlike single inheritance)
- **Testability**: Easy to create mock objects for testing
- **Decoupling**: Reduces dependencies between components
- **Code Reuse**: Default implementations via protocol extensions
- **Type Safety**: Compile-time guarantees of behavior

---

## 💡 Clear Examples

### Example 1: Basic Protocol Definition and Conformance

```swift
// ========== DEFINING A PROTOCOL ==========

protocol Vehicle {
    // Property requirements
    var numberOfWheels: Int { get }
    var brand: String { get set }
    
    // Method requirements
    func start()
    func stop()
    func describe() -> String
}

// ========== CONFORMING TO A PROTOCOL ==========

struct Car: Vehicle {
    var numberOfWheels: Int = 4
    var brand: String
    
    func start() {
        print("\(brand) car is starting with a key turn...")
    }
    
    func stop() {
        print("\(brand) car stopped.")
    }
    
    func describe() -> String {
        return "\(brand) car with \(numberOfWheels) wheels"
    }
}

struct Motorcycle: Vehicle {
    var numberOfWheels: Int = 2
    var brand: String
    
    func start() {
        print("\(brand) motorcycle is starting with kick start...")
    }
    
    func stop() {
        print("\(brand) motorcycle stopped.")
    }
    
    func describe() -> String {
        return "\(brand) motorcycle with \(numberOfWheels) wheels"
    }
}

// ========== USING PROTOCOLS ==========

let myCar = Car(brand: "Toyota")
let myBike = Motorcycle(brand: "Harley")

myCar.start()  // Toyota car is starting with a key turn...
myBike.start() // Harley motorcycle is starting with kick start...

// Protocol as a type
let vehicles: [Vehicle] = [myCar, myBike]
for vehicle in vehicles {
    print(vehicle.describe())
}
// Output:
// Toyota car with 4 wheels
// Harley motorcycle with 2 wheels
```

**Key Points:**
- Protocols define required properties and methods
- Types can be structs, classes, or enums
- `{ get }` means read-only, `{ get set }` means read-write
- Protocols can be used as types

---

### Example 2: Protocol Extensions with Default Implementations

```swift
// ========== PROTOCOL WITH EXTENSION ==========

protocol Drawable {
    func draw()
    func description() -> String
}

// Provide default implementation
extension Drawable {
    func description() -> String {
        return "This is a drawable object"
    }
    
    // Add new method not in protocol
    func printDescription() {
        print(description())
    }
}

// ========== CONFORMING TYPES ==========

struct Circle: Drawable {
    var radius: Double
    
    func draw() {
        print("Drawing a circle with radius \(radius)")
    }
    
    // Uses default implementation of description()
}

struct Rectangle: Drawable {
    var width: Double
    var height: Double
    
    func draw() {
        print("Drawing rectangle \(width)x\(height)")
    }
    
    // Override default implementation
    func description() -> String {
        return "Rectangle with area \(width * height)"
    }
}

// ========== USAGE ==========

let circle = Circle(radius: 5.0)
circle.draw()               // Drawing a circle with radius 5.0
circle.printDescription()   // This is a drawable object

let rect = Rectangle(width: 10, height: 20)
rect.draw()                 // Drawing rectangle 10.0x20.0
rect.printDescription()     // Rectangle with area 200.0

// ========== PROTOCOL COMPOSITION ==========

protocol Resizable {
    mutating func resize(by factor: Double)
}

struct Square: Drawable, Resizable {
    var side: Double
    
    func draw() {
        print("Drawing square with side \(side)")
    }
    
    mutating func resize(by factor: Double) {
        side *= factor
    }
}

var square = Square(side: 10)
square.draw()              // Drawing square with side 10.0
square.resize(by: 2.0)
square.draw()              // Drawing square with side 20.0

// Protocol composition: accepting types conforming to multiple protocols
func drawAndResize(shape: Drawable & Resizable, factor: Double) {
    var mutableShape = shape
    mutableShape.resize(by: factor)
    mutableShape.draw()
}
```

**Key Points:**
- Protocol extensions provide default implementations
- Types can override defaults
- Extensions can add methods not in the protocol
- Protocol composition combines multiple protocols with `&`

---

### Example 3: Protocol-Oriented Programming (POP) Pattern

```swift
// ========== TRADITIONAL OOP APPROACH (PROBLEMATIC) ==========

class Animal {
    func makeSound() {
        fatalError("Subclass must override")
    }
}

class Dog: Animal {
    override func makeSound() {
        print("Woof!")
    }
}

// Problem: What if we want a Robot that makes sounds but isn't an Animal?

// ========== PROTOCOL-ORIENTED APPROACH ==========

protocol SoundMaking {
    func makeSound()
}

protocol Moving {
    func move()
}

// Default implementations
extension SoundMaking {
    func makeSound() {
        print("Making some sound...")
    }
}

extension Moving {
    func move() {
        print("Moving...")
    }
}

// ========== FLEXIBLE COMPOSITION ==========

struct DogPOP: SoundMaking, Moving {
    func makeSound() {
        print("Woof! Woof!")
    }
    
    func move() {
        print("Running on four legs")
    }
}

struct Robot: SoundMaking, Moving {
    func makeSound() {
        print("Beep boop!")
    }
    
    func move() {
        print("Rolling on wheels")
    }
}

struct Bird: SoundMaking, Moving {
    func makeSound() {
        print("Tweet tweet!")
    }
    
    func move() {
        print("Flying in the sky")
    }
}

// Even non-living things can conform
struct Speaker: SoundMaking {
    // Uses default implementation
}

// ========== USAGE ==========

let entities: [SoundMaking & Moving] = [
    DogPOP(),
    Robot(),
    Bird()
]

for entity in entities {
    entity.makeSound()
    entity.move()
    print("---")
}

// Output:
// Woof! Woof!
// Running on four legs
// ---
// Beep boop!
// Rolling on wheels
// ---
// Tweet tweet!
// Flying in the sky
// ---

let speaker = Speaker()
speaker.makeSound()  // Making some sound... (default implementation)

// ========== CONDITIONAL CONFORMANCE ==========

protocol Describable {
    func describe() -> String
}

extension Array: Describable where Element: Describable {
    func describe() -> String {
        return "[\(self.map { $0.describe() }.joined(separator: ", "))]"
    }
}

extension Int: Describable {
    func describe() -> String {
        return "Number: \(self)"
    }
}

let numbers = [1, 2, 3]
print(numbers.describe())  // [Number: 1, Number: 2, Number: 3]
```

**Key Points:**
- POP favors composition over inheritance
- Protocols enable flexible, reusable designs
- Multiple protocols can be combined
- Conditional conformance extends functionality based on generic constraints

---

## 🎯 Interview Questions

### Q1: What is the difference between protocols and classes in Swift? When would you use one over the other?

**Answer:**

**Protocols:**
- Define a contract/blueprint without implementation (unless extended)
- Cannot store data (only computed properties)
- Types can conform to multiple protocols
- Work with structs, classes, and enums
- Support value semantics (with structs)
- Used for polymorphism without inheritance

**Classes:**
- Provide full implementation
- Can store data in properties
- Support single inheritance only
- Reference types (shared mutable state)
- Support deinitializers
- Used when you need inheritance or reference semantics

**When to use:**
- **Use Protocols when:**
  - You want multiple inheritance-like behavior
  - You need flexibility (structs, classes, enums can all conform)
  - You want to define capabilities/behaviors (Codable, Hashable, etc.)
  - You're designing testable code (easy to mock)
  - You want Protocol-Oriented Programming

- **Use Classes when:**
  - You need inheritance hierarchy
  - You need reference semantics (shared state)
  - You need deinitializers (cleanup resources)
  - You're working with Objective-C interoperability

**Example:**
```swift
// Protocol approach (flexible)
protocol PaymentMethod {
    func processPayment(amount: Double)
}

struct CreditCard: PaymentMethod { /* ... */ }
struct PayPal: PaymentMethod { /* ... */ }
struct ApplePay: PaymentMethod { /* ... */ }

// Class approach (inheritance)
class UIViewController { /* base implementation */ }
class MyViewController: UIViewController { /* inherits behavior */ }
```

---

### Q2: Explain protocol extensions and how they enable default implementations. What are the benefits?

**Answer:**

**Protocol Extensions** allow you to add default implementations to protocol methods and add new methods entirely. This is one of Swift's most powerful features.

**Syntax:**
```swift
protocol Greetable {
    var name: String { get }
    func greet() -> String
}

extension Greetable {
    // Default implementation
    func greet() -> String {
        return "Hello, \(name)!"
    }
    
    // Additional method (not in protocol)
    func greetEnthusiastically() -> String {
        return "HELLO, \(name.uppercased())!!!"
    }
}

struct Person: Greetable {
    var name: String
    // Automatically gets greet() and greetEnthusiastically()
}

let person = Person(name: "Alice")
print(person.greet())                    // Hello, Alice!
print(person.greetEnthusiastically())    // HELLO, ALICE!!!
```

**Benefits:**

1. **Reduce Code Duplication**: Common logic written once
2. **Optional Implementation**: Types can override or use default
3. **Add Functionality**: Extend protocols without breaking existing code
4. **Protocol-Oriented Programming**: Compose behavior without inheritance
5. **Backward Compatibility**: Add new methods without requiring updates

**Real-world example:**
```swift
protocol Validatable {
    var isValid: Bool { get }
}

extension Validatable {
    func validate() throws {
        guard isValid else {
            throw ValidationError.invalid
        }
    }
}

struct Email: Validatable {
    var address: String
    var isValid: Bool {
        return address.contains("@")
    }
}

let email = Email(address: "test@example.com")
try email.validate()  // Uses default implementation
```

---

### Q3: What are associated types in protocols? Provide an example and explain when to use them.

**Answer:**

**Associated Types** are placeholder types in protocols that are specified by the conforming type. They enable generic protocols.

**Syntax:**
```swift
protocol Container {
    associatedtype Item  // Placeholder type
    
    var count: Int { get }
    mutating func add(_ item: Item)
    func get(at index: Int) -> Item?
}
```

**Example:**
```swift
// Generic Stack implementation
protocol Stack {
    associatedtype Element
    
    var items: [Element] { get set }
    mutating func push(_ item: Element)
    mutating func pop() -> Element?
    func peek() -> Element?
}

extension Stack {
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.isEmpty ? nil : items.removeLast()
    }
    
    func peek() -> Element? {
        return items.last
    }
}

// Conform with Int
struct IntStack: Stack {
    typealias Element = Int  // Can be inferred
    var items: [Int] = []
}

// Conform with String
struct StringStack: Stack {
    var items: [String] = []  // Type inferred from property
}

var intStack = IntStack()
intStack.push(1)
intStack.push(2)
print(intStack.pop())  // Optional(2)

var stringStack = StringStack()
stringStack.push("Swift")
stringStack.push("Protocols")
print(stringStack.peek())  // Optional("Protocols")
```

**When to use:**
- When you need protocol with generic types
- When each conforming type needs its own type (not known in advance)
- When building reusable, type-safe abstractions

**Real-world example (similar to Swift's Sequence):**
```swift
protocol Repository {
    associatedtype Model
    
    func fetch(id: String) -> Model?
    func save(_ model: Model)
}

struct UserRepository: Repository {
    typealias Model = User
    
    func fetch(id: String) -> User? {
        // Fetch user from database
        return nil
    }
    
    func save(_ model: User) {
        // Save user to database
    }
}

struct ProductRepository: Repository {
    typealias Model = Product
    
    func fetch(id: String) -> Product? {
        // Fetch product
        return nil
    }
    
    func save(_ model: Product) {
        // Save product
    }
}
```

---

### Q4: What is protocol composition and how do you use the `&` operator?

**Answer:**

**Protocol Composition** allows you to combine multiple protocols into a single requirement using the `&` operator. This enables you to specify that a type must conform to multiple protocols.

**Syntax:**
```swift
func doSomething(with object: ProtocolA & ProtocolB) {
    // object must conform to both protocols
}
```

**Example:**
```swift
protocol Named {
    var name: String { get }
}

protocol Aged {
    var age: Int { get }
}

protocol Employed {
    var jobTitle: String { get }
}

// Function requiring multiple protocols
func printInfo(person: Named & Aged) {
    print("\(person.name) is \(person.age) years old")
}

func printFullInfo(person: Named & Aged & Employed) {
    print("\(person.name), \(person.age), works as \(person.jobTitle)")
}

struct Person: Named, Aged, Employed {
    var name: String
    var age: Int
    var jobTitle: String
}

let john = Person(name: "John", age: 30, jobTitle: "iOS Developer")
printInfo(person: john)         // John is 30 years old
printFullInfo(person: john)     // John, 30, works as iOS Developer

// Variable with protocol composition
var employee: Named & Employed = john
print(employee.name)            // John
print(employee.jobTitle)        // iOS Developer
// print(employee.age)          // ❌ Error: age not in composition
```

**Real-world use case (UIKit):**
```swift
protocol Identifiable {
    var id: UUID { get }
}

protocol Timestamped {
    var createdAt: Date { get }
    var updatedAt: Date { get }
}

protocol Persistable {
    func save()
    func delete()
}

// Function accepting all three
func syncToServer<T: Identifiable & Timestamped & Persistable>(_ object: T) {
    print("Syncing object with ID: \(object.id)")
    print("Last updated: \(object.updatedAt)")
    object.save()
}

// Cleaner than: func syncToServer<T: Identifiable, Timestamped, Persistable>
```

**Benefits:**
- More precise type requirements
- Combines behaviors without inheritance
- Clearer intent in function signatures
- Enables flexible API design

---

### Q5: Explain the difference between protocol requirements and protocol extensions. What happens with dispatch?

**Answer:**

This is a critical concept about **static vs dynamic dispatch**.

**Protocol Requirements** (dynamic dispatch):
```swift
protocol Animal {
    func makeSound()  // Required method
}

extension Animal {
    func makeSound() {  // Default implementation
        print("Some sound")
    }
}

struct Dog: Animal {
    func makeSound() {
        print("Woof!")
    }
}

let dog = Dog()
dog.makeSound()  // "Woof!" ✅

let animal: Animal = Dog()
animal.makeSound()  // "Woof!" ✅ (dynamic dispatch)
```

**Protocol Extensions without requirement** (static dispatch):
```swift
protocol Animal {
    // makeSound() NOT declared here
}

extension Animal {
    func makeSound() {  // Added in extension only
        print("Some sound")
    }
}

struct Dog: Animal {
    func makeSound() {
        print("Woof!")
    }
}

let dog = Dog()
dog.makeSound()  // "Woof!" ✅

let animal: Animal = Dog()
animal.makeSound()  // "Some sound" ❌ (static dispatch!)
```

**Key Difference:**
- **With protocol requirement**: Uses dynamic dispatch (runtime polymorphism)
- **Without protocol requirement**: Uses static dispatch (compile-time resolution)

**Complete Example:**
```swift
protocol Drawable {
    func draw()  // Required
}

extension Drawable {
    func draw() {
        print("Default draw")
    }
    
    func render() {  // Not required
        print("Default render")
    }
}

struct Circle: Drawable {
    func draw() {
        print("Drawing circle")
    }
    
    func render() {
        print("Rendering circle")
    }
}

let circle = Circle()
circle.draw()    // "Drawing circle" ✅
circle.render()  // "Rendering circle" ✅

let drawable: Drawable = Circle()
drawable.draw()   // "Drawing circle" ✅ (dynamic dispatch)
drawable.render() // "Default render" ❌ (static dispatch!)
```

**Best Practice:**
Always declare methods in the protocol if you want polymorphic behavior:
```swift
protocol Drawable {
    func draw()
    func render()  // ✅ Now both use dynamic dispatch
}

extension Drawable {
    func draw() { print("Default draw") }
    func render() { print("Default render") }
}
```

This is one of the most common interview questions about protocols!

---

## 📝 Practice Exercises

### Exercise 1: Payment Processing System
Create a protocol-based payment system that supports multiple payment methods.

**Requirements:**
1. Create a `PaymentMethod` protocol with:
   - `paymentType: String { get }`
   - `processPayment(amount: Double) -> Bool`
   - `refund(amount: Double) -> Bool`

2. Add a protocol extension with a default `description()` method

3. Implement three payment methods:
   - `CreditCard` (requires card number and CVV)
   - `PayPal` (requires email)
   - `ApplePay` (requires device ID)

4. Create a `PaymentProcessor` that accepts `[PaymentMethod]` and processes payments

**Challenge:** Add a `SecurePayment` protocol for methods requiring authentication, and use protocol composition.

---

### Exercise 2: Protocol-Oriented Data Repository
Build a generic data repository using protocols and associated types.

**Requirements:**
1. Create a `Repository` protocol with:
   - `associatedtype Model`
   - `func getAll() -> [Model]`
   - `func get(byId id: String) -> Model?`
   - `func save(_ model: Model)`
   - `func delete(byId id: String)`

2. Create a `User` struct with `id`, `name`, `email`

3. Implement `UserRepository` conforming to `Repository`

4. Create a `Product` struct and `ProductRepository`

5. Write a generic function that works with any repository:
```swift
func printAllItems<R: Repository>(from repo: R) where R.Model: CustomStringConvertible
```

---

### Exercise 3: Protocol Composition for View Configuration
Design a flexible view configuration system using protocol composition.

**Requirements:**
1. Create protocols:
   - `Themeable`: `var theme: Theme { get set }`
   - `Animatable`: `func animate(duration: Double)`
   - `Tappable`: `var onTap: (() -> Void)? { get set }`

2. Create a `Button` struct conforming to all three

3. Create functions that accept specific protocol combinations:
   - `applyTheme(to view: Themeable)`
   - `setupInteraction(for view: Tappable & Animatable)`
   - `configureView(_ view: Themeable & Tappable & Animatable)`

4. Demonstrate how protocol composition makes your API flexible

**Challenge:** Add default implementations in protocol extensions and show dynamic vs static dispatch differences.

---

## 🔗 Key Takeaways

1. **Protocols define contracts** - They specify what a type must do, not how
2. **Protocol-Oriented Programming** - Swift's preferred paradigm over class inheritance
3. **Protocol extensions** - Provide default implementations and add functionality
4. **Multiple conformance** - Types can conform to multiple protocols (flexibility)
5. **Associated types** - Enable generic protocols with type placeholders
6. **Protocol composition** - Combine protocols with `&` for precise requirements
7. **Dynamic vs static dispatch** - Methods in protocol definitions use dynamic dispatch
8. **Value type friendly** - Protocols work with structs, classes, and enums
9. **Standard library uses protocols** - Equatable, Hashable, Codable, Sequence, etc.
10. **Design principle** - "Program to an interface, not an implementation"

**Remember:** Protocols are the foundation of flexible, testable, and maintainable Swift code. Master them early!
