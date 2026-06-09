<img width="841" alt="Screenshot 2025-06-22 at 10 51 31 AM" src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" />


# 🍎 SwiftUI Components — Complete Reference Guide

> A well-structured reference for every SwiftUI component and modifier visible in the WWDC component explorer.  
> Covers **what it is**, **when to use it**, and **real-world examples**.

---

## 📋 Table of Contents

### 🧩 Core UI Controls
- [Button](#button)
- [Toggle](#toggle)
- [Picker](#picker)

### 🗂️ Navigation & Layout Containers
- [NavigationStack](#navigationstack)
- [TabView](#tabview)
- [List](#list)
- [Layout](#layout)

### 🪟 Presentation Modifiers
- [sheet](#sheet)
- [confirmationDialog](#confirmationdialog)
- [popover](#popover)

### 🎛️ Interaction Modifiers
- [searchable](#searchable)
- [draggable](#draggable)
- [focusable](#focusable)

### 🎨 Styling Protocols
- [ButtonStyle](#buttonstyle)
- [ToggleStyle](#togglestyle)
- [CustomHoverEffect](#customhovereffect)

### 🖼️ Drawing & Graphics
- [Canvas](#canvas)
- [Shader](#shader)
- [Material](#material)
- [visualEffect](#visualeffect)

### ⚙️ Advanced APIs
- [SensoryFeedback](#sensoryfeedback)
- [Group(subviewsOf:)](#groupsubviewsof)
- [Animation](#animation)
- [TextRenderer](#textrenderer)

---

## 🧩 Core UI Controls

---

### `Button`

**What it is:**  
The most fundamental interactive element in SwiftUI. Triggers an action when tapped/clicked.

**Syntax:**
```swift
Button("Label") {
    // action
}

Button(action: { /* action */ }) {
    Label("Download", systemImage: "arrow.down.circle")
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Form submission | "Submit", "Save", "Login" buttons |
| Destructive action | "Delete Account" with `.destructive` role |
| Toolbar actions | Share, Edit, Add buttons in navigation bar |
| Custom styled CTA | Gradient background call-to-action button |

**Example — Login Screen Button:**
```swift
Button {
    viewModel.login()
} label: {
    Text("Sign In")
        .frame(maxWidth: .infinity)
        .padding()
        .background(Color.blue)
        .foregroundColor(.white)
        .clipShape(RoundedRectangle(cornerRadius: 12))
}
```

**Example — Destructive Button:**
```swift
Button("Delete Account", role: .destructive) {
    showDeleteConfirmation = true
}
```

---

### `Toggle`

**What it is:**  
A control that switches between ON and OFF states. Backed by a `@State` or `@Binding` Bool.

**Syntax:**
```swift
Toggle("Enable Notifications", isOn: $notificationsEnabled)
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Settings screen | Dark mode, push notifications, location access |
| Feature flags | Enable/disable app features |
| Filter options | Show only favorites, hide completed tasks |

**Example — Settings Screen:**
```swift
struct SettingsView: View {
    @AppStorage("darkMode") private var darkMode = false
    @AppStorage("notifications") private var notifications = true

    var body: some View {
        Form {
            Section("Appearance") {
                Toggle("Dark Mode", isOn: $darkMode)
            }
            Section("Alerts") {
                Toggle("Push Notifications", isOn: $notifications)
            }
        }
    }
}
```

---

### `Picker`

**What it is:**  
A control that lets users select a value from a set of mutually exclusive options. Supports multiple display styles.

**Syntax:**
```swift
Picker("Sort By", selection: $sortOption) {
    Text("Name").tag(SortOption.name)
    Text("Date").tag(SortOption.date)
    Text("Size").tag(SortOption.size)
}
.pickerStyle(.segmented) // or .menu, .wheel, .inline
```

**Real-World Use Cases:**

| Use Case | Picker Style |
|----------|--------------|
| Sort/filter options | `.segmented` or `.menu` |
| Date/time selection | `.wheel` |
| Settings dropdowns | `.menu` |
| Onboarding choices | `.inline` |

**Example — Product Category Filter:**
```swift
enum Category: String, CaseIterable {
    case all = "All"
    case electronics = "Electronics"
    case clothing = "Clothing"
    case food = "Food"
}

@State private var selected = Category.all

Picker("Category", selection: $selected) {
    ForEach(Category.allCases, id: \.self) { cat in
        Text(cat.rawValue).tag(cat)
    }
}
.pickerStyle(.segmented)
```

---

## 🗂️ Navigation & Layout Containers

---

### `NavigationStack`

**What it is:**  
The modern replacement for `NavigationView` (iOS 16+). Manages a stack of views with push/pop navigation. Supports programmatic navigation via a path binding.

**Syntax:**
```swift
NavigationStack(path: $navigationPath) {
    ContentView()
        .navigationDestination(for: Product.self) { product in
            ProductDetailView(product: product)
        }
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Master-detail navigation | Product list → Product detail |
| Deep link handling | Open specific screen from notification |
| Multi-step flows | Onboarding, checkout, wizard |

**Example — Product List to Detail:**
```swift
struct ProductListView: View {
    @State private var path = NavigationPath()
    let products: [Product]

    var body: some View {
        NavigationStack(path: $path) {
            List(products) { product in
                NavigationLink(value: product) {
                    Text(product.name)
                }
            }
            .navigationTitle("Products")
            .navigationDestination(for: Product.self) { product in
                ProductDetailView(product: product)
            }
        }
    }
}
```

---

### `TabView`

**What it is:**  
Displays multiple child views in a tabbed interface. Each tab is represented by a `tabItem`. Also works as a horizontal page scroll with `.tabViewStyle(.page)`.

**Syntax:**
```swift
TabView {
    HomeView()
        .tabItem { Label("Home", systemImage: "house") }

    SearchView()
        .tabItem { Label("Search", systemImage: "magnifyingglass") }
}
```

**Real-World Use Cases:**

| Use Case | Style |
|----------|-------|
| Main app navigation | Default tab bar at bottom |
| Onboarding carousel | `.page` style |
| Image gallery swiper | `.page` style with `indexDisplayMode` |

**Example — E-commerce App Tab Bar:**
```swift
struct MainTabView: View {
    var body: some View {
        TabView {
            HomeView()
                .tabItem { Label("Home", systemImage: "house.fill") }

            SearchView()
                .tabItem { Label("Search", systemImage: "magnifyingglass") }

            CartView()
                .tabItem { Label("Cart", systemImage: "cart.fill") }

            ProfileView()
                .tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.blue)
    }
}
```

---

### `List`

**What it is:**  
A container that shows rows of data in a single scrollable column. Supports swipe actions, reordering, deletion, and sections.

**Syntax:**
```swift
List(items) { item in
    Text(item.name)
}
```

**Real-World Use Cases:**

| Use Case | Feature Used |
|----------|--------------|
| Contact list | `List` + `Section` |
| Todo app | `.onDelete`, `.onMove` |
| Settings page | Grouped `List` with `Form` |
| Chat messages | `List` with `.listRowSeparator(.hidden)` |

**Example — Todo List with Swipe to Delete:**
```swift
struct TodoListView: View {
    @State private var todos = ["Buy groceries", "Call dentist", "Review PR"]

    var body: some View {
        List {
            ForEach(todos, id: \.self) { todo in
                Text(todo)
            }
            .onDelete { indexSet in
                todos.remove(atOffsets: indexSet)
            }
            .onMove { from, to in
                todos.move(fromOffsets: from, toOffset: to)
            }
        }
        .toolbar { EditButton() }
    }
}
```

---

### `Layout`

**What it is:**  
A protocol that lets you create fully custom layout containers — beyond HStack, VStack, and ZStack. You define `sizeThatFits` and `placeSubviews`.

**Syntax:**
```swift
struct EqualWidthHStack: Layout {
    func sizeThatFits(proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) -> CGSize { ... }
    func placeSubviews(in bounds: CGRect, proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) { ... }
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Tag/chip cloud | Wrapping flow layout |
| Equal-width buttons | Toolbar with proportional buttons |
| Radial menu | Circular icon arrangement |
| Masonry grid | Pinterest-style layout |

**Example — Flow Layout (Tag Cloud):**
```swift
struct FlowLayout: Layout {
    var spacing: CGFloat = 8

    func sizeThatFits(proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) -> CGSize {
        let containerWidth = proposal.width ?? .infinity
        var height: CGFloat = 0
        var rowWidth: CGFloat = 0
        var rowHeight: CGFloat = 0

        for subview in subviews {
            let size = subview.sizeThatFits(.unspecified)
            if rowWidth + size.width > containerWidth {
                height += rowHeight + spacing
                rowWidth = 0
                rowHeight = 0
            }
            rowWidth += size.width + spacing
            rowHeight = max(rowHeight, size.height)
        }
        return CGSize(width: containerWidth, height: height + rowHeight)
    }

    func placeSubviews(in bounds: CGRect, proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) {
        var x = bounds.minX
        var y = bounds.minY
        var rowHeight: CGFloat = 0

        for subview in subviews {
            let size = subview.sizeThatFits(.unspecified)
            if x + size.width > bounds.maxX {
                x = bounds.minX
                y += rowHeight + spacing
                rowHeight = 0
            }
            subview.place(at: CGPoint(x: x, y: y), proposal: .unspecified)
            x += size.width + spacing
            rowHeight = max(rowHeight, size.height)
        }
    }
}

// Usage
FlowLayout(spacing: 10) {
    ForEach(tags, id: \.self) { tag in
        TagChip(title: tag)
    }
}
```
## 🪟 Presentation Modifiers

---

### `sheet`

**What it is:**  
Presents a modal view that slides up from the bottom. Supports detents (`.medium`, `.large`, custom) for partial-height sheets (iOS 16+).

**Syntax:**
```swift
.sheet(isPresented: $showSheet) {
    SheetContentView()
}

// With detents
.sheet(isPresented: $showSheet) {
    FilterView()
        .presentationDetents([.medium, .large])
        .presentationDragIndicator(.visible)
}
```

**Real-World Use Cases:**

| Use Case | Detent |
|----------|--------|
| Filter/sort panel | `.medium` |
| Share sheet | `.large` |
| Quick action menu | `.fraction(0.3)` |
| Full detail view | `.large` |

**Example — Product Filter Sheet:**
```swift
struct ProductListView: View {
    @State private var showFilters = false

    var body: some View {
        List { /* products */ }
        .toolbar {
            Button("Filter") { showFilters = true }
        }
        .sheet(isPresented: $showFilters) {
            FilterView()
                .presentationDetents([.medium, .large])
                .presentationDragIndicator(.visible)
                .presentationCornerRadius(20)
        }
    }
}
```

---

### `confirmationDialog`

**What it is:**  
Presents an action sheet-style dialog with multiple labeled options. The preferred replacement for `ActionSheet`. On iPad it shows as a popover.

**Syntax:**
```swift
.confirmationDialog("Are you sure?", isPresented: $showDialog, titleVisibility: .visible) {
    Button("Delete", role: .destructive) { delete() }
    Button("Cancel", role: .cancel) {}
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Destructive confirmation | Delete post, remove friend |
| Multiple choice action | "Edit", "Share", "Report" |
| Image source picker | Camera vs Photo Library |

**Example — Delete Post Confirmation:**
```swift
struct PostView: View {
    @State private var showDeleteDialog = false

    var body: some View {
        VStack { /* post content */ }
        .contextMenu {
            Button("Delete Post", role: .destructive) {
                showDeleteDialog = true
            }
        }
        .confirmationDialog(
            "Delete this post?",
            isPresented: $showDeleteDialog,
            titleVisibility: .visible
        ) {
            Button("Delete", role: .destructive) { deletePost() }
            Button("Cancel", role: .cancel) {}
        } message: {
            Text("This action cannot be undone.")
        }
    }
}
```

---

### `popover`

**What it is:**  
Presents a small floating overlay anchored to a view. On iPhone it behaves like a sheet; on iPad and Mac it renders as a true popover bubble.

**Syntax:**
```swift
.popover(isPresented: $showPopover) {
    PopoverContent()
        .padding()
        .frame(width: 250)
}
```

**Real-World Use Cases:**

| Use Case | Platform |
|----------|----------|
| Tooltip / info overlay | iPad/Mac |
| Date picker | iPad toolbar |
| Color picker | Mac/iPad palette |
| Quick settings | iPad sidebar |

**Example — Info Tooltip:**
```swift
struct InfoButton: View {
    @State private var showInfo = false

    var body: some View {
        Button {
            showInfo = true
        } label: {
            Image(systemName: "info.circle")
        }
        .popover(isPresented: $showInfo) {
            VStack(alignment: .leading, spacing: 8) {
                Text("About This Feature")
                    .font(.headline)
                Text("This calculates your daily average based on the last 30 days of data.")
                    .font(.subheadline)
                    .foregroundStyle(.secondary)
            }
            .padding()
            .frame(width: 280)
        }
    }
}
```

---

## 🎛️ Interaction Modifiers

---

### `searchable`

**What it is:**  
Adds a search bar to a `NavigationStack` or `NavigationSplitView`. Handles placement, focus, and tokens automatically.

**Syntax:**
```swift
.searchable(text: $searchText, prompt: "Search products...")
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Product search | Filter items by name |
| Contact finder | Search by name or number |
| File browser | Search documents |
| Settings search | iOS Settings-style search |

**Example — Searchable Product List:**
```swift
struct SearchableProductList: View {
    @State private var searchText = ""
    let products: [Product]

    var filtered: [Product] {
        searchText.isEmpty ? products :
        products.filter { $0.name.localizedCaseInsensitiveContains(searchText) }
    }

    var body: some View {
        NavigationStack {
            List(filtered) { product in
                ProductRow(product: product)
            }
            .navigationTitle("Products")
            .searchable(text: $searchText, prompt: "Search by name...")
        }
    }
}
```

---

### `draggable`

**What it is:**  
Makes a view draggable as part of SwiftUI's drag-and-drop system. Works with `dropDestination` on the receiving side. Supports `Transferable` types.

**Syntax:**
```swift
Text(item.name)
    .draggable(item)
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Kanban board | Drag tasks between columns |
| Photo organizer | Reorder images |
| Playlist builder | Drag songs into playlist |
| File manager | Move files between folders |

**Example — Kanban Card Drag:**
```swift
struct KanbanCard: View {
    let task: Task

    var body: some View {
        RoundedRectangle(cornerRadius: 10)
            .fill(.white)
            .overlay(Text(task.title).padding())
            .shadow(radius: 3)
            .draggable(task)
    }
}

struct KanbanColumn: View {
    @Binding var tasks: [Task]
    let status: TaskStatus

    var body: some View {
        VStack {
            ForEach(tasks) { task in
                KanbanCard(task: task)
            }
        }
        .dropDestination(for: Task.self) { droppedTasks, _ in
            tasks.append(contentsOf: droppedTasks)
            return true
        }
    }
}
```

---

### `focusable`

**What it is:**  
Marks a view as capable of receiving keyboard focus. Essential for tvOS navigation, macOS keyboard accessibility, and custom focus management.

**Syntax:**
```swift
TextField("Username", text: $username)
    .focused($focusedField, equals: .username)

// Custom view focus
MyCustomView()
    .focusable()
    .onKeyPress(.return) { handleReturn() }
```

**Real-World Use Cases:**

| Use Case | Platform |
|----------|----------|
| Multi-field form navigation | iOS/macOS |
| TV remote D-pad navigation | tvOS |
| Game controller support | tvOS/iPadOS |
| Keyboard shortcut handling | macOS |

**Example — Login Form Focus Flow:**
```swift
enum Field { case email, password }

struct LoginForm: View {
    @State private var email = ""
    @State private var password = ""
    @FocusState private var focused: Field?

    var body: some View {
        VStack {
            TextField("Email", text: $email)
                .focused($focused, equals: .email)
                .onSubmit { focused = .password }

            SecureField("Password", text: $password)
                .focused($focused, equals: .password)
                .onSubmit { login() }
        }
        .onAppear { focused = .email }
    }
}
```

---

## 🎨 Styling Protocols

---

### `ButtonStyle`

**What it is:**  
A protocol that lets you create fully reusable, custom button appearances. Receives the button's `label` and `isPressed` state.

**Syntax:**
```swift
struct PrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .background(configuration.isPressed ? Color.blue.opacity(0.7) : Color.blue)
            .foregroundColor(.white)
            .clipShape(Capsule())
            .scaleEffect(configuration.isPressed ? 0.96 : 1.0)
            .animation(.easeOut(duration: 0.1), value: configuration.isPressed)
    }
}

// Usage
Button("Get Started") { }
    .buttonStyle(PrimaryButtonStyle())
```

**Real-World Use Cases:**

| Style | Use Case |
|-------|----------|
| `PrimaryButtonStyle` | Main CTAs across the app |
| `IconButtonStyle` | Toolbar icon buttons |
| `GhostButtonStyle` | Secondary/outline buttons |
| `LoadingButtonStyle` | Async actions with spinner |

---

### `ToggleStyle`

**What it is:**  
Lets you replace the default toggle switch with any custom view while keeping the binding behavior.

**Syntax:**
```swift
struct CheckboxToggleStyle: ToggleStyle {
    func makeBody(configuration: Configuration) -> some View {
        HStack {
            Image(systemName: configuration.isOn ? "checkmark.square.fill" : "square")
                .foregroundColor(configuration.isOn ? .blue : .gray)
                .onTapGesture { configuration.isOn.toggle() }
            configuration.label
        }
    }
}

Toggle("Accept Terms", isOn: $accepted)
    .toggleStyle(CheckboxToggleStyle())
```

**Real-World Use Cases:**

| Style | Use Case |
|-------|----------|
| Checkbox | Terms acceptance, multi-select lists |
| Star/Heart | Favorite toggling |
| Custom pill | App-branded toggle |
| Icon swap | Feature on/off with icon change |

---

### `CustomHoverEffect`

**What it is:**  
Defines a custom visual effect when the user hovers over a view — applicable on iPad with pointer, Mac Catalyst, and macOS.

**Syntax:**
```swift
struct ScaleHoverEffect: CustomHoverEffect {
    func body(content: Content) -> some CustomHoverEffect {
        content.hoverEffect { effect, isActive, _ in
            effect.scaleEffect(isActive ? 1.05 : 1.0)
                  .opacity(isActive ? 1.0 : 0.85)
        }
    }
}

MyButton()
    .hoverEffect(ScaleHoverEffect())
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Card lift effect | Dashboard cards on Mac |
| Button highlight | Sidebar navigation items |
| Grid cell hover | Photo grid thumbnail highlight |
| List row emphasis | Mac app list hover state |

---

## 🖼️ Drawing & Graphics

---

### `Canvas`

**What it is:**  
A view that renders 2D graphics using immediate-mode drawing. Much more performant than layering many SwiftUI shapes. Uses `GraphicsContext` for drawing.

**Syntax:**
```swift
Canvas { context, size in
    context.fill(
        Path(ellipseIn: CGRect(x: 0, y: 0, width: size.width, height: size.height)),
        with: .color(.blue)
    )
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Custom charts | Bar/line/pie charts without Charts framework |
| Game rendering | Sprites, particles, tile maps |
| Drawing app | Brush strokes, shapes |
| Data visualization | Heatmaps, force graphs |

**Example — Bar Chart:**
```swift
struct BarChartView: View {
    let data: [Double] = [0.4, 0.7, 0.5, 0.9, 0.6, 0.8]

    var body: some View {
        Canvas { context, size in
            let barWidth = size.width / CGFloat(data.count) - 4
            for (index, value) in data.enumerated() {
                let x = CGFloat(index) * (barWidth + 4)
                let barHeight = size.height * value
                let rect = CGRect(x: x, y: size.height - barHeight, width: barWidth, height: barHeight)
                context.fill(Path(roundedRect: rect, cornerRadius: 4), with: .color(.blue))
            }
        }
        .frame(height: 200)
    }
}
```

---

### `Shader`

**What it is:**  
Applies a Metal shader written in Metal Shading Language directly to a SwiftUI view. Enables GPU-accelerated visual effects unique to your app.

**Syntax:**
```swift
// In a .metal file:
// [[ stitchable ]] half4 rainbowShader(float2 pos, float time) { ... }

view.colorEffect(ShaderLibrary.rainbowShader(.float(time)))
view.distortionEffect(ShaderLibrary.waveEffect(.float(time)), maxSampleOffset: .init(width: 10, height: 10))
view.layerEffect(ShaderLibrary.blurEffect(.float(radius)), maxSampleOffset: .init(width: radius, height: radius))
```

**Real-World Use Cases:**

| Shader Type | Use Case |
|-------------|----------|
| `colorEffect` | Duotone, rainbow, pixelate |
| `distortionEffect` | Water ripple, wave, glitch |
| `layerEffect` | Custom blur, emboss, mosaic |
| Animated shader | Interactive liquid/fire backgrounds |

---

### `Material`

**What it is:**  
Apple's built-in adaptive blur materials that adapt automatically to light/dark mode and underlying content. Inspired by UIVisualEffectView.

**Syntax:**
```swift
ZStack {
    Image("background")
    VStack { /* content */ }
        .background(.ultraThinMaterial)
}
```

**Available Materials:**

| Material | Opacity Level |
|----------|--------------|
| `.ultraThinMaterial` | Nearly transparent |
| `.thinMaterial` | Slightly opaque |
| `.regularMaterial` | Balanced |
| `.thickMaterial` | More opaque |
| `.ultraThickMaterial` | Most opaque |

**Real-World Use Cases:**

| Use Case | Material |
|----------|----------|
| Floating toolbar | `.regularMaterial` |
| Bottom sheet header | `.thinMaterial` |
| Context menu background | `.ultraThinMaterial` |
| Sidebar in split view | `.thickMaterial` |

**Example — Frosted Glass Card:**
```swift
ZStack {
    Image("city_background").resizable().scaledToFill()

    VStack(spacing: 8) {
        Text("San Francisco").font(.title2.bold())
        Text("72°F · Partly Cloudy").foregroundStyle(.secondary)
    }
    .padding()
    .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 16))
}
```

---

### `visualEffect`

**What it is:**  
Applies geometry-aware visual effects to a view without affecting its layout. The effect closure receives the view's proxy geometry, enabling scroll-driven and position-based effects.

**Syntax:**
```swift
view.visualEffect { content, proxy in
    content
        .offset(y: proxy.frame(in: .scrollView).minY * 0.3) // Parallax
        .opacity(1 - proxy.frame(in: .scrollView).minY / 200)
}
```

**Real-World Use Cases:**

| Use Case | Effect |
|----------|--------|
| Parallax header | Offset based on scroll position |
| Fade-on-scroll | Opacity tied to scroll progress |
| Scale on appear | Scale from 0 → 1 as view enters viewport |
| Sticky header blur | Blur increases as content scrolls under it |

**Example — Parallax Scroll Effect:**
```swift
ScrollView {
    LazyVStack {
        ForEach(items) { item in
            ItemCard(item: item)
                .visualEffect { content, proxy in
                    content.offset(
                        y: proxy.frame(in: .scrollView).minY > 0
                           ? -proxy.frame(in: .scrollView).minY * 0.2
                           : 0
                    )
                }
        }
    }
}
```

---
## ⚙️ Advanced APIs

---

### `SensoryFeedback`

**What it is:**  
Triggers haptic feedback and audio feedback on supported devices in response to value changes — without UIKit's `UIFeedbackGenerator`.

**Syntax:**
```swift
.sensoryFeedback(.impact(weight: .medium), trigger: buttonTapped)
.sensoryFeedback(.success, trigger: taskCompleted)
.sensoryFeedback(.error, trigger: validationFailed)
.sensoryFeedback(.selection, trigger: selectedItem)
```

**Available Feedback Types:**

| Type | When to Use |
|------|-------------|
| `.impact(weight:)` | Button taps, heavy interactions |
| `.success` | Task completed, form submitted |
| `.error` | Validation failed, network error |
| `.warning` | Caution action, low battery |
| `.selection` | Picker scroll, item selected |
| `.start` / `.stop` | Timer start/stop |

**Example — Fitness App:**
```swift
struct WorkoutTimer: View {
    @State private var isRunning = false
    @State private var completedRep = false

    var body: some View {
        VStack {
            Button(isRunning ? "Pause" : "Start") {
                isRunning.toggle()
            }
            Button("Log Rep") {
                completedRep.toggle()
            }
        }
        .sensoryFeedback(.start, trigger: isRunning) { _, new in new == true }
        .sensoryFeedback(.stop, trigger: isRunning) { _, new in new == false }
        .sensoryFeedback(.impact(weight: .heavy), trigger: completedRep)
    }
}
```

---

### `Group(subviewsOf:)`

**What it is:**  
Introduced in iOS 18 — allows a custom container view to introspect and iterate over the individual subviews passed into it. Replaces fragile `_VariadicView` hacks.

**Syntax:**
```swift
struct BadgeStack<Content: View>: View {
    @ViewBuilder var content: Content

    var body: some View {
        Group(subviewsOf: content) { subviews in
            HStack {
                ForEach(subviews) { subview in
                    subview
                        .padding(6)
                        .background(Capsule().fill(.blue.opacity(0.15)))
                }
            }
        }
    }
}

// Usage
BadgeStack {
    Text("Swift")
    Text("SwiftUI")
    Text("iOS")
}
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Tag/badge container | Wrap N tags with equal styling |
| Custom toolbar | Distribute N actions evenly |
| Segmented control | Build custom pill segmented control |
| Card grid | Auto-layout N cards |

---

### `Animation`

**What it is:**  
A protocol for defining reusable, composable, and fully custom animations in SwiftUI. Goes beyond `.spring()` and `.easeInOut()` — you can define timing curves from scratch.

**Syntax:**
```swift
// Built-in
.animation(.spring(response: 0.4, dampingFraction: 0.7), value: isExpanded)

// Custom Animation via protocol
struct BounceAnimation: CustomAnimation {
    func animate<V: VectorArithmetic>(value: V, time: Double, context: inout AnimationContext<V>) -> V? {
        // custom timing curve
    }
}
```

**Common Animation Types:**

| Animation | Personality |
|-----------|-------------|
| `.spring(response:dampingFraction:)` | Natural, physics-based |
| `.easeInOut(duration:)` | Smooth, symmetric |
| `.bouncy` | Playful, overshoots |
| `.smooth` | Calm, no overshoot |
| `.snappy` | Quick, responsive |

**Example — Card Expand Animation:**
```swift
struct ExpandableCard: View {
    @State private var isExpanded = false

    var body: some View {
        VStack {
            Text("Tap to expand")
            if isExpanded {
                Text("Hidden content revealed!")
                    .transition(.move(edge: .top).combined(with: .opacity))
            }
        }
        .frame(maxWidth: .infinity)
        .padding()
        .background(RoundedRectangle(cornerRadius: 16).fill(.blue.opacity(0.1)))
        .onTapGesture {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.75)) {
                isExpanded.toggle()
            }
        }
    }
}
```

---

### `TextRenderer`

**What it is:**  
A protocol introduced in iOS 18 that lets you intercept SwiftUI's text rendering pipeline and draw text with completely custom effects — per-glyph, per-line, or per-run.

**Syntax:**
```swift
struct GlowTextRenderer: TextRenderer {
    func draw(layout: Text.Layout, in context: inout GraphicsContext) {
        for line in layout {
            for run in line {
                var glow = context
                glow.addFilter(.blur(radius: 4))
                glow.draw(run)
                context.draw(run)
            }
        }
    }
}

Text("Hello, World!")
    .textRenderer(GlowTextRenderer())
```

**Real-World Use Cases:**

| Use Case | Example |
|----------|---------|
| Glow/neon text | Game titles, dark UI headers |
| Per-letter animation | Typewriter, wave reveal effect |
| Custom highlight | Highlighted search match rendering |
| Gradient text | Rainbow per-character gradient |

**Example — Wave Text Animation:**
```swift
struct WaveTextRenderer: TextRenderer {
    var animationProgress: Double

    var animatableData: Double {
        get { animationProgress }
        set { animationProgress = newValue }
    }

    func draw(layout: Text.Layout, in context: inout GraphicsContext) {
        for (index, line) in layout.enumerated() {
            for (runIndex, run) in line.enumerated() {
                let offset = sin(animationProgress * .pi * 2 + Double(runIndex) * 0.5) * 4
                var copy = context
                copy.translateBy(x: 0, y: offset)
                copy.draw(run)
            }
        }
    }
}
```

---

## 🗺️ Quick Reference Summary

| Component | Category | Key Use Case |
|-----------|----------|--------------|
| `Button` | Control | Any tap action |
| `Toggle` | Control | Boolean on/off setting |
| `Picker` | Control | Single selection from a list |
| `NavigationStack` | Navigation | Push/pop screen navigation |
| `TabView` | Navigation | Bottom tab bar or page swiper |
| `List` | Container | Scrollable rows with CRUD |
| `Layout` | Container | Fully custom arrangement |
| `sheet` | Presentation | Bottom modal overlay |
| `confirmationDialog` | Presentation | Destructive/multi-option prompt |
| `popover` | Presentation | Anchored floating overlay |
| `searchable` | Modifier | Search bar in navigation |
| `draggable` | Modifier | Drag-and-drop source |
| `focusable` | Modifier | Keyboard/remote focus |
| `ButtonStyle` | Protocol | Reusable button appearance |
| `ToggleStyle` | Protocol | Custom toggle appearance |
| `CustomHoverEffect` | Protocol | Pointer hover visual response |
| `Canvas` | Graphics | 2D immediate-mode drawing |
| `Shader` | Graphics | Metal GPU visual effects |
| `Material` | Graphics | Adaptive blur backgrounds |
| `visualEffect` | Graphics | Geometry-aware scroll effects |
| `SensoryFeedback` | Feedback | Haptic and audio feedback |
| `Group(subviewsOf:)` | Advanced | Container view introspection |
| `Animation` | Advanced | Custom animation curves |
| `TextRenderer` | Advanced | Per-glyph text effects |

---

## 📚 Resources

- [Apple SwiftUI Documentation](https://developer.apple.com/documentation/swiftui)
- [WWDC26 — What's New in SwiftUI](https://developer.apple.com/wwdc26/)
- [SwiftUI by Example — Hacking with Swift](https://www.hackingwithswift.com/quick-start/swiftui)
- [The SwiftUI Lab](https://swiftui-lab.com)

---

> 📝 **Note:** Examples target **iOS 17+** unless noted. Some APIs like `Group(subviewsOf:)` and `TextRenderer` require **iOS 18+**.  
> Last updated: **June 2026 · WWDC26**
