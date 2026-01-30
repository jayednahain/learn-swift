# Step 19: Error Handling

## 📚 Description

**Error handling** in Swift provides a robust way to handle runtime errors gracefully using the `Error` protocol, throwing functions, and `do-catch` blocks. Swift's error handling is type-safe, explicit, and doesn't use exceptions like other languages. Understanding error handling is crucial for building reliable iOS applications.

### What You'll Learn:
- The Error protocol and error types
- Throwing and propagating errors with `throws`
- Catching errors with `do-catch`
- Converting errors with `try`, `try?`, and `try!`
- Error propagation in async code
- Custom error types and best practices

### Key Concepts:
- **Error Protocol**: Marker protocol for error types
- **throws**: Indicates function can throw errors
- **throw**: Throws an error
- **do-catch**: Handles errors
- **try**: Calls throwing function
- **try?**: Converts error to optional
- **try!**: Force-unwraps (crashes on error)
- **defer**: Cleanup code that always executes

### Benefits:
- **Type Safety**: Explicit error handling at compile time
- **No Exceptions**: Errors don't crash app unexpectedly
- **Clear Error Paths**: Easy to see what can fail
- **Exhaustive Handling**: Compiler ensures error handling
- **Composable**: Errors propagate through call stack

---

## 💡 Clear Examples

### Example 1: Basic Error Handling with Enums

```swift
// ========== DEFINING ERRORS ==========

enum ValidationError: Error {
    case emptyString
    case tooShort(minimumLength: Int)
    case tooLong(maximumLength: Int)
    case invalidCharacters
    case invalidEmail
}

enum NetworkError: Error {
    case noConnection
    case timeout
    case serverError(statusCode: Int)
    case invalidResponse
    case decodingFailed
}

// ========== THROWING FUNCTIONS ==========

func validatePassword(_ password: String) throws -> Bool {
    guard !password.isEmpty else {
        throw ValidationError.emptyString
    }
    
    guard password.count >= 8 else {
        throw ValidationError.tooShort(minimumLength: 8)
    }
    
    guard password.count <= 128 else {
        throw ValidationError.tooLong(maximumLength: 128)
    }
    
    let hasNumber = password.rangeOfCharacter(from: .decimalDigits) != nil
    let hasLetter = password.rangeOfCharacter(from: .letters) != nil
    
    guard hasNumber && hasLetter else {
        throw ValidationError.invalidCharacters
    }
    
    return true
}

// ========== CATCHING ERRORS ==========

func testPassword(_ password: String) {
    do {
        let isValid = try validatePassword(password)
        print("✅ Password is valid: \(isValid)")
    } catch ValidationError.emptyString {
        print("❌ Password cannot be empty")
    } catch ValidationError.tooShort(let minLength) {
        print("❌ Password must be at least \(minLength) characters")
    } catch ValidationError.tooLong(let maxLength) {
        print("❌ Password cannot exceed \(maxLength) characters")
    } catch ValidationError.invalidCharacters {
        print("❌ Password must contain letters and numbers")
    } catch {
        print("❌ Unknown error: \(error)")
    }
}

testPassword("")           // Password cannot be empty
testPassword("short")      // Password must be at least 8 characters
testPassword("validpass123") // ✅ Password is valid

// ========== TRY VARIANTS ==========

// try - Must handle error
func example1() {
    do {
        try validatePassword("test")
    } catch {
        print("Error: \(error)")
    }
}

// try? - Converts to optional (nil on error)
func example2() {
    let result = try? validatePassword("test")
    if result == nil {
        print("Validation failed")
    }
}

// try! - Force unwrap (crashes on error) - USE SPARINGLY
func example3() {
    let result = try! validatePassword("validpassword123")
    print("Definitely valid: \(result)")
}

// ========== ERROR IN INITIALIZER ==========

struct User {
    let email: String
    let age: Int
    
    enum UserError: Error {
        case invalidEmail
        case invalidAge
    }
    
    init(email: String, age: Int) throws {
        guard email.contains("@") else {
            throw UserError.invalidEmail
        }
        
        guard age >= 0 && age <= 150 else {
            throw UserError.invalidAge
        }
        
        self.email = email
        self.age = age
    }
}

// Usage
do {
    let user = try User(email: "test@example.com", age: 25)
    print("User created: \(user.email)")
} catch User.UserError.invalidEmail {
    print("Invalid email address")
} catch User.UserError.invalidAge {
    print("Invalid age")
} catch {
    print("Unknown error")
}
```

**Key Points:**
- Errors are enum types conforming to Error protocol
- Use `throws` keyword in function signature
- `throw` keyword throws an error
- `do-catch` handles specific error cases
- Multiple `try` variants for different scenarios

---

### Example 2: Error Propagation and Transforming Errors

```swift
// ========== ERROR PROPAGATION ==========

enum FileError: Error {
    case fileNotFound
    case permissionDenied
    case corrupted
    case diskFull
}

enum DatabaseError: Error {
    case connectionFailed
    case queryFailed(reason: String)
    case recordNotFound
}

// Function that throws
func readFile(at path: String) throws -> String {
    guard path.hasSuffix(".txt") else {
        throw FileError.corrupted
    }
    
    // Simulate file reading
    guard !path.isEmpty else {
        throw FileError.fileNotFound
    }
    
    return "File contents"
}

// Function that propagates errors
func processFile(at path: String) throws -> Int {
    // Error automatically propagates if readFile throws
    let contents = try readFile(at: path)
    return contents.count
}

// Function that catches and transforms errors
func safeProcessFile(at path: String) -> Result<Int, Error> {
    do {
        let count = try processFile(at: path)
        return .success(count)
    } catch {
        return .failure(error)
    }
}

// ========== MULTIPLE THROWING CALLS ==========

func complexOperation() throws -> String {
    // Multiple throwing operations
    let data1 = try readFile(at: "file1.txt")
    let data2 = try readFile(at: "file2.txt")
    
    // If any throws, function exits immediately
    return data1 + data2
}

// Usage
do {
    let result = try complexOperation()
    print("Success: \(result)")
} catch FileError.fileNotFound {
    print("One of the files was not found")
} catch FileError.corrupted {
    print("One of the files is corrupted")
} catch {
    print("Other error: \(error)")
}

// ========== RETHROWS ==========

// Function that takes throwing closure
func performOperation<T>(_ operation: () throws -> T) rethrows -> T {
    print("Starting operation...")
    let result = try operation()
    print("Operation completed")
    return result
}

// Works with throwing closure
let result1 = try performOperation {
    return try readFile(at: "test.txt")
}

// Works with non-throwing closure (no try needed)
let result2 = performOperation {
    return "Direct value"
}

// ========== DEFER FOR CLEANUP ==========

func processResource(at path: String) throws -> String {
    print("Opening resource...")
    
    // defer executes when leaving scope (even on error)
    defer {
        print("Closing resource...")
    }
    
    let contents = try readFile(at: path)
    
    // More processing...
    if contents.isEmpty {
        throw FileError.corrupted
    }
    
    return contents
    
    // defer executes here, before return or throw
}

do {
    let data = try processResource(at: "test.txt")
    print("Got data: \(data)")
} catch {
    print("Error: \(error)")
}
// Output:
// Opening resource...
// Closing resource...

// ========== MULTIPLE DEFER STATEMENTS ==========

func multipleDef

ers() {
    defer { print("First defer (executes last)") }
    defer { print("Second defer") }
    defer { print("Third defer (executes first)") }
    
    print("Function body")
}

multipleDef

ers()
// Output:
// Function body
// Third defer (executes first)
// Second defer
// First defer (executes last)
```

**Key Points:**
- Errors automatically propagate through `throws` functions
- Use `rethrows` for functions taking throwing closures
- `defer` ensures cleanup code always executes
- Multiple `defer` statements execute in reverse order
- Errors can be caught and transformed to other types

---

### Example 3: Custom Errors and Result Type

```swift
// ========== CUSTOM ERROR WITH LOCALIZED DESCRIPTION ==========

struct APIError: Error {
    let code: Int
    let message: String
    
    static let networkError = APIError(code: 1001, message: "Network connection failed")
    static let unauthorized = APIError(code: 401, message: "Unauthorized access")
    static let notFound = APIError(code: 404, message: "Resource not found")
}

extension APIError: LocalizedError {
    var errorDescription: String? {
        return "API Error \(code): \(message)"
    }
    
    var failureReason: String? {
        switch code {
        case 401:
            return "Authentication token is invalid or expired"
        case 404:
            return "The requested resource does not exist"
        default:
            return "An unknown error occurred"
        }
    }
    
    var recoverySuggestion: String? {
        switch code {
        case 401:
            return "Please log in again"
        case 404:
            return "Check the resource URL and try again"
        default:
            return "Please try again later"
        }
    }
}

// Usage
func fetchData(from url: String) throws -> Data {
    guard !url.isEmpty else {
        throw APIError.notFound
    }
    
    throw APIError.networkError
}

do {
    let data = try fetchData(from: "")
} catch let error as APIError {
    print(error.errorDescription ?? "")
    print(error.failureReason ?? "")
    print(error.recoverySuggestion ?? "")
}

// ========== RESULT TYPE ==========

enum Result<Success, Failure: Error> {
    case success(Success)
    case failure(Failure)
    
    func map<NewSuccess>(_ transform: (Success) -> NewSuccess) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return .success(transform(value))
        case .failure(let error):
            return .failure(error)
        }
    }
    
    func flatMap<NewSuccess>(_ transform: (Success) -> Result<NewSuccess, Failure>) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return transform(value)
        case .failure(let error):
            return .failure(error)
        }
    }
}

// Using Result type instead of throws
func fetchUser(id: String) -> Result<String, APIError> {
    if id.isEmpty {
        return .failure(.notFound)
    }
    return .success("User data for \(id)")
}

// Pattern matching
let result = fetchUser(id: "123")
switch result {
case .success(let user):
    print("Got user: \(user)")
case .failure(let error):
    print("Error: \(error.message)")
}

// Converting between Result and throws
func convertToThrows() throws -> String {
    let result = fetchUser(id: "123")
    switch result {
    case .success(let value):
        return value
    case .failure(let error):
        throw error
    }
}

// Or use get()
do {
    let user = try fetchUser(id: "123").get()
    print(user)
} catch {
    print("Failed: \(error)")
}

// ========== ASYNC ERROR HANDLING ==========

func fetchDataAsync() async throws -> String {
    // Simulate async work
    try await Task.sleep(nanoseconds: 1_000_000_000)
    
    throw APIError.networkError
}

// Usage with async/await
Task {
    do {
        let data = try await fetchDataAsync()
        print("Data: \(data)")
    } catch {
        print("Async error: \(error)")
    }
}
```

**Key Points:**
- Conform to `LocalizedError` for user-friendly messages
- `Result` type represents success or failure
- Convert between `Result` and `throws`
- Error handling works with async/await
- Custom errors can include additional context

---

## 🎯 Interview Questions

### Q1: What's the difference between `try`, `try?`, and `try!`? When should you use each?

**Answer:**

**`try` - Standard Error Handling:**
Must be used with `do-catch` block or in a throwing function.

```swift
func loadData() throws -> String {
    // ... can throw
    throw FileError.notFound
}

// Must catch
do {
    let data = try loadData()
    print(data)
} catch {
    print("Error: \(error)")
}

// Or propagate
func caller() throws {
    let data = try loadData()  // Propagates error
}
```

**`try?` - Optional Try:**
Converts result to optional, returns `nil` on error.

```swift
// Returns Optional<String>
let data = try? loadData()

if let data = data {
    print("Success: \(data)")
} else {
    print("Failed (don't know why)")
}

// Useful for optional chaining
let result = try? loadData()?.uppercased()

// Good for when you don't care about specific error
if try? saveSettings() != nil {
    print("Settings saved")
}
```

**`try!` - Force Try (Dangerous):**
Crashes if error thrown. Use only when 100% certain no error possible.

```swift
// Crashes on error! ⚠️
let data = try! loadData()

// Only use when:
// 1. In development/testing
// 2. Truly impossible to fail
let regex = try! NSRegularExpression(pattern: "[0-9]+")

// ❌ BAD: In production with user input
let userFile = try! loadFile(userPath)  // Can crash app!

// ✅ GOOD: With literal that can't fail
let date = try! Date("2024-01-01", strategy: .iso8601)
```

**Comparison Table:**

| Feature | try | try? | try! |
|---------|-----|------|------|
| Error handling | Required (do-catch) | Converts to nil | Crashes app |
| Return type | Original | Optional | Original |
| Use case | Normal errors | Optional behavior | Known safe only |
| Error info | Full error details | Lost | Lost (crash) |

**When to use:**
- **`try`**: 99% of the time (proper error handling)
- **`try?`**: When error details don't matter, optional behavior is fine
- **`try!`**: Rarely, only in truly safe scenarios or development

**Real-world example:**
```swift
// Good use of try
func loadUserProfile() throws -> Profile {
    do {
        let data = try loadData()
        let profile = try decode(data)
        return profile
    } catch {
        log("Failed to load profile: \(error)")
        throw error
    }
}

// Good use of try?
if let image = try? loadCachedImage() {
    display(image)  // Use if available, otherwise skip
}

// Acceptable use of try!
let dateFormatter = DateFormatter()
let knownGoodDate = try! dateFormatter.date(from: "2024-01-01")!
```

---

### Q2: Explain the `defer` statement. What are common use cases and gotchas?

**Answer:**

**`defer`** ensures code executes when leaving the current scope, regardless of how you exit (return, throw, break, etc.).

**Basic Usage:**
```swift
func processFile() throws {
    print("1. Opening file")
    
    defer {
        print("4. Closing file")  // Always executes
    }
    
    print("2. Reading file")
    
    if someCondition {
        print("3a. Early return")
        return  // defer executes here
    }
    
    print("3b. Normal path")
    // defer executes here too
}
```

**Common Use Cases:**

**1. Resource Cleanup:**
```swift
func databaseOperation() throws {
    let connection = openDatabase()
    defer { connection.close() }  // Ensures cleanup
    
    try connection.execute(query)
    // Even if error, connection closes
}
```

**2. Lock Management:**
```swift
func criticalSection() {
    lock.acquire()
    defer { lock.release() }  // Guaranteed release
    
    // Critical code
    if condition {
        return  // Lock still released
    }
}
```

**3. State Restoration:**
```swift
func temporaryChange() {
    let originalValue = setting
    defer { setting = originalValue }  // Restore
    
    setting = newValue
    // Do work with new value
    // Original restored on exit
}
```

**Execution Order (LIFO - Last In, First Out):**
```swift
func multipleDefers() {
    defer { print("First defer (executes LAST)") }
    print("Body 1")
    defer { print("Second defer") }
    print("Body 2")
    defer { print("Third defer (executes FIRST)") }
    print("Body 3")
}

// Output:
// Body 1
// Body 2
// Body 3
// Third defer (executes FIRST)
// Second defer
// First defer (executes LAST)
```

**Gotchas and Important Notes:**

**1. Defer Captures Values at Execution, Not Declaration:**
```swift
func captureExample() {
    var value = 1
    defer { print("Deferred value: \(value)") }
    
    value = 2
    print("Current value: \(value)")
}

// Output:
// Current value: 2
// Deferred value: 2  // ⚠️ Uses final value!
```

**2. Defer in Loops:**
```swift
func loopExample() {
    for i in 1...3 {
        defer { print("Defer \(i)") }
        print("Loop \(i)")
    }
}

// Output:
// Loop 1
// Defer 1  // Executes each iteration
// Loop 2
// Defer 2
// Loop 3
// Defer 3
```

**3. Defer and Return Values:**
```swift
func returnExample() -> Int {
    var value = 1
    defer { value = 2 }  // ⚠️ Doesn't affect return
    return value  // Returns 1, not 2
}

// Return value captured before defer executes
```

**4. Nested Defer:**
```swift
func nestedDefer() {
    defer {
        print("Outer 1")
        defer { print("Inner 1") }
        print("Outer 2")
        defer { print("Inner 2") }
        print("Outer 3")
    }
    print("Body")
}

// Output:
// Body
// Outer 1
// Outer 2
// Outer 3
// Inner 2  // LIFO within defer
// Inner 1
```

**Best Practices:**
✅ Use for cleanup (files, locks, connections)
✅ Keep defer simple and side-effect focused
✅ Place defer early (right after acquiring resource)
✅ Be aware of capture semantics
❌ Don't use for control flow
❌ Don't have complex logic in defer
❌ Don't expect defer to modify return values

**Real-World Example:**
```swift
func downloadAndProcess() throws -> Data {
    let tempFile = createTempFile()
    defer { 
        removeTempFile(tempFile)  // Cleanup guaranteed
    }
    
    let data = try download(to: tempFile)
    
    guard validate(data) else {
        throw ValidationError.invalid  // Still cleans up
    }
    
    return process(data)
}
```

---

### Q3: What is `rethrows` and how does it differ from `throws`? Provide examples.

**Answer:**

**`rethrows`** indicates a function only throws if its closure parameter throws. It provides better ergonomics when working with higher-order functions.

**Problem with `throws`:**
```swift
// Using throws
func perform(operation: () throws -> Void) throws {
    try operation()
}

// Even non-throwing closures require try
perform {
    print("Hello")  // Doesn't throw
}
// Error: Call can throw but is not marked with 'try'

try perform {  // ❌ Annoying: need try even though closure doesn't throw
    print("Hello")
}
```

**Solution with `rethrows`:**
```swift
// Using rethrows
func perform(operation: () throws -> Void) rethrows {
    try operation()
}

// Non-throwing closure: no try needed
perform {
    print("Hello")  // ✅ Works without try
}

// Throwing closure: requires try
try perform {
    throw MyError()  // ✅ Requires try
}
```

**Standard Library Examples:**
```swift
// Array.map uses rethrows
extension Array {
    func map<T>(_ transform: (Element) throws -> T) rethrows -> [T] {
        var result: [T] = []
        for element in self {
            result.append(try transform(element))
        }
        return result
    }
}

// Non-throwing transform
let numbers = [1, 2, 3]
let doubled = numbers.map { $0 * 2 }  // No try needed

// Throwing transform
let strings = ["1", "2", "invalid"]
let parsed = try strings.map { str -> Int in
    guard let num = Int(str) else {
        throw ParseError.invalid
    }
    return num
}  // try required because closure throws
```

**More Examples:**
```swift
// rethrows with multiple closures
func chain<T, U, V>(
    _ first: () throws -> T,
    _ second: (T) throws -> U,
    _ third: (U) throws -> V
) rethrows -> V {
    let t = try first()
    let u = try second(t)
    return try third(u)
}

// None throw: no try needed
let result1 = chain(
    { 1 },
    { $0 + 1 },
    { $0 * 2 }
)

// One throws: try required
let result2 = try chain(
    { 1 },
    { value throws -> Int in
        throw MyError()
    },
    { $0 * 2 }
)

// Custom higher-order function
func retry<T>(
    times: Int,
    operation: () throws -> T
) rethrows -> T {
    var lastError: Error?
    
    for _ in 0..<times {
        do {
            return try operation()
        } catch {
            lastError = error
        }
    }
    
    if let error = lastError {
        throw error
    }
    
    fatalError("Retry failed")
}

// Use without try
let value1 = retry(times: 3) {
    return 42  // Doesn't throw
}

// Use with try
let value2 = try retry(times: 3) {
    try riskyOperation()  // Throws
}
```

**Key Differences:**

| Feature | throws | rethrows |
|---------|--------|----------|
| Always requires try | Yes | Only if closure throws |
| Can throw directly | Yes | Only by propagating closure error |
| Use case | Function itself throws | Function throws only if parameter does |
| Example | `func load() throws` | `func map(_ f: () throws) rethrows` |

**When to use `rethrows`:**
- Higher-order functions (map, filter, reduce)
- Functions that execute closures
- Wrapper functions that delegate to closures
- When you want optional throwing behavior

**Real-World Pattern:**
```swift
func measure<T>(
    _ operation: () throws -> T
) rethrows -> (result: T, duration: TimeInterval) {
    let start = Date()
    let result = try operation()
    let duration = Date().timeIntervalSince(start)
    return (result, duration)
}

// Non-throwing
let (value, time) = measure {
    return expensive

Computation()
}

// Throwing
let (data, time) = try measure {
    try loadFromNetwork()
}
```

---

### Q4: How should you design custom error types? What are best practices?

**Answer:**

**Best Practices for Custom Error Types:**

**1. Use Enums with Associated Values:**
```swift
enum NetworkError: Error {
    case noConnection
    case timeout(seconds: TimeInterval)
    case serverError(statusCode: Int, message: String)
    case invalidURL(String)
    case decodingFailed(underlyingError: Error)
}

// Provides specific error information
throw NetworkError.serverError(statusCode: 500, message: "Internal Server Error")
```

**2. Implement LocalizedError for User-Facing Errors:**
```swift
enum ValidationError: LocalizedError {
    case emptyField(fieldName: String)
    case invalidFormat(fieldName: String, expected: String)
    case outOfRange(fieldName: String, min: Int, max: Int)
    
    var errorDescription: String? {
        switch self {
        case .emptyField(let field):
            return "\(field) cannot be empty"
        case .invalidFormat(let field, let expected):
            return "\(field) must be in \(expected) format"
        case .outOfRange(let field, let min, let max):
            return "\(field) must be between \(min) and \(max)"
        }
    }
    
    var failureReason: String? {
        switch self {
        case .emptyField:
            return "Required field is missing"
        case .invalidFormat:
            return "The format does not match requirements"
        case .outOfRange:
            return "Value is outside acceptable range"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .emptyField(let field):
            return "Please enter a value for \(field)"
        case .invalidFormat(let field, let expected):
            return "Please enter \(field) in \(expected) format"
        case .outOfRange(let field, let min, let max):
            return "Please enter a value between \(min) and \(max) for \(field)"
        }
    }
}
```

**3. Group Related Errors:**
```swift
enum APIError: Error {
    enum NetworkError: Error {
        case noConnection
        case timeout
        case dns Failed
    }
    
    enum ResponseError: Error {
        case invalidStatus(Int)
        case missingData
        case malformedResponse
    }
    
    enum ParsingError: Error {
        case decodingFailed(Error)
        case unexpectedFormat
    }
    
    case network(NetworkError)
    case response(ResponseError)
    case parsing(ParsingError)
}

// Usage
throw APIError.network(.timeout)
throw APIError.response(.invalidStatus(404))
```

**4. Include Context and Debugging Information:**
```swift
struct DetailedError: Error {
    let code: String
    let message: String
    let underlyingError: Error?
    let context: [String: Any]
    let timestamp: Date
    
    init(
        code: String,
        message: String,
        underlyingError: Error? = nil,
        context: [String: Any] = [:]
    ) {
        self.code = code
        self.message = message
        self.underlyingError = underlyingError
        self.context = context
        self.timestamp = Date()
    }
}

// Usage
throw DetailedError(
    code: "USER_001",
    message: "Failed to create user",
    underlyingError: databaseError,
    context: [
        "email": userEmail,
        "attemptCount": 3
    ]
)
```

**5. Use Custom Error Protocol for Domain-Specific Errors:**
```swift
protocol AppError: LocalizedError {
    var code: String { get }
    var category: ErrorCategory { get }
    var isRecoverable: Bool { get }
}

enum ErrorCategory {
    case network
    case validation
    case business
    case system
}

enum UserError: AppError {
    case invalidCredentials
    case accountLocked
    case sessionExpired
    
    var code: String {
        switch self {
        case .invalidCredentials: return "USER_001"
        case .accountLocked: return "USER_002"
        case .sessionExpired: return "USER_003"
        }
    }
    
    var category: ErrorCategory {
        return .business
    }
    
    var isRecoverable: Bool {
        switch self {
        case .invalidCredentials: return true
        case .accountLocked: return false
        case .sessionExpired: return true
        }
    }
    
    var errorDescription: String? {
        switch self {
        case .invalidCredentials:
            return "Invalid email or password"
        case .accountLocked:
            return "Your account has been locked"
        case .sessionExpired:
            return "Your session has expired"
        }
    }
}
```

**6. Error Wrapping Pattern:**
```swift
enum DataLayerError: Error {
    case storageError(underlying: Error)
    case networkError(underlying: Error)
    case parsingError(underlying: Error)
    
    init(wrapping error: Error) {
        if error is StorageError {
            self = .storageError(underlying: error)
        } else if error is NetworkError {
            self = .networkError(underlying: error)
        } else {
            self = .parsingError(underlying: error)
        }
    }
}

// Usage
do {
    try lowLevelOperation()
} catch {
    throw DataLayerError(wrapping: error)
}
```

**Anti-Patterns to Avoid:**
❌ Generic errors without context
```swift
enum Error: Swift.Error {
    case error  // Too vague!
}
```

❌ String-based errors
```swift
throw NSError(domain: "Error", code: -1, userInfo: nil)  // Not type-safe
```

❌ Too many error types
```swift
// Don't create separate error for every function
enum LoadUserError: Error { }
enum SaveUserError: Error { }
// Instead, use one: enum UserError: Error { }
```

**Summary - Error Design Checklist:**
✅ Use enum for related errors
✅ Include associated values for context
✅ Implement `LocalizedError` for user-facing messages
✅ Group related errors hierarchically
✅ Provide debugging information
✅ Make errors recoverable when possible
✅ Document what errors your functions throw
✅ Use consistent naming (e.g., `*Error`)

---

### Q5: How does error handling work with async/await? Explain Task cancellation.

**Answer:**

**Async/Await Error Handling:**

Combines `async` with `throws` for asynchronous operations that can fail.

**Basic Pattern:**
```swift
func fetchUser(id: String) async throws -> User {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, response) = try await URLSession.shared.data(from: url)
    
    guard let httpResponse = response as? HTTPURLResponse,
          (200...299).contains(httpResponse.statusCode) else {
        throw NetworkError.invalidResponse
    }
    
    let user = try JSONDecoder().decode(User.self, from: data)
    return user
}

// Usage
Task {
    do {
        let user = try await fetchUser(id: "123")
        print("User: \(user.name)")
    } catch {
        print("Failed: \(error)")
    }
}
```

**Multiple Async Throws:**
```swift
func loadUserProfile() async throws -> Profile {
    // Sequential - stops at first error
    let user = try await fetchUser(id: "123")
    let posts = try await fetchPosts(userId: user.id)
    let friends = try await fetchFriends(userId: user.id)
    
    return Profile(user: user, posts: posts, friends: friends)
}

// Parallel - using async let
func loadUserProfileParallel() async throws -> Profile {
    async let user = fetchUser(id: "123")
    async let posts = fetchPosts(userId: "123")
    async let friends = fetchFriends(userId: "123")
    
    // All three complete (or one throws)
    return try await Profile(
        user: user,
        posts: posts,
        friends: friends
    )
}
```

**Task Cancellation:**

**1. Checking for Cancellation:**
```swift
func longRunningTask() async throws -> String {
    for i in 1...100 {
        // Check if cancelled
        try Task.checkCancellation()  // Throws CancellationError
        
        // Or manual check
        if Task.isCancelled {
            print("Task was cancelled")
            throw CancellationError()
        }
        
        await Task.sleep(nanoseconds: 100_000_000)
        print("Step \(i)")
    }
    return "Completed"
}

// Cancel task
let task = Task {
    try await longRunningTask()
}

// Cancel after 1 second
DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
    task.cancel()
}
```

**2. Handling Cancellation:**
```swift
func fetchWithCancellation() async throws -> Data {
    let task = Task {
        try await URLSession.shared.data(from: url)
    }
    
    // Simulate cancellation
    task.cancel()
    
    do {
        let (data, _) = try await task.value
        return data
    } catch is CancellationError {
        print("Task was cancelled")
        throw NetworkError.cancelled
    } catch {
        throw error
    }
}
```

**3. Task Groups with Error Handling:**
```swift
func fetchMultipleUsers(ids: [String]) async throws -> [User] {
    try await withThrowingTaskGroup(of: User.self) { group in
        for id in ids {
            group.addTask {
                try await fetchUser(id: id)
            }
        }
        
        var users: [User] = []
        for try await user in group {
            users.append(user)
        }
        return users
    }
}

// First error cancels all tasks
```

**4. Cancellation with Cleanup:**
```swift
func downloadWithCleanup(url: URL) async throws -> Data {
    let tempFile = createTempFile()
    
    defer {
        removeTempFile(tempFile)  // Always cleanup
    }
    
    try Task.checkCancellation()
    
    let download = URLSession.shared.downloadTask(with: url)
    
    // Handle cancellation
    if Task.isCancelled {
        download.cancel()
        throw CancellationError()
    }
    
    let (data, _) = try await URLSession.shared.data(from: url)
    return data
}
```

**5. Structured Concurrency with Automatic Cancellation:**
```swift
await withTaskCancellationHandler {
    // Main operation
    try await longOperation()
} onCancel: {
    // Cleanup on cancellation
    print("Cancelling and cleaning up...")
    cleanup()
}
```

**Key Points:**
- `async throws` combines async and error handling
- `Task.checkCancellation()` throws `CancellationError`
- `Task.isCancelled` for manual checks
- Cancellation is cooperative (must check manually)
- Task groups cancel all tasks on first error
- Use `defer` for cleanup in async functions
- Cancellation doesn't force-stop code

**Best Practices:**
✅ Check cancellation in loops
✅ Handle `CancellationError` appropriately
✅ Clean up resources on cancellation
✅ Document cancellation behavior
✅ Use structured concurrency

---

## 📝 Practice Exercises

### Exercise 1: File System Operations
Create a robust file handling system with proper error handling.

**Requirements:**
1. Create custom `FileSystemError` enum
2. Implement functions:
   - `readFile(at path: String) throws -> String`
   - `writeFile(content: String, to path: String) throws`
   - `copyFile(from: String, to: String) throws`
3. Use `defer` for cleanup
4. Add error recovery mechanisms

**Challenge:** Add async versions with cancellation support.

---

### Exercise 2: Network Request Manager
Build a type-safe networking layer with comprehensive error handling.

**Requirements:**
1. Create error hierarchy:
   - `NetworkError`
   - `ParsingError`  
   - `ValidationError`
2. Implement `Result<Success, Failure>` based API
3. Add retry logic with `rethrows`
4. Implement timeout handling

**Challenge:** Add error logging and analytics.

---

### Exercise 3: Form Validation
Create a validation framework with detailed error reporting.

**Requirements:**
1. Create `ValidationError` with field-specific errors
2. Implement validators for:
   - Email
   - Password (with requirements)
   - Phone number
   - Credit card
3. Combine multiple validations
4. Provide user-friendly error messages

**Challenge:** Add async validation (check email availability).

---

## 🔗 Key Takeaways

1. **Explicit error handling** - No hidden exceptions
2. **Type-safe errors** - Errors are first-class types
3. **throws keyword** - Marks functions that can throw
4. **do-catch blocks** - Handle errors gracefully
5. **try variants** - `try`, `try?`, `try!` for different scenarios
6. **defer statement** - Guaranteed cleanup code
7. **rethrows** - Better ergonomics for higher-order functions
8. **Result type** - Alternative to throws for async/callbacks
9. **Custom errors** - Use enums with associated values
10. **async throws** - Combines async and error handling

**Best Practices:**
- Design clear error hierarchies
- Implement `LocalizedError` for user-facing errors
- Use `try?` sparingly (only when error doesn't matter)
- Avoid `try!` in production code
- Always use `defer` for cleanup
- Document what errors your functions throw

**Remember:** Swift error handling is about making failure explicit and recoverable!
