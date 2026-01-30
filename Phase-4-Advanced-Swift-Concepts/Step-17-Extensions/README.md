# Step 17: Extensions

## 📚 Description

**Extensions** add new functionality to existing types (classes, structs, enums, protocols) without modifying their original source code. This powerful feature enables you to extend types from the Swift standard library, third-party frameworks, or your own code. Extensions are fundamental to Swift's design philosophy and enable clean, organized code.

### What You'll Learn:
- Adding computed properties to existing types
- Adding new methods (instance and type methods)
- Adding new initializers
- Protocol conformance via extensions
- Nested types in extensions
- Extending generic types with constraints
- Best practices and limitations

### Key Concepts:
- **Extension**: Adds functionality to existing types
- **Retroactive Modeling**: Add protocol conformance to types you didn't write
- **Default Implementation**: Protocol extensions provide default behavior
- **Constrained Extension**: Extensions with `where` clauses
- **Cannot add stored properties**: Only computed properties

### Benefits:
- **Code Organization**: Group related functionality
- **Separation of Concerns**: Keep protocol conformance separate
- **Extend Anything**: Even types you don't own (String, Int, Array)
- **Maintain Original Source**: No need to modify existing code
- **Backward Compatibility**: Add features without breaking changes

---

## 💡 Clear Examples

### Example 1: Extending Standard Library Types

```swift
// ========== EXTENDING STRING ==========

extension String {
    // Computed property
    var isValidEmail: Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let predicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        return predicate.evaluate(with: self)
    }
    
    // Method
    func trimmed() -> String {
        return self.trimmingCharacters(in: .whitespacesAndNewlines)
    }
    
    // Subscript
    subscript(offset: Int) -> Character {
        return self[index(startIndex, offsetBy: offset)]
    }
    
    // Another method
    func truncate(to length: Int, addEllipsis: Bool = true) -> String {
        if self.count <= length {
            return self
        }
        let truncated = String(self.prefix(length))
        return addEllipsis ? truncated + "..." : truncated
    }
}

// ========== USAGE ==========

let email = "test@example.com"
print(email.isValidEmail)  // true

let text = "  Hello World  "
print(text.trimmed())  // "Hello World"

let name = "Swift"
print(name[0])  // "S"

let longText = "This is a very long text"
print(longText.truncate(to: 10))  // "This is a..."

// ========== EXTENDING INT ==========

extension Int {
    var squared: Int {
        return self * self
    }
    
    var cubed: Int {
        return self * self * self
    }
    
    func times(_ closure: () -> Void) {
        for _ in 0..<self {
            closure()
        }
    }
    
    var isEven: Bool {
        return self % 2 == 0
    }
    
    var isPrime: Bool {
        guard self > 1 else { return false }
        guard self != 2 else { return true }
        guard !isEven else { return false }
        
        let sqrtValue = Int(Double(self).squareRoot())
        for i in stride(from: 3, through: sqrtValue, by: 2) {
            if self % i == 0 {
                return false
            }
        }
        return true
    }
}

// ========== USAGE ==========

print(5.squared)  // 25
print(3.cubed)    // 27

3.times {
    print("Hello")
}
// Output: Hello (3 times)

print(4.isEven)   // true
print(7.isPrime)  // true

// ========== EXTENDING ARRAY ==========

extension Array where Element: Numeric {
    var sum: Element {
        return reduce(0, +)
    }
    
    var average: Double {
        guard !isEmpty else { return 0 }
        let total = self.compactMap { $0 as? Double }.reduce(0, +)
        return total / Double(count)
    }
}

let numbers = [1, 2, 3, 4, 5]
print(numbers.sum)      // 15
print(numbers.average)  // 3.0
```

**Key Points:**
- Extend any type, including those from the standard library
- Add computed properties, methods, subscripts
- Use `where` clauses for constrained extensions
- Cannot add stored properties

---

### Example 2: Protocol Conformance Through Extensions

```swift
// ========== SEPARATING PROTOCOL CONFORMANCE ==========

struct User {
    var id: String
    var name: String
    var email: String
    var age: Int
}

// Main implementation
extension User {
    func greet() -> String {
        return "Hello, I'm \(name)"
    }
}

// Equatable conformance
extension User: Equatable {
    static func == (lhs: User, rhs: User) -> Bool {
        return lhs.id == rhs.id
    }
}

// Hashable conformance
extension User: Hashable {
    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}

// CustomStringConvertible conformance
extension User: CustomStringConvertible {
    var description: String {
        return "User(name: \(name), email: \(email))"
    }
}

// Codable conformance (automatic synthesis)
extension User: Codable {}

// Custom protocol
protocol Validatable {
    func validate() -> Bool
}

extension User: Validatable {
    func validate() -> Bool {
        return !name.isEmpty && 
               email.contains("@") && 
               age >= 0
    }
}

// ========== USAGE ==========

let user1 = User(id: "1", name: "Alice", email: "alice@example.com", age: 25)
let user2 = User(id: "1", name: "Alice Smith", email: "alice@example.com", age: 25)

print(user1 == user2)        // true (same id)
print(user1.description)     // User(name: Alice, email: alice@example.com)
print(user1.validate())      // true

// Works in Sets and Dictionaries (Hashable)
var users: Set<User> = [user1, user2]
print(users.count)  // 1 (same hash)

// Codable
let encoder = JSONEncoder()
if let data = try? encoder.encode(user1) {
    print("Encoded successfully")
}

// ========== CONDITIONAL CONFORMANCE ==========

extension Array: Validatable where Element: Validatable {
    func validate() -> Bool {
        return allSatisfy { $0.validate() }
    }
}

let userList = [user1, user2]
print(userList.validate())  // true (all users valid)
```

**Key Points:**
- Organize code by keeping protocol conformance in separate extensions
- Add conformance to types you didn't create
- Conditional conformance with `where` clauses
- Cleaner, more maintainable code structure

---

### Example 3: Extending Your Own Types and Generic Constraints

```swift
// ========== ORGANIZING CODE WITH EXTENSIONS ==========

struct Calculator {
    // Basic structure
}

// Basic operations
extension Calculator {
    func add(_ a: Double, _ b: Double) -> Double {
        return a + b
    }
    
    func subtract(_ a: Double, _ b: Double) -> Double {
        return a - b
    }
}

// Advanced operations
extension Calculator {
    func power(_ base: Double, _ exponent: Double) -> Double {
        return pow(base, exponent)
    }
    
    func squareRoot(_ value: Double) -> Double {
        return sqrt(value)
    }
}

// Statistical operations
extension Calculator {
    func mean(_ values: [Double]) -> Double {
        guard !values.isEmpty else { return 0 }
        return values.reduce(0, +) / Double(values.count)
    }
    
    func median(_ values: [Double]) -> Double? {
        let sorted = values.sorted()
        guard !sorted.isEmpty else { return nil }
        
        let middle = sorted.count / 2
        if sorted.count % 2 == 0 {
            return (sorted[middle - 1] + sorted[middle]) / 2
        } else {
            return sorted[middle]
        }
    }
}

let calc = Calculator()
print(calc.add(5, 3))           // 8.0
print(calc.power(2, 3))         // 8.0
print(calc.mean([1, 2, 3, 4]))  // 2.5

// ========== EXTENDING ENUMS ==========

enum HTTPMethod {
    case get
    case post
    case put
    case delete
}

extension HTTPMethod {
    var requiresBody: Bool {
        switch self {
        case .get, .delete:
            return false
        case .post, .put:
            return true
        }
    }
    
    var stringValue: String {
        switch self {
        case .get: return "GET"
        case .post: return "POST"
        case .put: return "PUT"
        case .delete: return "DELETE"
        }
    }
}

let method = HTTPMethod.post
print(method.stringValue)      // "POST"
print(method.requiresBody)     // true

// ========== GENERIC EXTENSIONS WITH CONSTRAINTS ==========

struct Stack<Element> {
    private var items: [Element] = []
    
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
}

// Extension only for Equatable elements
extension Stack where Element: Equatable {
    func contains(_ item: Element) -> Bool {
        return items.contains(item)
    }
    
    func index(of item: Element) -> Int? {
        return items.firstIndex(of: item)
    }
}

// Extension only for numeric elements
extension Stack where Element: Numeric {
    func sum() -> Element {
        return items.reduce(0, +)
    }
}

// Extension only for comparable elements
extension Stack where Element: Comparable {
    func sorted() -> [Element] {
        return items.sorted()
    }
    
    func max() -> Element? {
        return items.max()
    }
}

var intStack = Stack<Int>()
intStack.push(1)
intStack.push(2)
intStack.push(3)

print(intStack.contains(2))   // true (Equatable)
print(intStack.sum())         // 6 (Numeric)
print(intStack.max() ?? 0)    // 3 (Comparable)

var stringStack = Stack<String>()
stringStack.push("Swift")
stringStack.push("iOS")

print(stringStack.sorted())   // ["iOS", "Swift"] (Comparable)
// print(stringStack.sum())   // ❌ Error: String is not Numeric

// ========== ADDING INITIALIZERS ==========

struct Temperature {
    var celsius: Double
}

extension Temperature {
    // Add convenience initializer
    init(fahrenheit: Double) {
        self.celsius = (fahrenheit - 32) * 5/9
    }
    
    init(kelvin: Double) {
        self.celsius = kelvin - 273.15
    }
    
    var fahrenheit: Double {
        return celsius * 9/5 + 32
    }
    
    var kelvin: Double {
        return celsius + 273.15
    }
}

let temp1 = Temperature(celsius: 25)
let temp2 = Temperature(fahrenheit: 77)
let temp3 = Temperature(kelvin: 298.15)

print(temp1.fahrenheit)  // 77.0
print(temp2.celsius)     // 25.0
print(temp3.celsius)     // 25.0
```

**Key Points:**
- Organize functionality across multiple extensions
- Generic extensions with `where` constraints
- Add initializers (convenience) to structs
- Extend enums with computed properties and methods

---

## 🎯 Interview Questions

### Q1: What are the limitations of extensions in Swift? What can and cannot be done?

**Answer:**

**CAN DO in Extensions:**
✅ Add computed properties (instance and type)
✅ Add instance methods and type methods
✅ Add new initializers (convenience initializers for classes, any for structs)
✅ Add subscripts
✅ Add nested types
✅ Make existing type conform to protocol
✅ Provide default protocol implementations
✅ Add generic constraints with `where`

**CANNOT DO in Extensions:**
❌ Add stored properties
❌ Add property observers to existing properties
❌ Override existing methods or properties
❌ Add designated initializers to classes
❌ Add deinitializers

**Example showing limitations:**
```swift
struct Person {
    var name: String
}

extension Person {
    // ✅ ALLOWED: Computed property
    var uppercasedName: String {
        return name.uppercased()
    }
    
    // ❌ ERROR: Cannot add stored property
    // var age: Int = 0
    
    // ✅ ALLOWED: Method
    func greet() {
        print("Hello, \(name)")
    }
    
    // ✅ ALLOWED: Initializer (struct)
    init(firstName: String, lastName: String) {
        self.name = "\(firstName) \(lastName)"
    }
}

class Vehicle {
    var brand: String
    
    init(brand: String) {
        self.brand = brand
    }
}

extension Vehicle {
    // ✅ ALLOWED: Convenience initializer
    convenience init() {
        self.init(brand: "Unknown")
    }
    
    // ❌ ERROR: Cannot add designated initializer
    // init(brand: String, year: Int) {
    //     self.brand = brand
    // }
    
    // ❌ ERROR: Cannot override
    // override func description() -> String {
    //     return brand
    // }
}
```

**Why stored properties aren't allowed:**
- Would change memory layout
- Break existing code expectations
- Complicate initialization
- Solution: Use computed properties or associated objects (Objective-C)

---

### Q2: Explain retroactive modeling and why it's powerful. Provide real-world examples.

**Answer:**

**Retroactive Modeling** is the ability to make types conform to protocols even if you didn't write the original type. This is unique to Swift and extremely powerful.

**Example 1: Making Int conform to custom protocol**
```swift
protocol Describable {
    func describe() -> String
}

// Add protocol conformance to standard library type
extension Int: Describable {
    func describe() -> String {
        return "The number \(self)"
    }
}

print(42.describe())  // "The number 42"
```

**Example 2: Real-world networking scenario**
```swift
// Your protocol
protocol APIConvertible {
    func toJSON() -> [String: Any]
}

// Make standard library types conform
extension String: APIConvertible {
    func toJSON() -> [String: Any] {
        return ["value": self]
    }
}

extension Int: APIConvertible {
    func toJSON() -> [String: Any] {
        return ["value": self]
    }
}

extension Array: APIConvertible where Element: APIConvertible {
    func toJSON() -> [String: Any] {
        return ["items": self.map { $0.toJSON() }]
    }
}

// Now all these types work with your API code
func sendToAPI<T: APIConvertible>(_ data: T) {
    let json = data.toJSON()
    print("Sending: \(json)")
}

sendToAPI("Hello")
sendToAPI(42)
sendToAPI([1, 2, 3])
```

**Example 3: SwiftUI integration**
```swift
protocol Identifiable {
    var id: String { get }
}

// Make your models work with SwiftUI List
extension String: Identifiable {
    var id: String { return self }
}

// Now String can be used directly in Lists without wrapper
// let items = ["Apple", "Banana", "Cherry"]
// List(items) { item in Text(item) }
```

**Why it's powerful:**
1. **Extend third-party code** without modifying source
2. **Integrate with protocols** across your app
3. **Standard library integration** - make types work with your systems
4. **Clean architecture** - separate concerns without wrapper classes
5. **Generic constraints** - enable constrained extensions

**Comparison with other languages:**
```swift
// Swift: Retroactive modeling
extension Int: CustomProtocol { }

// Java/C#: Requires wrapper class (Adapter pattern)
class IntWrapper implements CustomInterface {
    private int value;
    // ... adapter code
}
```

---

### Q3: What is the difference between extending a protocol and extending a concrete type? When would you use each?

**Answer:**

**Extending a Protocol** (Protocol Extension):
Adds default implementations that apply to ALL conforming types.

```swift
protocol Vehicle {
    var speed: Int { get }
    func start()
}

// Protocol extension - applies to ALL vehicles
extension Vehicle {
    // Default implementation
    func start() {
        print("Starting vehicle...")
    }
    
    // Add new functionality
    func describe() -> String {
        return "Vehicle traveling at \(speed) km/h"
    }
    
    var isFast: Bool {
        return speed > 100
    }
}

struct Car: Vehicle {
    var speed: Int
    // Gets start(), describe(), isFast for free
}

struct Bicycle: Vehicle {
    var speed: Int
    // Also gets start(), describe(), isFast
}

let car = Car(speed: 120)
let bike = Bicycle(speed: 20)

print(car.isFast)   // true (inherited from protocol extension)
print(bike.isFast)  // false
```

**Extending a Concrete Type**:
Adds functionality to specific type only.

```swift
struct Car {
    var brand: String
    var speed: Int
}

// Concrete extension - ONLY for Car
extension Car {
    func honk() {
        print("\(brand) says: Beep beep!")
    }
    
    var isLuxury: Bool {
        return ["BMW", "Mercedes", "Audi"].contains(brand)
    }
}

let car = Car(brand: "BMW", speed: 120)
car.honk()  // BMW says: Beep beep!

// Bicycle doesn't have honk() method
```

**When to use Protocol Extensions:**
✅ When you want behavior shared across multiple types
✅ When designing APIs for flexibility
✅ When implementing Protocol-Oriented Programming
✅ When providing default implementations

```swift
protocol Loggable {
    func log()
}

extension Loggable {
    func log() {
        print("Logging: \(self)")
    }
}

// All types get logging for free
struct User: Loggable { var name: String }
struct Product: Loggable { var title: String }
```

**When to use Concrete Type Extensions:**
✅ When functionality is specific to one type
✅ When organizing code for single type
✅ When extending standard library types
✅ When you don't need polymorphism

```swift
extension String {
    var isURL: Bool {
        return hasPrefix("http://") || hasPrefix("https://")
    }
}
```

**Combining both approaches:**
```swift
// Protocol defines contract
protocol Persistable {
    func save()
}

// Protocol extension provides default
extension Persistable {
    func save() {
        print("Saving to database...")
    }
}

// Concrete type can customize
struct User: Persistable {
    var name: String
}

extension User {
    // Override protocol default for this specific type
    func save() {
        print("Saving user \(name) to user table")
    }
    
    // Add User-specific functionality
    func validateEmail() -> Bool {
        // User-specific logic
        return true
    }
}
```

---

### Q4: How do constrained extensions work with generics? Provide examples with `where` clauses.

**Answer:**

**Constrained Extensions** allow you to add functionality only when generic types meet specific requirements using `where` clauses.

**Example 1: Numeric constraints**
```swift
extension Array where Element: Numeric {
    func sum() -> Element {
        return reduce(0, +)
    }
    
    var average: Double {
        guard !isEmpty else { return 0 }
        // Convert to Double for averaging
        return Double(truncating: sum() as! NSNumber) / Double(count)
    }
}

let integers = [1, 2, 3, 4, 5]
print(integers.sum())      // 15

let doubles = [1.5, 2.5, 3.5]
print(doubles.sum())       // 7.5

// Doesn't work with non-numeric types
let strings = ["a", "b", "c"]
// strings.sum()  // ❌ Error: String is not Numeric
```

**Example 2: Equatable constraints**
```swift
extension Array where Element: Equatable {
    func removeDuplicates() -> [Element] {
        var result: [Element] = []
        for item in self {
            if !result.contains(item) {
                result.append(item)
            }
        }
        return result
    }
    
    func allEqual() -> Bool {
        guard let first = first else { return true }
        return allSatisfy { $0 == first }
    }
}

let numbers = [1, 2, 2, 3, 3, 3, 4]
print(numbers.removeDuplicates())  // [1, 2, 3, 4]

let sameNumbers = [5, 5, 5]
print(sameNumbers.allEqual())  // true
```

**Example 3: Multiple constraints**
```swift
extension Array where Element: Comparable & Hashable {
    func sortedUnique() -> [Element] {
        return Array(Set(self)).sorted()
    }
    
    func secondLargest() -> Element? {
        let unique = Set(self).sorted()
        guard unique.count >= 2 else { return nil }
        return unique[unique.count - 2]
    }
}

let items = [3, 1, 4, 1, 5, 9, 2, 6, 5]
print(items.sortedUnique())      // [1, 2, 3, 4, 5, 6, 9]
print(items.secondLargest() ?? 0) // 6
```

**Example 4: Protocol constraints**
```swift
protocol Identifiable {
    var id: String { get }
}

extension Array where Element: Identifiable {
    func find(byId id: String) -> Element? {
        return first { $0.id == id }
    }
    
    func groupById() -> [String: [Element]] {
        return Dictionary(grouping: self) { $0.id }
    }
}

struct User: Identifiable {
    var id: String
    var name: String
}

let users = [
    User(id: "1", name: "Alice"),
    User(id: "2", name: "Bob"),
    User(id: "1", name: "Alice2")
]

print(users.find(byId: "1")?.name ?? "")  // Alice
print(users.groupById().keys)             // ["1", "2"]
```

**Example 5: Optional constraints**
```swift
extension Array where Element == Optional<String> {
    func compactAndTrim() -> [String] {
        return compactMap { $0?.trimmingCharacters(in: .whitespaces) }
            .filter { !$0.isEmpty }
    }
}

let optionalStrings: [String?] = ["  hello  ", nil, "  ", "world", nil]
print(optionalStrings.compactAndTrim())  // ["hello", "world"]
```

**Example 6: Nested type constraints**
```swift
extension Dictionary where Key == String, Value: Numeric {
    func sumValues() -> Value {
        return values.reduce(0, +)
    }
}

let scores: [String: Int] = ["Alice": 95, "Bob": 87, "Charlie": 92]
print(scores.sumValues())  // 274

let prices: [String: Double] = ["Apple": 1.5, "Banana": 0.8]
print(prices.sumValues())  // 2.3
```

**Benefits of constrained extensions:**
- Type-safe functionality
- Cleaner API (methods only available when applicable)
- Compiler enforces constraints
- Better code organization

---

### Q5: Can you explain extension shadowing and method dispatch with extensions? What are the gotchas?

**Answer:**

**Extension Shadowing** occurs when methods in the base type and extension have the same signature. Understanding dispatch is crucial.

**Case 1: Extensions DON'T override**
```swift
class Animal {
    func makeSound() {
        print("Generic animal sound")
    }
}

extension Animal {
    func makeSound() {  // ❌ This doesn't override!
        print("Extension animal sound")
    }
}

let animal = Animal()
animal.makeSound()  // "Generic animal sound" (original wins)
```

**Case 2: Extensions CAN add new methods**
```swift
class Animal {
    func eat() {
        print("Eating...")
    }
}

extension Animal {
    func sleep() {  // ✅ New method - works fine
        print("Sleeping...")
    }
}

let animal = Animal()
animal.eat()    // Eating...
animal.sleep()  // Sleeping...
```

**Case 3: Dynamic dispatch with protocols**
```swift
protocol Animal {
    func makeSound()  // Protocol requirement
}

extension Animal {
    func makeSound() {
        print("Default sound")
    }
    
    func move() {  // Not in protocol
        print("Default move")
    }
}

struct Dog: Animal {
    func makeSound() {
        print("Woof!")
    }
    
    func move() {
        print("Dog running")
    }
}

// As concrete type
let dog = Dog()
dog.makeSound()  // "Woof!" ✅
dog.move()       // "Dog running" ✅

// As protocol type
let animal: Animal = Dog()
animal.makeSound()  // "Woof!" ✅ (dynamic dispatch - in protocol)
animal.move()       // "Default move" ❌ (static dispatch - not in protocol!)
```

**Case 4: Extension priority order**
```swift
protocol A {
    func test()
}

protocol B {
    func test()
}

extension A {
    func test() {
        print("A default")
    }
}

extension B {
    func test() {
        print("B default")
    }
}

struct MyType: A, B {
    // Must implement test() - ambiguous otherwise
    func test() {
        print("MyType implementation")
    }
}

let obj = MyType()
obj.test()  // "MyType implementation"

let objA: A = MyType()
objA.test()  // "MyType implementation" (dynamic dispatch)
```

**Case 5: Struct vs Class extension behavior**
```swift
struct ValueType {
    func original() {
        print("Original method")
    }
}

extension ValueType {
    func extended() {
        print("Extended method")
    }
}

// Both work identically
var value = ValueType()
value.original()   // Original method
value.extended()   // Extended method

class ReferenceType {
    func original() {
        print("Original method")
    }
}

extension ReferenceType {
    func extended() {
        print("Extended method")
    }
}

// Extensions can't override, but subclasses can
class Subclass: ReferenceType {
    override func original() {  // ✅ Can override
        print("Subclass override")
    }
    
    // Can't override extended() - it's not virtual
}
```

**Key Takeaways:**
1. **Extensions DON'T override** existing methods
2. **Protocol requirements use dynamic dispatch** (polymorphic)
3. **Extension-only methods use static dispatch** (not polymorphic)
4. **Always declare methods in protocol** if you want runtime polymorphism
5. **Type constraints** determine which extension applies

**Best Practice:**
```swift
// ✅ GOOD: Method in protocol, default in extension
protocol Drawable {
    func draw()  // In protocol - dynamic dispatch
}

extension Drawable {
    func draw() {
        print("Default draw")
    }
}

// ❌ BAD: Method only in extension
protocol Drawable {
    // draw() not here
}

extension Drawable {
    func draw() {  // Static dispatch - less flexible
        print("Default draw")
    }
}
```

---

## 📝 Practice Exercises

### Exercise 1: Standard Library Extensions
Create useful extensions for Swift standard library types.

**Requirements:**
1. Extend `String` with:
   - `isValidURL` property
   - `capitalizingFirstLetter()` method
   - `toDate(format: String)` method
   - `words` property (array of words)

2. Extend `Array` with:
   - `chunked(into size: Int) -> [[Element]]` (split into subarrays)
   - `removeDuplicates()` where `Element: Hashable`
   - `shuffled()` method

3. Extend `Int` with:
   - `factorial` property
   - `isPerfectSquare` property
   - `roman` property (converts to Roman numerals)

**Challenge:** Add documentation comments and unit tests for each extension.

---

### Exercise 2: Protocol-Oriented Repository Pattern
Build a generic repository system using protocol extensions.

**Requirements:**
1. Create a `Repository` protocol with:
   ```swift
   protocol Repository {
       associatedtype Model: Identifiable
       var items: [Model] { get set }
   }
   ```

2. Add protocol extension with default implementations:
   - `func find(byId id:) -> Model?`
   - `func findAll() -> [Model]`
   - `func save(_ model: Model)`
   - `func delete(byId id:)`

3. Create constrained extensions:
   - Where `Model: Comparable`: add `sortedItems()` method
   - Where `Model: Codable`: add `saveToJSON()` and `loadFromJSON()`

4. Implement concrete repositories for `User`, `Product`, `Order`

**Challenge:** Add pagination support for repositories with large datasets.

---

### Exercise 3: Domain-Specific Extensions
Create a math library using extensions.

**Requirements:**
1. Extend `Double` with trigonometric helpers:
   - `degrees` and `radians` conversion properties
   - `sin`, `cos`, `tan` properties
   - `rounded(to places: Int)` method

2. Extend `Array where Element == Double`:
   - `standardDeviation` property
   - `variance` property
   - `normalize()` method (scale to 0-1)

3. Create a `Matrix` struct and extend it with:
   - Subscript access `matrix[row, column]`
   - Addition/subtraction operations
   - Transpose functionality

4. Extend `Range where Bound == Int`:
   - `random()` method
   - `array` property

**Challenge:** Add performance optimizations using constrained extensions.

---

## 🔗 Key Takeaways

1. **Extensions add functionality** - No need to modify original source code
2. **Organize with extensions** - Separate concerns, group related functionality
3. **Cannot add stored properties** - Only computed properties allowed
4. **Retroactive modeling** - Add protocol conformance to any type
5. **Protocol extensions** - Provide default implementations for all conforming types
6. **Constrained extensions** - Use `where` clauses for generic type constraints
7. **No overriding** - Extensions don't override existing methods
8. **Dynamic dispatch** - Only for protocol requirements, not extension-only methods
9. **Standard library extensions** - Powerful way to enhance built-in types
10. **Code organization** - Keep protocol conformance in separate extensions

**Best Practices:**
- Use extensions to separate protocol conformances
- Group related functionality in focused extensions
- Provide clear extension names and organization
- Document public extensions thoroughly
- Be careful with method dispatch and shadowing

**Remember:** Extensions are one of Swift's most powerful features for code organization and reuse!
