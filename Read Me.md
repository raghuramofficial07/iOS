# iOS
Built For iOS
<img width="1470" alt="Screenshot 2025-05-13 at 10 33 30 AM" src="https://github.com/user-attachments/assets/a3a54919-74f5-476e-9733-ab25d3d1a37e" />

📱 Project Overview

This is a beginner-friendly iOS application that I developed using Swift and UIKit, designed to play custom sound effects at the press of a button. The app demonstrates fundamental iOS development concepts such as:

Handling UI interactions using @IBAction
Dynamically playing audio files using AVFoundation
Implementing basic animations for a better user experience
This project helped me understand how to interact with system resources like sound files, handle button events in real time, and use built-in iOS frameworks to build a responsive and engaging mobile experience.

🔧 How It Works

Each button in the app has a title (e.g., "C", "D", etc.) that corresponds to the name of a sound file stored in the app’s bundle. When a user taps a button:

The app retrieves the button title using sender.currentTitle.
It uses that title to locate the corresponding .wav file in the app bundle.
AVAudioPlayer is used to play the sound in real time.
A short visual feedback is given by changing the button’s alpha to make it look pressed, then restoring it after 0.2 seconds.
This kind of interaction mimics the feel of pressing a musical key or soundboard button, offering an intuitive user experience.


https://github.com/user-attachments/assets/f2cf1401-d4e9-46fc-87ba-6b8e5176c1d0


📹 Demo

Refer to the screen recording video to see the app in action.
You can upload this clip to YouTube or embed it in your Blogger post.

🔗 Project Goals

Learn Swift and UIKit basics
Understand AVFoundation usage
Create an interactive user interface
Improve code structure and readability



