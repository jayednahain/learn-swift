# Step 24: UIKit Basics

## 📚 Description

UIKit is Apple's original imperative UI framework. Understanding UIKit is important for maintaining existing apps and advanced UI customization.

### What You'll Learn:
- UIViewController lifecycle
- UIView and subviews
- Auto Layout programmatically
- UITableView and UICollectionView
- Storyboards and XIBs

### Key Concepts:
- **UIViewController**: Manages view hierarchy
- **UIView**: Visual elements
- **Auto Layout**: Constraint-based layout
- **IBOutlet/IBAction**: Interface Builder connections
- **Delegate Pattern**: UIKit's primary communication pattern

---

## 💡 Clear Examples

### Example 1: ViewController Lifecycle

```swift
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        // Initial setup
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        // Before view appears
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        // After view appears
    }
}
```

---

## 🎯 Key Interview Questions

### Q1: Explain the UIViewController lifecycle methods.

**Answer:**
- `viewDidLoad`: View loaded into memory
- `viewWillAppear`: Before view appears
- `viewDidAppear`: After view appeared
- `viewWillDisappear`: Before view disappears
- `viewDidDisappear`: After view disappeared

---

## 🔗 Key Takeaways

1. **UIViewController** manages view lifecycle
2. **Auto Layout** for responsive layouts
3. **Delegate pattern** for communication
4. **UITableView** for lists
5. **Storyboards** vs programmatic UI

