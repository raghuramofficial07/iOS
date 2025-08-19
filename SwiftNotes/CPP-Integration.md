<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/C++%20%2B%20Swift%20Interop-black?logo=apple&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/Built%20by-RaghuRam-black?labelColor=black&color=black">
</p>

# » C++ and Swift Integration – CGPA Calculator

---

## » Table of Contents
- Introduction  
- Code Example  
- Questions & Answers (From Chat)  
- Concept Highlights  
- Real-World Use Case  
- Quick Reference Table  
- Author Footer  

---

## » Introduction
In this document, we explore how to **integrate C++ code into a Swift iOS application**.  
The example used here is a **CGPA calculator**. While calculating CGPA can be easily done in Swift, the purpose here is to **learn cross-language interoperability**.

---

## » Code Example

### C++ Function Declaration
```cpp
#ifdef __cplusplus
extern "C" {
#endif

double calculateCGPA(const double* grades, int n);

#ifdef __cplusplus
}
#endif
```

---

## » Questions & Answers (From Chat)

### Quote from our chat:
> **“What We are Implementing?”**

**Answer:**  
We are implementing a **function declaration** for `calculateCGPA`.  
This tells the compiler:  
- There exists a function named `calculateCGPA`.  
- It takes two inputs:  
  - `const double* grades` → a pointer to an array of grades.  
  - `int n` → the number of grades.  
- It returns a **double** (the CGPA result).

---

### Quote from our chat:
> **“Why we are Implementing?”**

**Answer:**  
We are implementing it this way because **Swift cannot directly call C++ functions**.  
- C++ compilers use **name mangling** (they internally change function names).  
- Swift only understands **C-style functions** (no name mangling).  
- By writing `extern "C"`, we are telling the compiler:  
  → *“Treat this function as if it were written in C, so Swift can find it.”*  

The `#ifdef __cplusplus ... #endif` part is just a **safety check**:  
- If the file is compiled with a **C++ compiler**, `extern "C"` is applied.  
- If compiled with a **C compiler**, it ignores it safely.  

---

## » Concept Highlights
- **C++ and Swift cannot directly communicate** → Bridging is needed.  
- **extern "C"** removes C++ name mangling → makes functions callable from Swift.  
- **CGPA calculation** is a simple example → the real goal is **learning interoperability**.  

---

## » Real-World Use Case
Imagine you have an existing **C++ math engine** or a **scientific computation library**.  
Instead of rewriting everything in Swift, you can **expose those C++ functions to Swift** with this approach and reuse them in iOS apps.  

---

## » Quick Reference Table

| Keyword / Code | Meaning |
|----------------|---------|
| `extern "C"` | Forces the compiler to use C naming (no C++ mangling). |
| `#ifdef __cplusplus` | Check if code is being compiled as C++. |
| `const double*` | Pointer to an array of doubles (grades). |
| `int n` | Number of grades in the array. |

---

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam  
**License:** MIT  
**Platform:** Swift (iOS UIKit) + C++  

<p align="left">
  <img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
  <img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>
