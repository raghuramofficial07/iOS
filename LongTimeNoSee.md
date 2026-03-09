<p align="center">
<img src="https://img.shields.io/badge/SwiftUI-Developer%20Quick%20Recap-black?style=for-the-badge&logo=swift&logoColor=white">
<img src="https://img.shields.io/badge/Platform-iOS-black?style=for-the-badge&logo=apple&logoColor=white">
<img src="https://img.shields.io/badge/Language-Swift-black?style=for-the-badge&logo=swift&logoColor=white">
<img src="https://img.shields.io/badge/Framework-SwiftUI-black?style=for-the-badge&logo=swift&logoColor=white">
</p>

```
# SwiftUI Complete Quick Reference

## » Introduction

SwiftUI is Apple's declarative UI framework used to build applications
for iOS, macOS, watchOS and visionOS.

Core Idea

    UI = Function(State)

When state changes, the UI updates automatically.

------------------------------------------------------------------------

## » Basic SwiftUI Syntax

``` swift
import SwiftUI

struct ContentView: View {

    var body: some View {
        Text("Hello World")
    }

}
```

  Keyword     Meaning
  ----------- --------------------
  struct      UI component
  View        SwiftUI protocol
  body        UI layout
  some View   opaque return type

------------------------------------------------------------------------

## » App Entry Point

``` swift
@main
struct MyApp: App {

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }

}
```

------------------------------------------------------------------------

## » Core UI Elements

### Text

``` swift
Text("Hello SwiftUI")
```

Customization

``` swift
Text("Hello")
    .font(.title)
    .fontWeight(.bold)
    .foregroundColor(.blue)
    .padding()
```

------------------------------------------------------------------------

### Button

``` swift
Button("Tap Me") {
    print("Button pressed")
}
```

Custom Button

``` swift
Button(action: {
    print("Clicked")
}) {
    Text("Login")
        .padding()
        .background(Color.blue)
        .foregroundColor(.white)
        .cornerRadius(10)
}
```

------------------------------------------------------------------------

### TextField

``` swift
@State private var username = ""

TextField("Enter username", text: $username)
```

Styled

``` swift
TextField("Email", text: $email)
    .textFieldStyle(.roundedBorder)
```

------------------------------------------------------------------------

### SecureField

``` swift
SecureField("Password", text: $password)
```

------------------------------------------------------------------------

### Toggle

``` swift
Toggle("Dark Mode", isOn: $isDarkMode)
```

------------------------------------------------------------------------

## » Layout System

### VStack

``` swift
VStack {
    Text("One")
    Text("Two")
}
```

### HStack

``` swift
HStack {
    Image(systemName: "star")
    Text("Favorite")
}
```

### ZStack

``` swift
ZStack {
    Color.blue
    Text("Overlay")
}
```

### Spacer

``` swift
HStack {
    Text("Left")
    Spacer()
    Text("Right")
}
```

------------------------------------------------------------------------

## » State Management

### @State

``` swift
@State private var count = 0
```

### @Binding

``` swift
@Binding var name: String
```

### ObservableObject

``` swift
class UserModel: ObservableObject {
    @Published var username = ""
}
```

------------------------------------------------------------------------

## » Navigation

``` swift
NavigationStack {
    Text("Home")
}
```

NavigationLink

``` swift
NavigationLink("Go to Detail", destination: DetailView())
```

------------------------------------------------------------------------

## » Lists

``` swift
List(items, id: \.self) { item in
    Text(item)
}
```

------------------------------------------------------------------------

## » Animations

``` swift
withAnimation {
    isExpanded.toggle()
}
```

------------------------------------------------------------------------

## » Networking

``` swift
func fetchData() async {

    let url = URL(string: "https://api.example.com")!

    let (data, _) = try await URLSession.shared.data(from: url)

    let result = try JSONDecoder().decode(Model.self, from: data)

}
```

------------------------------------------------------------------------

## » Author Footer

**Created By:** Vemparala Sri Satya RaghuRam\
**License:** MIT\
**Platform:** Swift (iOS UIKit)

<p align="left">
<img src="https://img.shields.io/badge/%23BeyondCertifications-black?logo=tag&logoColor=white&labelColor=black&color=black">
<img src="https://img.shields.io/badge/%23IndustryOriented-black?logo=tag&logoColor=white&labelColor=black&color=black">
<img src="https://img.shields.io/badge/%23CodeWithRaghuRam-black?logo=tag&logoColor=white&labelColor=black&color=black">
</p>

