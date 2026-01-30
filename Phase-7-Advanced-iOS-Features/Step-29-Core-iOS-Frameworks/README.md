# Step 29: Core iOS Frameworks

## 📚 Description

iOS provides powerful frameworks for location, maps, notifications, and more.

### What You'll Learn:
- Core Location for GPS
- MapKit for maps
- UserNotifications for push/local notifications
- Core Animation for animations
- Combine for reactive programming

### Key Concepts:
- **Core Location**: GPS and location services
- **MapKit**: Map display and annotations
- **UserNotifications**: Local and remote notifications
- **Core Animation**: Layer-based animations
- **Combine**: Reactive streams

---

## 💡 Clear Examples

### Example 1: Core Location

```swift
import CoreLocation

class LocationManager: NSObject, CLLocationManagerDelegate {
    let manager = CLLocationManager()
    
    func requestLocation() {
        manager.delegate = self
        manager.requestWhenInUseAuthorization()
        manager.startUpdatingLocation()
    }
    
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        if let location = locations.first {
            print("Location: \(location.coordinate)")
        }
    }
}
```

### Example 2: Local Notifications

```swift
import UserNotifications

func scheduleNotification() {
    let content = UNMutableNotificationContent()
    content.title = "Reminder"
    content.body = "Don't forget to check the app!"
    
    let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 60, repeats: false)
    let request = UNNotificationRequest(identifier: "reminder", content: content, trigger: trigger)
    
    UNUserNotificationCenter.current().add(request)
}
```

---

## 🎯 Key Interview Questions

### Q1: How do you request location permissions?

**Answer:**
1. Add keys to Info.plist
2. Request authorization from CLLocationManager
3. Handle authorization status changes

---

## 🔗 Key Takeaways

1. **Core Location** requires permissions
2. **MapKit** integrates with Apple Maps
3. **Notifications** need user authorization
4. **Combine** for reactive patterns
5. **Always** handle permission denials

