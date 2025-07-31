<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Calculator-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# » Rounded Glass Card View (iOS UIKit)

> A reusable UIView subclass that supports corner radius, glassmorphic blur, and shadows — without affecting internal text transparency.

## » Table of Contents

- [Introduction](#introduction)
- [Full Source Code](#full-source-code)
- [Explanation Line by Line](#explanation-line-by-line)
- [Storyboard Integration](#storyboard-integration)
- [Concept Highlights](#concept-highlights)
- [Use Cases](#use-cases)
- [Quick Reference Table](#quick-reference-table)
- [Author Footer](#-author-footer)

## » Introduction

In UIKit, creating a *blurred, rounded* card with shadow effects while preserving the readability of inner content (like labels and buttons) requires a clear separation between **container styling** and **child content rendering**.  
This guide explains how to do that with a custom subclass called `RoundedCardView`.

## » Full Source Code

```swift
// See RoundedCardView.swift
```

## » Explanation Line by Line

| Line / Concept                  | Purpose                                                                 |
|--------------------------------|-------------------------------------------------------------------------|
| `@IBInspectable`               | Allows these values to be edited in Xcode's Storyboard panel           |
| `init(frame:)`                 | Called when view is created in code                                    |
| `required init?(coder:)`      | Called when view is loaded from storyboard (decoded by NSCoder)        |
| `insertSubview(blurView, at: 0)` | Places the blur **behind all inner elements**                          |
| `NSLayoutConstraint.activate([...])` | Pins the blur to fill the entire view                                 |
| `layer.cornerRadius`          | Rounds the outer container                                              |
| `blurView.layer.cornerRadius` | Rounds the blur effect layer                                            |
| `layer.shadowColor` etc.      | Applies subtle depth with shadow                                        |

## » Storyboard Integration

1. Create a `UIView` in Interface Builder.
2. Set its **Class** to `RoundedCardView` in the Identity Inspector.
3. Optionally adjust corner radius, shadow, and blur style via Attributes Inspector.
4. Add labels, buttons, or text as inner content — their opacity remains untouched.

## » Concept Highlights

- `masksToBounds = false`: Ensures shadow **is visible** outside the rounded view.
- `UIVisualEffectView`: Adds the **glass-like blur** behind your content.
- `@IBInspectable`: Makes the class reusable and configurable via the GUI.
- Safe `init(coder:)`: Required to support storyboard decoding properly.

## » Use Cases

- Profile cards with a soft blur background.
- Widgets with overlaid text on translucent panels.
- iOS-style dashboard components (e.g., weather, calendar views).

## » Quick Reference Table

| Feature               | Code Element                  |
|-----------------------|-------------------------------|
| Blur effect           | `UIVisualEffectView`          |
| Rounded corners       | `layer.cornerRadius`          |
| Shadow                | `layer.shadow...`             |
| Safe storyboard use   | `init(coder:)`                |
| Visual constraints    | `NSLayoutConstraint.activate` |

## Code 

```swift
import UIKit

class RoundedCardView: UIView {
    
    private let blurView = UIVisualEffectView(effect: UIBlurEffect(style: .systemThinMaterial))
    
    @IBInspectable var cornerRadius: CGFloat = 16
    @IBInspectable var shadowOpacity: Float = 0.15
    @IBInspectable var shadowRadius: CGFloat = 8
    @IBInspectable var shadowOffsetY: CGFloat = 4

    override init(frame: CGRect) {
        super.init(frame: frame)
        setupCard()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        setupCard()
    }

    private func setupCard() {
        blurView.translatesAutoresizingMaskIntoConstraints = false
        insertSubview(blurView, at: 0)

        NSLayoutConstraint.activate([
            blurView.topAnchor.constraint(equalTo: topAnchor),
            blurView.bottomAnchor.constraint(equalTo: bottomAnchor),
            blurView.leadingAnchor.constraint(equalTo: leadingAnchor),
            blurView.trailingAnchor.constraint(equalTo: trailingAnchor)
        ])

        blurView.clipsToBounds = true
        layer.cornerRadius = cornerRadius
        layer.masksToBounds = false

        layer.shadowColor = UIColor.black.cgColor
        layer.shadowOpacity = shadowOpacity
        layer.shadowRadius = shadowRadius
        layer.shadowOffset = CGSize(width: 0, height: shadowOffsetY)
    }

    override func layoutSubviews() {
        super.layoutSubviews()
        layer.cornerRadius = cornerRadius
        blurView.layer.cornerRadius = cornerRadius
    }
}
```

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit)  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
