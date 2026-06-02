# iOS App Development: A Journey Through Swift

<img width="841" alt="Screenshot 2025-06-22 at 10 51 31 AM" src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" />

# 📱 LiDAR Angle Calculator — Complete Study Notes

### SwiftUI + ARKit + LiDAR Sensor | Every Term Explained

-----

## 📌 Table of Contents

1. [What Are We Building?](#1-what-are-we-building)
1. [Why Do We Need LiDAR?](#2-why-do-we-need-lidar)
1. [Frameworks & Imports](#3-frameworks--imports)
1. [Data Models](#4-data-models)
1. [LiDARSessionManager — The Brain](#5-lidarsessionmanager--the-brain)
1. [The 5-Step Pipeline: Touch → 3D Angle](#6-the-5-step-pipeline-touch--3d-angle)
1. [Camera Intrinsics — The Math](#7-camera-intrinsics--the-math)
1. [Coordinate Spaces Explained](#8-coordinate-spaces-explained)
1. [Angle Calculation — The Dot Product](#9-angle-calculation--the-dot-product)
1. [ARViewContainer — Bridging UIKit to SwiftUI](#10-arviewcontainer--bridging-uikit-to-swiftui)
1. [SwiftUI View Layer — Every Component](#11-swiftui-view-layer--every-component)
1. [The Coordinate Bug & Fix — GeometryReader](#12-the-coordinate-bug--fix--geometryreader)
1. [Full Data Flow Diagram](#13-full-data-flow-diagram)
1. [Quick Reference: Every Term at a Glance](#14-quick-reference-every-term-at-a-glance)

-----

## 1. What Are We Building?

We are building a **3D Angle Calculator** app for iPhone.

**How it works in plain words:**

1. The user taps **Point A** on the screen — say, the corner of a table.
1. The user taps **Point B** on the screen — say, the edge of a wall.
1. The app uses the **LiDAR sensor** to find how far each point is from the phone in real space.
1. Using that depth + the touch location, it converts the 2D screen taps into **real 3D world coordinates**.
1. It then calculates the **angle** between the two directions, using maths (dot product formula).

**Result:** A real-world angle in degrees, shown on screen with a connecting line between the two points.

-----

## 2. Why Do We Need LiDAR?

### The Problem with Normal Touch

When you touch a screen, iOS gives you only **(X, Y)** — a flat point. That tells you *where on the screen* you tapped, but **not how far away** the real object is.

|Sensor       |What it gives   |Axis   |
|-------------|----------------|-------|
|Touch        |Screen position |X, Y   |
|LiDAR        |Real-world depth|Z      |
|Touch + LiDAR|Full 3D position|X, Y, Z|

Without Z (depth), you cannot calculate a meaningful 3D angle. Two points may look 90° apart on screen but be at totally different real-world distances — changing the actual angle completely.

### What is LiDAR?

**LiDAR = Light Detection And Ranging**

- Available on **iPhone 12 Pro and later** (Pro models only)
- Fires **invisible infrared laser pulses** at the scene
- Measures **how long they take to bounce back** (Time of Flight)
- Produces a **depth map** — a grid where every pixel stores a distance in metres
- Accuracy: millimetre-level, up to ~5 metres

Think of it as the phone “seeing” the world in 3D, not just taking a flat photo.

-----

## 3. Frameworks & Imports

```swift
import SwiftUI
import ARKit
import RealityKit
import simd
```

### `import SwiftUI`

**What:** Apple’s modern UI framework for building app interfaces using declarative syntax.
**Why:** All the visual parts — buttons, text, overlays, the camera view — are built with SwiftUI.

### `import ARKit`

**What:** Apple’s Augmented Reality framework. Manages the AR session, camera tracking, and LiDAR depth data.
**Why:** We use ARKit to start the LiDAR session, receive depth maps every frame, and understand where the camera is in 3D space.

### `import RealityKit`

**What:** Apple’s 3D rendering engine for AR experiences.
**Why:** `ARView` (from RealityKit) is used to display the live camera feed and AR content on screen.

### `import simd`

**What:** **Single Instruction, Multiple Data** — Apple’s low-level math library for 3D vectors and matrices.
**Why:** All 3D position data (x, y, z) and matrix transforms use SIMD types because they are extremely fast for spatial calculations.

-----

## 4. Data Models

### `struct TouchPoint3D`

```swift
struct TouchPoint3D {
    let screenPosition: CGPoint        // 2D touch on screen
    let worldPosition: SIMD3<Float>    // 3D world coordinate via LiDAR
}
```

**What:** A custom data container that bundles two pieces of information about a single tap — where on the screen it happened AND where it is in the real 3D world.

**Why:** We need both values together because:

- `screenPosition` → used to **draw the marker** on screen in the right spot
- `worldPosition` → used to **calculate the angle** in real space

**`CGPoint`**: Apple’s struct for a 2D point — has `.x` and `.y` (in screen pixels).

**`SIMD3<Float>`**: A 3-element float vector — stores `(x, y, z)` in metres in world space. Uses SIMD hardware acceleration for fast maths.

-----

## 5. LiDARSessionManager — The Brain

```swift
class LiDARSessionManager: NSObject, ObservableObject, ARSessionDelegate {
```

This class manages everything non-visual: the AR session, depth sampling, and angle calculation.

### `NSObject`

**What:** The root base class for all Objective-C compatible classes.
**Why:** `ARSessionDelegate` (and most Apple frameworks) require conformance to `NSObject`.

### `ObservableObject`

**What:** A SwiftUI protocol. Any class conforming to it can publish changes to SwiftUI views.
**Why:** When the angle is calculated or status changes, SwiftUI views automatically re-render because they observe this object.

### `ARSessionDelegate`

**What:** A protocol with callback functions that ARKit calls automatically.
**Why:** We implement `session(_:didUpdate:)` so we receive a **new depth frame 60 times per second** — always having the latest LiDAR data ready.

-----

### `@Published` Properties

```swift
@Published var pointA: TouchPoint3D? = nil
@Published var pointB: TouchPoint3D? = nil
@Published var angleDegrees: Double? = nil
@Published var statusMessage: String = "Tap Point A to begin"
```

**What:** `@Published` is a property wrapper in SwiftUI’s Combine framework.
**Why:** When any `@Published` variable changes, all SwiftUI views watching this object automatically update their UI. No manual refresh needed.

**The `?` (Optional):** Points start as `nil` because the user hasn’t tapped yet. Optional means the value can either exist or be absent.

-----

### `ARSession`

```swift
let session = ARSession()
```

**What:** The core object that runs the AR experience. It continuously captures camera frames, processes LiDAR data, and tracks the phone’s movement through space.
**Why:** We create one shared session and pass it to both the `ARView` (for display) and our delegate (for data). Only one session can run at a time.

-----

### `ARWorldTrackingConfiguration`

```swift
let config = ARWorldTrackingConfiguration()
config.sceneReconstruction = .mesh
config.frameSemantics = .sceneDepth
config.environmentTexturing = .automatic
```

**What:** The configuration tells ARKit **what features to activate** when running the session.

|Setting               |Value        |Meaning                                          |
|----------------------|-------------|-------------------------------------------------|
|`sceneReconstruction` |`.mesh`      |LiDAR builds a 3D polygon mesh of the environment|
|`frameSemantics`      |`.sceneDepth`|Attach a depth map to every camera frame         |
|`environmentTexturing`|`.automatic` |Apply real camera textures to AR geometry        |

**Why `.mesh`:** Without it, LiDAR still works for depth, but we also want proper AR surface detection.
**Why `.sceneDepth`:** This is the critical one — it’s what gives us the per-pixel distance data we need for our angle calculation.

-----

### `ARFrame` and `currentFrame`

```swift
private var currentFrame: ARFrame?

func session(_ session: ARSession, didUpdate frame: ARFrame) {
    DispatchQueue.main.async {
        self.currentFrame = frame
    }
}
```

**What:** `ARFrame` is a snapshot of the world at one instant — it contains the camera image, depth map, device position, and more.
**Why:** We store the latest frame so that when the user taps, we can immediately sample the depth at that moment.

**`DispatchQueue.main.async`:** ARKit delivers frames on a background thread. Since we update `@Published` properties (which affect UI), we must switch to the **main thread** first. This prevents crashes.

-----

### `ARWorldTrackingConfiguration.supportsSceneReconstruction(.mesh)`

```swift
isLiDARSupported = ARWorldTrackingConfiguration.supportsSceneReconstruction(.mesh)
```

**What:** A static check that returns `true` only on LiDAR-equipped devices.
**Why:** We gracefully handle non-Pro iPhones by showing a warning instead of crashing.

-----

## 6. The 5-Step Pipeline: Touch → 3D Angle

This is the core algorithm. A user tap goes through 5 transformations:

```
Screen Tap (CGPoint)
      ↓  Step 1
Depth Map Pixel (Int, Int)
      ↓  Step 2
Depth Value in Metres (Float32)
      ↓  Step 3
Camera-Space 3D Point (SIMD3<Float>)
      ↓  Step 4
World-Space 3D Point (SIMD3<Float>)
      ↓  Step 5
Angle between Two Points (Double, in degrees)
```

-----

### Step 1: Screen Point → Depth Map Pixel

```swift
let normalizedX = Float(screenPoint.x / viewSize.width)
let normalizedY = Float(screenPoint.y / viewSize.height)

let depthX = Int(normalizedY * Float(depthWidth))
let depthY = Int((1 - normalizedX) * Float(depthHeight))
```

**What:** Convert the tap location (in screen pixels) to the corresponding location in the LiDAR depth map image.

**Why normalise first?** Screen and depth map have different resolutions. By dividing by the screen size, we get a value between 0.0 and 1.0, then multiply by the depth map size.

**Why swap X and Y?** The LiDAR depth map is **always in landscape orientation** (wider than tall), even when the phone is held in portrait. So the axes are rotated — screen X maps to depth Y and vice versa. This is a known ARKit quirk.

**`CVPixelBufferGetWidth / GetHeight`:** These are Core Video functions that return the dimensions of the pixel buffer holding the depth data.

-----

### Step 2: Read the Depth Value

```swift
CVPixelBufferLockBaseAddress(depthMap, .readOnly)
let rowBytes = CVPixelBufferGetBytesPerRow(depthMap)
let baseAddress = CVPixelBufferGetBaseAddress(depthMap)!
let depthPointer = baseAddress
    .advanced(by: clampedY * rowBytes)
    .assumingMemoryBound(to: Float32.self)
let depthValue = depthPointer[clampedX]
CVPixelBufferUnlockBaseAddress(depthMap, .readOnly)
```

**What:** Read the raw depth value (in metres) stored at our target pixel in the LiDAR depth map.

**Why `CVPixelBufferLockBaseAddress`?** The depth map lives in memory shared between the CPU and GPU. Before reading it on the CPU, you must **lock** it so the GPU doesn’t modify it mid-read. You **unlock** it after.

**`rowBytes`:** The number of bytes in one row of the image — used to jump to the correct row in memory.

**`.advanced(by: clampedY * rowBytes)`:** Raw memory pointer arithmetic — navigate to row `clampedY`.

**`.assumingMemoryBound(to: Float32.self)`:** Tell Swift to treat the raw bytes as `Float32` values (each pixel = one 32-bit float = depth in metres).

**`depthPointer[clampedX]`:** Index into that row to get the specific pixel’s depth.

**`clampedX / clampedY`:** We clamp (constrain) the pixel index to valid range `[0, width-1]` so we never read outside the buffer and crash.

-----

### Step 3: Unproject to Camera Space

```swift
let fx = cameraIntrinsics[0][0]   // Focal length X
let fy = cameraIntrinsics[1][1]   // Focal length Y
let cx = cameraIntrinsics[2][0]   // Principal point X
let cy = cameraIntrinsics[2][1]   // Principal point Y

let rayX = (imageX - cx) / fx
let rayY = (imageY - cy) / fy
let rayCamera = SIMD3<Float>(rayX, rayY, 1.0)

let pointCamera = rayCamera * depthValue
```

**What:** Convert the 2D pixel location + depth value into a 3D position in camera space (relative to the lens).

**Why:** Depth alone doesn’t tell us the 3D position — we also need to know the **direction** the camera was pointing. Camera intrinsics define how pixels relate to real-world directions.

Full explanation of camera intrinsics is in Section 7.

-----

### Step 4: Camera Space → World Space

```swift
let cameraTransform = frame.camera.transform
let pointCameraH = SIMD4<Float>(pointCamera.x, pointCamera.y, pointCamera.z, 1.0)
let pointWorldH  = cameraTransform * pointCameraH
```

**What:** Transform the 3D point from “relative to the camera” to “absolute position in the real world.”

**Why:** When you move your phone, the camera moves with it. A point 0.5m in front of the camera means something different depending on where the camera is. The `cameraTransform` matrix encodes the camera’s current position and rotation in world space.

**`SIMD4<Float>`:** We extend the 3D vector to 4D by adding a `w = 1.0` component. This is called a **homogeneous coordinate** — it allows us to apply both rotation and translation in a single matrix multiplication.

**`frame.camera.transform`:** A 4×4 matrix that represents where the camera is (translation) and where it is pointing (rotation) in world space.

-----

### Step 5: Calculate the Angle

Covered in detail in Section 9.

-----

## 7. Camera Intrinsics — The Math

### What Are Intrinsics?

The **camera intrinsic matrix** describes the lens properties of the camera — how the physical world maps to image pixels. It is a 3×3 matrix:

```
K = | fx   0   cx |
    |  0  fy   cy |
    |  0   0    1 |
```

|Symbol|Name             |Meaning                                                                         |
|------|-----------------|--------------------------------------------------------------------------------|
|`fx`  |Focal Length X   |How much the lens magnifies in the X direction (pixels per metre at 1m distance)|
|`fy`  |Focal Length Y   |How much the lens magnifies in the Y direction                                  |
|`cx`  |Principal Point X|The pixel column of the optical centre (usually ≈ image width / 2)              |
|`cy`  |Principal Point Y|The pixel row of the optical centre (usually ≈ image height / 2)                |

### Why Do We Need This?

A pixel at position `(u, v)` in the image corresponds to a **ray** going through 3D space. To find the direction of that ray:

```
rayX = (u - cx) / fx
rayY = (v - cy) / fy
ray = (rayX, rayY, 1.0)   ← normalised ray direction
```

Scale this ray by the depth value `d`:

```
3D point = ray × d = (rayX × d, rayY × d, 1.0 × d)
```

This is exactly what our code does. ARKit automatically calculates `intrinsics` from the device’s camera specs — we just read them from `frame.camera.intrinsics`.

-----

## 8. Coordinate Spaces Explained

There are **3 different coordinate systems** used in this project:

### 1. Screen Space (2D)

- Origin: **top-left corner** of the screen
- Unit: **points** (not pixels — on retina screens, 1 point = 2 or 3 pixels)
- Type: `CGPoint(x:, y:)`
- Used for: drawing markers, detecting taps

### 2. Camera Space (3D)

- Origin: **the camera lens**
- Unit: **metres**
- Z axis: points **away from the lens** (forward into the scene)
- Moves and rotates **with the phone**
- Used for: the intermediate step of unprojecting pixels to 3D

### 3. World Space (3D)

- Origin: **where you first started the AR session** (the phone’s position at launch)
- Unit: **metres**
- Completely **fixed** — does not move when the phone moves
- Used for: final 3D positions of Point A and Point B, angle calculation

```
Touch taps happen in Screen Space
LiDAR gives depth relative to Camera Space
We convert to World Space for stable, accurate measurements
```

-----

## 9. Angle Calculation — The Dot Product

### The Formula

```
θ = arccos( (A⃗ · B⃗) / (|A⃗| × |B⃗|) )
```

In code:

```swift
let vecA = a.worldPosition - cameraPos   // Vector from camera to Point A
let vecB = b.worldPosition - cameraPos   // Vector from camera to Point B

let dot      = simd_dot(vecA, vecB)      // Dot product
let magA     = simd_length(vecA)         // Length of vector A
let magB     = simd_length(vecB)         // Length of vector B

let cosTheta = simd_clamp(dot / (magA * magB), -1.0, 1.0)
let radians  = acos(cosTheta)
let degrees  = Double(radians) * (180.0 / .pi)
```

### Breaking Down Each Term

**`vecA = a.worldPosition - cameraPos`**
A vector pointing **from the camera to Point A**. Subtracting the camera position gives us a direction, not just a location.

**`simd_dot(vecA, vecB)` — Dot Product**
Multiply corresponding components and add:

```
dot = (Ax×Bx) + (Ay×By) + (Az×Bz)
```

This single number captures how “aligned” the two vectors are.

**`simd_length(vecA)` — Magnitude / Length**

```
|A| = √(Ax² + Ay² + Az²)
```

The actual distance from camera to Point A in metres.

**`dot / (magA * magB)`**
Dividing by the product of magnitudes **normalises** the dot product, giving us the **cosine of the angle**.

**`simd_clamp(..., -1.0, 1.0)`**
Due to floating-point rounding errors, the value might be 1.0000001 — which would cause `acos` to return `NaN`. Clamping ensures it stays within the valid range `[-1, 1]`.

**`acos(cosTheta)`**
Inverse cosine — converts the cosine value back to an angle in **radians**.

**`radians × (180 / π)`**
Converts radians to degrees. `π radians = 180°`.

### Why Calculate from the Camera?

We measure the angle **at the camera** — like two laser beams going from the phone to each point. This is the most natural measurement: it’s the angle your eye “sees” between the two points.

-----

## 10. ARViewContainer — Bridging UIKit to SwiftUI

```swift
struct ARViewContainer: UIViewRepresentable {
    let session: ARSession
    
    func makeUIView(context: Context) -> ARView {
        let arView = ARView(frame: .zero)
        arView.session = session
        arView.environment.sceneUnderstanding.options = [.occlusion, .physics]
        return arView
    }
    
    func updateUIView(_ uiView: ARView, context: Context) {}
}
```

### `UIViewRepresentable`

**What:** A SwiftUI protocol that wraps any UIKit view so it can be used inside a SwiftUI layout.
**Why:** `ARView` is a UIKit view (it inherits from `UIView`). SwiftUI cannot use UIKit views directly — `UIViewRepresentable` acts as the bridge.

### `makeUIView(context:)`

**What:** Called once when the view is first created. Must return the UIKit view instance.
**Why:** We create and configure `ARView` here — connect it to our shared `session`, enable occlusion and physics.

### `updateUIView(_:context:)`

**What:** Called whenever SwiftUI state changes. We leave this empty.
**Why:** We don’t need to update `ARView` from SwiftUI — it manages itself through the session.

### `ARView`

**What:** RealityKit’s main view. Shows the live camera feed and renders any 3D AR content.
**Why:** We use it purely as a camera background. All our UI overlays (markers, buttons, HUD) are drawn on top by SwiftUI.

### `.occlusion`

**What:** Makes virtual content hide behind real-world objects detected by LiDAR.
**Why:** Professional AR behaviour — if a marker would appear “behind” a wall, it is correctly hidden.

### `.physics`

**What:** Enables LiDAR-based collision detection for AR objects.
**Why:** Makes the AR scene physically aware of the real environment.

-----

## 11. SwiftUI View Layer — Every Component

### `GeometryReader`

```swift
GeometryReader { geo in
    ZStack(alignment: .topLeading) { ... }
}
```

**What:** A SwiftUI container view that reads and exposes the size of its parent view.
**Why:** We need the actual screen size (`geo.size`) for two things:

1. Normalising touch coordinates for depth map sampling
1. Providing a coordinate space anchor so `.position(x:y:)` places markers exactly at tap locations

Without `GeometryReader`, `.position()` coordinates are relative to the view’s own centre, not the screen’s top-left — causing markers to appear in the wrong place.

-----

### `ZStack`

```swift
ZStack(alignment: .topLeading) {
    // Layer 1: AR camera feed
    // Layer 2: touch overlay
    // Layer 3: connecting line
    // Layer 4: point markers
    // Layer 5: crosshair
    // Layer 6: HUD
}
```

**What:** Stacks child views on top of each other (Z axis = depth on screen).
**Why:** We need the camera feed behind everything, then overlays on top. `ZStack` makes this possible without complex frame management.

**`alignment: .topLeading`:** Sets the coordinate system origin to the top-left corner of the screen. This is critical for `.position(x:y:)` to work correctly with touch coordinates.

-----

### `DragGesture(minimumDistance: 0)`

```swift
.gesture(
    DragGesture(minimumDistance: 0)
        .onEnded { value in
            handleTap(at: value.location, size: geo.size)
        }
)
```

**What:** Detects touch input. `minimumDistance: 0` means it triggers even for a tap (no drag required).
**Why:** SwiftUI’s `TapGesture` doesn’t give you the exact touch location — it only tells you “a tap happened.” `DragGesture` with `minimumDistance: 0` is the standard workaround to get precise coordinates.

**`value.location`:** The `CGPoint` of where the touch ended — in the local coordinate space of the `GeometryReader`.

-----

### `.position(x:y:)` Modifier

```swift
PointMarker(label: "A", color: .cyan)
    .position(x: a.screenPosition.x,
              y: a.screenPosition.y)
```

**What:** Places a view at exact coordinates within its parent container.
**Why:** The marker must appear precisely where the user tapped. Without this, SwiftUI would centre it in the parent by default.

**Key rule:** `.position()` coordinates are relative to the **parent container’s coordinate space**. This is why we need `GeometryReader` and `ZStack(alignment: .topLeading)` — they ensure the coordinate space matches the touch coordinates.

-----

### `PointMarker`

```swift
struct PointMarker: View {
    let label: String
    let color: Color
    @State private var isAnimating = false
    // ...
}
```

**What:** The circular marker that appears at the tap location.
**Components:**

- Outer ripple `Circle` — animates expanding outward to show “just tapped here”
- Inner filled `Circle` — semi-transparent background
- Border `Circle` — solid ring for visibility against any background
- `Text` label — “A” or “B”

**`@State private var isAnimating`:** Local state that drives the ripple animation. Set to `true` on `.onAppear`.

-----

### `ConnectingLine`

```swift
struct ConnectingLine: View {
    let from: CGPoint
    let to: CGPoint
    
    var body: some View {
        Path { path in
            path.move(to: from)
            path.addLine(to: to)
        }
        .stroke(LinearGradient(...), style: StrokeStyle(lineWidth: 2, dash: [8, 4]))
    }
}
```

**What:** Draws a dashed gradient line between Point A and Point B on screen.

**`Path`:** SwiftUI’s vector drawing API. You describe a path (move here, draw line there) and then stroke or fill it.

**`path.move(to:)`:** Lifts the “pen” to a starting location without drawing.
**`path.addLine(to:)`:** Draws a straight line from current position to the given point.

**`StrokeStyle(lineWidth: 2, dash: [8, 4])`:** Makes the line dashed — 8 points drawn, 4 points gap, repeating.

**`LinearGradient`:** Colours the line with a gradient from cyan (Point A’s colour) to orange (Point B’s colour) — giving a visual sense of direction.

-----

### `CrosshairView`

```swift
struct CrosshairView: View {
    var body: some View {
        ZStack {
            Rectangle().frame(width: 20, height: 1.5)   // horizontal bar
            Rectangle().frame(width: 1.5, height: 20)   // vertical bar
            Circle().strokeBorder(.white.opacity(0.5), lineWidth: 1).frame(width: 32, height: 32)
        }
    }
}
```

**What:** A targeting crosshair displayed at the centre of the screen.
**Why:** Helps the user understand where to aim the phone when selecting points. Professional AR apps always include some form of aiming indicator.

-----

### `StatusBar`

```swift
struct StatusBar: View {
    let message: String
    let isLiDARSupported: Bool
}
```

**What:** A floating pill-shaped bar at the top showing the current state of the app (e.g., “Tap Point A on any surface”).

**`.ultraThinMaterial`:** iOS’s blur material — creates a frosted-glass effect so the text is readable over any camera background. Part of Apple’s visual design language.

**`in: Capsule()`:** The shape used as the background clip. `Capsule()` is a rounded rectangle with fully circular ends.

-----

### `AngleResultCard`

```swift
struct AngleResultCard: View {
    let angle: Double
    let pointA: TouchPoint3D
    let pointB: TouchPoint3D
    
    var distanceCm: Float {
        simd_distance(pointA.worldPosition, pointB.worldPosition) * 100
    }
}
```

**What:** The result panel shown at the bottom after both points are selected. Shows angle, distance, and type.

**`simd_distance`:** A SIMD function that calculates the straight-line distance between two 3D points.

```
distance = √((Bx-Ax)² + (By-Ay)² + (Bz-Az)²)
```

Multiply by 100 to convert metres → centimetres.

**`angleCategory`:** A computed property that classifies the angle using a `switch` statement:

- < 45° → Acute (Sharp)
- 45–90° → Acute
- 90° → Right Angle
- 90–135° → Obtuse
- 135° → Obtuse (Wide)

-----

### `AngleArcView`

```swift
Path { p in
    p.addArc(
        center: CGPoint(x: 10, y: 60),
        radius: 22,
        startAngle: .degrees(-90),
        endAngle: .degrees(angle - 90),
        clockwise: false
    )
}
```

**What:** Draws a small arc graphic that visually shows the calculated angle.

**`addArc`:** Draws a circular arc between two angles. `startAngle: .degrees(-90)` starts from the top (12 o’clock position). The arc sweeps to `angle - 90` degrees, visually representing the calculated angle.

**Why `-90`?** In iOS, `0°` in the drawing system is the 3 o’clock (right) position. Subtracting 90° rotates the start point to the top (12 o’clock), which is more intuitive.

-----

### `ControlPanel`

```swift
struct ControlPanel: View {
    let isSelectingA: Bool
    let onSelectA: () -> Void
    let onSelectB: () -> Void
    let onReset: () -> Void
}
```

**What:** The three buttons at the bottom — “Set A”, “Set B”, and Reset.

**Closure parameters (`() -> Void`):** Functions passed as parameters. This is Swift’s pattern for callbacks. The parent view passes actions; the child view calls them when buttons are pressed. Keeps views decoupled.

**Active button highlight:** The active button gets a solid coloured background (`cyan` or `orange`); inactive gets a semi-transparent white. Driven by `isSelectingA` boolean.

-----

### `InfoChip`

```swift
struct InfoChip: View {
    let icon: String   // SF Symbols name
    let label: String
    let value: String
}
```

**What:** A small vertical stack showing an icon, value, and label — used inside `AngleResultCard` for Distance, Type, and Source.

**`Image(systemName:)`:** Loads an icon from Apple’s **SF Symbols** library — over 5000 built-in icons.

-----

### `@main` and `App`

```swift
@main
struct LiDARAngleApp: App {
    var body: some Scene {
        WindowGroup {
            LiDARAngleView()
        }
    }
}
```

**`@main`:** Marks this struct as the **entry point** of the app — equivalent to the old `main.m` file.
**`App` protocol:** The SwiftUI way to define the application’s root.
**`WindowGroup`:** A container that manages one or more windows. On iPhone it always shows as a single full-screen window.
**`LiDARAngleView()`:** The first view shown when the app launches.

-----

## 12. The Coordinate Bug & Fix — GeometryReader

### The Bug

Original code:

```swift
ZStack {
    // ...
    PointMarker(point: a.screenPosition, label: "A", color: .cyan)
    // ^ .position() inside the marker was relative to ZStack's centre
}
```

When `.position(point)` is called inside a plain `ZStack`, the origin is the **centre of the ZStack**, not the top-left corner. So a tap at screen coordinates (100, 200) would place the marker at (100, 200) **offset from centre** — completely wrong.

### The Fix

```swift
GeometryReader { geo in
    ZStack(alignment: .topLeading) {
        // ...
        PointMarker(label: "A", color: .cyan)
            .position(x: a.screenPosition.x,
                      y: a.screenPosition.y)
    }
}
```

**Step 1:** `GeometryReader` gives the entire view a coordinate space anchored to its own top-left corner.

**Step 2:** `ZStack(alignment: .topLeading)` tells the ZStack to also use top-left as its origin.

**Step 3:** `.position(x:y:)` on the **parent** (not inside `PointMarker`) uses coordinates in that same top-left-anchored space.

**Step 4:** `value.location` from `DragGesture` gives coordinates in the same `GeometryReader` space — so they match perfectly.

**Result:** Tap at (250, 400) → marker appears at exactly (250, 400) on screen.

-----

## 13. Full Data Flow Diagram

```
USER TAPS SCREEN
      │
      ▼
DragGesture.onEnded
value.location → CGPoint(x, y) in GeometryReader space
      │
      ▼
handleTap(at:size:)
      │
      ▼
LiDARSessionManager.setPoint()
      │
      ├─→ worldPosition(for:viewSize:)
      │         │
      │         ├─ 1. Normalize: (x/width, y/height)
      │         ├─ 2. Map to depth map pixel (swap axes for landscape)
      │         ├─ 3. Read Float32 depth value from CVPixelBuffer (metres)
      │         ├─ 4. Unproject using camera intrinsics → camera-space 3D point
      │         └─ 5. Multiply by camera.transform → world-space 3D point
      │
      ├─→ Store as TouchPoint3D { screenPosition, worldPosition }
      │
      ├─→ @Published pointA / pointB updated → SwiftUI re-renders
      │
      └─→ calculateAngle() [when both points set]
                │
                ├─ vecA = pointA.worldPosition - cameraPosition
                ├─ vecB = pointB.worldPosition - cameraPosition
                ├─ dot = simd_dot(vecA, vecB)
                ├─ angle = arccos(dot / (|vecA| × |vecB|))
                └─ @Published angleDegrees updated → AngleResultCard shows
```

-----

## 14. Quick Reference: Every Term at a Glance

|Term                             |Category        |What It Is                             |Why We Use It                                           |
|---------------------------------|----------------|---------------------------------------|--------------------------------------------------------|
|`LiDAR`                          |Hardware        |Laser depth sensor on Pro iPhones      |Provides real-world Z depth for each tap                |
|`ARKit`                          |Framework       |Apple’s AR framework                   |Runs LiDAR session, tracks camera, delivers depth frames|
|`RealityKit`                     |Framework       |Apple’s 3D rendering engine            |Provides `ARView` for camera feed display               |
|`simd`                           |Framework       |Fast low-level math library            |Efficient 3D vector and matrix operations               |
|`ARSession`                      |ARKit class     |Manages the running AR experience      |Central hub for all AR data                             |
|`ARFrame`                        |ARKit class     |One snapshot of the world              |Contains depth map + camera transform per frame         |
|`ARWorldTrackingConfiguration`   |ARKit class     |Settings for the AR session            |Activates LiDAR mesh and depth semantics                |
|`sceneDepth`                     |Config setting  |Enables depth map on every frame       |Gives us per-pixel LiDAR measurements                   |
|`sceneReconstruction`            |Config setting  |Builds 3D mesh of environment          |Enables occlusion and surface detection                 |
|`CVPixelBuffer`                  |Core Video      |Raw memory buffer of pixel data        |Holds the LiDAR depth map                               |
|`CVPixelBufferLock/Unlock`       |Core Video      |CPU/GPU memory access control          |Prevents crash when reading depth data                  |
|`SIMD3<Float>`                   |simd type       |3-element float vector (x,y,z)         |Stores 3D world positions efficiently                   |
|`SIMD4<Float>`                   |simd type       |4-element float vector (x,y,z,w)       |Homogeneous coordinates for matrix math                 |
|`camera.intrinsics`              |ARKit property  |3×3 camera matrix (fx, fy, cx, cy)     |Maps pixels to real-world rays                          |
|`camera.transform`               |ARKit property  |4×4 position+rotation matrix           |Converts camera space to world space                    |
|`simd_dot`                       |simd function   |Dot product of two vectors             |Used in angle formula                                   |
|`simd_length`                    |simd function   |Length/magnitude of a vector           |Used to normalise dot product                           |
|`simd_clamp`                     |simd function   |Constrains value to range              |Prevents `acos` from receiving invalid input            |
|`simd_distance`                  |simd function   |Distance between two 3D points         |Calculates real-world distance in metres                |
|`acos`                           |Swift math      |Inverse cosine                         |Converts cosine value to angle                          |
|`ObservableObject`               |SwiftUI         |Protocol for observable data classes   |Enables automatic UI updates                            |
|`@Published`                     |SwiftUI         |Property wrapper                       |Triggers SwiftUI view refresh on change                 |
|`@StateObject`                   |SwiftUI         |Owns and observes an `ObservableObject`|Keeps `LiDARSessionManager` alive                       |
|`@State`                         |SwiftUI         |Local view state                       |Drives local animations and toggles                     |
|`GeometryReader`                 |SwiftUI         |Reads parent view size                 |Provides consistent coordinate space                    |
|`ZStack`                         |SwiftUI         |Layers views on top of each other      |Stacks AR feed + overlays + HUD                         |
|`UIViewRepresentable`            |SwiftUI         |Protocol to use UIKit views in SwiftUI |Wraps `ARView` for use in SwiftUI                       |
|`DragGesture(minimumDistance: 0)`|SwiftUI         |Touch detector                         |Gets precise tap coordinates                            |
|`.position(x:y:)`                |SwiftUI modifier|Places view at exact coordinates       |Snaps marker to tap location                            |
|`CGPoint`                        |Core Graphics   |2D coordinate (x, y)                   |Stores screen touch location                            |
|`CGSize`                         |Core Graphics   |2D size (width, height)                |Stores screen dimensions                                |
|`Path`                           |SwiftUI         |Vector shape drawing API               |Draws connecting line and angle arc                     |
|`DispatchQueue.main.async`       |Foundation      |Switches to main thread                |Required for UI updates from background thread          |
|`SF Symbols`                     |Apple design    |Built-in icon library                  |Used for icons in InfoChip and StatusBar                |
|`.ultraThinMaterial`             |SwiftUI         |Frosted glass blur background          |Makes text readable over camera feed                    |
|`Capsule()`                      |SwiftUI shape   |Rounded-end rectangle                  |Used as pill-shaped background for StatusBar            |
|`Homogeneous coordinates`        |Math concept    |4D representation of 3D points         |Enables combined rotation + translation matrix math     |
|`Dot product`                    |Math concept    |Sum of component products              |Core of angle formula                                   |
|`Magnitude`                      |Math concept    |Length of a vector                     |Used to normalise dot product                           |
|`Radians`                        |Math concept    |Angle unit (0 to 2π)                   |What `acos` returns                                     |
|`Degrees`                        |Math concept    |Angle unit (0 to 360)                  |What we display to the user                             |
|`World space`                    |3D concept      |Fixed coordinate system in real world  |Stable reference for angle calculation                  |
|`Camera space`                   |3D concept      |Coordinate system relative to lens     |Intermediate step in unprojection                       |
|`Screen space`                   |2D concept      |Pixel coordinates on phone display     |Where taps are detected                                 |
|`Unprojection`                   |Computer vision |Converting 2D pixel → 3D ray           |Core technique combining touch + depth                  |
|`Camera intrinsics`              |Computer vision |Lens parameters (fx, fy, cx, cy)       |Required for accurate unprojection                      |
|`Normalised coordinates`         |Math concept    |Values scaled to 0.0–1.0 range         |Converts between different resolutions                  |

-----

*Notes prepared for the LiDAR Angle Calculator SwiftUI project*
*Covers: ARKit · RealityKit · simd · SwiftUI · Camera Intrinsics · 3D Math*
