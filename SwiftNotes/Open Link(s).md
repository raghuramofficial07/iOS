<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Swift-iOS-blue?logo=swift&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# UIApplication.open() URL Handling in iOS

---

## » Table of Contents

- Introduction
- Code Usage
- Breakdown of Parameters
- Available Options
- Completion Handler
- Real-World Use Cases
- Code Variations
- Quick Reference Table
- Author Footer

---

## » Introduction

This documentation explains the use of `UIApplication.shared.open(...)` in Swift iOS development to open external URLs like websites, phone calls, emails, or apps like WhatsApp or Maps.

---

## » Code Usage

```swift
if let url = URL(string: "https://apple.com") {
    UIApplication.shared.open(url, options: [:], completionHandler: nil)
}
```

This opens the Apple website in Safari.

---

## » Breakdown of Parameters

| Parameter             | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `UIApplication.shared`| Access the shared app instance (singleton)                                 |
| `.open()`             | A method that opens the given URL                                           |
| `url`                 | A `URL` object (can be http, tel, mailto, etc.)                             |
| `options: [:]`        | Dictionary for extra configuration. `[:]` = no custom behavior              |
| `completionHandler`   | Optional callback executed after the URL is attempted to be opened         |

---

## » Available Options

The `options:` dictionary supports keys from `UIApplication.OpenExternalURLOptionsKey`.

| Key                            | Description                                                                 |
|--------------------------------|-----------------------------------------------------------------------------|
| `.universalLinksOnly`         | Opens only if app supports Universal Links (no Safari fallback)             |
| `.sourceApplication`          | [Rare] Used to specify which app initiated the request                      |
| `.annotation`                 | [Rare] Used to pass metadata when opening the URL                           |

### Example with Options:

```swift
UIApplication.shared.open(
    url,
    options: [.universalLinksOnly: true],
    completionHandler: { success in
        print(success ? "Opened!" : "Failed to open")
    }
)
```

---

## » Completion Handler

To handle success or failure:

```swift
UIApplication.shared.open(url, options: [:]) { success in
    if success {
        print("URL opened successfully.")
    } else {
        print("Failed to open URL.")
    }
}
```

---

## » Real-World Use Cases

| Use Case               | URL Format                              |
|------------------------|------------------------------------------|
| Open Website           | `https://apple.com`                     |
| Call Phone Number      | `tel://9876543210`                      |
| Open Google Maps       | `maps://?q=coffee`                      |
| Send Email             | `mailto:test@example.com`              |
| WhatsApp Message       | `whatsapp://send?text=Hello%20there`   |

---

## » Code Variations

### One Function for Multiple Buttons Using Tag:

```swift
@IBAction func openLink(_ sender: UIButton) {
    let urlMap = [
        1: "https://apple.com",
        2: "https://google.com",
        3: "https://github.com",
        4: "https://linkedin.com"
    ]

    if let urlString = urlMap[sender.tag],
       let url = URL(string: urlString),
       UIApplication.shared.canOpenURL(url) {
        UIApplication.shared.open(url, options: [:], completionHandler: nil)
    }
}
```

---

## » Quick Reference Table

| Term                   | Explanation                                          |
|------------------------|------------------------------------------------------|
| `.shared`              | Singleton access to the app                          |
| `.open()`              | Opens a resource via URL                             |
| `options`              | Dictionary of settings for the open operation        |
| `completionHandler`    | Callback to know if open was successful              |
| `universalLinksOnly`   | Only opens if app supports universal links           |

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

---
