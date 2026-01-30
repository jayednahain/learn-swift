# Step 22: iOS App Structure

## 📚 Description

Understanding the structure of an iOS app is fundamental to development. This includes the Xcode project structure, app lifecycle, configuration files, and how all components work together.

### What You'll Learn:
- Xcode project structure and organization
- App lifecycle (AppDelegate, SceneDelegate)
- Info.plist configuration
- Bundle identifiers and app IDs
- Build settings and schemes
- Asset management

### Key Concepts:
- **AppDelegate**: Manages app-level events
- **SceneDelegate**: Manages window/scene lifecycle (iOS 13+)
- **Info.plist**: App configuration file
- **Bundle Identifier**: Unique app identifier
- **Assets.xcassets**: Image and resource management
- **Storyboard/XIB**: Interface builder files

---

## 💡 Clear Examples

### Example 1: AppDelegate Lifecycle

```swift
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    var window: UIWindow?  // For iOS 12 and earlier
    
    // Called when app finishes launching
    func application(_ application: UIApplication, 
                    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        print("App Did Finish Launching")
        
        // Setup code here
        configureAppearance()
        registerForNotifications()
        
        return true
    }
    
    // Called when app becomes active
    func applicationDidBecomeActive(_ application: UIApplication) {
        print("App Did Become Active")
        // Resume tasks, refresh UI
    }
    
    // Called when app enters foreground
    func applicationWillEnterForeground(_ application: UIApplication) {
        print("App Will Enter Foreground")
    }
    
    // Called when app enters background
    func applicationDidEnterBackground(_ application: UIApplication) {
        print("App Did Enter Background")
        // Save data, release resources
    }
    
    // Called when app will terminate
    func applicationWillTerminate(_ application: UIApplication) {
        print("App Will Terminate")
        // Save critical data
    }
    
    private func configureAppearance() {
        // Global appearance customization
    }
    
    private func registerForNotifications() {
        // Push notification setup
    }
}
```

### Example 2: SceneDelegate (iOS 13+)

```swift
import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    
    var window: UIWindow?
    
    // Scene is being created
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        
        // Setup window manually (if needed)
        window = UIWindow(windowScene: windowScene)
        let viewController = ViewController()
        window?.rootViewController = viewController
        window?.makeKeyAndVisible()
        
        print("Scene Will Connect")
    }
    
    // Scene entered foreground
    func sceneDidBecomeActive(_ scene: UIScene) {
        print("Scene Did Become Active")
    }
    
    // Scene will enter foreground
    func sceneWillEnterForeground(_ scene: UIScene) {
        print("Scene Will Enter Foreground")
    }
    
    // Scene did enter background
    func sceneDidEnterBackground(_ scene: UIScene) {
        print("Scene Did Enter Background")
    }
    
    // Scene was disconnected
    func sceneDidDisconnect(_ scene: UIScene) {
        print("Scene Did Disconnect")
    }
}
```

### Example 3: Project Organization

```
MyApp/
├── AppDelegate.swift
├── SceneDelegate.swift
├── Info.plist
├── Models/
│   ├── User.swift
│   └── Product.swift
├── Views/
│   ├── HomeView.swift
│   └── ProfileView.swift
├── ViewControllers/
│   ├── HomeViewController.swift
│   └── ProfileViewController.swift
├── ViewModels/
│   ├── HomeViewModel.swift
│   └── ProfileViewModel.swift
├── Services/
│   ├── NetworkService.swift
│   └── DatabaseService.swift
├── Utilities/
│   ├── Extensions.swift
│   └── Constants.swift
└── Resources/
    ├── Assets.xcassets
    └── Localizable.strings
```

---

## 🎯 Interview Questions

### Q1: Explain the iOS app lifecycle and key delegate methods.

**Answer:**

**App Lifecycle States:**
1. **Not Running**: App hasn't been launched
2. **Inactive**: App in foreground but not receiving events
3. **Active**: App in foreground and receiving events
4. **Background**: App executing code but not visible
5. **Suspended**: App in memory but not executing code

**Key Methods:**
- `didFinishLaunchingWithOptions`: Initial setup
- `applicationDidBecomeActive`: Resume tasks
- `applicationWillResignActive`: Pause tasks
- `applicationDidEnterBackground`: Save state
- `applicationWillTerminate`: Final cleanup

### Q2: What's the difference between AppDelegate and SceneDelegate?

**Answer:**

**AppDelegate** (Process lifecycle):
- Manages app-level events
- Single instance per app
- Handles app launch, termination
- Push notifications, URL schemes

**SceneDelegate** (UI lifecycle):
- Manages window/scene events (iOS 13+)
- Multiple instances (multi-window support)
- Handles scene connection, activation
- Window management

**iOS 12 and earlier**: Only AppDelegate
**iOS 13+**: Both AppDelegate and SceneDelegate

### Q3: What is Info.plist and what does it contain?

**Answer:**

**Info.plist** is the app's configuration file containing:
- Bundle identifier
- App version and build number
- Supported orientations
- Required device capabilities
- Permission descriptions (camera, location, etc.)
- URL schemes
- Background modes
- App icons and launch screens

Example entries:
- `CFBundleIdentifier`: com.company.app
- `UIRequiredDeviceCapabilities`: camera
- `NSCameraUsageDescription`: "We need camera access"

---

## 🔗 Key Takeaways

1. **AppDelegate** manages app-level lifecycle
2. **SceneDelegate** manages UI lifecycle (iOS 13+)
3. **Info.plist** contains app configuration
4. **Organize by feature** or layer (MVC, MVVM)
5. **Bundle identifier** uniquely identifies your app
6. **Asset catalogs** manage images efficiently
7. **Understand lifecycle** for proper state management

