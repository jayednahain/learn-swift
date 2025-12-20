# Step 4: Control Flow

## 📚 Description

Control flow statements determine the order in which code executes. They allow you to make decisions, repeat actions, and handle different scenarios. Swift provides powerful control flow constructs including conditional statements and various loop types.

### What You'll Learn:
- Conditional statements: `if`, `else if`, `else`
- `switch` statement with pattern matching
- Loops: `for-in`, `while`, `repeat-while`
- Control transfer statements: `break`, `continue`, `fallthrough`, `return`
- Using `stride` for custom ranges
- `where` clauses in `switch` statements

### Key Concepts:
- **if/else**: Execute code based on conditions
- **switch**: Multi-way branching with pattern matching (much more powerful than Java!)
- **for-in**: Iterate through sequences
- **while**: Loop while a condition is true
- **repeat-while**: Execute at least once, then loop

---

## 💡 Clear Examples

### Example 1: Conditional Statements (if/else)

```swift
// Basic if statement
let temperature = 75

if temperature > 80 {
    print("It's hot outside!")
}

// if-else
let age = 20
if age >= 18 {
    print("You can vote")
} else {
    print("You cannot vote yet")
}

// if-else if-else (multiple conditions)
let score = 85

if score >= 90 {
    print("Grade: A")
} else if score >= 80 {
    print("Grade: B")
} else if score >= 70 {
    print("Grade: C")
} else if score >= 60 {
    print("Grade: D")
} else {
    print("Grade: F")
}

// Combining conditions
let username = "admin"
let password = "secret123"

if username == "admin" && password == "secret123" {
    print("Login successful")
} else {
    print("Invalid credentials")
}

// Nested if statements
let isWeekend = true
let isRaining = false

if isWeekend {
    if isRaining {
        print("Stay home and watch movies")
    } else {
        print("Go to the park")
    }
} else {
    print("Go to work")
}

// Using if with optionals
let userAge: Int? = 25

if let age = userAge {
    if age >= 21 {
        print("Can purchase alcohol")
    } else {
        print("Too young")
    }
} else {
    print("Age not provided")
}

/* Output:
Grade: B
Login successful
Go to the park
Can purchase alcohol
*/
```

**Explanation:**
- `if` executes code when condition is true
- `else if` provides additional conditions
- `else` handles all other cases
- Multiple conditions can be combined with `&&` and `||`
- Optional binding can be used in `if` statements

---

### Example 2: Switch Statement (Pattern Matching)

```swift
// Basic switch
let dayOfWeek = 3

switch dayOfWeek {
case 1:
    print("Monday")
case 2:
    print("Tuesday")
case 3:
    print("Wednesday")
case 4:
    print("Thursday")
case 5:
    print("Friday")
case 6, 7:  // Multiple values
    print("Weekend!")
default:
    print("Invalid day")
}
// Output: Wednesday

// Switch with ranges
let age = 25

switch age {
case 0..<13:
    print("Child")
case 13..<20:
    print("Teenager")
case 20..<65:
    print("Adult")
case 65...:
    print("Senior")
default:
    print("Invalid age")
}
// Output: Adult

// Switch with tuples (matching multiple values)
let coordinates = (x: 0, y: 0)

switch coordinates {
case (0, 0):
    print("At origin")
case (_, 0):  // _ matches any value
    print("On x-axis")
case (0, _):
    print("On y-axis")
case (-2...2, -2...2):
    print("Inside 2x2 box")
default:
    print("Outside the box")
}
// Output: At origin

// Value binding in switch
let point = (x: 3, y: 0)

switch point {
case (0, 0):
    print("Origin")
case (let x, 0):
    print("On x-axis at x = \(x)")
case (0, let y):
    print("On y-axis at y = \(y)")
case (let x, let y):
    print("Point at (\(x), \(y))")
}
// Output: On x-axis at x = 3

// Switch with where clause
let temperature = 85

switch temperature {
case let temp where temp < 32:
    print("Freezing: \(temp)°F")
case let temp where temp < 60:
    print("Cold: \(temp)°F")
case let temp where temp < 80:
    print("Pleasant: \(temp)°F")
case let temp where temp < 100:
    print("Hot: \(temp)°F")
default:
    print("Extreme heat!")
}
// Output: Hot: 85°F

// Switch with String
let command = "start"

switch command {
case "start":
    print("Starting...")
case "stop":
    print("Stopping...")
case "pause":
    print("Pausing...")
default:
    print("Unknown command")
}
// Output: Starting...

// Compound cases (iOS version checking pattern)
let iOSVersion = 17

switch iOSVersion {
case 13, 14, 15:
    print("Older iOS version")
case 16:
    print("iOS 16")
case 17...:
    print("iOS 17 or newer - latest features!")
default:
    print("Very old iOS version")
}
// Output: iOS 17 or newer - latest features!

/* Note: Swift switch statements don't fall through by default!
   No need for 'break' statements like in Java/C
   Use 'fallthrough' keyword if you explicitly want fall-through behavior
*/
```

**Explanation:**
- Swift `switch` is much more powerful than Java/C
- Must be exhaustive (cover all cases or use `default`)
- No fall-through by default (safer than Java)
- Can match ranges, tuples, and patterns
- `where` clause adds additional conditions
- Value binding captures matched values

---

### Example 3: Loops (for-in, while, repeat-while)

```swift
// For-in loop with range
print("Counting 1 to 5:")
for number in 1...5 {
    print(number, terminator: " ")
}
print()  // New line
// Output: 1 2 3 4 5

// For-in with array
let fruits = ["Apple", "Banana", "Orange", "Mango"]
print("\nFruits:")
for fruit in fruits {
    print("I like \(fruit)")
}

// For-in with index and value (enumerated)
print("\nFruits with index:")
for (index, fruit) in fruits.enumerated() {
    print("\(index + 1). \(fruit)")
}

// For-in with dictionary
let scores = ["Alice": 95, "Bob": 87, "Charlie": 92]
print("\nScores:")
for (name, score) in scores {
    print("\(name): \(score)")
}

// For-in with stride (custom step)
print("\nEven numbers from 0 to 10:")
for number in stride(from: 0, through: 10, by: 2) {
    print(number, terminator: " ")
}
print()
// Output: 0 2 4 6 8 10

print("\nCountdown from 10 to 1:")
for number in stride(from: 10, through: 1, by: -1) {
    print(number, terminator: " ")
}
print()

// Underscore (_) when you don't need the value
print("\nPrint 'Hello' 3 times:")
for _ in 1...3 {
    print("Hello")
}

// While loop
var countdown = 5
print("\nWhile countdown:")
while countdown > 0 {
    print(countdown, terminator: " ")
    countdown -= 1
}
print("Liftoff!")
// Output: 5 4 3 2 1 Liftoff!

// Repeat-while loop (like do-while in Java)
var number = 1
print("\nRepeat-while:")
repeat {
    print(number, terminator: " ")
    number += 1
} while number <= 5
print()
// Output: 1 2 3 4 5

// Difference between while and repeat-while
var count = 10
while count < 10 {
    print("This won't print")
}

repeat {
    print("This prints once even though condition is false")
} while count < 10

// Control transfer statements
// Break - exits the loop immediately
print("\nUsing break:")
for i in 1...10 {
    if i == 5 {
        break  // Stop at 5
    }
    print(i, terminator: " ")
}
print()
// Output: 1 2 3 4

// Continue - skips current iteration
print("\nUsing continue (skip even numbers):")
for i in 1...10 {
    if i % 2 == 0 {
        continue  // Skip even numbers
    }
    print(i, terminator: " ")
}
print()
// Output: 1 3 5 7 9

// Labeled statements (for nested loops)
print("\nLabeled break:")
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i == 2 && j == 2 {
            break outerLoop  // Breaks out of outer loop
        }
        print("(\(i), \(j))", terminator: " ")
    }
}
print()
// Output: (1, 1) (1, 2) (1, 3) (2, 1)

// Real-world example: Input validation
var attempts = 0
let maxAttempts = 3
var isAuthenticated = false

while attempts < maxAttempts && !isAuthenticated {
    print("\nAttempt \(attempts + 1) of \(maxAttempts)")
    // Simulate authentication
    let password = "wrong"  // Simulated user input
    
    if password == "correct" {
        isAuthenticated = true
        print("Login successful!")
    } else {
        attempts += 1
        print("Wrong password")
    }
}

if !isAuthenticated {
    print("Maximum attempts reached. Account locked.")
}

/* Output examples shown inline above */
```

**Explanation:**
- `for-in` is most common for iterating sequences
- `enumerated()` provides index and value
- `stride` creates custom ranges with steps
- `while` checks condition before executing
- `repeat-while` executes at least once
- `break` exits loop completely
- `continue` skips to next iteration
- Labeled statements control nested loops

---

## 🎯 2025 Interview Questions

### Question 1: What are the key differences between Swift's switch statement and Java's switch statement?

**Answer:**
Swift's `switch` is significantly more powerful than Java's:

**1. No Fall-Through by Default:**
```swift
// Swift - no fall-through
let number = 2
switch number {
case 1:
    print("One")
case 2:
    print("Two")  // Only this executes
case 3:
    print("Three")
default:
    break
}
// Output: Two

// Java equivalent would need break statements
```

**2. Must Be Exhaustive:**
Swift requires all possible values to be handled (compile error otherwise):
```swift
let value = 5
switch value {
case 1...10:
    print("Between 1 and 10")
default:  // Required if not all cases covered
    print("Other")
}
```

**3. Pattern Matching:**
Swift supports powerful pattern matching:
```swift
// Tuples
let point = (x: 1, y: 2)
switch point {
case (0, 0):
    print("Origin")
case (_, 0):
    print("On x-axis")
case (0, _):
    print("On y-axis")
case (let x, let y):
    print("(\(x), \(y))")
}

// Ranges
let age = 25
switch age {
case 0..<13:
    print("Child")
case 13..<20:
    print("Teen")
case 20...:
    print("Adult")
default:
    break
}
```

**4. Value Binding:**
```swift
let coordinates = (x: 5, y: 0)
switch coordinates {
case (let x, 0):
    print("On x-axis at \(x)")  // Captures and uses x value
default:
    break
}
```

**5. Where Clauses:**
```swift
let temperature = 75
switch temperature {
case let temp where temp < 32:
    print("Freezing")
case let temp where temp >= 80:
    print("Hot")
default:
    print("Moderate")
}
```

**6. No Primitive Limitation:**
Swift switch works with any type (String, custom types, etc.):
```swift
let name = "Alice"
switch name {
case "Alice", "Bob":
    print("Valid user")
default:
    print("Unknown user")
}
```

**Java Limitations (pre-Java 17):**
- Fall-through by default (error-prone)
- Only worked with primitives and enums
- No pattern matching
- No range support

**Modern Java (17+) has improved with pattern matching, but Swift's switch remains more expressive.**

**2025 Best Practice:** Use Swift's switch for complex conditional logic - it's cleaner than multiple if-else chains.

---

### Question 2: Explain the difference between `while` and `repeat-while` loops. When would you use each?

**Answer:**
**While Loop:**
- Checks condition **before** executing the body
- May never execute if condition is initially false
```swift
var count = 0
while count < 0 {
    print("This never executes")
    count += 1
}
```

**Repeat-While Loop:**
- Executes body **at least once**, then checks condition
- Equivalent to `do-while` in Java/C
```swift
var count = 0
repeat {
    print("This executes once")  // Executes even though count < 0 is false
    count += 1
} while count < 0
```

**When to Use Each:**

**Use `while` when:**
- Condition might be false from the start
- Reading input until valid
```swift
// Reading configuration files
var fileExists = checkFileExists()
while !fileExists {
    print("Waiting for file...")
    sleep(1)
    fileExists = checkFileExists()
}
```

**Use `repeat-while` when:**
- Body must execute at least once
- Menu-driven programs
- Validation with guaranteed first attempt
```swift
// iOS: User input validation
var userInput: String
repeat {
    print("Enter username (min 3 chars):")
    userInput = readLine() ?? ""
} while userInput.count < 3

// Game loop (must run at least once)
repeat {
    displayGameState()
    let action = getUserAction()
    processAction(action)
} while !gameOver
```

**Real-World iOS Example (2025):**
```swift
// API retry logic
var retryCount = 0
var response: NetworkResponse?

repeat {
    response = try await fetchDataFromAPI()
    retryCount += 1
    if response == nil {
        try await Task.sleep(nanoseconds: 1_000_000_000)  // Wait 1 second
    }
} while response == nil && retryCount < 3

// Form validation (must validate at least once)
var isValid = false
repeat {
    let email = emailTextField.text ?? ""
    isValid = validateEmail(email)
    if !isValid {
        showError("Invalid email format")
    }
} while !isValid && userWantsToRetry
```

**Key Difference:** Use `repeat-while` when the loop body **must run at least once** regardless of initial condition.

---

### Question 3: What is the purpose of labeled statements in Swift? Provide a practical use case.

**Answer:**
**Labeled statements** allow you to explicitly control which loop to break or continue in nested loop scenarios.

**Syntax:**
```swift
labelName: for ... {
    // Can use: break labelName or continue labelName
}
```

**Problem Without Labels:**
```swift
// Only breaks inner loop
for i in 1...3 {
    for j in 1...3 {
        if i == 2 && j == 2 {
            break  // Only breaks inner loop, outer continues
        }
        print("(\(i), \(j))", terminator: " ")
    }
    print()
}
/* Output:
(1, 1) (1, 2) (1, 3) 
(2, 1) 
(3, 1) (3, 2) (3, 3)
*/
```

**Solution With Labels:**
```swift
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i == 2 && j == 2 {
            break outerLoop  // Breaks outer loop completely
        }
        print("(\(i), \(j))", terminator: " ")
    }
    print()
}
/* Output:
(1, 1) (1, 2) (1, 3) 
(2, 1)
*/
```

**Practical Use Cases (iOS Development 2025):**

**1. Grid Search (UICollectionView/Grid):**
```swift
// Finding a specific cell in a grid
func findProductInGrid(targetID: String) -> (row: Int, col: Int)? {
    let gridSize = 10
    
    searchGrid: for row in 0..<gridSize {
        for col in 0..<gridSize {
            let product = products[row][col]
            if product.id == targetID {
                return (row, col)
                // Or: break searchGrid
            }
        }
    }
    return nil
}
```

**2. Data Validation (Multi-level):**
```swift
// Validating nested JSON structure
validation: for section in jsonSections {
    for item in section.items {
        for field in item.fields {
            if !field.isValid {
                print("Invalid field found in \(section.name)")
                break validation  // Stop all validation
            }
        }
    }
}
```

**3. Matrix Processing:**
```swift
// Finding first occurrence in 2D array
func findValue(_ target: Int, in matrix: [[Int]]) -> (Int, Int)? {
    rowLoop: for (rowIndex, row) in matrix.enumerated() {
        for (colIndex, value) in row.enumerated() {
            if value == target {
                print("Found at (\(rowIndex), \(colIndex))")
                break rowLoop
            }
        }
    }
    return nil
}
```

**4. Game Development (Nested Game Loops):**
```swift
gameLoop: while gameIsRunning {
    eventLoop: for event in eventQueue {
        switch event {
        case .quit:
            break gameLoop  // Exit entire game
        case .pause:
            continue gameLoop  // Skip to next frame
        default:
            processEvent(event)
        }
    }
    updateGameState()
    render()
}
```

**Best Practices (2025):**
- Use labels sparingly (prefer refactoring to functions)
- Name labels descriptively (`searchLoop`, `validationLoop`, not `loop1`)
- Consider extracting nested loops into separate functions first
- Labels are most useful in search/validation algorithms

**Alternative to Labels (Preferred in Modern Swift):**
```swift
// Instead of labels, extract to function with early return
func findInGrid(target: String) -> Bool {
    for row in 0..<10 {
        for col in 0..<10 {
            if grid[row][col] == target {
                return true  // Clean exit
            }
        }
    }
    return false
}
```

---

### Question 4: How does Swift's `for-in` loop compare to traditional C-style for loops? Why did Swift remove C-style loops?

**Answer:**
**C-Style For Loop (Removed in Swift 3):**
```swift
// OLD Swift (no longer valid)
for var i = 0; i < 10; i++ {
    print(i)
}
```

**Modern Swift For-In Loop:**
```swift
// Modern Swift
for i in 0..<10 {
    print(i)
}
```

**Why Swift Removed C-Style Loops:**

**1. Safety:**
C-style loops were error-prone:
```swift
// Common bugs in C-style loops:
// - Off-by-one errors
// - Infinite loops due to wrong increment
// - Forgot to update counter
```

**2. Readability:**
`for-in` is more expressive:
```swift
// ✅ Clear intent
for user in users {
    processUser(user)
}

// ❌ Less clear (old style)
for var i = 0; i < users.count; i++ {
    processUser(users[i])
}
```

**3. Performance:**
For-in can be optimized better by the compiler:
```swift
// Compiler can optimize for-in loops
for item in collection {
    // Compiler knows collection won't change
}
```

**Modern Alternatives for Common C-Style Patterns:**

**Pattern 1: Basic Counter:**
```swift
// Old C-style
for var i = 0; i < 10; i++ { }

// Modern Swift
for i in 0..<10 { }
```

**Pattern 2: Custom Step:**
```swift
// Old C-style
for var i = 0; i < 100; i += 5 { }

// Modern Swift
for i in stride(from: 0, to: 100, by: 5) { }
```

**Pattern 3: Reverse Order:**
```swift
// Old C-style
for var i = 10; i > 0; i-- { }

// Modern Swift
for i in stride(from: 10, through: 1, by: -1) { }
// Or: for i in (1...10).reversed() { }
```

**Pattern 4: Index-Based Access:**
```swift
// Modern Swift with enumerated
let fruits = ["Apple", "Banana", "Cherry"]
for (index, fruit) in fruits.enumerated() {
    print("\(index): \(fruit)")
}
```

**Pattern 5: Complex Conditions:**
```swift
// When you need complex loop logic, use while
var i = 0
while i < 100 {
    // Custom logic
    i += customIncrement()
}
```

**Real-World iOS Example (2025):**
```swift
// Animating UI elements sequentially
let views = [view1, view2, view3, view4]

for (index, view) in views.enumerated() {
    UIView.animate(
        withDuration: 0.3,
        delay: Double(index) * 0.1,
        animations: { view.alpha = 1.0 }
    )
}

// Processing API responses in batches
for batch in stride(from: 0, to: items.count, by: 50) {
    let endIndex = min(batch + 50, items.count)
    let batchItems = Array(items[batch..<endIndex])
    await processBatch(batchItems)
}
```

**Benefits of For-In (2025):**
- **Safer**: Fewer off-by-one errors
- **Clearer**: Intent is obvious
- **Shorter**: Less boilerplate code
- **Performant**: Compiler optimizations
- **Protocol-based**: Works with any `Sequence`

**When You Miss C-Style Loops:**
Use `while` loops with manual counter management, but this is rare in modern Swift.

---

### Question 5: Explain the `where` clause in Swift's switch statements and for-in loops. How does it improve code readability?

**Answer:**
The `where` clause adds **additional filtering conditions** to `switch` cases and `for-in` loops, making code more expressive and readable.

**In Switch Statements:**
```swift
// Without where (less clear)
let temperature = 85
switch temperature {
case -100...32:
    print("Freezing")
case 33...79:
    print("Pleasant")
case 80...100:
    print("Hot")
default:
    print("Extreme")
}

// With where (more expressive)
let temp = 85
switch temp {
case let t where t < 32:
    print("Freezing: \(t)°F")
case let t where t < 80:
    print("Pleasant: \(t)°F")
case let t where t < 100:
    print("Hot: \(t)°F")
default:
    print("Extreme temperature")
}
```

**Complex Pattern Matching:**
```swift
let point = (x: 3, y: 4)

switch point {
case let (x, y) where x == y:
    print("On the diagonal")
case let (x, y) where x == -y:
    print("On the inverse diagonal")
case let (x, y) where x * x + y * y <= 25:
    print("Inside radius 5 circle")
case let (x, y) where abs(x) < 2 && abs(y) < 2:
    print("Inside 2x2 box")
default:
    print("Outside special regions")
}
// Output: Inside radius 5 circle (3² + 4² = 25)
```

**In For-In Loops:**
```swift
// Filter while iterating
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Only process even numbers greater than 5
for number in numbers where number % 2 == 0 && number > 5 {
    print(number)  // Prints: 6, 8, 10
}

// Without where (less elegant)
for number in numbers {
    if number % 2 == 0 && number > 5 {
        print(number)
    }
}
```

**Real-World iOS Examples (2025):**

**1. User Interface Filtering:**
```swift
// SwiftUI List filtering
struct UserListView: View {
    @State private var users: [User]
    @State private var showActiveOnly = true
    
    var body: some View {
        List {
            ForEach(users) { user in
                // Only show active users when filter is on
                if !showActiveOnly || user.isActive {
                    UserRow(user: user)
                }
            }
        }
    }
}

// With where clause (cleaner)
for user in users where user.isActive || !showActiveOnly {
    displayUser(user)
}
```

**2. API Response Processing:**
```swift
struct APIResponse {
    let items: [Item]
}

// Process only valid, non-expired items
for item in response.items where item.isValid && !item.isExpired {
    processItem(item)
}

// Equivalent without where
for item in response.items {
    guard item.isValid && !item.isExpired else { continue }
    processItem(item)
}
```

**3. Enum Pattern Matching:**
```swift
enum NetworkStatus {
    case connected(speed: Int)
    case disconnected(reason: String)
    case connecting
}

let status = NetworkStatus.connected(speed: 100)

switch status {
case .connected(let speed) where speed > 50:
    print("Fast connection: \(speed) Mbps")
case .connected(let speed):
    print("Slow connection: \(speed) Mbps")
case .disconnected(let reason) where reason.contains("timeout"):
    print("Connection timeout - retry")
case .disconnected(let reason):
    print("Disconnected: \(reason)")
case .connecting:
    print("Connecting...")
}
```

**4. Data Validation:**
```swift
// Validating user inputs
let formFields = [
    ("email", "test@example.com"),
    ("age", "25"),
    ("phone", ""),
    ("name", "John")
]

for (field, value) in formFields where !value.isEmpty {
    print("Valid field: \(field) = \(value)")
}

// Complex validation
for (field, value) in formFields 
    where !value.isEmpty && value.count >= 3 {
    saveField(field, value: value)
}
```

**5. Collection Processing (iOS Development):**
```swift
// Processing UIViews
let allViews = view.subviews

// Only animate visible views with alpha < 1
for subview in allViews where !subview.isHidden && subview.alpha < 1.0 {
    UIView.animate(withDuration: 0.3) {
        subview.alpha = 1.0
    }
}

// Processing array of optional values
let optionalNumbers: [Int?] = [1, nil, 3, nil, 5, 6]
for case let number? in optionalNumbers where number > 3 {
    print(number)  // Prints: 5, 6
}
```

**Benefits of `where` Clause:**

1. **Readability**: Intent is clearer than nested ifs
2. **Conciseness**: Less boilerplate code
3. **Performance**: Compiler can optimize better
4. **Expressiveness**: Declarative style (what, not how)
5. **Type Safety**: Works with Swift's type system

**Best Practices (2025):**
- Use `where` to simplify conditional logic in loops
- Prefer `where` over nested `if` statements
- Keep `where` conditions concise (complex logic → extract to function)
- Combine with optional binding for elegant code

**Modern Swift Pattern:**
```swift
// Combining optional binding with where
let userAges: [Int?] = [nil, 25, 30, nil, 15, 40]

for case let age? in userAges where age >= 18 {
    print("Adult age: \(age)")
}
// Output: Adult age: 25, Adult age: 30, Adult age: 40
```

---

## 📝 Practice Exercises

1. Write a program to check if a number is positive, negative, or zero using if-else
2. Create a switch statement for days of the week that groups weekdays vs weekends
3. Use a for-in loop to print the multiplication table for a given number
4. Implement FizzBuzz using control flow (print "Fizz" for multiples of 3, "Buzz" for 5, "FizzBuzz" for both)
5. Create a number guessing game using while loop with attempts limit

---

## 🔗 Navigation
- [← Previous: Step 3 - Operators](../Step-3-Operators/README.md)
- [→ Next: Step 5 - Optionals](../Step-5-Optionals/README.md)
