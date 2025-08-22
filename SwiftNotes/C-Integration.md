<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

# » C++ Bridging with Swift (Monochrome Guide)

## » Table of Contents
- Introduction
- File Structure
- C++ Header and Implementation
- Bridging Header
- Swift Usage
- Multiple Functions Example
- Separate API Header (`CalcAPI.h`)
- Final Folder Layout
- Author Footer

---

## » Introduction
This document provides a **step-by-step monochrome-style guide** to integrating C++ functions into a Swift iOS project using **Bridging Headers**.  
The approach is designed for clarity and maintainability, ensuring that C++ logic is reusable and Swift code remains clean.

---

## » File Structure
Minimal structure for an Xcode project with C++ bridging:

```
MyApp/
 ├── CalcAPI.h              (C API declarations for Swift)
 ├── CalcLib.cpp            (C++ implementations)
 ├── MyApp-Bridging-Header.h (includes CalcAPI.h)
 ├── ViewController.swift   (Swift UI logic)
 ├── Info.plist
 └── MyApp.xcodeproj
```

---

## » C++ Header and Implementation

### CalcAPI.h
```cpp
// CalcAPI.h
// This file contains the C-style API exposed to Swift

#ifdef __cplusplus
extern "C" {
#endif

double performCalculation(const char* operation, double value);

double square(double value);
double cube(double value);
double add(double a, double b);
double subtract(double a, double b);

#ifdef __cplusplus
} // extern "C"
#endif
```

### CalcLib.cpp
```cpp
#include "CalcAPI.h"
#include <cmath>
#include <cstring>

double performCalculation(const char* operation, double value) {
    if (std::strcmp(operation, "log") == 0) {
        return std::log(value);
    }
    else if (std::strcmp(operation, "pie") == 0) {
        return 3.141592653589793 * value;
    }
    else {
        return -1.0;
    }
}

double square(double value) { return value * value; }
double cube(double value) { return value * value * value; }
double add(double a, double b) { return a + b; }
double subtract(double a, double b) { return a - b; }
```

---

## » Bridging Header

### MyApp-Bridging-Header.h
```cpp
// MyApp-Bridging-Header.h
// Bridge Swift with CalcAPI.h

#include "CalcAPI.h"
```

This ensures that all C functions declared in **CalcAPI.h** are visible to Swift.

To verify via terminal:
```bash
cd MyApp
xcodebuild -showBuildSettings | grep "SWIFT_OBJC_BRIDGING_HEADER"
```

Expected:
```
SWIFT_OBJC_BRIDGING_HEADER = MyApp/MyApp-Bridging-Header.h
```

---

## » Swift Usage

### Example in ViewController.swift
```swift
import UIKit

class ViewController: UIViewController {
    @IBOutlet weak var inputField: UITextField!
    @IBOutlet weak var resultLabel: UILabel!

    @IBAction func calculatePressed(_ sender: UIButton) {
        guard let userText = inputField.text else { return }
        let op = userText.cString(using: .utf8)

        if let operation = op {
            let result = performCalculation(operation, 10.0)
            resultLabel.text = "Result: \(result)"
        }

        let squared = square(5.0)
        let added   = add(10.0, 20.0)
        print("Square = \(squared), Add = \(added)")
    }
}
```

---

## » Multiple Functions Example

- **performCalculation** → String-based dispatcher (`log`, `pie`, etc.)
- **square, cube, add, subtract** → Directly callable functions

Both are exposed to Swift through the bridging header.

---

## » Separate API Header (CalcAPI.h)

This design choice keeps the exposed API clean and reusable.  
The bridging header only includes `CalcAPI.h` instead of duplicating function declarations.

---

## » Final Folder Layout
```
MyApp/
 ├── CalcAPI.h
 ├── CalcLib.cpp
 ├── MyApp-Bridging-Header.h
 ├── ViewController.swift
 ├── Info.plist
 └── MyApp.xcodeproj
```

---

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit + C++ Integration)  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
