
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

```bash
git clone https://github.com/your-username/TrueFalseQuizApp.git
cd TrueFalseQuizApp
open TrueFalseQuizApp.xcodeproj
