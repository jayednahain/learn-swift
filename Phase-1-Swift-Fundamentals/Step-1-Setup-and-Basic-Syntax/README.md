# Step 1: Setup & Basic Syntax

## 📚 Description

Swift is a powerful and intuitive programming language developed by Apple for iOS, macOS, watchOS, and tvOS development. Before writing Swift code, you need to set up your development environment and understand the basic syntax.

### What You'll Learn:
- How to install Xcode (macOS required)
- Understanding Swift Playgrounds for quick practice
- Creating your first "Hello, World!" program
- Understanding `.swift` file structure
- Using Swift REPL for quick testing

### Key Concepts:
- **Xcode**: Apple's official IDE (Integrated Development Environment) for Swift and iOS development
- **Swift Playgrounds**: An interactive environment perfect for learning and experimenting with Swift code
- **REPL** (Read-Eval-Print Loop): A command-line tool for executing Swift code interactively
- **`.swift` files**: Source code files containing Swift programs

---

## 💡 Clear Examples

### Example 1: Hello, World! Program

```swift
// This is a single-line comment
print("Hello, World!")

/* 
   This is a 
   multi-line comment
*/

// Output: Hello, World!
```

**Explanation:**
- `print()` is a built-in function that outputs text to the console
- No need for semicolons at the end of statements (optional)
- Comments can be single-line (`//`) or multi-line (`/* */`)

---

### Example 2: Basic Swift File Structure

```swift
// File: MyFirstApp.swift

import Foundation  // Importing a standard library

// Global constants and variables
let appName = "My First App"
var versionNumber = 1.0

// Function definition
func greetUser(name: String) {
    print("Welcome to \(appName), \(name)!")
    print("Version: \(versionNumber)")
}

// Main execution
greetUser(name: "Swift Learner")

/* Output:
Welcome to My First App, Swift Learner!
Version: 1.0
*/
```

**Explanation:**
- `import Foundation` brings in Apple's foundational framework
- String interpolation using `\()` allows embedding variables in strings
- Functions are defined with `func` keyword
- Code executes from top to bottom

---

### Example 3: Using Swift REPL (Command Line)

```bash
# Open Terminal and type:
$ swift

# Now you're in Swift REPL mode:
1> let message = "Learning Swift is fun!"
message: String = "Learning Swift is fun!"

2> print(message)
Learning Swift is fun!

3> let sum = 10 + 20
sum: Int = 30

4> :quit  // Exit REPL
```

**Explanation:**
- REPL allows you to test Swift code instantly without creating files
- Each line is evaluated immediately
- Great for quick experiments and learning syntax
- Type `:quit` to exit REPL

---

## 🎯 2025 Interview Questions

### Question 1: What is the difference between Swift Playgrounds and Xcode?

**Answer:**
- **Swift Playgrounds**: 
  - Interactive learning environment designed for beginners
  - Available on iPad and Mac
  - Great for experimenting with Swift code quickly
  - Immediate visual feedback
  - Limited to Swift language features (not full iOS app development)
  
- **Xcode**:
  - Full-featured IDE for professional development
  - Used to build complete iOS, macOS, watchOS, and tvOS apps
  - Includes Interface Builder, debugger, testing tools, and simulators
  - macOS only
  - Industry-standard tool for Swift/iOS development

**When to use what:**
- Use Playgrounds for learning Swift syntax and quick prototyping
- Use Xcode for building actual applications

---

### Question 2: Why doesn't Swift require semicolons at the end of statements?

**Answer:**
Swift's compiler is smart enough to determine where statements end, making semicolons optional. This design choice:
- Makes code cleaner and more readable
- Reduces syntax errors for beginners
- Aligns with modern language design trends (like Python, Kotlin)
- Semicolons are only required when writing multiple statements on the same line:
  ```swift
  let x = 10; let y = 20; print(x + y)  // Semicolons needed here
  ```

---

### Question 3: What are the system requirements for iOS development with Swift in 2025?

**Answer:**
**Hardware:**
- Mac computer (MacBook, iMac, Mac Mini, or Mac Studio)
- Minimum 8GB RAM (16GB recommended)
- At least 50GB free disk space for Xcode and simulators

**Software:**
- macOS Monterey (12.0) or later (Sonoma 14.0+ recommended for latest features)
- Xcode 15.0 or later
- Swift 5.9+

**For App Store deployment:**
- Apple Developer Account ($99/year for publishing to App Store)
- Valid Apple ID

**Alternative (without Mac):**
- Cloud-based Mac services (MacStadium, MacInCloud) - rental options
- Note: Hackintosh is not recommended and violates Apple's terms

---

### Question 4: What is the purpose of `import Foundation` in a Swift file?

**Answer:**
`import Foundation` brings in Apple's Foundation framework, which provides:
- **Essential data types**: String, Array, Dictionary, Date, URL, etc.
- **File management**: FileManager, Bundle
- **Networking**: URLSession
- **Data formatting**: DateFormatter, NumberFormatter
- **Threading**: Timer, OperationQueue

It's one of the most commonly imported frameworks because it provides fundamental building blocks for Swift applications. Without importing Foundation, you'd only have access to Swift's standard library (basic types like Int, Bool, etc.).

**Example:**
```swift
// Without Foundation
let number = 42  // Works (Swift standard library)

// With Foundation
import Foundation
let date = Date()  // Requires Foundation
let url = URL(string: "https://apple.com")  // Requires Foundation
```

---

### Question 5: Explain the concept of "Swift is a type-safe language." Why is this important?

**Answer:**
Swift is type-safe, meaning:
- Every variable and constant must have a specific type
- The compiler checks type compatibility at compile time
- Prevents type-related errors before the code runs
- Ensures that operations are performed on compatible types

**Benefits:**
1. **Fewer runtime errors**: Type mismatches caught during compilation
2. **Better code clarity**: Type information makes code self-documenting
3. **Improved performance**: Type information allows compiler optimizations
4. **Enhanced IDE support**: Better autocomplete and error detection

**Example:**
```swift
var age: Int = 25
age = "twenty-five"  // ❌ Compile error: Cannot assign String to Int

var name: String = "John"
let length = name.count  // ✅ Compiler knows name is String

// Swift infers types when possible
let price = 99.99  // Compiler infers: Double
let city = "New York"  // Compiler infers: String
```

This compile-time type checking prevents common bugs that plague dynamically-typed languages, making Swift both safe and fast.

---

## 📝 Practice Exercises

1. Install Xcode from the Mac App Store
2. Create your first Swift Playground and write 5 different `print()` statements
3. Open Terminal and experiment with Swift REPL for 10 minutes
4. Create a `.swift` file and run it using `swift filename.swift` command
5. Research the latest Swift version (Swift 6.0 in 2025) and its new features

---

## 🔗 Next Step
Continue to [Step 2: Data Types & Variables](../Step-2-Data-Types-and-Variables/README.md)
