# Step 27: Concurrency & Multithreading

## 📚 Description

Modern iOS development requires understanding concurrency. Learn async/await, actors, GCD, and thread safety.

### What You'll Learn:
- Async/await fundamentals
- Actors for thread safety
- Grand Central Dispatch (GCD)
- MainActor for UI updates
- Structured concurrency

### Key Concepts:
- **async/await**: Modern concurrency
- **Task**: Unit of asynchronous work
- **Actor**: Thread-safe reference type
- **@MainActor**: Ensure main thread execution
- **GCD**: Low-level concurrency API

---

## 💡 Clear Examples

### Example 1: Async/Await

```swift
func fetchData() async throws -> Data {
    let url = URL(string: "https://api.example.com/data")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return data
}

Task {
    do {
        let data = try await fetchData()
        print("Got data: \(data.count) bytes")
    } catch {
        print("Error: \(error)")
    }
}
```

### Example 2: Actor for Thread Safety

```swift
actor Counter {
    private var value = 0
    
    func increment() {
        value += 1
    }
    
    func getValue() -> Int {
        return value
    }
}

let counter = Counter()
await counter.increment()
let value = await counter.getValue()
```

### Example 3: MainActor for UI Updates

```swift
@MainActor
class ViewModel: ObservableObject {
    @Published var data: [Item] = []
    
    func loadData() async {
        let items = await fetchItems()
        data = items  // Safe - on main thread
    }
}
```

---

## 🎯 Key Interview Questions

### Q1: What's the difference between async/await and GCD?

**Answer:**
- **async/await**: High-level, structured, safer
- **GCD**: Low-level, more control, callback-based
- async/await preferred for new code

---

## 🔗 Key Takeaways

1. **async/await** for modern concurrency
2. **Actors** for thread-safe state
3. **@MainActor** for UI updates
4. **Task** for structured concurrency
5. **Avoid blocking** the main thread

