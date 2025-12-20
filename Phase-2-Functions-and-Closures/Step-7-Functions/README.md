# Step 7: Functions

## 📚 Description

Functions are self-contained blocks of code that perform specific tasks. They are fundamental building blocks in Swift programming, allowing you to organize code into reusable pieces. Swift functions are flexible and powerful, with features like named parameters, default values, and the ability to return multiple values.

### What You'll Learn:
- Function syntax and structure
- Parameters and return types
- External and internal parameter names
- Default parameter values
- Variadic parameters (`...`)
- `inout` parameters (pass by reference)
- Function types as variables
- Nested functions

### Key Concepts:
- **Function Declaration**: Define reusable code blocks
- **Parameters**: Input values passed to functions
- **Return Types**: Values functions return to callers
- **Function Types**: Functions can be stored in variables and passed around
- **First-Class Functions**: Functions are treated as values in Swift

---

## 💡 Clear Examples

### Example 1: Basic Functions and Parameters

```swift
// Simple function with no parameters or return value
func greet() {
    print("Hello, World!")
}

greet()  // Output: Hello, World!

// Function with parameter
func greet(name: String) {
    print("Hello, \(name)!")
}

greet(name: "Alice")  // Output: Hello, Alice!

// Function with return value
func add(a: Int, b: Int) -> Int {
    return a + b
}

let sum = add(a: 5, b: 3)
print("Sum: \(sum)")  // Output: Sum: 8

// Multiple parameters with different types
func calculateArea(width: Double, height: Double) -> Double {
    return width * height
}

let area = calculateArea(width: 10.5, height: 8.2)
print("Area: \(area)")  // Output: Area: 86.1

// Function with multiple return values (tuple)
func getMinMax(numbers: [Int]) -> (min: Int, max: Int)? {
    guard !numbers.isEmpty else { return nil }
    
    var currentMin = numbers[0]
    var currentMax = numbers[0]
    
    for number in numbers[1...] {
        if number < currentMin {
            currentMin = number
        }
        if number > currentMax {
            currentMax = number
        }
    }
    
    return (currentMin, currentMax)
}

if let bounds = getMinMax(numbers: [8, -6, 2, 109, 3, 71]) {
    print("Min: \(bounds.min), Max: \(bounds.max)")
    // Output: Min: -6, Max: 109
}

// Void return type (no return value)
func printMessage(_ message: String) -> Void {
    print(message)
}
// Equivalent to:
func printMessage2(_ message: String) {
    print(message)
}

// Function with early return
func checkAge(age: Int) -> String {
    if age < 0 {
        return "Invalid age"
    }
    if age < 18 {
        return "Minor"
    }
    return "Adult"
}

print(checkAge(age: 25))  // Output: Adult
```

**Explanation:**
- Functions are declared with `func` keyword
- Parameters are specified with `name: Type`
- Return type follows `->` arrow
- Multiple return values use tuples
- Optionals can be returned when result might not exist

---

### Example 2: External vs Internal Parameter Names and Default Values

```swift
// External and Internal Parameter Names
// Syntax: func name(externalName internalName: Type)

// Using external names makes function calls more readable
func greet(person name: String, from hometown: String) {
    print("Hello \(name)! Glad you're visiting from \(hometown).")
}

// Call with external names
greet(person: "Alice", from: "New York")
// Output: Hello Alice! Glad you're visiting from New York.

// Omitting external names with underscore _
func calculateSum(_ a: Int, _ b: Int) -> Int {
    return a + b
}

// No parameter labels needed
let total = calculateSum(10, 20)
print("Total: \(total)")  // Output: Total: 30

// Default Parameter Values
func greet(name: String, greeting: String = "Hello") {
    print("\(greeting), \(name)!")
}

greet(name: "Bob")  // Uses default: Hello, Bob!
greet(name: "Charlie", greeting: "Hi")  // Custom: Hi, Charlie!

// Multiple default values
func makeGreeting(
    name: String,
    title: String = "Mr.",
    greeting: String = "Hello",
    punctuation: String = "!"
) -> String {
    return "\(greeting), \(title) \(name)\(punctuation)"
}

print(makeGreeting(name: "Smith"))
// Output: Hello, Mr. Smith!

print(makeGreeting(name: "Johnson", title: "Dr."))
// Output: Hello, Dr. Johnson!

print(makeGreeting(name: "Brown", greeting: "Hi", punctuation: "."))
// Output: Hi, Mr. Brown.

// Variadic Parameters (accepts zero or more values)
func calculateAverage(_ numbers: Double...) -> Double {
    guard !numbers.isEmpty else { return 0 }
    
    let sum = numbers.reduce(0, +)
    return sum / Double(numbers.count)
}

print(calculateAverage(1, 2, 3, 4, 5))  // Output: 3.0
print(calculateAverage(10, 20))  // Output: 15.0
print(calculateAverage())  // Output: 0.0

// Variadic with other parameters
func buildMessage(prefix: String, values: Int..., suffix: String) -> String {
    let valueString = values.map { String($0) }.joined(separator: ", ")
    return "\(prefix): \(valueString) \(suffix)"
}

print(buildMessage(prefix: "Numbers", values: 1, 2, 3, 4, suffix: "end"))
// Output: Numbers: 1, 2, 3, 4 end

// Real-world example: Logging function
func log(
    _ message: String,
    level: String = "INFO",
    file: String = #file,
    line: Int = #line
) {
    print("[\(level)] \(file):\(line) - \(message)")
}

log("Application started")
// Output: [INFO] /path/to/file.swift:123 - Application started

log("Error occurred", level: "ERROR")
// Output: [ERROR] /path/to/file.swift:124 - Error occurred
```

**Explanation:**
- External names improve readability at call site
- Internal names used within function body
- Underscore `_` omits external name
- Default values make parameters optional
- Variadic parameters accept multiple values
- Default value expressions like `#file` and `#line` are useful for debugging

---

### Example 3: Inout Parameters, Function Types, and Nested Functions

```swift
// INOUT PARAMETERS - Modify parameters (pass by reference)

func swapIntegers(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

var x = 10
var y = 20
print("Before: x=\(x), y=\(y)")  // Before: x=10, y=20

swapIntegers(&x, &y)  // Use & to pass by reference
print("After: x=\(x), y=\(y)")  // After: x=20, y=10

// Inout with arrays
func appendItem(_ item: String, to array: inout [String]) {
    array.append(item)
}

var fruits = ["Apple", "Banana"]
appendItem("Orange", to: &fruits)
print(fruits)  // ["Apple", "Banana", "Orange"]

// Multiple inout parameters
func increment(_ value: inout Int, by amount: inout Int) {
    value += amount
    amount = 0  // Reset amount
}

var count = 5
var step = 3
increment(&count, by: &step)
print("Count: \(count), Step: \(step)")  // Count: 8, Step: 0

// FUNCTION TYPES - Functions as variables

// Define function type: (ParameterTypes) -> ReturnType
var mathOperation: (Int, Int) -> Int

// Assign function to variable
mathOperation = add
print(mathOperation(10, 5))  // 15

// Define and use inline
func multiply(a: Int, b: Int) -> Int {
    return a * b
}

mathOperation = multiply
print(mathOperation(10, 5))  // 50

// Function as parameter
func performOperation(
    _ a: Int,
    _ b: Int,
    using operation: (Int, Int) -> Int
) -> Int {
    return operation(a, b)
}

let result1 = performOperation(10, 5, using: add)
print("Add: \(result1)")  // 15

let result2 = performOperation(10, 5, using: multiply)
print("Multiply: \(result2)")  // 50

// Function returning function
func makeIncrementer(incrementAmount: Int) -> (Int) -> Int {
    func increment(value: Int) -> Int {
        return value + incrementAmount
    }
    return increment
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)
print(incrementByTwo(5))  // 7
print(incrementByTwo(10))  // 12

let incrementByTen = makeIncrementer(incrementAmount: 10)
print(incrementByTen(5))  // 15

// NESTED FUNCTIONS - Functions inside functions

func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int {
        return input + 1
    }
    
    func stepBackward(input: Int) -> Int {
        return input - 1
    }
    
    return backward ? stepBackward : stepForward
}

var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)

print("Counting to zero:")
while currentValue != 0 {
    print("\(currentValue)... ", terminator: "")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// Output: 3... 2... 1... zero!

// Real-world iOS example: Validation function
func createValidator(minLength: Int) -> (String) -> Bool {
    func validate(input: String) -> Bool {
        return input.count >= minLength
    }
    return validate
}

let passwordValidator = createValidator(minLength: 8)
print(passwordValidator("short"))  // false
print(passwordValidator("longenough123"))  // true

// Complex example: Calculator with operations
func calculate(
    operation: String,
    a: Double,
    b: Double
) -> Double? {
    func add() -> Double { return a + b }
    func subtract() -> Double { return a - b }
    func multiply() -> Double { return a * b }
    func divide() -> Double? {
        guard b != 0 else { return nil }
        return a / b
    }
    
    switch operation {
    case "+": return add()
    case "-": return subtract()
    case "*": return multiply()
    case "/": return divide()
    default: return nil
    }
}

if let result = calculate(operation: "+", a: 10, b: 5) {
    print("Result: \(result)")  // 15.0
}
```

**Explanation:**
- `inout` allows modifying parameters (use `&` when calling)
- Functions can be assigned to variables and passed as parameters
- Function types: `(ParameterTypes) -> ReturnType`
- Nested functions have access to outer function's variables
- Functions returning functions enable powerful patterns

---

## 🎯 2025 Interview Questions

### Question 1: Explain the difference between external and internal parameter names in Swift. Why are they useful?

**Answer:**
Swift functions can have **two names for each parameter**: an external name (used at call site) and an internal name (used inside function).

**Syntax:**
```swift
func functionName(externalName internalName: Type) {
    // Use internalName here
}

// Call with external name
functionName(externalName: value)
```

**Example:**
```swift
// With both external and internal names
func greet(person name: String, from hometown: String) {
    print("Hello \(name) from \(hometown)")
}

// Call site is readable
greet(person: "Alice", from: "New York")
```

**Why They're Useful:**

**1. Readability at Call Site:**
```swift
// ❌ Unclear what parameters mean
func move(x: Int, y: Int) { }
move(x: 10, y: 20)  // What do these numbers represent?

// ✅ Clear intent
func move(toX x: Int, toY y: Int) { }
move(toX: 10, toY: 20)  // Obviously moving to position (10, 20)
```

**2. Natural Language-Like Syntax:**
```swift
// Swift aims for readable code
func insert(_ item: String, at index: Int, in array: inout [String]) {
    array.insert(item, at: index)
}

var items = ["A", "B", "C"]
insert("X", at: 1, in: &items)
// Reads like: "Insert X at index 1 in items"
```

**3. Better API Design:**
```swift
// iOS UIView example
func addSubview(_ view: UIView)
// Reads naturally: view.addSubview(mySubview)

// vs unclear alternative:
func addSubview(view: UIView)
// Would read as: view.addSubview(view: mySubview) - redundant!
```

**Omitting External Names:**
```swift
// Use underscore _ to omit external name
func calculateSum(_ a: Int, _ b: Int) -> Int {
    return a + b
}

calculateSum(10, 20)  // Clean for mathematical operations
```

**Real-World iOS Example (2025):**
```swift
// SwiftUI API style
func fetchData(from url: URL, using session: URLSession) async throws -> Data {
    let (data, _) = try await session.data(from: url)
    return data
}

// Clear at call site
let data = try await fetchData(from: apiURL, using: .shared)
```

**Best Practices:**
- Use external names for clarity (default behavior if you only specify one name)
- Omit with `_` for obvious cases (mathematical operations, conversions)
- First parameter often omits external name if function name is descriptive
- Follow Apple's API design guidelines for consistency

---

### Question 2: What are `inout` parameters in Swift? How do they differ from regular parameters?

**Answer:**
**`inout` parameters** allow functions to modify the values of parameters and have those changes persist outside the function. They enable "pass by reference" semantics.

**Regular Parameters (Pass by Value):**
```swift
func tryToModify(value: Int) {
    var value = value  // Must make copy to modify
    value += 10
    // Changes don't affect original
}

var number = 5
tryToModify(value: number)
print(number)  // Still 5
```

**Inout Parameters (Pass by Reference):**
```swift
func modify(value: inout Int) {
    value += 10  // Modifies original
}

var number = 5
modify(value: &number)  // Use & to pass reference
print(number)  // 15 - changed!
```

**How It Works:**
1. Value is **copied** into function (in)
2. Function modifies the copy
3. Modified value is **copied back** to original (out)
4. This is called "copy-in copy-out" or "call by value result"

**Key Differences:**

| Aspect | Regular Parameter | Inout Parameter |
|--------|------------------|-----------------|
| Modification | Cannot modify | Can modify |
| Call syntax | `func(value)` | `func(&value)` |
| Constants | Can pass `let` | Must use `var` |
| Persistence | Changes local only | Changes persist |
| Use case | Read-only | Modify in place |

**Common Use Cases:**

**1. Swapping Values:**
```swift
func swap<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

var x = "Hello"
var y = "World"
swap(&x, &y)
print("\(x), \(y)")  // World, Hello
```

**2. Array Modification:**
```swift
func removeInvalidItems(from array: inout [String]) {
    array = array.filter { !$0.isEmpty }
}

var items = ["Apple", "", "Banana", "", "Cherry"]
removeInvalidItems(from: &items)
print(items)  // ["Apple", "Banana", "Cherry"]
```

**3. Performance Optimization (Large Structs):**
```swift
struct LargeData {
    var values: [Int]  // Large array
    // ... more properties
}

// ❌ Slower: copies entire struct
func processData(_ data: LargeData) -> LargeData {
    var modified = data
    modified.values.append(42)
    return modified
}

// ✅ Faster: modifies in place
func processData(_ data: inout LargeData) {
    data.values.append(42)
}
```

**iOS-Specific Example (2025):**
```swift
// UIKit: Modifying animation parameters
func configureAnimation(
    duration: inout TimeInterval,
    options: inout UIView.AnimationOptions
) {
    if UIAccessibility.isReduceMotionEnabled {
        duration = 0  // Instant for accessibility
        options.insert(.curveLinear)
    }
}

var animDuration: TimeInterval = 0.3
var animOptions: UIView.AnimationOptions = []
configureAnimation(duration: &animDuration, options: &animOptions)

UIView.animate(withDuration: animDuration, options: animOptions) {
    // Animation code
}
```

**Restrictions:**
```swift
// ❌ Cannot pass constants
let constant = 5
// modify(value: &constant)  // Error!

// ❌ Cannot pass literals
// modify(value: &10)  // Error!

// ❌ Cannot pass expressions
var a = 5, b = 10
// modify(value: &(a + b))  // Error!

// ✅ Must pass variable
var variable = 5
modify(value: &variable)  // Works!
```

**Important Notes:**
- Use sparingly - prefer returning new values (functional style)
- Not thread-safe by default
- Can't use with properties computed with `get`/`set`
- Useful for performance-critical code with large data structures

**Modern Alternative (2025):**
```swift
// ✅ Functional style (preferred for most cases)
func increment(_ value: Int) -> Int {
    return value + 1
}

var count = 5
count = increment(count)

// ✅ Use inout only when necessary (multiple modifications)
func validateAndFormat(text: inout String) {
    text = text.trimmingCharacters(in: .whitespaces)
    text = text.lowercased()
    // Multiple modifications - inout makes sense
}
```

---

### Question 3: Explain function types in Swift. How can functions be used as first-class citizens?

**Answer:**
In Swift, **functions are first-class citizens**, meaning they can be:
- Assigned to variables
- Passed as parameters
- Returned from other functions
- Stored in data structures

**Function Types:**

Every function has a type based on its parameters and return value:
```swift
// Function type: (Int, Int) -> Int
func add(a: Int, b: Int) -> Int {
    return a + b
}

// Function type: (String) -> Void
func greet(name: String) {
    print("Hello, \(name)")
}

// Function type: () -> String
func getMessage() -> String {
    return "Hello"
}
```

**1. Assigning Functions to Variables:**
```swift
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func multiply(_ a: Int, _ b: Int) -> Int {
    return a * b
}

// Store function in variable
var operation: (Int, Int) -> Int = add
print(operation(5, 3))  // 8

// Change the function
operation = multiply
print(operation(5, 3))  // 15
```

**2. Functions as Parameters (Higher-Order Functions):**
```swift
func performOperation(
    _ a: Int,
    _ b: Int,
    using operation: (Int, Int) -> Int
) -> Int {
    return operation(a, b)
}

let sum = performOperation(10, 5, using: add)
print(sum)  // 15

let product = performOperation(10, 5, using: multiply)
print(product)  // 50

// Pass inline function
let result = performOperation(10, 5, using: { $0 - $1 })
print(result)  // 5
```

**3. Functions Returning Functions:**
```swift
func makeMultiplier(factor: Int) -> (Int) -> Int {
    func multiply(value: Int) -> Int {
        return value * factor
    }
    return multiply
}

let doubler = makeMultiplier(factor: 2)
print(doubler(5))  // 10
print(doubler(10))  // 20

let tripler = makeMultiplier(factor: 3)
print(tripler(5))  // 15
```

**4. Storing Functions in Collections:**
```swift
let operations: [(Int, Int) -> Int] = [add, multiply]

for operation in operations {
    print(operation(4, 2))
}
// Output: 6, 8
```

**Real-World iOS Examples (2025):**

**1. Button Actions (SwiftUI):**
```swift
import SwiftUI

struct ActionButton: View {
    let title: String
    let action: () -> Void  // Function type as property
    
    var body: some View {
        Button(title, action: action)
    }
}

// Usage
ActionButton(title: "Save") {
    saveBata()
}
```

**2. Networking Completion Handlers:**
```swift
typealias CompletionHandler = (Result<Data, Error>) -> Void

func fetchData(
    from url: URL,
    completion: @escaping CompletionHandler
) {
    URLSession.shared.dataTask(with: url) { data, response, error in
        if let error = error {
            completion(.failure(error))
        } else if let data = data {
            completion(.success(data))
        }
    }.resume()
}

// Usage
fetchData(from: apiURL) { result in
    switch result {
    case .success(let data):
        print("Received \(data.count) bytes")
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

**3. Custom Validators:**
```swift
typealias Validator = (String) -> Bool

struct FormField {
    let name: String
    let validator: Validator
}

let emailValidator: Validator = { input in
    input.contains("@") && input.contains(".")
}

let lengthValidator: Validator = { input in
    input.count >= 6
}

let fields = [
    FormField(name: "Email", validator: emailValidator),
    FormField(name: "Password", validator: lengthValidator)
]

func validate(input: String, using field: FormField) -> Bool {
    return field.validator(input)
}
```

**4. Strategy Pattern:**
```swift
class DataProcessor {
    var processingStrategy: (Data) -> Data
    
    init(strategy: @escaping (Data) -> Data) {
        self.processingStrategy = strategy
    }
    
    func process(_ data: Data) -> Data {
        return processingStrategy(data)
    }
}

// Different strategies
let compressStrategy: (Data) -> Data = { data in
    // Compress data
    return data
}

let encryptStrategy: (Data) -> Data = { data in
    // Encrypt data
    return data
}

let processor = DataProcessor(strategy: compressStrategy)
let processed = processor.process(myData)
```

**Benefits:**
- **Flexibility**: Change behavior at runtime
- **Modularity**: Separate concerns cleanly
- **Testability**: Easy to inject mock functions
- **Functional Programming**: Enable map, filter, reduce patterns

**Modern Swift (2025):**
```swift
// Async function types
typealias AsyncFetcher = () async throws -> Data

func performAsyncOperation(
    using fetcher: AsyncFetcher
) async throws -> Data {
    return try await fetcher()
}

// Usage
try await performAsyncOperation {
    try await URLSession.shared.data(from: url).0
}
```

---

### Question 4: What are variadic parameters in Swift? Provide practical use cases from iOS development.

**Answer:**
**Variadic parameters** allow a function to accept zero or more values of a specified type. They're denoted with three dots `...` after the type.

**Syntax:**
```swift
func functionName(_ values: Type...) {
    // values is an array of Type inside the function
}
```

**Basic Example:**
```swift
func sum(_ numbers: Int...) -> Int {
    return numbers.reduce(0, +)
}

print(sum(1, 2, 3))  // 6
print(sum(1, 2, 3, 4, 5))  // 15
print(sum())  // 0 (empty)
```

**How It Works:**
```swift
func printValues(_ values: String...) {
    print("Type: \(type(of: values))")  // Array<String>
    print("Count: \(values.count)")
    
    for value in values {
        print("- \(value)")
    }
}

printValues("Apple", "Banana", "Cherry")
/* Output:
Type: Array<String>
Count: 3
- Apple
- Banana
- Cherry
*/
```

**Rules:**
1. Function can have **only one** variadic parameter
2. Variadic parameter can appear anywhere in parameter list
3. Parameters after variadic must have explicit labels

```swift
// ✅ Valid
func format(prefix: String, values: Int..., suffix: String) -> String {
    return "\(prefix): \(values) \(suffix)"
}

format(prefix: "Numbers", values: 1, 2, 3, suffix: "end")

// ❌ Invalid: Can't have multiple variadic parameters
// func invalid(a: Int..., b: String...) { }
```

**Practical iOS Use Cases (2025):**

**1. Logging/Debugging:**
```swift
enum LogLevel {
    case debug, info, warning, error
}

func log(_ level: LogLevel, _ items: Any..., separator: String = " ") {
    let timestamp = Date()
    let message = items.map { "\($0)" }.joined(separator: separator)
    print("[\(timestamp)] [\(level)] \(message)")
}

// Usage
log(.info, "User", "logged in", "successfully")
// Output: [2025-12-20...] [info] User logged in successfully

log(.error, "Failed to fetch:", url, "Status:", statusCode)
// Output: [2025-12-20...] [error] Failed to fetch: https://... Status: 404
```

**2. SwiftUI - Combining Views:**
```swift
import SwiftUI

// Custom view that takes multiple child views
struct VStackWithDividers<Content: View>: View {
    let spacing: CGFloat
    let content: [Content]
    
    init(spacing: CGFloat = 10, @ViewBuilder _ content: () -> Content) {
        self.spacing = spacing
        self.content = [content()]
    }
    
    var body: some View {
        VStack(spacing: spacing) {
            ForEach(0..<content.count, id: \.self) { index in
                content[index]
                if index < content.count - 1 {
                    Divider()
                }
            }
        }
    }
}
```

**3. Building SQL-like Queries:**
```swift
class QueryBuilder {
    private var conditions: [String] = []
    
    func where(_ conditions: String...) -> QueryBuilder {
        self.conditions.append(contentsOf: conditions)
        return self
    }
    
    func build() -> String {
        return "SELECT * WHERE " + conditions.joined(separator: " AND ")
    }
}

// Usage
let query = QueryBuilder()
    .where("age > 18", "city = 'NYC'", "active = true")
    .build()

print(query)
// Output: SELECT * WHERE age > 18 AND city = 'NYC' AND active = true
```

**4. Analytics Events:**
```swift
class Analytics {
    func track(event: String, properties: [String: Any]...) {
        var combined: [String: Any] = [:]
        
        for dict in properties {
            combined.merge(dict) { _, new in new }
        }
        
        print("Event: \(event)")
        print("Properties: \(combined)")
    }
}

// Usage
let analytics = Analytics()
analytics.track(
    event: "Purchase",
    ["product": "iPhone"],
    ["price": 999],
    ["currency": "USD"]
)
```

**5. Combining Network Requests:**
```swift
func fetchAll(
    _ urls: URL...
) async throws -> [Data] {
    var results: [Data] = []
    
    for url in urls {
        let (data, _) = try await URLSession.shared.data(from: url)
        results.append(data)
    }
    
    return results
}

// Usage
let results = try await fetchAll(url1, url2, url3)
```

**6. Validation with Multiple Rules:**
```swift
func validate(_ input: String, rules: ((String) -> Bool)...) -> Bool {
    return rules.allSatisfy { $0(input) }
}

// Validation rules
let minLength: (String) -> Bool = { $0.count >= 8 }
let hasNumber: (String) -> Bool = { $0.rangeOfCharacter(from: .decimalDigits) != nil }
let hasUpper: (String) -> Bool = { $0.rangeOfCharacter(from: .uppercaseLetters) != nil }

// Validate password
let isValid = validate("Password123", rules: minLength, hasNumber, hasUpper)
print(isValid)  // true
```

**7. String Concatenation Helper:**
```swift
func buildPath(_ components: String..., separator: String = "/") -> String {
    return components.joined(separator: separator)
}

let apiPath = buildPath("api", "v1", "users", "123")
print(apiPath)  // api/v1/users/123

let filePath = buildPath("Documents", "MyApp", "data.json")
print(filePath)  // Documents/MyApp/data.json
```

**Benefits:**
- **Flexible APIs**: Accept variable number of arguments
- **Cleaner Code**: No need to create arrays manually
- **Better DX**: More intuitive function calls
- **Type Safety**: Compiler enforces type consistency

**Common Pattern (2025):**
```swift
// Combining with other parameters
func format(
    style: String = "default",
    _ items: Any...,
    separator: String = ", "
) -> String {
    let content = items.map { "\($0)" }.joined(separator: separator)
    return "[\(style)] \(content)"
}

print(format("bold", "Swift", "iOS", "2025"))
// Output: [bold] Swift, iOS, 2025
```

**Performance Note:** Variadic parameters create an array, so there's slight overhead. For performance-critical code with known parameter counts, use multiple parameters instead.

---

### Question 5: Explain the concept of nested functions in Swift. When would you use them?

**Answer:**
**Nested functions** are functions defined inside other functions. They have access to the outer function's variables and parameters, creating a closure-like behavior.

**Basic Syntax:**
```swift
func outerFunction(parameter: Int) -> Int {
    func innerFunction() -> Int {
        return parameter * 2  // Can access outer parameter
    }
    
    return innerFunction()
}

print(outerFunction(parameter: 5))  // 10
```

**Key Characteristics:**

1. **Scope**: Only accessible within outer function
2. **Access**: Can read/modify outer function's variables
3. **Capture**: Capture values from enclosing scope
4. **Return**: Can be returned from outer function

**When to Use Nested Functions:**

**1. Code Organization (Keep Helper Private):**
```swift
func processUserData(_ data: [String: Any]) -> String {
    // Helper functions only needed here
    func extractName() -> String {
        return data["name"] as? String ?? "Unknown"
    }
    
    func extractAge() -> Int {
        return data["age"] as? Int ?? 0
    }
    
    func formatInfo() -> String {
        return "\(extractName()), Age: \(extractAge())"
    }
    
    return formatInfo()
}

let result = processUserData(["name": "Alice", "age": 25])
print(result)  // Alice, Age: 25
```

**2. Factory Pattern (Returning Functions):**
```swift
func makeMultiplier(factor: Int) -> (Int) -> Int {
    func multiply(value: Int) -> Int {
        return value * factor
    }
    return multiply
}

let doubler = makeMultiplier(factor: 2)
let tripler = makeMultiplier(factor: 3)

print(doubler(10))  // 20
print(tripler(10))  // 30
```

**3. Complex Logic with Multiple Steps:**
```swift
func validatePassword(_ password: String) -> (isValid: Bool, message: String) {
    func checkLength() -> Bool {
        return password.count >= 8
    }
    
    func checkUppercase() -> Bool {
        return password.rangeOfCharacter(from: .uppercaseLetters) != nil
    }
    
    func checkLowercase() -> Bool {
        return password.rangeOfCharacter(from: .lowercaseLetters) != nil
    }
    
    func checkNumber() -> Bool {
        return password.rangeOfCharacter(from: .decimalDigits) != nil
    }
    
    // Main validation logic
    if !checkLength() {
        return (false, "Password must be at least 8 characters")
    }
    if !checkUppercase() {
        return (false, "Password must contain uppercase letter")
    }
    if !checkLowercase() {
        return (false, "Password must contain lowercase letter")
    }
    if !checkNumber() {
        return (false, "Password must contain number")
    }
    
    return (true, "Password is valid")
}

let result = validatePassword("Password123")
print(result)  // (true, "Password is valid")
```

**Real-World iOS Examples (2025):**

**1. SwiftUI - View Building:**
```swift
import SwiftUI

struct ProfileView: View {
    let user: User
    
    var body: some View {
        VStack {
            headerView()
            statsView()
            actionButtons()
        }
    }
    
    // Nested view builders
    private func headerView() -> some View {
        VStack {
            Image(systemName: "person.circle")
                .font(.system(size: 80))
            Text(user.name)
                .font(.title)
        }
    }
    
    private func statsView() -> some View {
        HStack {
            statItem(title: "Posts", value: user.postCount)
            statItem(title: "Followers", value: user.followers)
            statItem(title: "Following", value: user.following)
        }
    }
    
    private func statItem(title: String, value: Int) -> some View {
        VStack {
            Text("\(value)")
                .font(.headline)
            Text(title)
                .font(.caption)
        }
    }
    
    private func actionButtons() -> some View {
        HStack {
            Button("Follow") { /* action */ }
            Button("Message") { /* action */ }
        }
    }
}
```

**2. Data Processing Pipeline:**
```swift
func processImageData(_ data: Data) -> UIImage? {
    func decode() -> UIImage? {
        return UIImage(data: data)
    }
    
    func resize(_ image: UIImage, to size: CGSize) -> UIImage? {
        UIGraphicsBeginImageContextWithOptions(size, false, 0.0)
        image.draw(in: CGRect(origin: .zero, size: size))
        let resized = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        return resized
    }
    
    func compress(_ image: UIImage, quality: CGFloat) -> Data? {
        return image.jpegData(compressionQuality: quality)
    }
    
    // Process pipeline
    guard let original = decode() else { return nil }
    guard let resized = resize(original, to: CGSize(width: 300, height: 300)) else { return nil }
    
    return resized
}
```

**3. Async Operation Builder:**
```swift
func fetchUserProfile(id: String) async throws -> UserProfile {
    func fetchBasicInfo() async throws -> BasicInfo {
        let url = URL(string: "https://api.example.com/users/\(id)")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode(BasicInfo.self, from: data)
    }
    
    func fetchPosts() async throws -> [Post] {
        let url = URL(string: "https://api.example.com/users/\(id)/posts")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode([Post].self, from: data)
    }
    
    func combineData(info: BasicInfo, posts: [Post]) -> UserProfile {
        return UserProfile(info: info, posts: posts)
    }
    
    // Parallel fetch
    async let info = fetchBasicInfo()
    async let posts = fetchPosts()
    
    return try await combineData(info: info, posts: posts)
}
```

**4. State Machine:**
```swift
enum AppState {
    case idle, loading, loaded, error
}

func handleUserAction(_ action: String, currentState: AppState) -> AppState {
    func canLoad() -> Bool {
        return currentState == .idle || currentState == .error
    }
    
    func handleLoad() -> AppState {
        return canLoad() ? .loading : currentState
    }
    
    func handleSuccess() -> AppState {
        return currentState == .loading ? .loaded : currentState
    }
    
    func handleError() -> AppState {
        return currentState == .loading ? .error : currentState
    }
    
    switch action {
    case "load": return handleLoad()
    case "success": return handleSuccess()
    case "error": return handleError()
    default: return currentState
    }
}
```

**Benefits:**

1. **Encapsulation**: Keep implementation details private
2. **Readability**: Break complex logic into named steps
3. **Access to Outer Scope**: Can use outer function's variables
4. **No Namespace Pollution**: Functions only visible where needed
5. **Testability**: Can still unit test outer function

**When NOT to Use:**

- Functions needed elsewhere (make them top-level or class methods)
- Very complex nested functions (extract to separate functions)
- Deep nesting (hard to read)

**Best Practices (2025):**
```swift
// ✅ Good: Logical grouping
func processOrder(_ order: Order) -> Result {
    func validate() -> Bool { /* ... */ }
    func calculateTotal() -> Double { /* ... */ }
    func applyDiscount() -> Double { /* ... */ }
    
    guard validate() else { return .failure }
    let total = applyDiscount(calculateTotal())
    return .success(total)
}

// ❌ Avoid: Too deeply nested
func outer() {
    func middle() {
        func inner() {
            // Too deep!
        }
    }
}

// ✅ Better: Use closures for simple cases
let result = { /* simple logic */ }()
```

---

## 📝 Practice Exercises

1. Write a function that takes a person's name and optional age, returning a greeting message
2. Create a calculator function that accepts an operation string and two numbers
3. Implement a function with variadic parameters to find the maximum value
4. Write a function that returns another function (function factory pattern)
5. Create a validation function using nested functions for email validation

---

## 🔗 Navigation
- [← Previous: Phase 1 - Collections](../../Phase-1-Swift-Fundamentals/Step-6-Collections/README.md)
- [→ Next: Step 8 - Closures](../Step-8-Closures/README.md)
