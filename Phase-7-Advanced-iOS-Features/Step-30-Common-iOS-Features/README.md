# Step 30: Common iOS Features

## 📚 Description

Implement common iOS features like camera access, photo library, sharing, and web views.

### What You'll Learn:
- Camera and photo library access
- Activity View Controller for sharing
- WKWebView for web content
- Gestures (tap, swipe, pinch)
- Dark mode support
- Localization

### Key Concepts:
- **UIImagePickerController**: Camera/photos
- **PHPhotoLibrary**: Photo library access
- **UIActivityViewController**: Share sheet
- **WKWebView**: Web browser component
- **UIGestureRecognizer**: Touch handling

---

## 💡 Clear Examples

### Example 1: Camera Access

```swift
import UIKit

class CameraViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
    
    func openCamera() {
        let picker = UIImagePickerController()
        picker.sourceType = .camera
        picker.delegate = self
        present(picker, animated: true)
    }
    
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        if let image = info[.originalImage] as? UIImage {
            // Use captured image
        }
        picker.dismiss(animated: true)
    }
}
```

### Example 2: Sharing Content

```swift
func shareContent() {
    let text = "Check out this app!"
    let url = URL(string: "https://example.com")!
    
    let activityVC = UIActivityViewController(
        activityItems: [text, url],
        applicationActivities: nil
    )
    
    present(activityVC, animated: true)
}
```

---

## 🎯 Key Interview Questions

### Q1: How do you support Dark Mode?

**Answer:**
- Use semantic colors
- Test in both light and dark modes
- Override with `.preferredColorScheme()` if needed

---

## 🔗 Key Takeaways

1. **Request permissions** for camera/photos
2. **Share sheet** built-in with UIActivityViewController
3. **WKWebView** for web content
4. **Gestures** enhance user experience
5. **Dark mode** and localization for polish

