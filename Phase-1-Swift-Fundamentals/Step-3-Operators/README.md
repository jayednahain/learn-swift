# Step 3: Operators

## 📚 Description

Operators are special symbols that perform operations on variables and values. Swift provides a rich set of operators similar to other programming languages but with some powerful additions. Understanding operators is essential for writing expressions and performing calculations.

### What You'll Learn:
- Arithmetic operators (+, -, *, /, %)
- Comparison operators (==, !=, >, <, >=, <=)
- Logical operators (&&, ||, !)
- Assignment operators (=, +=, -=, etc.)
- Range operators (..., ..<)
- Nil-coalescing operator (??)
- Ternary conditional operator (? :)

### Key Concepts:
- **Arithmetic Operators**: Perform mathematical calculations
- **Comparison Operators**: Compare values and return boolean results
- **Logical Operators**: Combine boolean values
- **Range Operators**: Create sequences of values
- **Nil-Coalescing**: Provide default values for optionals

---

## 💡 Clear Examples

### Example 1: Arithmetic and Assignment Operators

```swift
// Basic Arithmetic Operators
let sum = 10 + 5  // Addition: 15
let difference = 10 - 5  // Subtraction: 5
let product = 10 * 5  // Multiplication: 50
let quotient = 10 / 5  // Division: 2
let remainder = 10 % 3  // Modulo (remainder): 1

print("Sum: \(sum), Difference: \(difference), Product: \(product)")
print("Quotient: \(quotient), Remainder: \(remainder)")

// Unary Minus (negation)
let positiveNumber = 10
let negativeNumber = -positiveNumber  // -10

// Compound Assignment Operators
var score = 100
score += 20  // score = score + 20 → 120
score -= 10  // score = score - 10 → 110
score *= 2   // score = score * 2 → 220
score /= 4   // score = score / 4 → 55
score %= 10  // score = score % 10 → 5

print("Final score: \(score)")

// String Concatenation with +
let firstName = "John"
let lastName = "Doe"
let fullName = firstName + " " + lastName
print("Full Name: \(fullName)")  // John Doe

/* Output:
Sum: 15, Difference: 5, Product: 50
Quotient: 2, Remainder: 1
Final score: 5
Full Name: John Doe
*/
```

**Explanation:**
- Arithmetic operators work as expected from mathematics
- `%` (modulo) returns the remainder after division
- Compound assignment operators are shortcuts for common operations
- `+` can concatenate strings in addition to adding numbers

---

### Example 2: Comparison and Logical Operators

```swift
// Comparison Operators (return Bool)
let a = 10
let b = 20

print("a == b: \(a == b)")  // Equal to: false
print("a != b: \(a != b)")  // Not equal to: true
print("a > b: \(a > b)")    // Greater than: false
print("a < b: \(a < b)")    // Less than: true
print("a >= b: \(a >= b)")  // Greater than or equal: false
print("a <= b: \(a <= b)")  // Less than or equal: true

// String comparison
let name1 = "Alice"
let name2 = "Bob"
print("name1 < name2: \(name1 < name2)")  // Alphabetical comparison: true

// Logical Operators
let isLoggedIn = true
let hasPermission = false
let isAdmin = true

// AND (&&) - both conditions must be true
let canEdit = isLoggedIn && hasPermission
print("Can edit: \(canEdit)")  // false

// OR (||) - at least one condition must be true
let canView = isLoggedIn || isAdmin
print("Can view: \(canView)")  // true

// NOT (!) - inverts the boolean value
let isGuest = !isLoggedIn
print("Is guest: \(isGuest)")  // false

// Combining logical operators
let age = 25
let hasLicense = true
let canDrive = (age >= 18) && hasLicense
print("Can drive: \(canDrive)")  // true

// Complex logical expressions
let temperature = 75
let isComfortable = (temperature >= 68 && temperature <= 78)
print("Comfortable temperature: \(isComfortable)")  // true

/* Output:
a == b: false
a != b: true
a > b: false
a < b: true
a >= b: false
a <= b: true
name1 < name2: true
Can edit: false
Can view: true
Is guest: false
Can drive: true
Comfortable temperature: true
*/
```

**Explanation:**
- Comparison operators return `Bool` (true/false)
- Swift can compare strings alphabetically
- `&&` (AND) requires all conditions to be true
- `||` (OR) requires at least one condition to be true
- `!` (NOT) flips the boolean value
- Parentheses can group conditions for clarity

---

### Example 3: Range, Ternary, and Nil-Coalescing Operators

```swift
// Range Operators
// Closed Range (...) - includes both start and end
let closedRange = 1...5  // 1, 2, 3, 4, 5
print("Closed range:")
for number in closedRange {
    print(number, terminator: " ")
}
print()  // New line

// Half-Open Range (..<) - excludes the end value
let halfOpenRange = 1..<5  // 1, 2, 3, 4 (no 5)
print("Half-open range:")
for number in halfOpenRange {
    print(number, terminator: " ")
}
print()  // New line

// One-sided ranges
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let firstFive = numbers[..<5]  // First 5 elements: [1, 2, 3, 4, 5]
let lastFive = numbers[5...]   // From index 5 to end: [6, 7, 8, 9, 10]
print("First five: \(firstFive)")
print("Last five: \(lastFive)")

// Ternary Conditional Operator (? :)
// Syntax: condition ? valueIfTrue : valueIfFalse
let age = 20
let canVote = age >= 18 ? "Yes" : "No"
print("Can vote: \(canVote)")  // Yes

let score = 85
let grade = score >= 90 ? "A" : (score >= 80 ? "B" : "C")
print("Grade: \(grade)")  // B

// Practical example
let temperature = 30
let weather = temperature > 25 ? "Hot" : "Cool"
print("Weather: \(weather)")  // Hot

// Nil-Coalescing Operator (??)
// Provides a default value if an optional is nil
let userName: String? = nil
let displayName = userName ?? "Guest"
print("Welcome, \(displayName)!")  // Welcome, Guest!

let userAge: Int? = 25
let age2 = userAge ?? 0
print("Age: \(age2)")  // Age: 25

// Chaining nil-coalescing
let primaryEmail: String? = nil
let secondaryEmail: String? = nil
let email = primaryEmail ?? secondaryEmail ?? "no-email@example.com"
print("Email: \(email)")  // no-email@example.com

// Real-world example: User profile
struct UserProfile {
    var nickname: String?
    var firstName: String
    var lastName: String
}

let user = UserProfile(nickname: nil, firstName: "John", lastName: "Doe")
let displayUsername = user.nickname ?? "\(user.firstName) \(user.lastName)"
print("Display name: \(displayUsername)")  // Display name: John Doe

/* Output:
Closed range:
1 2 3 4 5 
Half-open range:
1 2 3 4 
First five: [1, 2, 3, 4, 5]
Last five: [6, 7, 8, 9, 10]
Can vote: Yes
Grade: B
Weather: Hot
Welcome, Guest!
Age: 25
Email: no-email@example.com
Display name: John Doe
*/
```

**Explanation:**
- `...` creates a closed range (includes both ends)
- `..<` creates a half-open range (excludes end value)
- Ternary operator `? :` is a concise if-else statement
- Nil-coalescing `??` provides default values for optionals
- These operators make code more concise and readable

---

## 🎯 2025 Interview Questions

### Question 1: What's the difference between `...` and `..<` range operators? When would you use each?

**Answer:**
**Closed Range (`...`)** - Includes both start and end values:
```swift
let range = 1...5  // [1, 2, 3, 4, 5]
```

**Half-Open Range (`..<`)** - Excludes the end value:
```swift
let range = 1..<5  // [1, 2, 3, 4]
```

**When to use each:**

**Use `...` (Closed Range) when:**
- Iterating through a complete sequence including the final value
- Range matches real-world inclusive boundaries
```swift
// Print grades from 1 to 10 (both inclusive)
for grade in 1...10 {
    print("Grade: \(grade)")
}

// Temperature range (includes both ends)
let comfortableTemp = 68...78
```

**Use `..<` (Half-Open Range) when:**
- Working with array indices (arrays are 0-indexed, count is exclusive)
- Avoiding off-by-one errors
- Most loops over collections
```swift
let items = ["A", "B", "C", "D", "E"]
for i in 0..<items.count {  // 0 to 4 (count is 5)
    print(items[i])
}

// Safer than using ... with count (would crash on items[5])
```

**Best Practice (2025):**
- Use `..<` for array indices (more common)
- Use `...` when the end value is meaningful and should be included
- `..<` is preferred in modern Swift to avoid off-by-one errors

---

### Question 2: Explain the nil-coalescing operator (`??`). How is it different from optional binding?

**Answer:**
The **nil-coalescing operator (`??`)** provides a default value when an optional is `nil`.

**Syntax:** `optionalValue ?? defaultValue`

**Example:**
```swift
let username: String? = nil
let displayName = username ?? "Guest"  // Returns "Guest" if username is nil
```

**Difference from Optional Binding:**

**Nil-Coalescing (`??`):**
- Returns a non-optional value (always provides a result)
- Inline, expression-based
- Best for providing defaults
```swift
let age: Int? = nil
let userAge = age ?? 18  // Always returns an Int
print("Age: \(userAge)")  // Age: 18
```

**Optional Binding (`if let` / `guard let`):**
- Safely unwraps optionals
- Control flow based (creates scope)
- Best when you need to handle presence/absence differently
```swift
let age: Int? = nil
if let userAge = age {
    print("Age is \(userAge)")  // Only executes if age has a value
} else {
    print("Age not provided")  // Executes if age is nil
}
```

**When to use each (2025 patterns):**

**Use `??` when:**
```swift
// Simple default values
let title = article.title ?? "Untitled"
let count = items.count ?? 0
```

**Use `if let`/`guard let` when:**
```swift
// Different logic for nil vs non-nil
guard let userID = session.userID else {
    showLoginScreen()
    return
}
// Continue with userID...

// Multiple optionals
if let name = user.name, let email = user.email {
    sendWelcomeEmail(to: email, name: name)
}
```

**Can be combined:**
```swift
// Get value, or use default, then validate
let age = userInput.age ?? 0
guard age >= 18 else {
    print("Must be 18 or older")
    return
}
```

---

### Question 3: How does Swift's `%` (modulo) operator work with negative numbers? Provide examples.

**Answer:**
Swift's modulo operator (`%`) returns the **remainder** after division. With negative numbers, the result's sign follows the **dividend** (first number), not the divisor.

**Formula:** `a % b = a - (b * (a / b))`

**Examples:**
```swift
// Positive numbers (straightforward)
print(10 % 3)   // 1  (10 ÷ 3 = 3 remainder 1)
print(17 % 5)   // 2  (17 ÷ 5 = 3 remainder 2)

// Negative dividend (first number is negative)
print(-10 % 3)  // -1  (sign follows -10)
print(-17 % 5)  // -2  (sign follows -17)

// Negative divisor (second number is negative)
print(10 % -3)  // 1   (sign follows 10, divisor's sign ignored)
print(17 % -5)  // 2   (sign follows 17)

// Both negative
print(-10 % -3) // -1  (sign follows -10)
```

**Detailed Calculation:**
```swift
// Example: -10 % 3
// -10 / 3 = -3 (integer division)
// -10 - (3 * -3) = -10 - (-9) = -1
// Result: -1

// Example: 10 % -3
// 10 / -3 = -3 (integer division)
// 10 - (-3 * -3) = 10 - 9 = 1
// Result: 1
```

**Practical Use Cases:**
```swift
// Check if even/odd (works with negatives)
func isEven(_ number: Int) -> Bool {
    return number % 2 == 0
}

print(isEven(10))   // true
print(isEven(-10))  // true
print(isEven(11))   // false
print(isEven(-11))  // false

// Circular array indexing (wrapping)
let items = ["A", "B", "C", "D", "E"]
let index = 7
let wrappedIndex = index % items.count  // 7 % 5 = 2
print(items[wrappedIndex])  // "C"

// Alternating patterns
for i in 0..<10 {
    if i % 2 == 0 {
        print("\(i) is even")
    } else {
        print("\(i) is odd")
    }
}
```

**2025 Interview Tip:** Mention that for mathematical modulo (always positive result), you might need custom logic:
```swift
func properModulo(_ a: Int, _ b: Int) -> Int {
    let result = a % b
    return result >= 0 ? result : result + b
}

print(properModulo(-10, 3))  // 2 (not -1)
```

---

### Question 4: Can you overload operators in Swift? Provide a practical example relevant to iOS development.

**Answer:**
**Yes**, Swift allows operator overloading - you can define custom behavior for existing operators on your own types.

**Syntax:**
```swift
static func operator(parameters) -> ReturnType {
    // Implementation
}
```

**Practical Example: CGPoint Addition (iOS/UIKit)**
```swift
import CoreGraphics

// Overload + operator for CGPoint
extension CGPoint {
    static func + (left: CGPoint, right: CGPoint) -> CGPoint {
        return CGPoint(x: left.x + right.x, y: left.y + right.y)
    }
    
    static func - (left: CGPoint, right: CGPoint) -> CGPoint {
        return CGPoint(x: left.x - right.x, y: left.y - right.y)
    }
    
    static func * (point: CGPoint, scalar: CGFloat) -> CGPoint {
        return CGPoint(x: point.x * scalar, y: point.y * scalar)
    }
}

// Usage in iOS app
let startPoint = CGPoint(x: 10, y: 20)
let offset = CGPoint(x: 5, y: 10)
let endPoint = startPoint + offset  // CGPoint(x: 15, y: 30)

let midPoint = startPoint * 0.5  // CGPoint(x: 5, y: 10)
print("End point: \(endPoint)")
```

**Real-World Example: Custom Money Type**
```swift
struct Money {
    let amount: Decimal
    let currency: String
    
    static func + (left: Money, right: Money) -> Money {
        guard left.currency == right.currency else {
            fatalError("Cannot add different currencies")
        }
        return Money(amount: left.amount + right.amount, currency: left.currency)
    }
    
    static func == (left: Money, right: Money) -> Bool {
        return left.amount == right.amount && left.currency == right.currency
    }
}

// Usage in e-commerce app
let price1 = Money(amount: 29.99, currency: "USD")
let price2 = Money(amount: 15.50, currency: "USD")
let total = price1 + price2  // Money(amount: 45.49, currency: "USD")
```

**SwiftUI Example: Custom Color Blending**
```swift
import SwiftUI

extension Color {
    static func + (left: Color, right: Color) -> Color {
        // Simplified color blending
        return left.opacity(0.5).overlay(right.opacity(0.5))
    }
}

// Usage
let customColor = Color.red + Color.blue  // Purple blend
```

**Best Practices (2025):**
- Only overload operators when it makes the code more readable
- Follow mathematical/logical conventions (don't make + do subtraction)
- Document custom operators clearly
- Common use cases: vector math, custom numeric types, DSLs

**⚠️ Caution:** Overuse can make code confusing. Only overload when:
1. The operation is intuitive (matches user expectations)
2. It significantly improves readability
3. It's consistent with Swift's standard library conventions

---

### Question 5: Explain operator precedence in Swift. What happens when you mix different operators?

**Answer:**
**Operator precedence** determines the order in which operators are evaluated in expressions without parentheses. Operators with higher precedence execute first.

**Swift Operator Precedence (highest to lowest):**

1. **Bitwise shift** (`<<`, `>>`)
2. **Multiplication** (`*`, `/`, `%`)
3. **Addition** (`+`, `-`)
4. **Range** (`...`, `..<`)
5. **Casting** (`is`, `as`)
6. **Nil-coalescing** (`??`)
7. **Comparison** (`<`, `<=`, `>`, `>=`, `==`, `!=`)
8. **Logical AND** (`&&`)
9. **Logical OR** (`||`)
10. **Ternary** (`? :`)
11. **Assignment** (`=`, `+=`, `-=`, etc.)

**Examples:**
```swift
// Multiplication before addition
let result1 = 2 + 3 * 4  // 2 + 12 = 14 (not 20)

// Division before subtraction
let result2 = 10 - 8 / 2  // 10 - 4 = 6 (not 1)

// Comparison before logical AND
let result3 = 5 > 3 && 10 < 20  // true && true = true

// Logical AND before logical OR
let result4 = true || false && false  // true || false = true
// Evaluated as: true || (false && false)

// Assignment has lowest precedence
var x = 5
var y = x = 10  // First x = 10, then y = x (y = 10)
print("x: \(x), y: \(y)")  // x: 10, y: 10
```

**Using Parentheses for Clarity:**
```swift
// Ambiguous without parentheses
let price = 100
let discount = 10
let tax = 0.08

// Calculate final price
let finalPrice1 = price - discount * tax  // 99.2 (discount * tax first)
let finalPrice2 = (price - discount) * (1 + tax)  // 97.2 (clearer intent)

// Complex logical expressions
let age = 25
let hasLicense = true
let hasCar = false
let canDrive1 = age >= 18 && hasLicense || hasCar  // (age >= 18 && hasLicense) || hasCar
let canDrive2 = age >= 18 && (hasLicense || hasCar)  // Different logic, clearer

print("Can drive 1: \(canDrive1)")  // true
print("Can drive 2: \(canDrive2)")  // true
```

**Real-World iOS Example:**
```swift
// SwiftUI view calculations
struct ContentView: View {
    let baseWidth = 200
    let padding = 20
    let scale = 1.5
    
    var body: some View {
        Rectangle()
            .frame(width: CGFloat(baseWidth + padding * 2) * scale)
            // Evaluated as: baseWidth + (padding * 2) = 240, then * scale = 360
    }
}
```

**Best Practices (2025):**
1. **Use parentheses for clarity**, even when not strictly necessary
2. **Complex expressions:** Break into multiple lines with intermediate variables
3. **Code reviews:** Ensure expressions are readable by others

**Example of refactoring for clarity:**
```swift
// ❌ Hard to read
let result = a + b * c - d / e % f

// ✅ Clear and maintainable
let product = b * c
let quotient = d / e
let remainder = quotient % f
let result = a + product - remainder

// Or with parentheses
let result = a + (b * c) - ((d / e) % f)
```

**2025 Interview Tip:** Mention that modern Swift emphasizes **readability over cleverness**. Use parentheses and intermediate variables to make intent clear, even if you know the precedence rules.

---

## 📝 Practice Exercises

1. Write a program to calculate the area and perimeter of a rectangle using arithmetic operators
2. Create a grade calculator using comparison and ternary operators (90+:A, 80+:B, 70+:C, else:F)
3. Practice range operators by printing even numbers from 2 to 20
4. Use nil-coalescing to provide default values for optional user input
5. Write a function to check if a year is a leap year using logical operators

---

## 🔗 Navigation
- [← Previous: Step 2 - Data Types & Variables](../Step-2-Data-Types-and-Variables/README.md)
- [→ Next: Step 4 - Control Flow](../Step-4-Control-Flow/README.md)
