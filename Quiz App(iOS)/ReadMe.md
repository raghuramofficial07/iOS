# 🧠 True or False Quiz App (iOS)

A beginner-friendly iOS quiz app built using Swift and UIKit that tests users with fun True/False trivia questions. It implements the **Model-View-Controller (MVC)** design pattern to clearly separate app logic, UI, and data structures. This is a great starter project for anyone learning iOS development.

---

## 📱 App Overview

<img width="1470" alt="Quiz App Demo" src="https://github.com/user-attachments/assets/056c1aac-8907-437e-9de8-cf4e4f9ad013" />



Uploading Screen Recording 2025-05-16 at 10.51.07 AM.mov…



- ✅ Multiple true/false trivia questions
- ✅ Instant visual feedback (green/red)
- ✅ Progress bar indicating quiz completion
- 🚧 Score tracking (coming soon)

---

## 🏗 Architecture: MVC Pattern

<img width="270" alt="Screenshot 2025-05-16 at 10 50 05 AM" src="https://github.com/user-attachments/assets/3d7bacad-a284-4724-9c0e-bbd71547cc00" />


This app follows the **Model-View-Controller (MVC)** design pattern.

### 🧠 Model
- `Question.swift`: Represents a single quiz question.
- `QuizBrain.swift`: Handles all quiz logic, state, and progress tracking.

### 🎨 View
- `Main.storyboard`: Contains UI layout with labels, buttons, and progress bar.

### 🕹 Controller
- `ViewController.swift`: Manages user interaction, calls model methods, and updates UI based on quiz progress.

---

## 📂 Project Structure

TrueFalseQuizApp/
│
├── Model/
│ ├── Question.swift # Quiz question model
│ └── QuizBrain.swift # Business logic and quiz state manager
│
├── Controller/
│ └── ViewController.swift # Main controller handling UI logic
│
├── View/
│ └── Main.storyboard # Interface layout using UIKit
│
└── Assets.xcassets # Image and color assets (if any)


---

## 🧑‍💻 Tech Stack (2025)

| Tech               | Description                                |
|--------------------|--------------------------------------------|
| **Language**       | Swift 5.10 (compatible with Swift 5+)      |
| **IDE**            | Xcode 15+                                  |
| **Framework**      | UIKit                                       |
| **Architecture**   | MVC (Model-View-Controller)                |
| **Minimum iOS**    | iOS 13.0+ (recommended iOS 15.0+)          |
| **UI Components**  | UILabel, UIButton, UIProgressView, Timer   |

---

## 💡 How It Works

1. The app displays one question at a time.
2. The user selects **True** or **False**.
3. It checks the answer using `QuizBrain.checkAnswer()`.
4. The button turns **green** if correct, **red** if incorrect.
5. After a 0.2s delay, the next question is shown via `updateUI()`.
6. Progress is visualized with a progress bar.

---

## 🧩 Key Features

- ✅ Organized using MVC architecture
- ✅ Clean and reusable `Question` and `QuizBrain` structs
- ✅ Dynamic question loading
- ✅ UI updates via scheduled `Timer`
- ✅ Background color indicators for instant feedback

---

## 🚀 Getting Started

### Prerequisites

- Xcode 15 or later
- macOS Ventura or later
- Swift 5.10+
- iOS Simulator or real iPhone (iOS 13+)

### Setup Instructions

---

## 🧑‍💻 Tech Stack (2025)

| Tech               | Description                                |
|--------------------|--------------------------------------------|
| **Language**       | Swift 5.10 (compatible with Swift 5+)      |
| **IDE**            | Xcode 15+                                  |
| **Framework**      | UIKit                                       |
| **Architecture**   | MVC (Model-View-Controller)                |
| **Minimum iOS**    | iOS 13.0+ (recommended iOS 15.0+)          |
| **UI Components**  | UILabel, UIButton, UIProgressView, Timer   |

---

## 💡 How It Works

1. The app displays one question at a time.
2. The user selects **True** or **False**.
3. It checks the answer using `QuizBrain.checkAnswer()`.
4. The button turns **green** if correct, **red** if incorrect.
5. After a 0.2s delay, the next question is shown via `updateUI()`.
6. Progress is visualized with a progress bar.

---

## 🧩 Key Features

- ✅ Organized using MVC architecture
- ✅ Clean and reusable `Question` and `QuizBrain` structs
- ✅ Dynamic question loading
- ✅ UI updates via scheduled `Timer`
- ✅ Background color indicators for instant feedback

---

## 🚀 Getting Started

### Prerequisites

- Xcode 15 or later
- macOS Ventura or later
- Swift 5.10+
- iOS Simulator or real iPhone (iOS 13+)
