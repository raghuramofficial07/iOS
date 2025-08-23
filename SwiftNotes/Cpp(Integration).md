<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/VSR%20Expo-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# » VSR Expo: Swift ↔ C++ Bridging with Segmented Control

## » Table of Contents
1. Introduction
2. Project Concept
3. Swift UI Flow
4. Bridging with C++
5. Full Code Examples
6. Storyboard Setup
7. Quick Reference Table
8. Author Footer

---

## » Introduction
This document explains how **Swift** and **C++** are bridged together using an `extern "C"` API for performing advanced mathematical calculations such as **log**, **sin**, **cos**, and **π (pi)**.  
The user interacts with a **UISegmentedControl** in Swift, selects an operation, and passes it to the C++ backend for computation.

---

## » Project Concept
- User enters a number in a **UITextField**.  
- Selects one operation from segmented control:
  - Logarithm (log)
  - Sine (sin)
  - Cosine (cos)
  - Pi (π value)
- Operation is converted to a **C-string** and passed into a C++ method.  
- C++ performs the calculation and returns result to Swift.  
- Swift displays the result on screen.

---

## » Swift UI Flow

```swift
@IBAction func calculateTapped(_ sender: Any) {
    guard let inputText = inputField.text,
          let value = Double(inputText) else {
        resultLabel.text = "Invalid input"
        return
    }

    let operation: String
    switch segmentControl.selectedSegmentIndex {
    case 0: operation = "log"
    case 1: operation = "sin"
    case 2: operation = "cos"
    case 3: operation = "pi"
    default: operation = ""
    }

    let cString = (operation as NSString).utf8String
    let result = performCalculation(cString, value)
    resultLabel.text = "Result: \(result)"
}
```

---

## » Bridging with C++

### Header (CalcAPI.h)
```cpp
#ifdef __cplusplus
extern "C" {
#endif

double performCalculation(const char* operation, double value);

#ifdef __cplusplus
}
#endif
```

### Implementation (CalcAPI.cpp)
```cpp
#include <cmath>
#include <string>
#include "CalcAPI.h"

extern "C" double performCalculation(const char* operation, double value) {
    std::string op(operation);

    if (op == "log") {
        return log(value);
    } else if (op == "sin") {
        return sin(value);
    } else if (op == "cos") {
        return cos(value);
    } else if (op == "pi") {
        return M_PI;
    } else {
        return 0.0;
    }
}
```

---

## » Full Code Examples

- **ViewController.swift**
Handles segmented control, passes input to C++ function, updates result label.  

- **CalcAPI.h**
Declares the extern C function for Swift visibility.  

- **CalcAPI.cpp**
Implements all mathematical operations using standard math functions.  

---

## » Storyboard Setup
1. Add **UITextField** → connect to `inputField`.  
2. Add **UISegmentedControl** with 4 segments: `Log`, `Sin`, `Cos`, `Pi`.  
3. Add **UIButton** → connect to `calculateTapped`.  
4. Add **UILabel** → connect to `resultLabel`.  

---

## » Quick Reference Table

| Operation | Input Example | Output Example |
|-----------|---------------|----------------|
| Log       | 10            | 2.302          |
| Sin       | 1 rad         | 0.841          |
| Cos       | 1 rad         | 0.540          |
| Pi        | -             | 3.14159        |

---

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit) + C++ Backend  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
