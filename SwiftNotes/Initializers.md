<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/VSR%20Expo-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# » VSR Expo – Initializers & Storyboard Integration

## » Table of Contents
- Introduction
- Why Initializers Matter
- Programmatic vs Storyboard Initializers
- What Happens if You Use Only One
- Quick Reference Table
- Author Footer

## » Introduction
This document captures the discussion around **Swift initializers**, specifically focusing on how custom UI components (like a rounded segmented control) should be initialized when using **Storyboards** and **Programmatic UI**.

## » Why Initializers Matter
In Swift, `init` methods are special constructors that allow us to properly set up a class or struct.  
For **UI components**, initializers ensure that custom properties (like corner radius, shadow, borders) are applied correctly when the view is created.

## » Programmatic vs Storyboard Initializers

### 1. Programmatic Initializer
```swift
override init(frame: CGRect) {
    super.init(frame: frame)
    setupUI() // Apply your corner radius, colors, etc.
}
```
- Called when you create a UI element in code, e.g.:
```swift
let button = RoundedButton(frame: CGRect(x: 0, y: 0, width: 100, height: 40))
```

### 2. Storyboard Initializer
```swift
required init?(coder: NSCoder) {
    super.init(coder: coder)
    setupUI() // Applies styling when dragged from storyboard
}
```
- Called when a UI element is loaded from **Storyboard or XIB**.

## » What Happens if You Use Only One
- **Only `init?(coder:)`:**  
  Works fine with Storyboard. Crashes if you try to create it in code.  
- **Only `init(frame:)`:**  
  Works fine when creating programmatically. Crashes if you drag it from Storyboard.  
- **Both present:**  
  Works in **all cases**.

## » Quick Reference Table

| Initializer       | When It’s Called                 | Needed For |
|-------------------|----------------------------------|------------|
| `init(frame:)`    | When creating views in code      | Programmatic UI |
| `init?(coder:)`   | When loading views from Storyboard/XIB | Storyboard UI |

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit)  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
