# Swift Protocol + Delegate (Clean Forever Notes)

<p align="center">
  <img src="https://img.shields.io/badge/Swift-black?logo=swift&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/SwiftUI-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Protocols-black?logo=code&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Delegates-black?logo=code&logoColor=white&labelColor=black&color=black">
</p>

# Protocol + Delegate (Single Clean Format)

---

## » Table of Contents
1. Introduction  
2. What is a Protocol?  
3. What is a Delegate?  
4. Core Flow (UI → Manager → Delegate → Implementation)  
5. Minimal Example (Sum of Two Numbers)  
6. If/Else Example (Multiple Delegate Paths)  
7. Slow Process (When to use async/await)  
8. Quick Reference Table  
9. Tags  
10. Author Footer  

---

## » Introduction

This document gives a **clean and future-proof understanding** of **Protocol + Delegate** in Swift / SwiftUI.

The goal:
- Keep code **simple**
- Keep flow **clear**
- Understand how the **result notification** reaches the correct receiver

---

## » What is a Protocol?

A **protocol** is a **rulebook**.

It defines **what functions must exist**, but it does not execute anything by itself.

Example rule:

```swift
protocol SumResultProtocol {
    func didGetSum(_ result: Int)
}
```

Meaning:
- Any class/struct that *conforms* to `SumResultProtocol`
- **must implement** `didGetSum(_:)`

---

## » What is a Delegate?

A **delegate** is a **protocol-typed reference** that can hold an **instance** of a class that conforms to the protocol.

Example:

```swift
var delegate: SumResultProtocol?
```

Key points:
- It can be `nil` (optional)
- It can store **any object** that matches the protocol rules
- It is used for **callback / notification**

---

## » Core Flow (UI → Manager → Delegate → Implementation)

This is the cleanest mental model:

```
UI Action
   ↓
Manager does work
   ↓
Manager calls Delegate method
   ↓
Delegate forwards call to the real implementation class
   ↓
UI / Receiver uses the result
```

Simple one-line memory hook:

**UI → Manager → calls delegate → delegate triggers the real implementation.**

---

## » Minimal Example (Sum of Two Numbers)

### 1) Protocol (Rule)

```swift
protocol SumResultProtocol {
    func didGetSum(_ result: Int)
}
```

### 2) Manager / Worker (Does work and notifies delegate)

```swift
final class Adder {

    var delegate: SumResultProtocol?

    func add(_ a: Int, _ b: Int) {
        let sum = a + b

        // Notify whoever is stored in delegate
        delegate?.didGetSum(sum)
    }
}
```

### 3) Receiver (Implements protocol)

```swift
final class ResultPrinter: SumResultProtocol {

    func didGetSum(_ result: Int) {
        print("Sum is:", result)
    }
}
```

### 4) Connect + Run

```swift
let adder = Adder()
let printer = ResultPrinter()

adder.delegate = printer   // ✅ delegate now holds an instance
adder.add(10, 20)          // ✅ triggers callback

// Output: Sum is: 30
```

---

## » If/Else Example (Multiple Delegate Paths)

Situation:
- If condition is true → call success delegate
- Else → call failure delegate

### 1) Protocol Rules

```swift
protocol LoginSuccessDelegate {
    func loginSuccess(username: String)
}

protocol LoginFailureDelegate {
    func loginFailed(reason: String)
}
```

### 2) Manager / Worker

```swift
final class LoginManager {

    var successDelegate: LoginSuccessDelegate?
    var failureDelegate: LoginFailureDelegate?

    func login(username: String, password: String) {

        if username == "RaghuRam" && password == "1234" {
            successDelegate?.loginSuccess(username: username)
        } else {
            failureDelegate?.loginFailed(reason: "Invalid username or password")
        }
    }
}
```

### 3) Receiver Implements Both

```swift
final class LoginUIHandler: LoginSuccessDelegate, LoginFailureDelegate {

    func loginSuccess(username: String) {
        print("✅ Welcome \(username)")
    }

    func loginFailed(reason: String) {
        print("❌ Login Failed:", reason)
    }
}
```

### 4) Connect + Run

```swift
let manager = LoginManager()
let handler = LoginUIHandler()

manager.successDelegate = handler
manager.failureDelegate = handler

manager.login(username: "RaghuRam", password: "1234")
manager.login(username: "RaghuRam", password: "0000")
```

---

## » Slow Process (When to use async/await)

If a process takes longer time (like network calls), then you should **await the slow work**.

### Correct pattern:
- `await` the slow function
- Then notify the delegate

```swift
protocol WeatherDelegate {
    func didUpdateWeather(_ weather: String)
}

final class WeatherManager {

    var delegate: WeatherDelegate?

    func fetchWeather() async {
        // ✅ long process
        let weather = await slowFetch()

        // ✅ notify delegate after result is ready
        delegate?.didUpdateWeather(weather)
    }

    private func slowFetch() async -> String {
        try? await Task.sleep(nanoseconds: 2_000_000_000) // 2 sec delay
        return "Sunny 32°C"
    }
}
```

Final memory hook:

**Use `await` for long work, not for the delegate call.**

---

## » Quick Reference Table

| Item | Meaning |
|------|---------|
| Protocol | Rules / required methods |
| Delegate | Protocol-typed reference holding an instance |
| Conformance | Class implements all required methods |
| Callback | Worker informs delegate when work is done |
| Optional Delegate | Safe calling with `delegate?` |
| async/await | Used when work takes long time (network/IO) |

---

## » Tags

<p align="left">
  <img src="https://img.shields.io/badge/%23Swift-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23SwiftUI-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23Protocols-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23Delegates-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23Architecture-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>

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
