# Step 28: Design Patterns

## 📚 Description

Design patterns solve common software design problems. Learn MVC, MVVM, Delegation, Singleton, and more.

### What You'll Learn:
- MVC (Model-View-Controller)
- MVVM (Model-View-ViewModel)
- Delegation pattern
- Singleton pattern
- Observer pattern
- Factory pattern

### Key Concepts:
- **MVC**: Apple's traditional pattern
- **MVVM**: Better separation with ViewModels
- **Delegate**: One-to-one communication
- **Singleton**: Single shared instance
- **Observer**: One-to-many notifications

---

## 💡 Clear Examples

### Example 1: MVVM Pattern

```swift
// Model
struct User {
    let id: String
    let name: String
}

// ViewModel
class UserViewModel: ObservableObject {
    @Published var users: [User] = []
    
    func loadUsers() async {
        // Fetch from network
        users = [User(id: "1", name: "Alice")]
    }
}

// View
struct UserListView: View {
    @StateObject var viewModel = UserViewModel()
    
    var body: some View {
        List(viewModel.users, id: \.id) { user in
            Text(user.name)
        }
        .task {
            await viewModel.loadUsers()
        }
    }
}
```

### Example 2: Delegation Pattern

```swift
protocol DataSourceDelegate: AnyObject {
    func didReceiveData(_ data: Data)
}

class DataSource {
    weak var delegate: DataSourceDelegate?
    
    func fetchData() {
        let data = Data()
        delegate?.didReceiveData(data)
    }
}
```

---

## 🎯 Key Interview Questions

### Q1: What's the difference between MVC and MVVM?

**Answer:**
- **MVC**: View Controller handles both view and logic
- **MVVM**: ViewModel separates business logic
- MVVM more testable and reusable

---

## 🔗 Key Takeaways

1. **Choose pattern** based on app complexity
2. **MVVM** popular with SwiftUI
3. **Delegation** for one-to-one communication
4. **Avoid** massive view controllers
5. **Testability** improves with good patterns

