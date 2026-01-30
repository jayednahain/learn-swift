# Step 20: Access Control

## 📚 Description

**Access Control** in Swift restricts access to parts of your code from code in other source files and modules. It enables you to hide implementation details and specify a preferred interface for your types. Swift provides five access levels: `open`, `public`, `internal`, `fileprivate`, and `private`.

### What You'll Learn:
- Five access control levels
- Access control for types, properties, methods
- Access control in frameworks and modules
- Getters and setters with different access levels
- Access control patterns and best practices

### Key Concepts:
- **Module**: A single unit of code distribution (framework or app)
- **Source File**: A single Swift file
- **open**: Accessible everywhere, can be subclassed/overridden outside module
- **public**: Accessible everywhere, but not subclassable outside module
- **internal**: Accessible within same module (default)
- **fileprivate**: Accessible within same source file
- **private**: Accessible within enclosing declaration

### Benefits:
- **Encapsulation**: Hide implementation details
- **API Design**: Control what's exposed to users
- **Safety**: Prevent misuse of internal APIs
- **Maintainability**: Clear public interface

---

## 💡 Clear Examples

### Example 1: Basic Access Levels

```swift
// ========== OPEN (Most permissive) ==========
// Can be accessed anywhere AND subclassed/overridden outside module

open class OpenClass {
    open var openProperty = "accessible everywhere"
    open func openMethod() { }
}

// In another module:
class SubclassFromAnotherModule: OpenClass {  // ✅ Allowed
    override func openMethod() { }  // ✅ Allowed
}

// ========== PUBLIC ==========
// Can be accessed anywhere but NOT subclassed outside module

public class PublicClass {
    public var publicProperty = "accessible everywhere"
    public func publicMethod() { }
}

// In another module:
// class SubclassFromAnotherModule: PublicClass { }  // ❌ Error

// ========== INTERNAL (Default) ==========
// Accessible within same module only

internal class InternalClass {  // 'internal' is default, can omit
    var internalProperty = "accessible in module"
    func internalMethod() { }
}

class AnotherInternalClass {  // Also internal by default
    let instance = InternalClass()  // ✅ Same module
}

// ========== FILEPRIVATE ==========
// Accessible within same source file

fileprivate class FileprivateClass {
    fileprivate var property = "accessible in file"
}

extension FileprivateClass {  // In same file
    func extendedMethod() {
        print(property)  // ✅ Accessible
    }
}

// ========== PRIVATE (Most restrictive) ==========
// Accessible within enclosing declaration

class OuterClass {
    private var privateProperty = "only in OuterClass"
    
    func accessPrivate() {
        print(privateProperty)  // ✅ Accessible
    }
    
    class NestedClass {
        func tryAccess() {
            // print(privateProperty)  // ❌ Error: Different declaration
        }
    }
}

extension OuterClass {  // Same file
    func extensionMethod() {
        print(privateProperty)  // ✅ Accessible in Swift 4+
    }
}
```

**Key Points:**
- `open` and `public` for framework APIs
- `internal` is default (most common)
- `fileprivate` for file-level privacy
- `private` for tightest encapsulation

---

### Example 2: Getters and Setters with Different Access

```swift
// ========== PRIVATE SET, PUBLIC GET ==========

struct User {
    private(set) var id: String  // Read-only outside, writable inside
    private(set) var createdAt: Date
    
    var name: String  // Fully public
    
    init(name: String) {
        self.id = UUID().uuidString
        self.createdAt = Date()
        self.name = name
    }
    
    mutating func regenerateID() {
        self.id = UUID().uuidString  // ✅ Can modify internally
    }
}

var user = User(name: "Alice")
print(user.id)  // ✅ Can read
// user.id = "new-id"  // ❌ Error: Cannot assign
user.name = "Bob"  // ✅ Can modify

// ========== FILEPRIVATE SET ==========

public struct BankAccount {
    public fileprivate(set) var balance: Double
    public let accountNumber: String
    
    public init(accountNumber: String, initialBalance: Double) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }
}

extension BankAccount {  // Same file
    mutating func deposit(_ amount: Double) {
        balance += amount  // ✅ Can modify in same file
    }
}

// ========== INTERNAL SET, PUBLIC GET ==========

public class Configuration {
    public internal(set) var apiKey: String = ""
    
    // Public API for reading
    public var isConfigured: Bool {
        return !apiKey.isEmpty
    }
    
    // Internal API for setting
    internal func configure(apiKey: String) {
        self.apiKey = apiKey
    }
}

// ========== COMPUTED PROPERTY WITH ACCESS CONTROL ==========

struct Rectangle {
    var width: Double
    var height: Double
    
    private var area: Double {  // Private computed property
        return width * height
    }
    
    var description: String {
        return "Rectangle: \(width)x\(height), area: \(area)"
    }
}
```

**Key Points:**
- Use `private(set)` for read-only properties
- Different access for getter and setter
- Common pattern for encapsulation

---

### Example 3: Access Control in Practice

```swift
// ========== API DESIGN ==========

// Public interface
public protocol DataSource {
    func fetchData() async throws -> Data
}

// Internal implementation
class NetworkDataSource: DataSource {
    // Private properties
    private let baseURL: URL
    private let session: URLSession
    
    // Private helper
    private func buildRequest() -> URLRequest {
        return URLRequest(url: baseURL)
    }
    
    // Public API
    func fetchData() async throws -> Data {
        let request = buildRequest()
        let (data, _) = try await session.data(for: request)
        return data
    }
    
    init(baseURL: URL) {
        self.baseURL = baseURL
        self.session = URLSession.shared
    }
}

// ========== FRAMEWORK DESIGN ==========

// Public class for users
public class PaymentProcessor {
    // Private implementation details
    private let apiClient: APIClient
    private var transactionCache: [String: Transaction] = [:]
    
    // Private helper methods
    private func validateCard(_ card: CreditCard) -> Bool {
        return card.number.count == 16
    }
    
    private func logTransaction(_ transaction: Transaction) {
        transactionCache[transaction.id] = transaction
    }
    
    // Public API
    public init(apiKey: String) {
        self.apiClient = APIClient(apiKey: apiKey)
    }
    
    public func processPayment(
        card: CreditCard,
        amount: Decimal
    ) async throws -> Transaction {
        guard validateCard(card) else {
            throw PaymentError.invalidCard
        }
        
        let transaction = try await apiClient.charge(card: card, amount: amount)
        logTransaction(transaction)
        return transaction
    }
}

// ========== SINGLETON PATTERN ==========

class Logger {
    static let shared = Logger()  // Internal singleton
    
    private init() { }  // Private init prevents external instantiation
    
    private var logs: [String] = []
    
    func log(_ message: String) {
        logs.append(message)
        print(message)
    }
    
    fileprivate func exportLogs() -> [String] {
        return logs
    }
}

// Usage
Logger.shared.log("Hello")
// let logger = Logger()  // ❌ Error: init is private

// ========== NESTED TYPES ==========

public struct APIResponse {
    public let data: Data
    public let statusCode: Int
    
    // Private nested enum
    private enum CodingKeys: String, CodingKey {
        case data
        case statusCode = "status_code"
    }
    
    // Public nested type
    public enum Status {
        case success
        case failure
    }
    
    public var status: Status {
        return (200...299).contains(statusCode) ? .success : .failure
    }
}
```

**Key Points:**
- Hide implementation details (private/fileprivate)
- Expose minimal public API
- Use private init for singletons
- Nested types inherit enclosing type's access level

---

## 🎯 Interview Questions

### Q1: Explain the five access control levels in Swift. When would you use each?

**Answer:**

**Access Control Hierarchy** (Most to Least Permissive):

**1. `open` - Most Permissive:**
- Accessible anywhere (inside and outside module)
- Can be subclassed/overridden outside module
- Only for classes and class members

```swift
// In Framework
open class BaseViewController {
    open func customize() { }
}

// In App (different module)
class MyViewController: BaseViewController {  // ✅ Can subclass
    override func customize() { }  // ✅ Can override
}
```

**When to use:**
- Framework base classes meant to be subclassed
- Designing extensible libraries
- UIKit classes (UIViewController, UIView, etc.)

**2. `public` - Publicly Accessible:**
- Accessible anywhere
- Cannot be subclassed/overridden outside module

```swift
public class FinalClass {  // ✅ Accessible, not subclassable outside
    public func method() { }
}

// In another module:
// class Sub: FinalClass { }  // ❌ Error
```

**When to use:**
- Framework APIs you want accessible but not extensible
- Preventing inheritance from external modules
- Most framework types

**3. `internal` - Module-Level (Default):**
- Accessible within same module
- Default if no access modifier specified

```swift
class InternalClass { }  // internal by default
internal func internalFunc() { }
```

**When to use:**
- App code (default)
- Internal framework implementation
- Most of your code (implicit)

**4. `fileprivate` - File-Level:**
- Accessible within same source file only

```swift
fileprivate class Helper { }

extension SomeClass {  // Same file
    func useHelper() {
        let h = Helper()  // ✅ Accessible
    }
}
```

**When to use:**
- Sharing between type and its extensions in same file
- Helper types/functions for single file
- Implementation details contained to one file

**5. `private` - Most Restrictive:**
- Accessible within enclosing declaration only

```swift
class MyClass {
    private var secret = "hidden"
    
    func reveal() {
        print(secret)  // ✅ Accessible
    }
}

extension MyClass {  // Same file (Swift 4+)
    func alsoReveal() {
        print(secret)  // ✅ Accessible
    }
}
```

**When to use:**
- Hiding implementation details
- Preventing access from same file but different types
- Tightest encapsulation

**Comparison Table:**

| Level | Same Declaration | Same File | Same Module | Different Module |
|-------|-----------------|-----------|-------------|------------------|
| private | ✅ | ✅ (extension) | ❌ | ❌ |
| fileprivate | ✅ | ✅ | ❌ | ❌ |
| internal | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ (no subclass) |
| open | ✅ | ✅ | ✅ | ✅ (can subclass) |

**Decision Tree:**
1. Is it a framework? → `open` or `public`
2. Should it be subclassable externally? → `open` vs `public`
3. Internal to app? → `internal` (default)
4. Specific to one file? → `fileprivate`
5. Hiding implementation? → `private`

---

### Q2: What's the difference between `private` and `fileprivate`? Why have both?

**Answer:**

**Historical Context:**
Swift 3 made `private` truly private (declaration-scoped), and added `fileprivate` for file-scoped access. Previously, `private` meant file-scope.

**Key Differences:**

**`private` - Declaration Scope:**
```swift
class MyClass {
    private var privateVar = "private"
    
    func accessPrivate() {
        print(privateVar)  // ✅ Accessible
    }
    
    class Nested {
        func tryAccess(instance: MyClass) {
            // print(instance.privateVar)  // ❌ Error: Different declaration
        }
    }
}

struct OtherStruct {  // Same file
    func tryAccess(instance: MyClass) {
        // print(instance.privateVar)  // ❌ Error: Different type
    }
}
```

**`fileprivate` - File Scope:**
```swift
fileprivate class Helper {
    fileprivate var value = 10
}

class MyClass {
    func useHelper() {
        let helper = Helper()
        print(helper.value)  // ✅ Accessible (same file)
    }
}

extension Helper {
    func doubleValue() -> Int {
        return value * 2  // ✅ Accessible
    }
}
```

**When to Use Each:**

**Use `private` when:**
- Hiding implementation within a type
- Preventing access even from extensions (different file)
- Tightest possible encapsulation

```swift
class BankAccount {
    private var balance: Double = 0  // Only this class
    
    func deposit(_ amount: Double) {
        balance += amount  // ✅
    }
}

extension BankAccount {  // Different file
    func sneakyAccess() {
        // print(balance)  // ❌ Error if extension in different file
    }
}
```

**Use `fileprivate` when:**
- Sharing between type and extensions in same file
- Multiple related types in same file
- Protocol conformance in same file

```swift
// File: User.swift

fileprivate protocol Cacheable {
    var cacheKey: String { get }
}

struct User {
    fileprivate var id: String
    var name: String
}

extension User: Cacheable {
    var cacheKey: String {
        return id  // ✅ Accessible (same file)
    }
}

class UserManager {
    func cache(_ user: User) {
        cache.set(user.id, forKey: user.cacheKey)  // ✅ Accessible
    }
}
```

**Common Pattern - Extensions in Same File:**
```swift
class ViewController {
    private var dataSource: [String] = []  // Private to class
    fileprivate var selectedIndex: Int = 0  // Shared with extensions
}

// Same file
extension ViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // return dataSource.count  // ❌ Error: private
        return selectedIndex  // ✅ fileprivate accessible
    }
}
```

**Why Have Both?**
1. **Granular Control**: Choose appropriate scope
2. **Intent**: `private` shows "truly internal", `fileprivate` shows "file-shared"
3. **Maintenance**: Easier to refactor with proper scoping
4. **Safety**: Prevent accidental access

**Best Practice:**
- Start with `private`
- Change to `fileprivate` only if needed for extensions/related types
- Avoid `fileprivate` across multiple unrelated types in same file

---

### Q3: How do you design a good public API using access control?

**Answer:**

**Principles of API Design with Access Control:**

**1. Hide Implementation Details:**
```swift
public class ImageCache {
    // Private implementation
    private var cache: NSCache<NSString, UIImage> = NSCache()
    private let queue = DispatchQueue(label: "ImageCache")
    private var downloadTasks: [URL: Task<UIImage, Error>] = [:]
    
    // Public interface
    public func image(for url: URL) async throws -> UIImage {
        // Implementation hidden
        if let cached = cache.object(forKey: url.absoluteString as NSString) {
            return cached
        }
        
        let image = try await downloadImage(from: url)
        cache.setObject(image, forKey: url.absoluteString as NSString)
        return image
    }
    
    public func clearCache() {
        cache.removeAllObjects()
    }
    
    // Private helper
    private func downloadImage(from url: URL) async throws -> UIImage {
        // Implementation details
        fatalError("Implementation")
    }
}
```

**2. Progressive Disclosure:**
```swift
// Basic public API
public struct NetworkClient {
    public init() { }
    
    public func request(_ url: URL) async throws -> Data {
        try await request(url, method: .get)
    }
    
    // Advanced API
    public func request(
        _ url: URL,
        method: HTTPMethod,
        headers: [String: String]? = nil,
        body: Data? = nil
    ) async throws -> Data {
        // Full control for advanced users
        fatalError("Implementation")
    }
}
```

**3. Read-Only Public Properties:**
```swift
public class User {
    public let id: String
    public private(set) var name: String  // Read-only publicly
    public private(set) var email: String
    private var hashedPassword: String  // Completely private
    
    public init(id: String, name: String, email: String) {
        self.id = id
        self.name = name
        self.email = email
        self.hashedPassword = ""
    }
    
    // Public methods to modify
    public func updateName(_ newName: String) throws {
        guard !newName.isEmpty else {
            throw ValidationError.emptyName
        }
        name = newName
    }
}
```

**4. Builder Pattern for Complex Configuration:**
```swift
public class DatabaseConfiguration {
    public let host: String
    public let port: Int
    public let database: String
    private let username: String  // Sensitive
    private let password: String  // Sensitive
    
    // Private init
    private init(host: String, port: Int, database: String, username: String, password: String) {
        self.host = host
        self.port = port
        self.database = database
        self.username = username
        self.password = password
    }
    
    // Public builder
    public class Builder {
        private var host = "localhost"
        private var port = 5432
        private var database = "default"
        private var username = ""
        private var password = ""
        
        public init() { }
        
        public func host(_ host: String) -> Builder {
            self.host = host
            return self
        }
        
        public func port(_ port: Int) -> Builder {
            self.port = port
            return self
        }
        
        public func credentials(username: String, password: String) -> Builder {
            self.username = username
            self.password = password
            return self
        }
        
        public func build() throws -> DatabaseConfiguration {
            guard !username.isEmpty else {
                throw ConfigError.missingUsername
            }
            return DatabaseConfiguration(
                host: host,
                port: port,
                database: database,
                username: username,
                password: password
            )
        }
    }
}

// Usage
let config = try DatabaseConfiguration.Builder()
    .host("localhost")
    .port(5432)
    .credentials(username: "admin", password: "secret")
    .build()
```

**5. Protocol-Based Design:**
```swift
// Public protocol
public protocol StorageProvider {
    func save(_ data: Data, forKey key: String) async throws
    func load(forKey key: String) async throws -> Data?
}

// Internal implementations
class UserDefaultsStorage: StorageProvider {
    func save(_ data: Data, forKey key: String) async throws {
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func load(forKey key: String) async throws -> Data? {
        return UserDefaults.standard.data(forKey: key)
    }
}

class FileSystemStorage: StorageProvider {
    private let fileManager = FileManager.default
    
    func save(_ data: Data, forKey key: String) async throws {
        // Implementation
    }
    
    func load(forKey key: String) async throws -> Data? {
        // Implementation
        return nil
    }
}

// Public factory
public class Storage {
    public static func userDefaults() -> StorageProvider {
        return UserDefaultsStorage()
    }
    
    public static func fileSystem() -> StorageProvider {
        return FileSystemStorage()
    }
}
```

**API Design Checklist:**
✅ Minimal public surface area
✅ Hide implementation details
✅ Use `private(set)` for read-only properties
✅ Provide clear, type-safe APIs
✅ Document public members
✅ Use protocols for flexibility
✅ Version your public API carefully
✅ Never expose internal types in public API

**Anti-Patterns:**
❌ Everything public by default
❌ Exposing internal implementation types
❌ Mutable public properties without validation
❌ Public properties that should be methods

---

### Q4: Explain access control with inheritance and protocols.

**Answer:**

**Access Control Rules for Inheritance:**

**1. Subclass Access Level:**
A subclass can't be more accessible than its superclass.

```swift
public class PublicBase { }
internal class InternalDerived: PublicBase { }  // ✅ OK

internal class InternalBase { }
// public class PublicDerived: InternalBase { }  // ❌ Error
```

**2. Overriding Methods:**
Can override to make MORE accessible (not less).

```swift
open class Base {
    internal func method() { }
}

class Derived: Base {
    public override func method() { }  // ✅ More accessible
}

// ❌ Can't make less accessible:
// private override func method() { }
```

**3. Open vs Public Classes:**
```swift
// Framework code
open class OpenClass {
    open func openMethod() { }
    public func publicMethod() { }
}

public class PublicClass {
    public func method() { }
}

// App code (different module)
class AppClass1: OpenClass {  // ✅ Can subclass open class
    override func openMethod() { }  // ✅ Can override open method
    // override func publicMethod() { }  // ❌ Can't override public method
}

// class AppClass2: PublicClass { }  // ❌ Can't subclass public class
```

**Access Control with Protocols:**

**1. Protocol Conformance:**
Protocol's access level determines where it can be conformed to.

```swift
public protocol PublicProtocol {
    func method()
}

// ✅ Can conform with any access level
private struct PrivateType: PublicProtocol {
    func method() { }  // Must be at least internal
}

public struct PublicType: PublicProtocol {
    public func method() { }  // Match protocol's level
}
```

**2. Protocol Requirements:**
Implementing type must provide at least the protocol's access level.

```swift
public protocol DataSource {
    var data: String { get }  // Implicitly public requirement
}

struct MyDataSource: DataSource {
    var data: String  // ✅ Internal is OK for internal conformance
}

public struct PublicDataSource: DataSource {
    public var data: String  // ✅ Must be public for public conformance
}
```

**3. Protocol Extension Access:**
```swift
public protocol Describable {
    var description: String { get }
}

extension Describable {
    // Default implementation inherits protocol's access
    public func fullDescription() -> String {
        return "Description: \(description)"
    }
}
```

**4. Conditional Conformance:**
```swift
public protocol Identifiable {
    var id: String { get }
}

extension Array: Identifiable where Element: Identifiable {
    public var id: String {
        return map { $0.id }.joined(separator: ",")
    }
}
```

**Complex Example:**
```swift
// Public protocol
public protocol Vehicle {
    var speed: Int { get }
    func start()
}

// Public base class
open class BaseVehicle {
    internal var engineRunning = false
    
    open func ignition() {
        engineRunning = true
    }
}

// Internal class conforming to public protocol
class Car: BaseVehicle, Vehicle {
    // Must provide public access for protocol requirements
    var speed: Int = 0
    
    func start() {  // Internal by default, but satisfies protocol
        ignition()
        speed = 10
    }
}

// Public class conforming publicly
public class PublicCar: BaseVehicle, Vehicle {
    public var speed: Int = 0  // Must be public
    
    public func start() {  // Must be public
        ignition()
        speed = 10
    }
    
    public override func ignition() {  // Can override and make public
        super.ignition()
        print("Public ignition")
    }
}
```

**Key Rules:**
1. Subclass ≤ Superclass access
2. Override can increase access (not decrease)
3. `open` allows external subclassing/overriding
4. Protocol requirements need matching access level
5. Protocol extensions inherit protocol's access

---

### Q5: What are common access control mistakes and anti-patterns?

**Answer:**

**Anti-Pattern 1: Everything Public**
```swift
// ❌ BAD: Exposing everything
public class UserManager {
    public var users: [User] = []  // Direct access
    public var cache: NSCache<NSString, User> = NSCache()
    public var database: Database = Database()
    
    public func addUser(_ user: User) {
        users.append(user)
    }
}

// ✅ GOOD: Encapsulated
public class UserManager {
    private var users: [User] = []  // Hidden
    private var cache: NSCache<NSString, User> = NSCache()
    private var database: Database = Database()
    
    public func addUser(_ user: User) {
        users.append(user)
        cache.setObject(user, forKey: user.id as NSString)
    }
    
    public func user(withId id: String) -> User? {
        // Controlled access
        return users.first { $0.id == id }
    }
}
```

**Anti-Pattern 2: Public Mutable Properties Without Validation**
```swift
// ❌ BAD: No validation
public struct User {
    public var email: String
    public var age: Int
}

var user = User(email: "", age: -5)  // Invalid!

// ✅ GOOD: Validated setters
public struct User {
    public let id: String
    
    private var _email: String
    public var email: String {
        get { _email }
        set {
            guard newValue.contains("@") else { return }
            _email = newValue
        }
    }
    
    private var _age: Int
    public var age: Int {
        get { _age }
        set {
            guard newValue >= 0 && newValue <= 150 else { return }
            _age = newValue
        }
    }
}
```

**Anti-Pattern 3: Exposing Internal Types in Public API**
```swift
// ❌ BAD: Internal type in public API
internal struct InternalError: Error { }

public class APIClient {
    public func request() throws -> Data {
        throw InternalError()  // ❌ Exposes internal type
    }
}

// ✅ GOOD: Public error type
public enum APIError: Error {
    case networkError
    case invalidResponse
}

public class APIClient {
    public func request() throws -> Data {
        throw APIError.networkError  // ✅ Public error
    }
}
```

**Anti-Pattern 4: Overusing fileprivate**
```swift
// ❌ BAD: Unnecessary fileprivate
fileprivate class Helper { }  // Only used in one place
fileprivate func helperFunction() { }

class MyClass {
    fileprivate var value = 10  // Could be private
}

// ✅ GOOD: Use private when possible
private class Helper { }
private func helperFunction() { }

class MyClass {
    private var value = 10
}
```

**Anti-Pattern 5: Mixing Access Levels Inconsistently**
```swift
// ❌ BAD: Inconsistent access
public class PaymentProcessor {
    var apiKey: String = ""  // Internal (inconsistent)
    public var amount: Double = 0
    private let client = APIClient()
    
    func process() { }  // Internal (inconsistent)
}

// ✅ GOOD: Consistent design
public class PaymentProcessor {
    private let apiKey: String
    private let client: APIClient
    
    public var amount: Double
    
    public init(apiKey: String) {
        self.apiKey = apiKey
        self.client = APIClient(apiKey: apiKey)
        self.amount = 0
    }
    
    public func process() async throws {
        // Public method
    }
}
```

**Anti-Pattern 6: Not Using `private(set)`**
```swift
// ❌ BAD: Fully mutable
public struct Score {
    public var value: Int = 0
}

var score = Score()
score.value = 1000000  // Anyone can set

// ✅ GOOD: Controlled mutation
public struct Score {
    public private(set) var value: Int = 0
    
    public mutating func increment() {
        value += 1
    }
    
    public mutating func add(_ points: Int) {
        value += points
    }
}
```

**Anti-Pattern 7: Public Init Without Validation**
```swift
// ❌ BAD: No validation in public init
public struct CreditCard {
    public let number: String
    
    public init(number: String) {
        self.number = number  // No validation
    }
}

let invalid = CreditCard(number: "abc")  // Invalid!

// ✅ GOOD: Validated initialization
public struct CreditCard {
    public let number: String
    
    private init(number: String) {
        self.number = number
    }
    
    public static func create(number: String) throws -> CreditCard {
        guard number.count == 16, number.allSatisfy({ $0.isNumber }) else {
            throw ValidationError.invalidCardNumber
        }
        return CreditCard(number: number)
    }
}
```

**Best Practices Summary:**
✅ Default to `private`, open up as needed
✅ Use `private(set)` for read-only properties
✅ Hide implementation details
✅ Validate in public APIs
✅ Keep public surface area minimal
✅ Be consistent with access levels
✅ Document public APIs
✅ Use protocols to hide concrete types

---

## 📝 Practice Exercises

### Exercise 1: Design a Framework API
Create a simple networking framework with proper access control.

**Requirements:**
1. Public `NetworkClient` class
2. Private session management
3. Public request methods
4. Internal logging
5. `private(set)` properties
6. Use `open` for extensible base classes

---

### Exercise 2: Refactor for Encapsulation
Take poorly designed code and apply proper access control.

Given: A class with all public properties
Task: Refactor with proper encapsulation

---

### Exercise 3: Build a Secure Configuration System
Create a configuration manager hiding sensitive data.

**Requirements:**
1. Public read access to non-sensitive config
2. Private storage of API keys/passwords
3. Internal methods for system configuration
4. Builder pattern for setup

---

## 🔗 Key Takeaways

1. **Five levels**: `open` > `public` > `internal` > `fileprivate` > `private`
2. **Default is internal** - Explicit about public APIs
3. **Encapsulation**: Hide implementation details
4. **private(set)**: Read-only public properties
5. **open vs public**: Subclassing control for frameworks
6. **fileprivate**: Share within file
7. **Start restrictive**: Begin with `private`, open up as needed
8. **Inheritance rules**: Subclass ≤ superclass access
9. **Protocol conformance**: Match protocol's access level
10. **API design**: Minimal public surface area

**Remember:** Access control is about API design and maintenance, not security!
