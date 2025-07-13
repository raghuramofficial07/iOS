<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>
# FlashChat iOS App

CocoaPods Integration with CLTypingLabel — Terminal Style Guide

# Overview

This guide documents how I integrated the CLTypingLabel pod into the FlashChat iOS app using CocoaPods. The goal was to add a typing animation effect on the welcome screen title with minimal code and maximum impact — all through terminal commands.

# STEP 1 — Install Homebrew (if not already installed)

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

# STEP 2 — Install CocoaPods via Homebrew
`brew install cocoapods`

# STEP 3 — Verify CocoaPods installation
`pod --version   # Example: 1.13.0`

# STEP 4 — Navigate to your Xcode project folder
`cd ~/Documents/iOS/FlashChat`

# STEP 5 — Initialize CocoaPods in your project
`pod init`

# STEP 6 — Edit Podfile to add platform & pod
`nano Podfile`
```
platform :ios, '13.0'

target 'FlashChat' do
  use_frameworks!
  pod 'CLTypingLabel'
end
```
# STEP 7 — Install the pod
`pod install`

# STEP 8 — Open the generated workspace file (always use this)
open FlashChat.xcworkspace
```

# Inside Podfile, replace with:

platform :ios, '13.0'

target 'FlashChat' do
  use_frameworks!
  pod 'CLTypingLabel'
end
```

open FlashChat.xcworkspace

Integrate CLTypingLabel in Xcode

1. Update Storyboard
Select your UILabel.
In the Identity Inspector, set the class to CLTypingLabel.
Connect it to an IBOutlet:
```swift
@IBOutlet weak var titleLabel: CLTypingLabel!
```
2. Modify WelcomeViewController.swift
```swift
import UIKit
import CLTypingLabel

class WelcomeViewController: UIViewController {

    @IBOutlet weak var titleLabel: CLTypingLabel!

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // The typing animation triggers automatically
        titleLabel.text = "⚡️FlashChat"
    }
}
```
```
Vemparala Sri Satya RaghuRam  
#CodeWithRaghuRam | #BeyondCertifications | #IndustryOriented
```



