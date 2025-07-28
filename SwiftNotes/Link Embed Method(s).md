
<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/OpenURL-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# » Unified URL Button Handler in iOS (UIKit)

## » Table of Contents
- [Introduction](#introduction)
- [UIButton Tag-Based URL Mapping](#uibutton-tag-based-url-mapping)
- [Swift Code](#swift-code)
- [Storyboard Setup](#storyboard-setup)
- [Concept Highlights](#concept-highlights)
- [Author Footer](#author-footer)

---

## » Introduction

This guide explains how to simplify multiple `@IBAction` methods for opening different URLs in your iOS app. Instead of writing separate functions for each button, we use one method with `UIButton` tags to handle them all.

---

## » UIButton Tag-Based URL Mapping

Assign different tag values to your buttons in **Storyboard > Attributes Inspector**:

| Button Name | Tag |
|-------------|-----|
| Apple       | 1   |
| Google      | 2   |
| GitHub      | 3   |
| LinkedIn    | 4   |

---

## » Swift Code

### Single Function Approach

```swift
@IBAction func openLinks(_ sender: UIButton) {
    let urls = [
        1: "https://apple.com",
        2: "https://google.com",
        3: "https://github.com",
        4: "https://linkedin.com"
    ]

    if let urlString = urls[sender.tag],
       let url = URL(string: urlString) {
        UIApplication.shared.open(url)
    }
}
```

### 🔁 Alternative (Splitting Logic Out)

```swift
func handleURL(forTag tag: Int) {
    let urls = [
        1: "https://apple.com",
        2: "https://google.com",
        3: "https://github.com",
        4: "https://linkedin.com"
    ]

    if let urlString = urls[tag],
       let url = URL(string: urlString) {
        UIApplication.shared.open(url)
    }
}

@IBAction func openLinks(_ sender: UIButton) {
    handleURL(forTag: sender.tag)
}
```

---

## » Storyboard Setup

1. Drag four buttons into your view controller.
2. Assign **tags** (1 to 4) as shown in the table above.
3. Connect all 4 buttons to the single `@IBAction openLinks(_:)`.

---

## » Concept Highlights

- 🔁 **Reusability**: One method handles all URL openings.
- 🧠 **Tags**: Map buttons to values for cleaner, scalable code.
- ✅ **Safe Opening**: Checks if URL is valid before opening.

---

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit)  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
