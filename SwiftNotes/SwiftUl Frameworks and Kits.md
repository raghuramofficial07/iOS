
<img width="841" alt="Screenshot 2025-06-22 at 10 51 31 AM" src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" />

# SwiftUI — Complete Frameworks & Kits Reference Guide

> A detailed reference covering every major framework, kit, and library available in the SwiftUI ecosystem — their purpose, use cases, key APIs, and when to use them.

-----

## 📦 Total Count: 35 Frameworks & Kits Covered

|# |Framework / Kit                |Domain                         |
|--|-------------------------------|-------------------------------|
|1 |SwiftUI (Core)                 |UI Framework                   |
|2 |UIKit                          |UI Framework (Legacy + Interop)|
|3 |AppKit                         |macOS UI Framework             |
|4 |Combine                        |Reactive Programming           |
|5 |Swift Concurrency (async/await)|Concurrency                    |
|6 |Foundation                     |Core Utilities                 |
|7 |CoreData                       |Persistence                    |
|8 |SwiftData                      |Modern Persistence             |
|9 |CloudKit                       |Cloud Storage                  |
|10|CoreLocation                   |Location Services              |
|11|MapKit                         |Maps & Navigation              |
|12|CoreMotion                     |Motion & Sensors               |
|13|CoreML                         |Machine Learning               |
|14|CreateML                       |ML Model Training              |
|15|Vision                         |Computer Vision                |
|16|NaturalLanguage                |NLP                            |
|17|ARKit                          |Augmented Reality              |
|18|RealityKit                     |3D / AR Rendering              |
|19|SceneKit                       |3D Graphics                    |
|20|SpriteKit                      |2D Games                       |
|21|GameKit                        |Multiplayer & Leaderboards     |
|22|AVFoundation                   |Audio & Video                  |
|23|CoreImage                      |Image Processing               |
|24|PhotosUI                       |Photo Library Access           |
|25|StoreKit                       |In-App Purchases               |
|26|AuthenticationServices         |Sign In / OAuth                |
|27|LocalAuthentication            |Biometrics                     |
|28|CryptoKit                      |Cryptography                   |
|29|Network                        |Networking                     |
|30|WebKit                         |Web Content                    |
|31|HealthKit                      |Health Data                    |
|32|HomeKit                        |Smart Home                     |
|33|WatchKit                       |Apple Watch                    |
|34|WidgetKit                      |Widgets                        |
|35|ActivityKit                    |Live Activities                |

-----

-----

# 1. SwiftUI (Core Framework)

## Overview

SwiftUI is Apple’s declarative UI framework introduced in 2019. It is the primary way to build user interfaces across all Apple platforms — iOS, macOS, watchOS, tvOS, and visionOS — using a single, unified codebase.

## Use Case

Building any modern Apple platform app UI. SwiftUI replaces the need for storyboards and programmatic UIKit for most use cases.

## Key Concepts

### Views & Modifiers

```swift
struct ContentView: View {
    var body: some View {
        Text("Hello, SwiftUI!")
            .font(.largeTitle)
            .foregroundStyle(.blue)
            .padding()
            .background(.ultraThinMaterial)
            .cornerRadius(12)
    }
}
```

### State Management

```swift
@State private var count = 0          // Local view state
@Binding var isPresented: Bool        // Two-way binding from parent
@StateObject var vm = MyViewModel()   // Owned ObservableObject
@ObservedObject var vm: MyViewModel   // Passed-in ObservableObject
@EnvironmentObject var store: Store   // Injected globally
@Environment(\.colorScheme) var scheme // System environment values
```

### Layout System

```swift
VStack(spacing: 16) { }   // Vertical stack
HStack(alignment: .top) { } // Horizontal stack
ZStack { }                 // Depth/overlay stack
LazyVGrid(columns: [...]) { } // Grid layout
LazyVStack { }             // Lazy loading list
```

### Navigation

```swift
// iOS 16+ Navigation Stack
NavigationStack {
    List(items) { item in
        NavigationLink(value: item) {
            Text(item.name)
        }
    }
    .navigationDestination(for: Item.self) { item in
        DetailView(item: item)
    }
}
```

### Animations

```swift
withAnimation(.spring(response: 0.5)) {
    isExpanded.toggle()
}

// Matched Geometry Effect
@Namespace var animation
.matchedGeometryEffect(id: item.id, in: animation)
```

## When to Use

- Any new Apple platform project
- Replacing legacy UIKit/AppKit views incrementally
- Building for multiple platforms simultaneously

## Minimum Deployment

- iOS 13+, macOS 10.15+, watchOS 6+, tvOS 13+

-----

-----

# 2. UIKit

## Overview

UIKit is Apple’s original imperative UI framework for iOS and tvOS, dating back to iPhone OS 2.0 (2008). It remains widely used and SwiftUI can fully interoperate with it.

## Use Case

- Projects requiring iOS 12 or earlier support
- Complex custom UI components not yet available in SwiftUI
- Integrating legacy codebases into SwiftUI apps

## SwiftUI Interoperability

### Wrapping UIKit in SwiftUI

```swift
struct MapViewRepresentable: UIViewRepresentable {
    func makeUIView(context: Context) -> MKMapView {
        return MKMapView()
    }
    
    func updateUIView(_ uiView: MKMapView, context: Context) {
        // Update map region, annotations, etc.
    }
}
```

### Wrapping UIViewController in SwiftUI

```swift
struct ImagePickerRepresentable: UIViewControllerRepresentable {
    @Binding var selectedImage: UIImage?
    
    func makeUIViewController(context: Context) -> UIImagePickerController {
        let picker = UIImagePickerController()
        picker.delegate = context.coordinator
        return picker
    }
    
    func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {}
    
    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }
    
    class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
        var parent: ImagePickerRepresentable
        init(_ parent: ImagePickerRepresentable) { self.parent = parent }
        
        func imagePickerController(_ picker: UIImagePickerController,
                                   didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey: Any]) {
            parent.selectedImage = info[.originalImage] as? UIImage
        }
    }
}
```

### Using SwiftUI inside UIKit

```swift
// Embed a SwiftUI view inside UIViewController
let hostingVC = UIHostingController(rootView: MySwiftUIView())
addChild(hostingVC)
view.addSubview(hostingVC.view)
hostingVC.didMove(toParent: self)
```

## Key Components

- `UIViewController` — Screen management
- `UITableView` / `UICollectionView` — Data-driven lists and grids
- `UINavigationController` — Stack-based navigation
- `UIGestureRecognizer` — Touch handling
- `UIKit Dynamics` — Physics-based animations

## When to Use

- Minimum deployment target below iOS 13
- Highly customized animations or gestures
- Third-party SDKs that only provide UIKit APIs

-----

-----

# 3. AppKit

## Overview

AppKit is Apple’s UI framework for macOS, equivalent to UIKit on iOS. SwiftUI on Mac compiles to AppKit under the hood.

## Use Case

Building native macOS applications — windows, menus, toolbars, panels, and desktop-class interactions.

## SwiftUI Interoperability

```swift
// Wrap NSView in SwiftUI
struct CustomNSViewWrapper: NSViewRepresentable {
    func makeNSView(context: Context) -> NSTextField {
        return NSTextField()
    }
    func updateNSView(_ nsView: NSTextField, context: Context) {}
}
```

## Key macOS-Specific SwiftUI APIs

```swift
// Menu bar extra (macOS 13+)
MenuBarExtra("App", systemImage: "star") {
    Button("Action") { }
}

// Window management
WindowGroup("Detail", for: Item.ID.self) { $id in
    DetailView(id: id)
}

// Toolbar
.toolbar {
    ToolbarItem(placement: .primaryAction) {
        Button("Add") { }
    }
}

// Sidebar split view
NavigationSplitView {
    SidebarView()
} detail: {
    DetailView()
}
```

## When to Use

- Building Mac Catalyst or native macOS apps
- Menu bar utilities
- Document-based applications

-----

-----

# 4. Combine

## Overview

Combine is Apple’s reactive programming framework (2019). It provides a declarative Swift API for processing values over time using publishers and subscribers.

## Use Case

- Handling asynchronous data streams
- Networking response processing
- Form validation
- Binding data models to SwiftUI views

## Core Concepts

### Publishers & Subscribers

```swift
import Combine

// Simple publisher
let publisher = [1, 2, 3, 4, 5].publisher

publisher
    .filter { $0 % 2 == 0 }
    .map { $0 * 10 }
    .sink { value in
        print(value) // 20, 40
    }
    .store(in: &cancellables)
```

### Networking with Combine

```swift
struct APIService {
    func fetchUsers() -> AnyPublisher<[User], Error> {
        let url = URL(string: "https://api.example.com/users")!
        return URLSession.shared
            .dataTaskPublisher(for: url)
            .map(\.data)
            .decode(type: [User].self, decoder: JSONDecoder())
            .receive(on: DispatchQueue.main)
            .eraseToAnyPublisher()
    }
}
```

### ObservableObject with Combine

```swift
class SearchViewModel: ObservableObject {
    @Published var query = ""
    @Published var results: [Item] = []
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        $query
            .debounce(for: 0.3, scheduler: RunLoop.main)
            .removeDuplicates()
            .flatMap { query in self.search(query: query) }
            .assign(to: &$results)
    }
}
```

## Key Operators

|Operator       |Purpose                  |
|---------------|-------------------------|
|`map`          |Transform values         |
|`filter`       |Filter values            |
|`flatMap`      |Merge inner publishers   |
|`debounce`     |Delay emissions          |
|`combineLatest`|Merge multiple publishers|
|`merge`        |Interleave publishers    |
|`zip`          |Pair emissions           |

## When to Use

- Complex asynchronous event chains
- Form validation pipelines
- Legacy code pre-Swift concurrency

-----

-----

# 5. Swift Concurrency (async/await)

## Overview

Introduced in Swift 5.5 / iOS 15, Swift Concurrency provides first-class async/await syntax, actors, and structured concurrency — the modern replacement for completion handlers and Combine in many cases.

## Use Case

- Asynchronous networking
- Background processing
- Task management
- Actor-isolated state to prevent data races

## Core APIs

### async/await

```swift
func fetchUser(id: String) async throws -> User {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data)
}

// Call site
Task {
    do {
        let user = try await fetchUser(id: "123")
        print(user.name)
    } catch {
        print(error)
    }
}
```

### SwiftUI Integration with `.task`

```swift
struct UserView: View {
    @State private var user: User?
    
    var body: some View {
        Group {
            if let user {
                Text(user.name)
            } else {
                ProgressView()
            }
        }
        .task {
            user = try? await fetchUser(id: "123")
        }
    }
}
```

### Actors

```swift
actor DataStore {
    private var cache: [String: Data] = [:]
    
    func store(key: String, data: Data) {
        cache[key] = data
    }
    
    func retrieve(key: String) -> Data? {
        cache[key]
    }
}

// Usage
let store = DataStore()
await store.store(key: "profile", data: profileData)
```

### Async Sequences

```swift
for await update in locationStream {
    updateUI(with: update)
}
```

### TaskGroup

```swift
let results = try await withThrowingTaskGroup(of: Image.self) { group in
    for url in imageURLs {
        group.addTask { try await downloadImage(from: url) }
    }
    var images: [Image] = []
    for try await image in group { images.append(image) }
    return images
}
```

## When to Use

- All new async code (prefer over Combine for simple pipelines)
- Any iOS 15+ / macOS 12+ project
- Background data loading in SwiftUI `.task` modifier

-----

-----

# 6. Foundation

## Overview

Foundation is the bedrock framework providing essential data types, collections, OS services, and utilities. Every Swift/Apple app uses it implicitly.

## Use Case

String manipulation, date/time handling, JSON encoding/decoding, file system access, URL handling, notifications, and more.

## Key APIs in SwiftUI Context

### JSON Codable

```swift
struct Product: Codable, Identifiable {
    let id: Int
    let name: String
    let price: Double
}

// Decode
let product = try JSONDecoder().decode(Product.self, from: data)

// Encode
let data = try JSONEncoder().encode(product)
```

### Date Formatting

```swift
// New (iOS 15+)
Text(date.formatted(.dateTime.month().day().year()))
Text(date.formatted(date: .long, time: .shortened))

// Relative
Text(date, format: .relative(presentation: .named))
```

### UserDefaults with AppStorage

```swift
// SwiftUI wrapper
@AppStorage("username") var username = ""
@AppStorage("isDarkMode") var isDarkMode = false

// Direct
UserDefaults.standard.set("John", forKey: "username")
```

### NotificationCenter

```swift
.onReceive(NotificationCenter.default.publisher(for: UIApplication.didBecomeActiveNotification)) { _ in
    refreshData()
}
```

### FileManager

```swift
let docs = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
let fileURL = docs.appendingPathComponent("data.json")
try data.write(to: fileURL)
```

## When to Use

- Always — Foundation is used in every Apple app automatically

-----

-----

# 7. Core Data

## Overview

Core Data is Apple’s object graph management and persistence framework. It provides an ORM-like layer on top of SQLite (or binary/in-memory stores).

## Use Case

- Persistent local data storage
- Offline-first applications
- Complex relational data models
- iCloud sync via NSPersistentCloudKitContainer

## SwiftUI Integration

### Setup

```swift
// PersistenceController.swift
struct PersistenceController {
    static let shared = PersistenceController()
    let container: NSPersistentContainer
    
    init() {
        container = NSPersistentContainer(name: "MyApp")
        container.loadPersistentStores { _, error in
            if let error { fatalError("Core Data error: \(error)") }
        }
        container.viewContext.automaticallyMergesChangesFromParent = true
    }
}

// App entry point
@main
struct MyApp: App {
    let persistence = PersistenceController.shared
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(\.managedObjectContext, persistence.container.viewContext)
        }
    }
}
```

### Fetching Data

```swift
struct TaskListView: View {
    @FetchRequest(
        sortDescriptors: [SortDescriptor(\.createdAt, order: .reverse)],
        predicate: NSPredicate(format: "isCompleted == false"),
        animation: .default
    )
    private var tasks: FetchedResults<Task>
    
    var body: some View {
        List(tasks) { task in
            Text(task.title ?? "Untitled")
        }
    }
}
```

### CRUD Operations

```swift
@Environment(\.managedObjectContext) private var viewContext

// Create
let task = Task(context: viewContext)
task.title = "Buy groceries"
task.createdAt = Date()
try viewContext.save()

// Delete
viewContext.delete(task)
try viewContext.save()
```

### iCloud Sync

```swift
let container = NSPersistentCloudKitContainer(name: "MyApp")
```

## When to Use

- Complex relational local data (replace with SwiftData for iOS 17+)
- Apps needing iCloud CoreData sync
- Large datasets with advanced querying needs

-----

-----

# 8. SwiftData

## Overview

SwiftData (iOS 17+, macOS 14+) is Apple’s modern replacement for Core Data. It uses Swift macros for a clean, Swift-native persistence API with zero boilerplate.

## Use Case

- Local data persistence in new apps targeting iOS 17+
- Replacing Core Data with minimal migration effort
- Apps needing automatic iCloud sync

## Full Example

### Model Definition

```swift
import SwiftData

@Model
class Task {
    var title: String
    var isCompleted: Bool
    var createdAt: Date
    var priority: Int
    
    @Relationship(deleteRule: .cascade)
    var subtasks: [Subtask] = []
    
    init(title: String, priority: Int = 0) {
        self.title = title
        self.isCompleted = false
        self.createdAt = Date()
        self.priority = priority
    }
}
```

### App Setup

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: [Task.self, Subtask.self])
    }
}
```

### Querying

```swift
struct TaskListView: View {
    @Query(sort: \.createdAt, order: .reverse) var tasks: [Task]
    @Environment(\.modelContext) private var context
    
    var body: some View {
        List(tasks) { task in
            Text(task.title)
        }
    }
}
```

### CRUD

```swift
// Insert
let task = Task(title: "New Task")
context.insert(task)

// Delete
context.delete(task)

// Update — just modify the property, SwiftData tracks changes
task.isCompleted = true
```

## When to Use

- Any new app with iOS 17+ minimum deployment
- Strongly prefer over Core Data for greenfield projects

-----

-----

# 9. CloudKit

## Overview

CloudKit is Apple’s cloud database and storage service, tightly integrated with iCloud. It provides public, private, and shared databases.

## Use Case

- Syncing user data across devices via iCloud
- Shared collaborative data between users
- Public content databases (e.g., app-wide leaderboard)

## Key APIs

### SwiftUI + CloudKit Pattern

```swift
// Enable iCloud capability in Xcode project first

// Using with Core Data
let container = NSPersistentCloudKitContainer(name: "MyApp")

// Direct CloudKit
let container = CKContainer.default()
let privateDB = container.privateCloudDatabase
let publicDB = container.publicCloudDatabase
```

### Saving a Record

```swift
let record = CKRecord(recordType: "Note")
record["title"] = "My Note" as CKRecordValue
record["content"] = "Hello CloudKit" as CKRecordValue

privateDB.save(record) { record, error in
    DispatchQueue.main.async {
        if let error { print("Error: \(error)") }
        else { print("Saved: \(record?.recordID.recordName ?? "")") }
    }
}
```

### Fetching Records

```swift
let predicate = NSPredicate(format: "title == %@", "My Note")
let query = CKQuery(recordType: "Note", predicate: predicate)

privateDB.fetch(withQuery: query) { result in
    switch result {
    case .success(let (matchResults, _)):
        for (_, recordResult) in matchResults {
            if case .success(let record) = recordResult {
                print(record["title"] as? String ?? "")
            }
        }
    case .failure(let error):
        print(error)
    }
}
```

## When to Use

- Cross-device iCloud sync without a backend server
- Apple-ecosystem-only apps (no Android cross-sync needed)
- Shared data between users (e.g., shared shopping lists)

-----

-----

# 10. CoreLocation

## Overview

CoreLocation provides location and heading data from GPS, Wi-Fi, and cellular signals.

## Use Case

- Getting the user’s current GPS coordinates
- Geofencing (region monitoring)
- Significant location change monitoring
- Heading / compass direction

## SwiftUI Integration

### Modern (iOS 17+ with Observable)

```swift
import CoreLocation

@Observable
class LocationManager: NSObject, CLLocationManagerDelegate {
    private let manager = CLLocationManager()
    var location: CLLocation?
    var authorizationStatus: CLAuthorizationStatus = .notDetermined
    
    override init() {
        super.init()
        manager.delegate = self
        manager.desiredAccuracy = kCLLocationAccuracyBest
    }
    
    func requestPermission() {
        manager.requestWhenInUseAuthorization()
    }
    
    func startUpdating() {
        manager.startUpdatingLocation()
    }
    
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        location = locations.last
    }
    
    func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
        authorizationStatus = manager.authorizationStatus
    }
}

// SwiftUI View
struct LocationView: View {
    @State private var locationManager = LocationManager()
    
    var body: some View {
        VStack {
            if let loc = locationManager.location {
                Text("Lat: \(loc.coordinate.latitude)")
                Text("Lng: \(loc.coordinate.longitude)")
            }
            Button("Start") { locationManager.startUpdating() }
        }
        .onAppear { locationManager.requestPermission() }
    }
}
```

### Geofencing

```swift
let region = CLCircularRegion(
    center: CLLocationCoordinate2D(latitude: 37.33, longitude: -122.01),
    radius: 100,
    identifier: "AppleHQ"
)
region.notifyOnEntry = true
manager.startMonitoring(for: region)
```

## When to Use

- Navigation or map-based apps
- Location-aware features (weather, nearby places)
- Delivery / ride-sharing apps

-----

-----

# 11. MapKit

## Overview

MapKit renders interactive Apple Maps within your app and provides geocoding, routing, and local search capabilities.

## Use Case

- Displaying maps with custom annotations
- Routing and turn-by-turn navigation
- Searching for places
- Drawing overlays (routes, regions)

## SwiftUI Map API (iOS 17+)

### Basic Map

```swift
import MapKit

struct MapView: View {
    @State private var position: MapCameraPosition = .automatic
    
    var body: some View {
        Map(position: $position) {
            Marker("Apple Park", coordinate: CLLocationCoordinate2D(latitude: 37.3349, longitude: -122.0090))
            UserAnnotation() // Blue dot for user location
        }
        .mapStyle(.standard(elevation: .realistic))
        .mapControls {
            MapUserLocationButton()
            MapCompass()
            MapScaleView()
        }
    }
}
```

### Custom Annotations

```swift
Map {
    ForEach(locations) { location in
        Annotation(location.name, coordinate: location.coordinate) {
            ZStack {
                Circle().fill(.red).frame(width: 44, height: 44)
                Image(systemName: "mappin").foregroundStyle(.white)
            }
        }
    }
}
```

### Local Search

```swift
let request = MKLocalSearch.Request()
request.naturalLanguageQuery = "coffee"
request.region = map.region

let search = MKLocalSearch(request: request)
let response = try await search.start()
let items = response.mapItems
```

## When to Use

- Apps requiring map visualization
- Location-based business directory apps
- Navigation and route planning

-----

-----

# 12. CoreMotion

## Overview

CoreMotion provides access to the device’s motion sensors — accelerometer, gyroscope, magnetometer, barometer, and pedometer.

## Use Case

- Step counting / fitness tracking
- Device orientation and tilt
- Shake detection
- Altitude / barometric pressure

## SwiftUI Integration

```swift
import CoreMotion

@Observable
class MotionManager {
    private let manager = CMMotionManager()
    var acceleration: CMAcceleration = CMAcceleration()
    var steps: Int = 0
    
    func startAccelerometer() {
        guard manager.isAccelerometerAvailable else { return }
        manager.accelerometerUpdateInterval = 0.1
        manager.startAccelerometerUpdates(to: .main) { data, _ in
            self.acceleration = data?.acceleration ?? CMAcceleration()
        }
    }
    
    func startPedometer() {
        let pedometer = CMPedometer()
        let start = Calendar.current.startOfDay(for: Date())
        pedometer.queryPedometerData(from: start, to: Date()) { data, _ in
            DispatchQueue.main.async {
                self.steps = data?.numberOfSteps.intValue ?? 0
            }
        }
    }
}
```

## When to Use

- Fitness and health apps
- Games using device tilt as input
- Fall detection features

-----

-----

# 13. CoreML

## Overview

CoreML is Apple’s on-device machine learning inference framework. It runs ML models locally without network calls, preserving privacy and enabling offline use.

## Use Case

- Image classification
- Object detection
- Text classification / sentiment analysis
- Tabular data prediction
- Sound classification

## SwiftUI Integration

### Image Classification

```swift
import CoreML
import Vision

func classifyImage(_ image: UIImage) {
    guard let model = try? VNCoreMLModel(for: MobileNetV2().model) else { return }
    
    let request = VNCoreMLRequest(model: model) { request, _ in
        guard let results = request.results as? [VNClassificationObservation],
              let top = results.first else { return }
        print("Class: \(top.identifier), Confidence: \(top.confidence)")
    }
    
    let handler = VNImageRequestHandler(cgImage: image.cgImage!)
    try? handler.perform([request])
}
```

### Text Classification

```swift
let model = try NLModel(mlModel: MySentimentModel().model)
let label = model.predictedLabel(for: "This is amazing!")
print(label ?? "unknown") // "Positive"
```

## When to Use

- Apps needing AI features without server costs
- Privacy-sensitive ML (no data leaves device)
- Augmented reality + object recognition

-----

-----

# 14. CreateML

## Overview

CreateML is Apple’s framework and macOS app for training custom ML models directly on Mac using Swift, with no Python required.

## Use Case

- Training custom image classifiers
- Object detector training
- Text classifier training
- Sound classifier training
- Export to `.mlmodel` for CoreML

## Example: Training a Classifier

```swift
import CreateML

// Image Classifier
let trainingData = URL(fileURLWithPath: "/path/to/training/images")
let classifier = try MLImageClassifier(trainingData: .labeledDirectories(at: trainingData))

// Evaluate
let evaluation = classifier.evaluation(on: testData)
print("Accuracy: \(evaluation.classificationError)")

// Export
try classifier.write(to: URL(fileURLWithPath: "/Models/MyClassifier.mlmodel"))
```

## When to Use

- Custom domain ML models (medical images, product defects)
- Avoiding cloud ML services
- Rapid prototyping of on-device AI features

-----

-----

# 15. Vision

## Overview

Vision is a high-level computer vision framework that works on top of CoreML and provides ready-made requests for common vision tasks.

## Use Case

- Face detection and landmark recognition
- Text recognition (OCR)
- Barcode / QR code scanning
- Body pose estimation
- Document detection
- Saliency detection

## Key Examples

### OCR (Text Recognition)

```swift
import Vision

func recognizeText(in image: UIImage) {
    let request = VNRecognizeTextRequest { request, _ in
        guard let observations = request.results as? [VNRecognizedTextObservation] else { return }
        let text = observations.compactMap { $0.topCandidates(1).first?.string }.joined(separator: "\n")
        print(text)
    }
    request.recognitionLevel = .accurate
    
    let handler = VNImageRequestHandler(cgImage: image.cgImage!)
    try? handler.perform([request])
}
```

### Face Detection

```swift
let request = VNDetectFaceRectanglesRequest { request, _ in
    guard let faces = request.results as? [VNFaceObservation] else { return }
    print("Found \(faces.count) faces")
}
```

### QR Code Scanning (with AVFoundation)

```swift
let request = VNDetectBarcodesRequest { request, _ in
    guard let barcodes = request.results as? [VNBarcodeObservation] else { return }
    for barcode in barcodes {
        print(barcode.payloadStringValue ?? "")
    }
}
```

### Body Pose

```swift
let request = VNDetectHumanBodyPoseRequest { request, _ in
    guard let observations = request.results as? [VNHumanBodyPoseObservation],
          let body = observations.first else { return }
    let wrist = try? body.recognizedPoint(.rightWrist)
    print(wrist?.location ?? .zero)
}
```

## When to Use

- Document scanning apps
- Fitness pose guidance
- Accessibility features (reading text aloud)
- Receipt / business card scanning

-----

-----

# 16. NaturalLanguage

## Overview

The NaturalLanguage framework provides linguistic analysis of text, including language detection, tokenization, part-of-speech tagging, named entity recognition, and sentiment analysis.

## Use Case

- Detecting the language of user input
- Tokenizing text into sentences/words
- Named entity recognition (people, places, organizations)
- Sentiment analysis
- Word embeddings

## Key APIs

```swift
import NaturalLanguage

// Language Detection
let recognizer = NLLanguageRecognizer()
recognizer.processString("Bonjour le monde")
let language = recognizer.dominantLanguage // .french

// Tokenization
let tokenizer = NLTokenizer(unit: .word)
tokenizer.string = "Hello, World! How are you?"
tokenizer.enumerateTokens(in: tokenizer.string!.startIndex...) { range, _ in
    print(String(tokenizer.string![range]))
    return true
}

// Named Entity Recognition
let tagger = NLTagger(tagSchemes: [.nameType])
tagger.string = "Tim Cook is the CEO of Apple in Cupertino."
tagger.enumerateTags(in: tagger.string!.startIndex..., unit: .word, scheme: .nameType) { tag, range in
    if let tag, [.personalName, .organizationName, .placeName].contains(tag) {
        print("\(tagger.string![range]): \(tag.rawValue)")
    }
    return true
}

// Sentiment
let sentimentPredictor = try NLModel(mlModel: NLModelConfiguration())
```

## When to Use

- Search and filtering with linguistic awareness
- Chat apps with language detection
- Content moderation
- Accessibility features

-----

-----

# 17. ARKit

## Overview

ARKit is Apple’s augmented reality platform that handles world tracking, plane detection, image anchoring, people occlusion, and LiDAR-based scene reconstruction.

## Use Case

- Placing virtual objects in the real world
- Face filters and face tracking
- Image and object recognition in AR
- Room-scale AR experiences
- Measuring real-world objects

## SwiftUI Integration (via RealityKit)

```swift
import ARKit
import RealityKit

struct ARViewContainer: UIViewRepresentable {
    func makeUIView(context: Context) -> ARView {
        let arView = ARView(frame: .zero)
        
        // Plane detection
        let config = ARWorldTrackingConfiguration()
        config.planeDetection = [.horizontal, .vertical]
        config.environmentTexturing = .automatic
        
        if ARWorldTrackingConfiguration.supportsSceneReconstruction(.mesh) {
            config.sceneReconstruction = .mesh
        }
        
        arView.session.run(config)
        
        // Tap to place object
        let tapGesture = UITapGestureRecognizer(target: context.coordinator,
                                                action: #selector(Coordinator.handleTap))
        arView.addGestureRecognizer(tapGesture)
        
        return arView
    }
    
    func updateUIView(_ uiView: ARView, context: Context) {}
    func makeCoordinator() -> Coordinator { Coordinator() }
    
    class Coordinator: NSObject {
        @objc func handleTap(_ recognizer: UITapGestureRecognizer) {
            guard let arView = recognizer.view as? ARView else { return }
            let location = recognizer.location(in: arView)
            
            if let result = arView.raycast(from: location, allowing: .estimatedPlane, alignment: .horizontal).first {
                let anchor = AnchorEntity(world: result.worldTransform)
                let box = ModelEntity(mesh: .generateBox(size: 0.1))
                anchor.addChild(box)
                arView.scene.addAnchor(anchor)
            }
        }
    }
}
```

## When to Use

- Furniture placement apps (IKEA-style)
- AR education and training
- Face filter apps
- Interior design visualization

-----

-----

# 18. RealityKit

## Overview

RealityKit is Apple’s high-performance 3D rendering engine designed specifically for AR and 3D content on Apple platforms. It’s the recommended rendering layer on top of ARKit, and the primary framework for visionOS.

## Use Case

- Rendering photorealistic 3D models in AR
- Physics simulations in AR
- Spatial computing on visionOS
- Animations and particle effects in 3D

## SwiftUI on visionOS

```swift
import RealityKit
import SwiftUI

// visionOS 3D content
struct My3DView: View {
    var body: some View {
        RealityView { content in
            // Load a USDZ model
            if let model = try? await ModelEntity(named: "toy_car") {
                model.generateCollisionShapes(recursive: true)
                content.add(model)
            }
        } update: { content in
            // Update animations
        }
        .gesture(TapGesture().targetedToAnyEntity().onEnded { value in
            value.entity.model?.materials = [SimpleMaterial(color: .red, isMetallic: true)]
        })
    }
}
```

### Physics

```swift
let box = ModelEntity(mesh: .generateBox(size: 0.1))
box.physicsBody = PhysicsBodyComponent(massProperties: .default, material: .default, mode: .dynamic)
box.collision = CollisionComponent(shapes: [.generateBox(size: [0.1, 0.1, 0.1])])
```

## When to Use

- visionOS spatial apps
- AR product visualization
- 3D interactive experiences
- Any ARKit app needing high-quality rendering

-----

-----

# 19. SceneKit

## Overview

SceneKit is Apple’s high-level 3D graphics framework for rendering complex 3D scenes without AR. It predates RealityKit and is suitable for games, 3D viewers, and non-AR 3D content.

## Use Case

- 3D model viewers
- Non-AR 3D animations
- Simple 3D games
- Data visualization in 3D

## SwiftUI Integration

```swift
import SceneKit

struct SceneKitView: UIViewRepresentable {
    func makeUIView(context: Context) -> SCNView {
        let scnView = SCNView()
        let scene = SCNScene()
        scnView.scene = scene
        scnView.allowsCameraControl = true
        scnView.autoenablesDefaultLighting = true
        scnView.backgroundColor = .black
        
        // Create a sphere
        let sphere = SCNSphere(radius: 1.0)
        sphere.firstMaterial?.diffuse.contents = UIColor.systemBlue
        let sphereNode = SCNNode(geometry: sphere)
        scene.rootNode.addChildNode(sphereNode)
        
        // Animate rotation
        let rotation = SCNAction.repeatForever(SCNAction.rotateBy(x: 0, y: 2 * .pi, z: 0, duration: 5))
        sphereNode.runAction(rotation)
        
        return scnView
    }
    
    func updateUIView(_ uiView: SCNView, context: Context) {}
}
```

## When to Use

- 3D model preview apps
- Non-AR games or simulations
- Educational 3D content (anatomy, astronomy)
- When you need more manual 3D control than RealityKit

-----

-----

# 20. SpriteKit

## Overview

SpriteKit is Apple’s 2D game framework providing a scene graph, physics engine, particle systems, and animation support — ideal for 2D games.

## Use Case

- 2D side-scrollers and arcade games
- Particle effects in 2D
- Animated game characters
- Physics-based puzzles

## SwiftUI Integration

```swift
import SpriteKit

class GameScene: SKScene {
    override func didMove(to view: SKView) {
        backgroundColor = .black
        physicsBody = SKPhysicsBody(edgeLoopFrom: frame)
        
        let ball = SKShapeNode(circleOfRadius: 20)
        ball.fillColor = .systemYellow
        ball.position = CGPoint(x: frame.midX, y: frame.midY)
        ball.physicsBody = SKPhysicsBody(circleOfRadius: 20)
        ball.physicsBody?.restitution = 0.8
        addChild(ball)
    }
    
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        guard let touch = touches.first else { return }
        let location = touch.location(in: self)
        let force = CGVector(dx: (location.x - frame.midX) * 0.5, dy: 300)
        children.first?.physicsBody?.applyImpulse(force)
    }
}

struct SpriteKitView: View {
    var scene: SKScene {
        let s = GameScene()
        s.size = CGSize(width: 400, height: 700)
        s.scaleMode = .fill
        return s
    }
    
    var body: some View {
        SpriteView(scene: scene)
            .ignoresSafeArea()
    }
}
```

## When to Use

- 2D games of any genre
- Animated particle effects alongside SwiftUI
- Card games, puzzle games, platformers

-----

-----

# 21. GameKit

## Overview

GameKit provides Game Center integration — leaderboards, achievements, multiplayer matchmaking, and real-time peer-to-peer gameplay.

## Use Case

- Game Center leaderboards
- Achievements and badges
- Real-time multiplayer (GKMatch)
- Turn-based multiplayer (GKTurnBasedMatch)
- Voice chat in multiplayer games

## Key APIs

```swift
import GameKit

// Authenticate
GKLocalPlayer.local.authenticateHandler = { viewController, error in
    if let vc = viewController {
        // Present the auth view controller
    }
    if GKLocalPlayer.local.isAuthenticated {
        print("Authenticated: \(GKLocalPlayer.local.displayName)")
    }
}

// Submit score
let score = GKLeaderboardScore()
GKLeaderboard.submitScore(12345, context: 0, player: GKLocalPlayer.local,
                           leaderboardIDs: ["com.myapp.highscore"]) { error in
    if let error { print(error) }
}

// Report achievement
let achievement = GKAchievement(identifier: "first_win")
achievement.percentComplete = 100
GKAchievement.report([achievement]) { error in }

// Show leaderboard UI
struct LeaderboardView: UIViewControllerRepresentable {
    func makeUIViewController(context: Context) -> GKGameCenterViewController {
        let vc = GKGameCenterViewController(state: .leaderboards)
        vc.gameCenterDelegate = context.coordinator
        return vc
    }
    func updateUIViewController(_ uiViewController: GKGameCenterViewController, context: Context) {}
    func makeCoordinator() -> Coordinator { Coordinator() }
    class Coordinator: NSObject, GKGameCenterControllerDelegate {
        func gameCenterViewControllerDidFinish(_ gameCenterViewController: GKGameCenterViewController) {
            gameCenterViewController.dismiss(animated: true)
        }
    }
}
```

## When to Use

- Any game wanting social features
- Competitive games with rankings
- Multiplayer games without a dedicated server

-----

-----

# 22. AVFoundation

## Overview

AVFoundation is Apple’s comprehensive audio and video framework, covering recording, playback, editing, and streaming of media.

## Use Case

- Playing audio and video
- Recording audio/video from camera/microphone
- Video editing and composition
- Speech synthesis (text-to-speech)
- Audio effects and mixing

## Key Examples

### Video Player

```swift
import AVKit

struct VideoPlayerView: View {
    let player = AVPlayer(url: URL(string: "https://example.com/video.mp4")!)
    
    var body: some View {
        VideoPlayer(player: player)
            .onAppear { player.play() }
            .onDisappear { player.pause() }
    }
}
```

### Audio Recording

```swift
import AVFoundation

class AudioRecorder: ObservableObject {
    var recorder: AVAudioRecorder?
    
    func startRecording() {
        let settings: [String: Any] = [
            AVFormatIDKey: Int(kAudioFormatMPEG4AAC),
            AVSampleRateKey: 44100,
            AVNumberOfChannelsKey: 1
        ]
        let url = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
            .appendingPathComponent("recording.m4a")
        recorder = try? AVAudioRecorder(url: url, settings: settings)
        recorder?.record()
    }
    
    func stopRecording() { recorder?.stop() }
}
```

### Text to Speech

```swift
import AVFoundation

let synth = AVSpeechSynthesizer()
let utterance = AVSpeechUtterance(string: "Hello from SwiftUI!")
utterance.rate = 0.5
utterance.voice = AVSpeechSynthesisVoice(language: "en-US")
synth.speak(utterance)
```

### Camera Capture

```swift
// Use UIViewControllerRepresentable to wrap AVCaptureSession
// or use PhotosUI / SwiftUI camera APIs (iOS 16+)
```

## When to Use

- Media playback apps
- Voice recording apps
- Video editing tools
- Podcast / audio apps

-----

-----

# 23. CoreImage

## Overview

CoreImage is a high-performance image processing framework with 200+ built-in filters and GPU-accelerated rendering.

## Use Case

- Applying photo filters
- Face detection
- Image blurring, sharpening, color correction
- QR code generation
- Chroma keying / green screen

## SwiftUI Integration

```swift
import CoreImage
import CoreImage.CIFilterBuiltins

func applySepiaTone(to image: UIImage) -> UIImage? {
    guard let ciImage = CIImage(image: image) else { return nil }
    
    let filter = CIFilter.sepiaTone()
    filter.inputImage = ciImage
    filter.intensity = 0.8
    
    let context = CIContext()
    guard let output = filter.outputImage,
          let cgImage = context.createCGImage(output, from: output.extent) else { return nil }
    
    return UIImage(cgImage: cgImage)
}

// QR Code Generation
func generateQRCode(from string: String) -> UIImage? {
    let filter = CIFilter.qrCodeGenerator()
    filter.message = Data(string.utf8)
    
    let context = CIContext()
    guard let output = filter.outputImage,
          let cgImage = context.createCGImage(output, from: output.extent) else { return nil }
    
    return UIImage(cgImage: cgImage)
}
```

## When to Use

- Photo editing apps
- Any image transformation pipeline
- QR code / barcode generation

-----

-----

# 24. PhotosUI

## Overview

PhotosUI provides SwiftUI-native access to the user’s photo library via `PhotosPicker` and the `Photos` framework.

## Use Case

- Selecting photos/videos from library
- Saving images to the camera roll
- Reading photo metadata
- Displaying photos efficiently

## SwiftUI API (iOS 16+)

```swift
import PhotosUI
import SwiftUI

struct PhotoPickerView: View {
    @State private var selectedItem: PhotosPickerItem?
    @State private var selectedImage: Image?
    
    var body: some View {
        VStack {
            selectedImage?
                .resizable()
                .scaledToFit()
                .frame(maxHeight: 300)
            
            PhotosPicker(selection: $selectedItem, matching: .images) {
                Label("Select Photo", systemImage: "photo")
            }
        }
        .onChange(of: selectedItem) {
            Task {
                if let data = try? await selectedItem?.loadTransferable(type: Data.self),
                   let uiImage = UIImage(data: data) {
                    selectedImage = Image(uiImage: uiImage)
                }
            }
        }
    }
}
```

### Multiple Selection

```swift
@State private var selectedItems: [PhotosPickerItem] = []

PhotosPicker(selection: $selectedItems, maxSelectionCount: 5, matching: .images) {
    Text("Select up to 5 photos")
}
```

### Save to Camera Roll

```swift
UIImageWriteToSavedPhotosAlbum(uiImage, nil, nil, nil)
// or
try await PHPhotoLibrary.shared().performChanges {
    PHAssetCreationRequest.forAsset().addResource(with: .photo, data: imageData, options: nil)
}
```

## When to Use

- Profile picture selection
- Photo editing apps
- Social media upload flows

-----

-----

# 25. StoreKit

## Overview

StoreKit enables in-app purchases and subscriptions, and integrates with the App Store for reviews and ratings.

## Use Case

- In-app purchases (consumable, non-consumable)
- Auto-renewable subscriptions
- Family sharing purchases
- App Store ratings prompt
- Promotional offers

## Modern StoreKit 2 (iOS 15+)

```swift
import StoreKit

// Fetch products
let products = try await Product.products(for: ["com.myapp.premium", "com.myapp.coins_100"])

// Purchase
let result = try await products.first!.purchase()
switch result {
case .success(let verification):
    switch verification {
    case .verified(let transaction):
        // Unlock feature
        await transaction.finish()
    case .unverified:
        // Handle unverified
        break
    }
case .userCancelled:
    break
case .pending:
    break
}

// Check subscription status
for await result in Transaction.currentEntitlements {
    if case .verified(let transaction) = result {
        print("Active: \(transaction.productID)")
    }
}
```

### Request App Store Review

```swift
import StoreKit

// iOS 16+
if let scene = UIApplication.shared.connectedScenes.first as? UIWindowScene {
    SKStoreReviewController.requestReview(in: scene)
}

// SwiftUI
@Environment(\.requestReview) var requestReview
Button("Rate App") { requestReview() }
```

## When to Use

- Any paid feature or subscription model
- Freemium apps
- Consumable virtual goods (coins, credits)

-----

-----

# 26. AuthenticationServices

## Overview

AuthenticationServices provides Sign in with Apple, OAuth web authentication sessions, and password credential management.

## Use Case

- Sign In with Apple button
- OAuth login flows (Google, GitHub, etc.) via `ASWebAuthenticationSession`
- AutoFill integration for passwords

## Sign In with Apple

```swift
import AuthenticationServices

struct SignInView: View {
    var body: some View {
        SignInWithAppleButton(.signIn) { request in
            request.requestedScopes = [.fullName, .email]
        } onCompletion: { result in
            switch result {
            case .success(let auth):
                if let credential = auth.credential as? ASAuthorizationAppleIDCredential {
                    let userID = credential.user
                    let email = credential.email
                    let name = credential.fullName?.givenName
                    print("Signed in: \(userID), \(email ?? ""), \(name ?? "")")
                }
            case .failure(let error):
                print(error)
            }
        }
        .signInWithAppleButtonStyle(.black)
        .frame(height: 50)
    }
}
```

### OAuth Web Session

```swift
let authURL = URL(string: "https://github.com/login/oauth/authorize?client_id=xxx")!
let session = ASWebAuthenticationSession(url: authURL, callbackURLScheme: "myapp") { callbackURL, error in
    guard let url = callbackURL else { return }
    let code = URLComponents(url: url, resolvingAgainstBaseURL: false)?
        .queryItems?.first(where: { $0.name == "code" })?.value
    print("OAuth code: \(code ?? "")")
}
session.presentationContextProvider = ...
session.start()
```

## When to Use

- Any app with user authentication
- Apps requiring Apple App Store compliance (must offer Sign in with Apple if offering other social logins)

-----

-----

# 27. LocalAuthentication

## Overview

LocalAuthentication enables biometric authentication (Face ID, Touch ID) and device passcode verification without requiring user credentials.

## Use Case

- Locking sensitive app sections behind Face ID
- Securing stored passwords or financial data
- Banking and password manager apps

## SwiftUI Integration

```swift
import LocalAuthentication

@Observable
class BiometricAuth {
    var isAuthenticated = false
    var error: String?
    
    func authenticate() {
        let context = LAContext()
        var error: NSError?
        
        guard context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) else {
            self.error = error?.localizedDescription
            return
        }
        
        context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics,
                               localizedReason: "Access your secure data") { success, authError in
            DispatchQueue.main.async {
                self.isAuthenticated = success
                self.error = authError?.localizedDescription
            }
        }
    }
}

struct SecureView: View {
    @State private var auth = BiometricAuth()
    
    var body: some View {
        Group {
            if auth.isAuthenticated {
                SecretContentView()
            } else {
                Button("Unlock with Face ID") {
                    auth.authenticate()
                }
            }
        }
    }
}
```

## When to Use

- Finance, banking, password manager apps
- Any screen containing sensitive user data
- Enterprise apps with compliance requirements

-----

-----

# 28. CryptoKit

## Overview

CryptoKit (iOS 13+) provides a Swift-native API for cryptographic operations — hashing, symmetric encryption, public-key cryptography, and digital signatures.

## Use Case

- Data hashing (SHA-256, SHA-512)
- Symmetric encryption (AES-GCM, ChaChaPoly)
- Key generation and key agreement (ECDH)
- Digital signatures (ECDSA, Ed25519)
- HMAC authentication

## Key Examples

```swift
import CryptoKit

// SHA-256 Hash
let data = Data("Hello, World!".utf8)
let hash = SHA256.hash(data: data)
let hexString = hash.compactMap { String(format: "%02x", $0) }.joined()
print(hexString)

// AES-GCM Symmetric Encryption
let key = SymmetricKey(size: .bits256)
let message = Data("Secret message".utf8)

let sealed = try AES.GCM.seal(message, using: key)
let decrypted = try AES.GCM.open(sealed, using: key)
print(String(data: decrypted, encoding: .utf8)!)

// HMAC
let authenticationCode = HMAC<SHA256>.authenticationCode(for: message, using: key)
let isValid = HMAC<SHA256>.isValidAuthenticationCode(authenticationCode, authenticating: message, using: key)

// Ed25519 Signing
let privateKey = Curve25519.Signing.PrivateKey()
let publicKey = privateKey.publicKey
let signature = try privateKey.signature(for: data)
let isVerified = publicKey.isValidSignature(signature, for: data)
```

## When to Use

- End-to-end encrypted messaging apps
- Secure local storage
- API request signing
- Data integrity verification

-----

-----

# 29. Network (Network.framework)

## Overview

Network.framework (iOS 12+) provides a modern, low-level networking API for TCP, UDP, WebSockets, and TLS connections — with better performance than BSD sockets and automatic VPN/proxy support.

## Use Case

- Custom TCP/UDP networking
- WebSocket connections
- Monitoring network connectivity
- Low-latency gaming networking

## Key Examples

### Network Path Monitoring

```swift
import Network

@Observable
class NetworkMonitor {
    private let monitor = NWPathMonitor()
    var isConnected = false
    var connectionType: NWInterface.InterfaceType?
    
    init() {
        monitor.pathUpdateHandler = { path in
            DispatchQueue.main.async {
                self.isConnected = path.status == .satisfied
                self.connectionType = path.availableInterfaces.first?.type
            }
        }
        monitor.start(queue: DispatchQueue.global())
    }
}

// SwiftUI
struct ContentView: View {
    @State private var network = NetworkMonitor()
    
    var body: some View {
        VStack {
            Image(systemName: network.isConnected ? "wifi" : "wifi.slash")
            Text(network.isConnected ? "Connected" : "Offline")
        }
    }
}
```

### WebSocket Connection

```swift
let connection = NWConnection(
    host: "ws.example.com",
    port: 443,
    using: .tls
)
connection.stateUpdateHandler = { state in
    switch state {
    case .ready: print("Connected")
    case .failed(let error): print("Error: \(error)")
    default: break
    }
}
connection.start(queue: .global())
```

## When to Use

- Real-time apps (chat, live updates)
- Network-aware UX (offline mode)
- Custom protocol implementations

-----

-----

# 30. WebKit

## Overview

WebKit provides `WKWebView`, a full-featured browser engine for loading web content, and enables bidirectional communication between native Swift and JavaScript.

## Use Case

- Displaying web content in-app
- Hybrid native/web apps
- In-app browsers
- Rendering HTML email content
- JavaScript ↔ Swift bridges

## SwiftUI Integration

```swift
import WebKit

struct WebView: UIViewRepresentable {
    let url: URL
    @Binding var isLoading: Bool
    
    func makeUIView(context: Context) -> WKWebView {
        let config = WKWebViewConfiguration()
        
        // JavaScript → Swift message handler
        config.userContentController.add(context.coordinator, name: "nativeBridge")
        
        let webView = WKWebView(frame: .zero, configuration: config)
        webView.navigationDelegate = context.coordinator
        return webView
    }
    
    func updateUIView(_ webView: WKWebView, context: Context) {
        webView.load(URLRequest(url: url))
    }
    
    func makeCoordinator() -> Coordinator { Coordinator(self) }
    
    class Coordinator: NSObject, WKNavigationDelegate, WKScriptMessageHandler {
        var parent: WebView
        init(_ parent: WebView) { self.parent = parent }
        
        func webView(_ webView: WKWebView, didStartProvisionalNavigation navigation: WKNavigation!) {
            parent.isLoading = true
        }
        
        func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
            parent.isLoading = false
            // Call JS from Swift
            webView.evaluateJavaScript("document.title") { result, _ in
                print("Page title: \(result ?? "")")
            }
        }
        
        // Receive message from JavaScript
        func userContentController(_ userContentController: WKUserContentController,
                                   didReceive message: WKScriptMessage) {
            print("From JS: \(message.body)")
        }
    }
}
```

## When to Use

- In-app web browsers
- Displaying rich HTML content
- Apps with web-based dashboards

-----

-----

# 31. HealthKit

## Overview

HealthKit provides read/write access to the user’s health data from the Health app — steps, heart rate, sleep, workouts, nutrition, and more.

## Use Case

- Fitness and wellness apps
- Reading step count and heart rate
- Logging workouts
- Sleep analysis
- Nutrition tracking

## SwiftUI Integration

```swift
import HealthKit

class HealthManager: ObservableObject {
    let store = HKHealthStore()
    @Published var stepCount: Double = 0
    @Published var heartRate: Double = 0
    
    func requestPermission() async {
        guard HKHealthStore.isHealthDataAvailable() else { return }
        
        let types: Set = [
            HKQuantityType(.stepCount),
            HKQuantityType(.heartRate)
        ]
        
        try? await store.requestAuthorization(toShare: [], read: types)
    }
    
    func fetchSteps() {
        let type = HKQuantityType(.stepCount)
        let startOfDay = Calendar.current.startOfDay(for: Date())
        let predicate = HKQuery.predicateForSamples(withStart: startOfDay, end: Date())
        
        let query = HKStatisticsQuery(quantityType: type, quantitySamplePredicate: predicate,
                                      options: .cumulativeSum) { _, result, _ in
            DispatchQueue.main.async {
                self.stepCount = result?.sumQuantity()?.doubleValue(for: .count()) ?? 0
            }
        }
        store.execute(query)
    }
}
```

## When to Use

- Health and fitness tracker apps
- Medical monitoring apps
- Workout companions

-----

-----

# 32. HomeKit

## Overview

HomeKit allows apps to control HomeKit-compatible smart home accessories — lights, locks, thermostats, cameras, and sensors.

## Use Case

- Smart home control apps
- Home automation scenes
- Security camera integration
- Energy monitoring

## Key APIs

```swift
import HomeKit

class HomeManager: NSObject, ObservableObject, HMHomeManagerDelegate {
    let manager = HMHomeManager()
    @Published var homes: [HMHome] = []
    
    override init() {
        super.init()
        manager.delegate = self
    }
    
    func homeManagerDidUpdateHomes(_ manager: HMHomeManager) {
        DispatchQueue.main.async {
            self.homes = manager.homes
        }
    }
    
    func toggleLight(accessory: HMAccessory) {
        guard let service = accessory.services.first(where: { $0.serviceType == HMServiceTypeLightbulb }),
              let characteristic = service.characteristics.first(where: { $0.characteristicType == HMCharacteristicTypePowerState })
        else { return }
        
        let currentValue = characteristic.value as? Bool ?? false
        characteristic.writeValue(!currentValue) { error in
            if let error { print("Error: \(error)") }
        }
    }
}
```

## When to Use

- Smart home companion apps
- Home automation / scene control
- Security and camera monitoring

-----

-----

# 33. WatchKit

## Overview

WatchKit is the framework for building Apple Watch apps. In modern watchOS, SwiftUI is the primary UI layer, with WatchKit providing system integration.

## Use Case

- Standalone Apple Watch apps
- Watch complications (clock face data)
- Workout sessions
- Connectivity with iPhone (WatchConnectivity)

## SwiftUI on watchOS

```swift
import WatchKit
import SwiftUI

@main
struct MyWatchApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        // Complications
        WKNotificationScene(controller: NotificationController.self, category: "reminder")
    }
}

struct ContentView: View {
    @State private var heartRate: Double = 0
    
    var body: some View {
        VStack {
            Text("\(Int(heartRate)) BPM")
                .font(.system(.title, design: .rounded))
            Image(systemName: "heart.fill")
                .foregroundStyle(.red)
        }
    }
}
```

### Watch Connectivity (iPhone ↔ Watch)

```swift
import WatchConnectivity

class WatchSessionManager: NSObject, WCSessionDelegate {
    static let shared = WatchSessionManager()
    
    override init() {
        super.init()
        if WCSession.isSupported() {
            WCSession.default.delegate = self
            WCSession.default.activate()
        }
    }
    
    func sendMessage(_ message: [String: Any]) {
        WCSession.default.sendMessage(message, replyHandler: nil)
    }
    
    func session(_ session: WCSession, didReceiveMessage message: [String: Any]) {
        print("Received: \(message)")
    }
    
    // Required delegate methods
    func session(_ session: WCSession, activationDidCompleteWith activationState: WCSessionActivationState, error: Error?) {}
    func sessionDidBecomeInactive(_ session: WCSession) {}
    func sessionDidDeactivate(_ session: WCSession) {}
}
```

## When to Use

- Fitness and health watch apps
- Quick-glance information apps
- Notification-heavy apps

-----

-----

# 34. WidgetKit

## Overview

WidgetKit enables creation of Home Screen, Lock Screen, and Standby widgets — small, glanceable views that display app content without opening the app.

## Use Case

- Home Screen widgets (small, medium, large, extra-large)
- Lock Screen widgets (iOS 16+)
- StandBy Mode widgets (iOS 17+)
- Interactive widgets (iOS 17+)

## Full Widget Example

```swift
import WidgetKit
import SwiftUI

// Timeline Entry
struct WeatherEntry: TimelineEntry {
    let date: Date
    let temperature: Int
    let condition: String
}

// Timeline Provider
struct WeatherProvider: TimelineProvider {
    func placeholder(in context: Context) -> WeatherEntry {
        WeatherEntry(date: Date(), temperature: 72, condition: "Sunny")
    }
    
    func getSnapshot(in context: Context, completion: @escaping (WeatherEntry) -> Void) {
        completion(WeatherEntry(date: Date(), temperature: 72, condition: "Sunny"))
    }
    
    func getTimeline(in context: Context, completion: @escaping (Timeline<WeatherEntry>) -> Void) {
        let entry = WeatherEntry(date: Date(), temperature: 72, condition: "Sunny")
        // Refresh every hour
        let nextUpdate = Calendar.current.date(byAdding: .hour, value: 1, to: Date())!
        completion(Timeline(entries: [entry], policy: .after(nextUpdate)))
    }
}

// Widget View
struct WeatherWidgetView: View {
    var entry: WeatherEntry
    @Environment(\.widgetFamily) var family
    
    var body: some View {
        switch family {
        case .systemSmall:
            VStack {
                Text("\(entry.temperature)°")
                    .font(.largeTitle.bold())
                Text(entry.condition)
            }
            .containerBackground(.blue.gradient, for: .widget)
        default:
            Text("Weather")
        }
    }
}

// Widget Configuration
@main
struct WeatherWidget: Widget {
    var body: some WidgetConfiguration {
        StaticConfiguration(kind: "WeatherWidget", provider: WeatherProvider()) { entry in
            WeatherWidgetView(entry: entry)
        }
        .configurationDisplayName("Weather")
        .description("Shows current weather.")
        .supportedFamilies([.systemSmall, .systemMedium, .accessoryRectangular])
    }
}
```

### Interactive Widget (iOS 17+)

```swift
struct ToggleWidgetView: View {
    var entry: SimpleEntry
    
    var body: some View {
        Toggle(isOn: entry.isEnabled) {
            Text("Enable")
        }
        .toggleStyle(.button)
        // Handled via AppIntent
    }
}
```

## When to Use

- Any app with time-sensitive or at-a-glance information
- Productivity apps (tasks, calendar)
- Fitness, weather, finance apps

-----

-----

# 35. ActivityKit (Live Activities)

## Overview

ActivityKit (iOS 16.1+) powers Live Activities — real-time, dynamic updates on the Lock Screen and Dynamic Island that show ongoing activity status without opening the app.

## Use Case

- Sports scores and live updates
- Food delivery order tracking
- Rideshare pickup status
- Flight tracking
- Workout progress

## Full Example

### Activity Attributes

```swift
import ActivityKit

struct DeliveryAttributes: ActivityAttributes {
    public typealias DeliveryStatus = ContentState
    
    public struct ContentState: Codable, Hashable {
        var driverName: String
        var estimatedArrival: Date
        var status: String
    }
    
    var orderNumber: String
    var restaurantName: String
}
```

### Starting a Live Activity

```swift
let attributes = DeliveryAttributes(orderNumber: "#1234", restaurantName: "Pizza Palace")
let state = DeliveryAttributes.ContentState(
    driverName: "Alex",
    estimatedArrival: Date().addingTimeInterval(20 * 60),
    status: "On the way"
)

do {
    let activity = try Activity.request(
        attributes: attributes,
        content: .init(state: state, staleDate: nil)
    )
    print("Activity ID: \(activity.id)")
} catch {
    print("Error: \(error)")
}
```

### Live Activity Widget View

```swift
struct DeliveryLiveActivityView: View {
    let context: ActivityViewContext<DeliveryAttributes>
    
    var body: some View {
        HStack {
            Image(systemName: "bicycle")
            VStack(alignment: .leading) {
                Text(context.state.driverName)
                    .font(.headline)
                Text("Arrives \(context.state.estimatedArrival, style: .relative)")
                    .font(.caption)
            }
            Spacer()
            Text(context.state.status)
                .font(.caption)
                .foregroundStyle(.secondary)
        }
        .padding()
        .activityBackgroundTint(.orange)
    }
}

// Dynamic Island
struct DeliveryDynamicIsland: Widget {
    var body: some WidgetConfiguration {
        ActivityConfiguration(for: DeliveryAttributes.self) { context in
            DeliveryLiveActivityView(context: context)
        } dynamicIsland: { context in
            DynamicIsland {
                DynamicIslandExpandedRegion(.leading) {
                    Image(systemName: "bicycle")
                }
                DynamicIslandExpandedRegion(.trailing) {
                    Text(context.state.estimatedArrival, style: .relative)
                }
            } compactLeading: {
                Image(systemName: "bicycle")
            } compactTrailing: {
                Text(context.state.estimatedArrival, style: .timer)
            } minimal: {
                Image(systemName: "bicycle")
            }
        }
    }
}
```

### Updating & Ending

```swift
// Update
let updatedState = DeliveryAttributes.ContentState(driverName: "Alex", estimatedArrival: Date().addingTimeInterval(5 * 60), status: "Almost there!")
await activity.update(.init(state: updatedState, staleDate: nil))

// End
await activity.end(.init(state: finalState, staleDate: nil), dismissalPolicy: .after(Date().addingTimeInterval(5)))
```

## When to Use

- Any real-time process with an end state (orders, rides, sports)
- Apps that users actively monitor on their Lock Screen
- Anything benefiting from Dynamic Island presence

-----

-----

## 🗂️ Quick Reference: Framework Selection Guide

|Need                        |Use                   |
|----------------------------|----------------------|
|Build any UI                |SwiftUI               |
|Wrap custom UIKit view      |`UIViewRepresentable` |
|Local data (iOS 17+)        |SwiftData             |
|Local data (iOS 13–16)      |Core Data             |
|Cloud sync                  |CloudKit              |
|Async networking            |Swift Concurrency     |
|Reactive pipelines          |Combine               |
|Maps                        |MapKit                |
|Location                    |CoreLocation          |
|Camera / video              |AVFoundation          |
|Photos library              |PhotosUI              |
|ML inference                |CoreML + Vision       |
|Train custom model          |CreateML              |
|Language analysis           |NaturalLanguage       |
|AR experiences              |ARKit + RealityKit    |
|3D graphics                 |SceneKit / RealityKit |
|2D games                    |SpriteKit             |
|Multiplayer games           |GameKit               |
|Biometrics                  |LocalAuthentication   |
|Sign In with Apple          |AuthenticationServices|
|In-app purchases            |StoreKit              |
|Encryption                  |CryptoKit             |
|Web content                 |WebKit                |
|Network status              |Network.framework     |
|Image filters               |CoreImage             |
|Motion / sensors            |CoreMotion            |
|Health data                 |HealthKit             |
|Smart home                  |HomeKit               |
|Apple Watch                 |WatchKit              |
|Home Screen widgets         |WidgetKit             |
|Lock Screen / Dynamic Island|ActivityKit           |

-----

*Generated for SwiftUI ecosystem — covering iOS, macOS, watchOS, tvOS, and visionOS platforms.*
*Last updated: June 2026*
