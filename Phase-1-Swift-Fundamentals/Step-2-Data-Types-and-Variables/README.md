# Step 2: Data Types & Variables

## 📚 Description

In Swift, variables and constants are containers that store data. Understanding data types is fundamental because Swift is a type-safe language, meaning every value must have a specific type. Swift provides type inference, so the compiler can often figure out the type automatically, but you can also specify types explicitly.

### What You'll Learn:
- Difference between variables (`var`) and constants (`let`)
- Basic data types: Int, Double, Float, Bool, String, Character
- Type inference vs explicit type annotation
- Type safety and type conversion
- Creating type aliases with `typealias`

### Key Concepts:
- **Variables (`var`)**: Mutable values that can be changed after declaration
- **Constants (`let`)**: Immutable values that cannot be changed once set
- **Type Inference**: Swift automatically determines the type based on the assigned value
- **Type Safety**: Swift ensures you don't accidentally use the wrong type
- **Type Annotation**: Explicitly declaring a variable's type

---

## 💡 Clear Examples

### Example 1: Variables vs Constants

```swift
// Constants (let) - Cannot be changed
let birthYear = 1990
let companyName = "Apple Inc."
let pi = 3.14159

// birthYear = 1995  // ❌ Error: Cannot assign to value: 'birthYear' is a 'let' constant

// Variables (var) - Can be changed
var age = 25
var currentCity = "New York"
var temperature = 72.5

age = 26  // ✅ Works fine
currentCity = "San Francisco"  // ✅ Works fine
temperature = 68.0  // ✅ Works fine

print("Age: \(age)")
print("City: \(currentCity)")
print("Temperature: \(temperature)°F")

/* Output:
Age: 26
City: San Francisco
Temperature: 68.0°F
*/
```

**Explanation:**
- Use `let` by default - only use `var` when you need to change the value
- Constants are safer and allow compiler optimizations
- Swift encourages immutability for better code quality

---

### Example 2: Basic Data Types with Type Inference and Explicit Annotation

```swift
// Type Inference (Swift automatically determines the type)
let name = "John Doe"  // String
let age = 30  // Int
let height = 5.9  // Double
let isStudent = false  // Bool
let grade = "A"  // String (not Character, because of quotes)

// Explicit Type Annotation
let firstName: String = "Jane"
let score: Int = 95
let weight: Double = 150.5
let isPassing: Bool = true
let initial: Character = "J"  // Character needs explicit type or special syntax

// Integer Types
let smallNumber: Int8 = 127  // -128 to 127
let mediumNumber: Int16 = 32767
let largeNumber: Int64 = 9223372036854775807
let positiveNumber: UInt = 100  // Only positive values (0 and above)

// Floating-Point Types
let floatNumber: Float = 3.14  // 32-bit (less precise, 6 decimal digits)
let doubleNumber: Double = 3.14159265359  // 64-bit (more precise, 15 decimal digits)

print("Name: \(name), Age: \(age)")
print("Float: \(floatNumber), Double: \(doubleNumber)")
print("Initial: \(initial)")

/* Output:
Name: John Doe, Age: 30
Float: 3.14, Double: 3.14159265359
Initial: J
*/
```

**Explanation:**
- Swift infers `Double` for decimal numbers by default
- Use explicit types when you need specific integer sizes or Float precision
- `Character` requires explicit annotation or special syntax

---

### Example 3: Type Safety and Type Conversion

```swift
// Type Safety - Prevents mixing incompatible types
let age: Int = 25
let price: Double = 99.99
// let total = age + price  // ❌ Error: Cannot add Int and Double

// Type Conversion (Explicit Casting)
let total = Double(age) + price  // ✅ Convert Int to Double first
print("Total: \(total)")  // Output: Total: 124.99

// String Conversion
let count = 42
let message = "The answer is \(count)"  // String interpolation
let explicitMessage = "Count: " + String(count)  // Explicit conversion

print(message)  // Output: The answer is 42
print(explicitMessage)  // Output: Count: 42

// Numeric Conversions
let intValue: Int = 100
let doubleValue = Double(intValue)  // Int → Double
let floatValue = Float(intValue)  // Int → Float

let decimalValue = 42.8
let convertedInt = Int(decimalValue)  // Double → Int (truncates decimal)
print("Converted: \(convertedInt)")  // Output: Converted: 42

// String to Number Conversion (Optional, because it can fail)
let numberString = "123"
if let number = Int(numberString) {
    print("Converted number: \(number)")  // Output: Converted number: 123
}

let invalidString = "abc"
let failedConversion = Int(invalidString)  // Returns nil (failed)
print("Failed: \(failedConversion)")  // Output: Failed: nil

// Type Aliases - Creating custom type names
typealias Age = Int
typealias Distance = Double
typealias Coordinate = (x: Double, y: Double)

let personAge: Age = 30
let marathonDistance: Distance = 26.2
let location: Coordinate = (x: 40.7128, y: -74.0060)

print("Age: \(personAge), Distance: \(marathonDistance) miles")
print("Location: \(location.x), \(location.y)")

/* Output:
Total: 124.99
The answer is 42
Count: 42
Converted: 42
Converted number: 123
Failed: nil
Age: 30, Distance: 26.2 miles
Location: 40.7128, -74.006
*/
```

**Explanation:**
- Swift doesn't automatically convert between types - you must do it explicitly
- Type conversion can fail (e.g., String to Int), returning `nil`
- `typealias` creates readable custom type names for existing types
- Always convert types explicitly to avoid errors

---

## 🎯 2025 Interview Questions

### Question 1: When should you use `let` vs `var` in Swift? What are the performance implications?

**Answer:**
**Use `let` (constants) when:**
- The value won't change after initialization
- You want to prevent accidental modifications
- For better performance and compiler optimizations

**Use `var` (variables) when:**
- The value needs to change during program execution
- You're accumulating results in loops
- Managing state that updates over time

**Performance Implications:**
- `let` allows the compiler to make assumptions about immutability, enabling optimizations
- `let` is thread-safe by nature (no synchronization needed)
- `var` may require additional checks and memory management
- Modern Swift compilers are smart, but `let` is still preferred for clarity and intent

**Best Practice (2025):**
```swift
// ✅ Good: Start with let, change to var only if needed
let userName = "Alice"
let maxAttempts = 3

// ✅ Good: Use var when value changes
var currentAttempts = 0
currentAttempts += 1

// ❌ Avoid: Using var when let would work
var appName = "MyApp"  // Should be let if it never changes
```

**Modern Swift (2025) emphasizes:**
- Immutability by default
- Explicit mutability only when necessary
- Leveraging Swift's value semantics

---

### Question 2: Why does Swift use `Double` instead of `Float` by default for decimal numbers?

**Answer:**
Swift defaults to `Double` for several reasons:

1. **Precision**: 
   - `Double`: 64-bit, ~15-17 significant digits
   - `Float`: 32-bit, ~6-7 significant digits
   
2. **Modern Hardware**: Today's processors handle 64-bit operations efficiently, so performance difference is minimal

3. **Accuracy**: Most real-world calculations benefit from Double's precision

4. **API Consistency**: Apple's frameworks predominantly use `Double`

**When to use Float:**
- Working with large arrays where memory is critical (graphics, ML)
- Interacting with APIs that specifically require Float
- Performance-critical code on memory-constrained devices

**Example:**
```swift
// Swift infers Double
let price = 19.99  // Double
let tax = 0.08  // Double

// Explicit Float (when needed)
let coordinate: Float = 40.7128  // For GPU/graphics work
```

**2025 Context:** With Apple Silicon (M1/M2/M3/M4) chips and modern iOS devices, `Double` performance is excellent, reinforcing its use as the default.

---

### Question 3: Explain type inference in Swift. Does it impact runtime performance?

**Answer:**
**Type Inference** is Swift's ability to automatically deduce the type of a variable/constant from its initial value.

**How it works:**
```swift
// Compiler infers types at compile time
let name = "Alice"  // Inferred as String
let age = 25  // Inferred as Int
let height = 5.7  // Inferred as Double
let isActive = true  // Inferred as Bool
```

**Performance Impact:**
- **Zero runtime impact**: Type inference happens at compile time
- The compiled binary is identical whether you use inference or explicit types
- It's purely a developer convenience feature

**Benefits:**
- Cleaner, more readable code
- Less boilerplate
- Still maintains type safety

**When to use explicit types:**
```swift
// Explicit when inference might be wrong
let percentage: Float = 0.5  // Without annotation, would be Double
let character: Character = "A"  // Without annotation, would be String

// Explicit for clarity in complex code
let userRole: UserRoleType = fetchUserRole()  // Makes intent clear
```

**Best Practice (2025):** Use type inference by default, explicit types for clarity or when inference differs from your intent.

---

### Question 4: What is the difference between `Int` and `UInt` in Swift? When should each be used?

**Answer:**
**Int (Signed Integer):**
- Can store both positive and negative numbers
- Range on 64-bit: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807
- Default choice for integers

**UInt (Unsigned Integer):**
- Can only store positive numbers (0 and above)
- Range on 64-bit: 0 to 18,446,744,073,709,551,615
- Doubles the positive range by eliminating negative values

**When to use Int (most of the time):**
```swift
var score = -10  // Can go negative
var temperature = -5  // Can be negative
var indexPosition = 0  // Even though it's positive, Int is standard
```

**When to use UInt (rarely):**
```swift
// Interfacing with C APIs that require unsigned
var memoryAddress: UInt = 0x7FFF

// Working with binary operations or bit manipulation
let flags: UInt8 = 0b11010010
```

**Apple's Guidelines (2025):**
- Use `Int` even for values that won't be negative (for consistency)
- Use `UInt` only when necessary (system APIs, specific algorithms)
- Mixing Int and UInt requires explicit conversion

**Why Int is preferred:**
```swift
let values: [Int] = [1, 2, 3, 4, 5]
print(values.count)  // Returns Int, not UInt

// Mixing causes friction
let uintValue: UInt = 100
let intValue: Int = 50
// let sum = uintValue + intValue  // ❌ Error: Cannot mix
let sum = Int(uintValue) + intValue  // ✅ Requires conversion
```

---

### Question 5: Explain `typealias` in Swift. Provide real-world use cases from iOS development (2025).

**Answer:**
**`typealias`** creates an alternative name for an existing type, improving code readability and maintainability.

**Syntax:**
```swift
typealias NewName = ExistingType
```

**Real-World Use Cases (2025):**

**1. Simplifying Complex Types:**
```swift
// SwiftUI: Simplifying closure types
typealias CompletionHandler = (Result<Data, Error>) -> Void
typealias UserDataCallback = (User?, NetworkError?) -> Void

// Using it
func fetchUserData(completion: CompletionHandler) {
    // Network call
}
```

**2. Making Code More Semantic:**
```swift
// iOS App Development
typealias UserID = String
typealias Timestamp = Double
typealias Coordinate = (latitude: Double, longitude: Double)

func getUserProfile(for userID: UserID) -> UserProfile {
    // Implementation
}

let location: Coordinate = (latitude: 37.7749, longitude: -122.4194)
```

**3. Working with Combine Framework:**
```swift
import Combine

typealias UserPublisher = AnyPublisher<User, Never>
typealias DataPublisher = AnyPublisher<Data, NetworkError>

func getCurrentUser() -> UserPublisher {
    // Return publisher
}
```

**4. Protocols and Generics:**
```swift
// Before typealias (verbose)
func process<T: Codable & Identifiable>(items: [T]) -> [T] {
    // Implementation
}

// With typealias (cleaner)
typealias CodableIdentifiable = Codable & Identifiable
func process<T: CodableIdentifiable>(items: [T]) -> [T] {
    // Implementation
}
```

**5. SwiftUI State Management (2025 patterns):**
```swift
import SwiftUI

typealias ViewState<T> = Result<T, AppError>
typealias AsyncData<T> = Task<T, Error>

@State private var userData: ViewState<User> = .failure(.loading)
```

**Benefits:**
- Improves code documentation (intent is clearer)
- Reduces repetition
- Makes refactoring easier (change alias definition, not every usage)
- Creates domain-specific language in your codebase

**Note:** `typealias` is compile-time only - it doesn't create a new type, just an alias. The underlying type remains the same.

---

## 📝 Practice Exercises

1. Create variables and constants for a person's profile: name, age, email, is_verified
2. Practice type conversions between Int, Double, Float, and String
3. Create a program that calculates area of a rectangle (use appropriate types)
4. Experiment with different integer types (Int8, Int16, Int32, Int64) and their limits
5. Create meaningful type aliases for a simple shopping cart system

---

## 🔗 Navigation
- [← Previous: Step 1 - Setup & Basic Syntax](../Step-1-Setup-and-Basic-Syntax/README.md)
- [→ Next: Step 3 - Operators](../Step-3-Operators/README.md)
