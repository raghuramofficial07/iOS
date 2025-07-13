<!-- GitHub Project Banner -->
<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>
LOOPS IN SWIFT — ONE-HOOD TERMINAL GUIDE

🧠 WHAT ARE LOOPS?
Loops let you repeat blocks of code until a condition is false.
Types of loops in Swift:
1. for-in
2. while
3. repeat-while

🔁 FOR-IN LOOP
Used when you know how many times you want to loop.
```swift
Example 1: Looping through a range
for number in 1...5 {
    print("Number is \(number)")
}
```
```
Output:
Number is 1
Number is 2
Number is 3
Number is 4
Number is 5
```
--
Example 2: Looping through an array
let fruits = ["Apple", "Banana", "Cherry"]

for fruit in fruits {
    print("I like \(fruit)")
}
```
Output:
I like Apple
I like Banana
I like Cherry
```
```
Example 3: Looping through a dictionary
let capitals = ["India": "Delhi", "France": "Paris"]

for (country, capital) in capitals {
    print("The capital of \(country) is \(capital)")
}
```
#🔄 WHILE LOOP
Used when the number of iterations is unknown in advance.
```swift
var count = 3

while count > 0 {
    print("Countdown: \(count)")
    count -= 1
}
```
```
Output:
Countdown: 3
Countdown: 2
Countdown: 1
```
```
🔁 REPEAT-WHILE LOOP
Same as while, but runs at least once.

```swfit

var counter = 1

repeat {
    print("Counter is \(counter)")
    counter += 1
} while counter <= 3
```
Output:
Counter is 1
Counter is 2
Counter is 3
```
```
#🛑 LOOP CONTROL STATEMENTS

1. break → Exits the loop early.
2. continue → Skips the current iteration.

Example using break:
```swift
for i in 1...5 {
    if i == 3 {
        break
    }
    print(i)
}
```
Output:
1
2
```
```swift
Example using continue:
for i in 1...5 {
    if i == 3 {
        continue
    }
    print(i)
}
```
Output:
1
2
4
5
```
```
#🔢 LOOPING WITH RANGES
```swift
for i in 1...5 {
    print(i)   // Closed range: includes 5
}
```
```swift
for i in 1..<5 {
    print(i)   // Half-open range: excludes 5
}
```
--
📘 REAL-WORLD USE CASES

| Use Case                  | Loop Type      |
|---------------------------|----------------|
| Animate frames            | for-in         |
| Retry login until success | while          |
| Prompt once then check    | repeat-while   |
| Skip invalid inputs       | for + continue |
| Exit loop on failure      | for + break    |

📎 QUICK REFERENCE TABLE

| Loop Type     | Runs At Least Once | Ideal Use                     |
|---------------|--------------------|-------------------------------|
| for-in        | ❌                 | Known range/collection        |
| while         | ❌                 | Unknown stop condition        |
| repeat-while  | ✅                 | Ensure at least one execution |
| break         | —                  | Exit loop immediately         |
| continue      | —                  | Skip current iteration        |

```
👨‍💻 AUTHOR

Vemparala Sri Satya RaghuRam  
#CodeWithRaghuRam | #BeyondCertifications | #IndustryOriented  
Swift Loops — Crystal Clear & Fully Understood 
```
