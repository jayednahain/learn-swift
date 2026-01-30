# Step 26: Data Persistence

## 📚 Description

Learn how to save and retrieve data in iOS apps using UserDefaults, FileManager, and Core Data.

### What You'll Learn:
- UserDefaults for simple data
- FileManager for file storage
- Core Data for complex data
- Codable for serialization
- Data migration strategies

### Key Concepts:
- **UserDefaults**: Key-value storage
- **FileManager**: File system access
- **Core Data**: Object graph management
- **Codable**: Serialization protocol
- **Data migration**: Updating stored data

---

## 💡 Clear Examples

### Example 1: UserDefaults

```swift
// Save
UserDefaults.standard.set("John", forKey: "username")
UserDefaults.standard.set(25, forKey: "age")

// Retrieve
let username = UserDefaults.standard.string(forKey: "username")
let age = UserDefaults.standard.integer(forKey: "age")
```

### Example 2: File Storage with Codable

```swift
struct User: Codable {
    let name: String
    let age: Int
}

func saveUser(_ user: User) throws {
    let encoder = JSONEncoder()
    let data = try encoder.encode(user)
    let url = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
        .appendingPathComponent("user.json")
    try data.write(to: url)
}

func loadUser() throws -> User {
    let url = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
        .appendingPathComponent("user.json")
    let data = try Data(contentsOf: url)
    let decoder = JSONDecoder()
    return try decoder.decode(User.self, from: data)
}
```

---

## 🎯 Key Interview Questions

### Q1: When should you use UserDefaults vs Core Data?

**Answer:**
- **UserDefaults**: Simple settings, small data
- **Core Data**: Complex relationships, large datasets, querying

---

## 🔗 Key Takeaways

1. **UserDefaults** for simple preferences
2. **FileManager** for files and documents
3. **Core Data** for complex data models
4. **Codable** simplifies serialization
5. **Choose right tool** for data size and complexity

