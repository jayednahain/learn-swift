# Step 25: Networking

## 📚 Description

Networking is essential for modern iOS apps. Learn URLSession, async/await, Codable, and API integration.

### What You'll Learn:
- URLSession for HTTP requests
- Async/await for networking
- Codable for JSON parsing
- Error handling in networking
- REST API integration

### Key Concepts:
- **URLSession**: Apple's networking API
- **URLRequest**: HTTP request configuration
- **Codable**: JSON encoding/decoding
- **async/await**: Modern asynchronous patterns
- **HTTP methods**: GET, POST, PUT, DELETE

---

## 💡 Clear Examples

### Example 1: Basic GET Request

```swift
func fetchUsers() async throws -> [User] {
    let url = URL(string: "https://api.example.com/users")!
    let (data, _) = try await URLSession.shared.data(from: url)
    let users = try JSONDecoder().decode([User].self, from: data)
    return users
}
```

### Example 2: POST Request with Codable

```swift
struct User: Codable {
    let name: String
    let email: String
}

func createUser(_ user: User) async throws {
    let url = URL(string: "https://api.example.com/users")!
    var request = URLRequest(url: url)
    request.httpMethod = "POST"
    request.setValue("application/json", forHTTPHeaderField: "Content-Type")
    request.httpBody = try JSONEncoder().encode(user)
    
    let (_, response) = try await URLSession.shared.data(for: request)
    guard (response as? HTTPURLResponse)?.statusCode == 201 else {
        throw NetworkError.invalidResponse
    }
}
```

---

## 🎯 Key Interview Questions

### Q1: How does async/await improve networking code?

**Answer:**
- More readable than completion handlers
- Automatic error propagation
- Sequential asynchronous code
- Better performance with structured concurrency

---

## 🔗 Key Takeaways

1. **URLSession** is the standard networking API
2. **Codable** simplifies JSON handling
3. **async/await** makes async code readable
4. **Error handling** is crucial for networking
5. **HTTPS required** for production apps

