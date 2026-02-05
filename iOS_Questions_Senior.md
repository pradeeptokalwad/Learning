# iOS Interview Questions

## Swift Language & Fundamentals

### 1. Explain the difference between value types and reference types in Swift
Answer :- Value types (structs, enums, tuples) are copied when assigned or passed to functions. Each instance keeps a unique copy of its data. Reference types (classes, closures) are shared; multiple references can point to the same instance. Structs are preferred for immutable data models, while classes are used when you need inheritance, identity, or shared mutable state.

### 2. What are optionals and why are they important?
Answer :- Optionals represent a value that may or may not be present. They prevent null pointer exceptions by forcing developers to explicitly handle the absence of a value. You can unwrap optionals using if-let, guard-let, optional chaining (?.), nil coalescing (??), or force unwrapping (!). This makes Swift safer and more predictable.

### 3. Explain closures and capture lists
Answer :- Closures are self-contained blocks of functionality that can capture and store references to variables and constants from their surrounding context. Capture lists allow you to define how closures capture values—either strongly, weakly, or unowned—to prevent retain cycles. Example: `[weak self]` prevents memory leaks in asynchronous operations.

### 4. What is the difference between weak and unowned references?
Answer :- Both prevent retain cycles. `weak` is an optional that becomes nil when the referenced object is deallocated—use when the reference might outlive the object. `unowned` is non-optional and crashes if accessed after deallocation—use when you're certain the reference won't outlive the object. Weak is safer but requires optional handling.

### 5. Explain generics and their benefits
Answer :- Generics enable writing flexible, reusable functions and types that work with any type. They provide type safety without sacrificing flexibility. For example, Array<T>, Dictionary<K,V> are generic types. Benefits include code reusability, type safety at compile-time, and avoiding type casting.

### 6. What are associated types in protocols?
Answer :- Associated types are placeholders for types used within a protocol. They allow protocols to be generic without using generic syntax. When a type conforms to the protocol, it specifies the actual type for the associated type using a typealias or by inference.

### 7. Explain protocol-oriented programming vs object-oriented programming
Answer :- Protocol-oriented programming (POP) favors composition over inheritance. Protocols define blueprints of methods and properties. Types can conform to multiple protocols, avoiding the limitations of single inheritance. POP promotes code reuse through protocol extensions, default implementations, and composition, making code more flexible and testable.

### 8. What is the difference between map, flatMap, and compactMap?
Answer :- 
- `map`: Transforms each element using a closure
- `flatMap`: Flattens nested collections and transforms elements
- `compactMap`: Transforms and removes nil values from the result
Example: `[1,2,3].map { $0 * 2 }` returns `[2,4,6]`

### 9. Explain Result type introduced in Swift 5
Answer :- Result<Success, Failure> is an enum that represents either success or failure. It's used for error handling in asynchronous operations, providing a type-safe alternative to throwing functions. It forces explicit error handling and makes success/failure cases clear.

### 10. What are property wrappers?
Answer :- Property wrappers add a layer of separation between code that manages property storage and code that defines a property. Examples include @State, @Binding, @Published. They eliminate boilerplate code and encapsulate common patterns like validation, persistence, or thread-safety.

## Memory Management

### 11. Explain ARC (Automatic Reference Counting)
Answer :- ARC automatically manages memory by tracking and managing reference counts for class instances. When an instance's reference count reaches zero, ARC deallocates it. While automatic, developers must avoid retain cycles using weak/unowned references, especially with closures and delegate patterns.

### 12. What causes retain cycles and how do you prevent them?
Answer :- Retain cycles occur when two objects hold strong references to each other, preventing deallocation. Common scenarios include:
- Closures capturing self strongly
- Delegate patterns with strong references
- Parent-child relationships with strong references

Prevention: Use weak/unowned references, capture lists in closures `[weak self]`, and make delegates weak.

### 13. Explain the difference between strong, weak, and unowned
**Answer:**
- **Strong**: Default, keeps object alive, increases reference count
- **Weak**: Optional, doesn't increase reference count, becomes nil when object deallocates
- **Unowned**: Non-optional, doesn't increase reference count, crashes if accessed after deallocation

### 14. What is a memory leak and how do you detect it?
Answer :- A memory leak occurs when allocated memory is never deallocated, causing increasing memory usage. Detection methods:
- Instruments (Leaks, Allocations tools)
- Memory graph debugger in Xcode
- Deinit debugging (print statements)
- SwiftLint rules for retain cycles

### 15. Explain autorelease pools
Answer :- Autorelease pools defer releasing objects until the end of the current run loop iteration. They're useful when creating many temporary objects in loops to reduce memory spikes. In Swift, you use `autoreleasepool {}` blocks to manage temporary object lifecycle manually.

## UIKit & Interface Development

### 16. Explain the view lifecycle methods
**Answer:**
- `viewDidLoad`: View loaded into memory, setup that happens once
- `viewWillAppear`: Called before view appears, updates that happen each time
- `viewDidAppear`: View is on screen, start animations/tracking
- `viewWillDisappear`: About to leave screen, save state
- `viewDidDisappear`: No longer visible, stop processes
- `viewWillLayoutSubviews/viewDidLayoutSubviews`: Layout is about to/has occurred

### 17. What is the difference between frame and bounds?
**Answer:**
- **Frame**: Position and size relative to superview's coordinate system
- **Bounds**: Position and size in its own coordinate system (origin usually 0,0)

Changing bounds affects the internal coordinate system, useful for custom drawing and scrolling.

### 18. Explain Auto Layout and constraints
Answer :- Auto Layout is a constraint-based layout system that defines relationships between views. Constraints describe how views should be positioned and sized relative to each other or their superview. It enables adaptive layouts for different screen sizes and orientations. Priorities, compression resistance, and hugging priorities control constraint behavior.

### 19. What is the responder chain?
Answer :- The responder chain is the hierarchy of objects that can respond to and handle events. Events travel up the chain from the initial responder until an object handles it. Order: View → Superview → ... → View Controller → Window → Application → App Delegate. Used for touch events, motion events, and action messages.

### 20. Explain table view cell reuse and why it's important
Answer :- Cell reuse is a memory optimization where UITableView reuses cells that scroll off-screen for new content. Instead of creating thousands of cell objects, only enough cells for the visible area are created. Use `dequeueReusableCell(withIdentifier:)` and register cells. Always reset cell content in `cellForRowAt` to avoid showing stale data.

### 21. What's the difference between UITableView and UICollectionView?
Answer :- UITableView displays single-column scrolling lists. UICollectionView is more flexible, supporting custom layouts, multiple columns, grids, and complex arrangements. CollectionView has a layout object (UICollectionViewLayout) that controls positioning. Use TableView for simple lists, CollectionView for complex layouts.

### 22. Explain CALayer and its relationship with UIView
Answer :- Every UIView has a CALayer that handles rendering. CALayer provides lower-level drawing capabilities: transformations, animations, borders, shadows, corner radius. UIView provides touch handling and high-level interface. Modify layer properties for visual effects without subclassing views.

### 23. What are the different types of animations in iOS?
**Answer:**
- **UIView animations**: Block-based, spring animations, transitions
- **Core Animation**: Layer-based, keyframe animations, CABasicAnimation
- **UIViewPropertyAnimator**: Interruptible, reversible, modern API
- **SwiftUI animations**: Declarative, implicit and explicit animations

### 24. Explain the delegate pattern
Answer :- Delegate pattern enables one-to-one communication where an object delegates responsibilities to another. The delegating object holds a weak reference to its delegate and calls methods on it. Common in UIKit (UITableViewDelegate, UITextFieldDelegate). Promotes loose coupling and reusability.

### 25. What is the target-action pattern?
Answer :- Target-action connects UI controls to methods. Control sends an action message to a target object when an event occurs (e.g., button tap). Uses Objective-C runtime for dynamic message dispatch. In Swift, can use closures as an alternative for simpler cases.

## SwiftUI

### 26. Explain the difference between @State, @Binding, and @ObservedObject
**Answer:**
- **@State**: Private, owned by view, triggers re-render on change, value type storage
- **@Binding**: Creates two-way connection to @State in another view, doesn't own data
- **@ObservedObject**: Reference to external ObservableObject, doesn't own lifecycle
- **@StateObject**: Like @ObservedObject but owns the object's lifecycle

### 27. What is the SwiftUI view lifecycle?
Answer :- SwiftUI views are value types that describe UI. Views are recreated frequently as state changes. Lifecycle events:
- `onAppear`: View appears on screen
- `onDisappear`: View leaves screen
- `task`: Async work tied to view lifetime
Body is called on every state change to rebuild the view tree.

### 28. Explain @ViewBuilder and function builders
Answer :- @ViewBuilder is a result builder that allows writing multiple views in a declarative way without explicit return statements or array syntax. It transforms the code block into a single view. Used in custom view containers and modifiers.

### 29. How does SwiftUI handle state management?
Answer :- SwiftUI uses property wrappers for state:
- Local state: @State, @StateObject
- External state: @ObservedObject, @EnvironmentObject
- Derived state: @Binding
- Published changes via ObservableObject and @Published
- Data flows down, events flow up

### 30. What is the difference between NavigationView and NavigationStack?
Answer :- NavigationView is the older API (deprecated in iOS 16). NavigationStack is the modern replacement with better programmatic navigation, type-safe navigation paths, and value-based navigation. It supports navigation state management and deep linking more elegantly.

## Architecture Patterns

### 31. Explain MVC, MVVM, and VIPER architectures
**Answer:**
- **MVC**: Model-View-Controller. Views send events to Controller, Controller updates Model, Model notifies View. Simple but Controllers can become massive.
- **MVVM**: Model-View-ViewModel. ViewModel holds presentation logic and exposes data to View via bindings. Better testability.
- **VIPER**: View-Interactor-Presenter-Entity-Router. Highly modular, each component has single responsibility. Complex but very testable.

### 32. What is Coordinator pattern and when would you use it?
Answer :- Coordinator pattern separates navigation logic from view controllers. A coordinator controls the flow between screens, creating and presenting view controllers. Benefits include:
- Reusable view controllers
- Easier to change navigation flow
- Testable navigation logic
- Deep linking support

### 33. Explain dependency injection and its benefits
Answer :- Dependency injection provides dependencies to objects rather than having them create dependencies internally. Benefits:
- Easier testing (mock dependencies)
- Loose coupling
- Better modularity
- Clearer dependencies

Methods: constructor injection, property injection, method injection.

### 34. What is the Repository pattern?
Answer :- Repository pattern abstracts data access logic, providing a collection-like interface for accessing domain objects. It decouples business logic from data sources (API, database, cache). Enables easy switching between data sources and centralized data access logic.

### 35. Explain the difference between Singleton and shared instance
Answer :- Singleton ensures only one instance exists globally with a private initializer. Shared instance is a conventionally named property but allows creating additional instances. Singletons are harder to test and can create tight coupling. Prefer dependency injection over singletons when possible.

## Networking & APIs

### 36. Explain URLSession and its different modes
Answer :- URLSession is the API for network requests. Modes:
- **Default**: Standard requests with caching
- **Ephemeral**: No persistent storage, no cache
- **Background**: Continues downloads/uploads when app is suspended

Tasks: Data tasks, Download tasks, Upload tasks, WebSocket tasks.

### 37. How do you handle authentication in iOS apps?
Answer :- Common approaches:
- OAuth 2.0 with refresh tokens
- JWT tokens stored in Keychain
- Certificate pinning for security
- URLSession authentication challenges
- Use URLProtocol for request interception
Store sensitive data in Keychain, never UserDefaults.

### 38. Explain Codable protocol
Answer :- Codable (Encodable & Decodable) enables automatic serialization/deserialization of Swift types to/from external representations like JSON. Compiler generates encoding/decoding code when all properties are Codable. Can customize with CodingKeys enum or custom encode/decode methods.

### 39. How would you implement retry logic for network requests?
Answer :- Strategies:
- Exponential backoff (increasing delays)
- Max retry count
- Retry only on specific errors (network timeout, server errors)
- Use Combine operators like `retry()` or manual recursion
- Consider network reachability before retrying

### 40. What is certificate pinning and why use it?
Answer :- Certificate pinning validates the server's SSL certificate against a known copy in the app, preventing man-in-the-middle attacks even with compromised Certificate Authorities. Implement using URLSession's authentication challenge handling or third-party libraries. Trade-off: requires app updates if certificates change.

## Concurrency & Multithreading

### 41. Explain GCD (Grand Central Dispatch)
Answer :- GCD is a low-level API for managing concurrent operations using dispatch queues. Types:
- **Serial queues**: Execute one task at a time in order
- **Concurrent queues**: Execute multiple tasks simultaneously
- **Main queue**: Serial queue for UI updates
- **Global queues**: System-provided concurrent queues with QoS levels

### 42. What are Quality of Service (QoS) levels?
Answer :- QoS levels prioritize work:
- **User-interactive**: UI updates, highest priority
- **User-initiated**: User-requested task, near immediate
- **Utility**: Long-running user-initiated, progress shown
- **Background**: Maintenance, not user-visible, lowest priority

### 43. Explain async/await in Swift
Answer :- Async/await is structured concurrency introduced in Swift 5.5. `async` marks functions that can suspend, `await` marks suspension points. Benefits:
- Reads like synchronous code
- Compiler enforces error handling
- Automatic context switching
- Avoids callback hell

### 44. What are actors in Swift?
Answer :- Actors are reference types that protect their mutable state from data races. Only one task can access actor's mutable state at a time. Methods are async by default. The compiler enforces isolation, preventing concurrent access issues. Main actor runs on main thread for UI updates.

### 45. Explain the difference between DispatchQueue and OperationQueue
**Answer:**
- **DispatchQueue**: Lightweight, C-based, simple closures, FIFO
- **OperationQueue**: Built on GCD, supports dependencies, cancellation, priorities, max concurrent operations, KVO-compliant

Use DispatchQueue for simple async tasks, OperationQueue for complex task graphs and cancellation.

### 46. What is a race condition and how do you prevent it?
Answer :- Race condition occurs when multiple threads access shared mutable state simultaneously, causing unpredictable results. Prevention:
- Serial queues
- Locks (NSLock, pthread_mutex)
- Actors (Swift concurrency)
- Atomic properties
- Avoid shared mutable state

### 47. Explain Thread Sanitizer and how it helps
Answer :- Thread Sanitizer (TSan) is a runtime tool that detects data races in multithreaded code. It monitors memory accesses and reports concurrent access to the same memory without synchronization. Enable in scheme settings. Helps find concurrency bugs before production.

## Data Persistence

### 48. Compare different data persistence options in iOS
**Answer:**
- **UserDefaults**: Small amounts of data, preferences
- **Keychain**: Secure storage for passwords, tokens
- **File System**: Documents, caches, temp files
- **Core Data**: Complex object graphs, relationships
- **SQLite**: Direct SQL access, flexibility
- **Realm**: Alternative to Core Data, simpler API
- **CloudKit**: iCloud sync

### 49. Explain Core Data stack components
**Answer:**
- **NSManagedObjectModel**: Schema definition
- **NSPersistentStoreCoordinator**: Manages persistent stores
- **NSManagedObjectContext**: Workspace for objects, tracks changes
- **NSPersistentContainer**: Simplifies stack setup (iOS 10+)

### 50. What are Core Data concurrency models?
Answer :- 
- **Main queue concurrency**: UI operations
- **Private queue concurrency**: Background operations
- Use child contexts for nested changes
- Always access contexts on their queue
- Use `performBlock` or `performBlockAndWait`

### 51. How do you handle Core Data migrations?
Answer :- Types:
- **Lightweight migration**: Automatic for simple changes (add/remove attributes)
- **Manual migration**: Custom NSEntityMigrationPolicy for complex changes
- **Progressive migration**: Step through multiple model versions

Use versioned data models (.xcdatamodeld) and mapping models.

### 52. What is the Keychain and when should you use it?
Answer :- Keychain is an encrypted database for storing sensitive data (passwords, tokens, certificates). Data persists across app reinstalls and can be shared between apps with the same team ID. Use for any security-critical data. Access via Security framework or wrapper libraries.

## Testing

### 53. Explain the testing pyramid in iOS
**Answer:**
- **Unit tests**: Test individual components in isolation (most tests)
- **Integration tests**: Test component interactions
- **UI tests**: Test user flows and interface (fewest tests)

More unit tests because they're faster, cheaper, and easier to maintain.

### 54. What is the difference between XCTest and XCUITest?
**Answer:**
- **XCTest**: Unit and integration testing framework, tests code logic directly
- **XCUITest**: UI testing framework, interacts with app as a user would, runs in separate process

### 55. How do you mock dependencies in Swift?
Answer :- Techniques:
- **Protocols**: Define protocol, create mock conformance
- **Dependency injection**: Pass mocks to initializers
- **Method swizzling**: Replace method implementations (Objective-C runtime)
- **Third-party frameworks**: Mockingbird, Cuckoo

### 56. Explain TDD (Test-Driven Development) workflow
**Answer:**
1. Write failing test
2. Write minimal code to pass test
3. Refactor
4. Repeat

Benefits: Better design, living documentation, regression safety, confidence in refactoring.

### 57. What is code coverage and what's a good target?
Answer :- Code coverage measures percentage of code executed by tests. 70-80% is often a good target. 100% isn't always practical or valuable. Focus on critical paths, edge cases, and business logic. Exclude trivial code (getters/setters, UI code difficult to test).

### 58. How do you test asynchronous code?
Answer :- Methods:
- **XCTestExpectation**: Wait for async operations
- **Swift Concurrency**: Test async/await functions directly
- **Combine testing**: Use test schedulers
- Mock time-based operations
- Avoid arbitrary wait times

## Performance Optimization

### 59. How do you identify performance bottlenecks?
Answer :- Tools:
- **Instruments**: Time Profiler, Allocations, Leaks
- **Xcode View Debugger**: View hierarchy issues
- **Metal System Trace**: GPU performance
- **Network Link Conditioner**: Test slow networks
- **Console logs and signposts**: Custom tracking

### 60. Explain app launch time optimization strategies
**Answer:**
- Minimize work in `didFinishLaunching`
- Defer non-critical initializations
- Reduce framework loading (merge/remove)
- Avoid disk I/O on main thread
- Use lazy initialization
- Optimize images and assets
- Measure with DYLD_PRINT_STATISTICS

### 61. How do you optimize scroll performance?
**Answer:**
- Cell reuse properly
- Async image loading
- Avoid complex layouts in cells
- Cache calculated heights
- Prefetch data
- Render shadows/corners efficiently
- Use instruments to find hitches

### 62. What is image rendering best practices?
**Answer:**
- Use correct image sizes (avoid scaling)
- Async decoding with `preparingThumbnail(ofSize:)`
- Format: HEIC for photos, vector for icons
- Asset catalogs with slicing
- Lazy loading
- Cache decoded images
- Use image downsampling for large images

### 63. Explain battery life optimization techniques
**Answer:**
- Batch network requests
- Use background modes sparingly
- Reduce location accuracy when possible
- Defer heavy tasks until charging
- Optimize animations (60 FPS not always needed)
- Monitor Energy Log in Instruments
- Avoid polling, use notifications

### 64. How do you reduce app size?
**Answer:**
- App thinning (automatic slicing)
- On-demand resources
- Remove unused code/assets
- Use vector images
- Compress images
- Limit embedded frameworks
- Use bitcode (for older apps)
- Analyze with App Thinning Size Report

## Security

### 65. What are iOS security best practices?
**Answer:**
- Use HTTPS only
- Certificate pinning for critical APIs
- Keychain for sensitive data
- Encrypt local databases
- Validate all inputs
- Don't log sensitive information
- Code obfuscation for sensitive logic
- Jailbreak detection (if needed)
- Use biometric authentication

### 66. How do you implement biometric authentication?
Answer :- Use LocalAuthentication framework:
```swift
LAContext().evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics)
```
Handle fallback to passcode, errors (not available, failed, locked out). Store result but not biometric data itself. Use for app unlock or sensitive operations.

### 67. Explain App Transport Security (ATS)
Answer :- ATS enforces secure connections (HTTPS with strong encryption). Blocks HTTP by default. Can whitelist domains in Info.plist but discouraged. Requirements: TLS 1.2+, forward secrecy, SHA-256 certificates. Protects user data in transit.

### 68. How do you prevent reverse engineering?
Answer :- Techniques:
- Code obfuscation
- String encryption
- Jailbreak detection
- Prevent debugging (ptrace)
- Checksum validation
- Runtime tampering detection
- Use backend for critical logic

Note: Determined attackers can bypass most protections.

### 69. What is the secure enclave?
Answer :- Secure enclave is a hardware-based security coprocessor isolated from the main processor. Stores encryption keys, biometric data, and performs cryptographic operations. Used by Keychain, Touch ID, Face ID. Data never leaves the enclave in unencrypted form.

## App Architecture & Design

### 70. How do you design for scalability?
**Answer:**
- Modular architecture (feature modules)
- Clear separation of concerns
- Protocol-oriented design
- Repository pattern for data
- Dependency injection
- Reactive programming (Combine/RxSwift)
- Microfeatures architecture
- CI/CD pipeline

### 71. Explain deep linking implementation
Answer :- Types:
- **Universal Links**: Associated domains, HTTPS URLs, fallback to web
- **Custom URL schemes**: Registered schemes, less secure
- Handle in SceneDelegate/AppDelegate
- Parse URL components
- Navigate to appropriate screen
- Store state if app was terminated

### 72. How do you implement feature flags?
Answer :- Feature flags enable/disable features remotely. Implementation:
- Remote config (Firebase, custom backend)
- Local fallbacks
- A/B testing integration
- Environment-specific flags
- Kill switches for emergencies
- Gradual rollouts

Benefits: Risk mitigation, experimentation, gradual releases.

### 73. What is modular architecture?
Answer :- Breaking app into independent modules/frameworks. Benefits:
- Parallel development
- Reusability
- Faster compilation
- Clear boundaries
- Easier testing
- Dynamic frameworks or SPM packages

### 74. How do you handle app configuration (dev/staging/prod)?
Answer :- Methods:
- Build configurations
- Schemes
- .xcconfig files
- Preprocessor flags
- Environment-specific Info.plist
- Runtime configuration from backend
- Never commit sensitive keys

## iOS Ecosystem & Latest Features

### 75. Explain App Clips
Answer :- App Clips are lightweight versions of your app (< 10 MB) that users can instantly launch without full installation. Used for quick experiences via NFC, QR codes, or Safari. Share code with main app via shared frameworks. Expire after period of non-use.

### 76. What are Widgets and how do they work?
Answer :- Widgets are home screen glanceable content using WidgetKit framework. Built with SwiftUI. Timeline-based updates (not continuous). Three sizes. Use TimelineProvider for content. Extensions run separately from main app. Limited user interaction (deep links only).

### 77. Explain the App Store Small Business Program
Answer :- Reduced 15% commission (vs 30%) for developers earning less than $1M per year. Encourages small business growth. Apply through App Store Connect. Reverts to 30% if exceed threshold.

### 78. What is StoreKit 2?
Answer :- Modern Swift-native API for in-app purchases released with iOS 15. Features:
- async/await support
- Automatic transaction handling
- Better testing
- Transaction verification
- Simplified API

### 79. Explain Sign in with Apple
Answer :- Privacy-focused authentication required for apps with third-party login. Features:
- Two-factor authentication
- Email relay to hide real email
- Authorization controller API
- Keychain integration
- Easy setup with ASAuthorizationController

### 80. What are App Intents?
Answer :- App Intents (iOS 16+) expose app functionality to Siri, Shortcuts, Spotlight. Protocol-based definition of actions. More powerful than SiriKit. Includes parameters, result types, suggested phrases. Built with Swift.

## System Design Questions

### 81. Design a news feed app like Twitter
Answer :- Components:
- **API**: REST/GraphQL for posts, pagination
- **Caching**: Local database for offline, reduce API calls
- **Images**: Async loading, caching (SDWebImage/Kingfisher)
- **Feed updates**: Pull-to-refresh, pagination, prefetching
- **Posting**: Optimistic UI, background upload
- **Architecture**: MVVM-C, repository pattern
- **Real-time**: WebSockets or polling for new posts

### 82. Design an image caching system
**Answer:**
- **Memory cache**: LRU cache for fast access
- **Disk cache**: Persistent storage
- **Download manager**: Concurrent downloads, priority queue
- **Image processing**: Resize, format conversion
- **Cache policies**: Size limits, TTL
- **Prefetching**: Predict needed images
- **Cancellation**: Cancel requests for off-screen images

### 83. Design offline-first architecture
**Answer:**
- **Local database**: Core Data/Realm as source of truth
- **Sync engine**: Queue operations when offline
- **Conflict resolution**: Last-write-wins or custom logic
- **Change tracking**: Detect local changes
- **Network monitoring**: Queue/retry when online
- **Delta sync**: Sync only changes
- **UI feedback**: Show sync status

### 84. How would you implement real-time chat?
**Answer:**
- **Transport**: WebSocket for bidirectional communication
- **Protocol**: Custom or XMPP
- **Message queue**: Handle offline messages
- **Database**: Store chat history
- **Read receipts**: Track delivery/read status
- **Typing indicators**: Ephemeral state
- **Push notifications**: When app backgrounded
- **Encryption**: End-to-end (Signal Protocol)

### 85. Design a video streaming app
**Answer:**
- **Player**: AVPlayer with custom controls
- **Adaptive bitrate**: HLS for quality adjustment
- **Caching**: Progressive download or pre-cache
- **Background audio**: Continue in background
- **Picture-in-Picture**: Multitasking support
- **Analytics**: Track playback, errors, quality
- **DRM**: FairPlay for protected content
- **Offline viewing**: Download management

## Behavioral & Leadership

### 86. How do you mentor junior developers?
**Answer:**
- Code reviews with constructive feedback
- Pair programming sessions
- Share resources and best practices
- Set clear goals and expectations
- Regular 1-on-1s
- Encourage questions
- Delegate appropriately challenging tasks
- Celebrate growth and achievements

### 87. How do you handle technical disagreements?
**Answer:**
- Listen to all perspectives
- Focus on data and facts
- Consider trade-offs objectively
- Prototype if needed
- Align on goals first
- Be willing to compromise
- Document decisions
- Accept team consensus

### 88. Describe your approach to code reviews
**Answer:**
- Review promptly
- Be constructive and respectful
- Focus on code, not person
- Explain reasoning
- Suggest alternatives
- Catch bugs, ensure tests
- Check consistency with standards
- Approve if minor issues can be addressed later

### 89. How do you stay updated with iOS development?
**Answer:**
- WWDC videos and sessions
- Apple documentation
- Tech blogs (Swift.org, objc.io, NSHipster)
- Twitter iOS community
- Open source projects
- Conferences and meetups
- Experimenting with new features
- Reading release notes

### 90. How do you prioritize technical debt?
**Answer:**
- Balance with feature development
- Track in backlog with clear impact
- Address high-impact, low-effort first
- Schedule dedicated time
- Prevent new debt through standards
- Refactor incrementally
- Business case for stakeholders
- Don't let it block progress

## Advanced Topics

### 91. Explain Metal and when to use it
Answer :- Metal is Apple's low-level GPU API for rendering and compute. Use cases:
- High-performance graphics
- Machine learning inference
- Image processing
- AR applications
- Need direct GPU control

More complex than Core Graphics but much faster for compute-intensive tasks.

### 92. What is Core ML and how does it work?
Answer :- Core ML is Apple's machine learning framework. Converts trained models (TensorFlow, PyTorch) to Core ML format. Runs on-device with hardware acceleration (GPU, Neural Engine). Privacy-friendly. Use cases: image recognition, natural language processing, predictions.

### 93. Explain ARKit capabilities
Answer :- ARKit enables augmented reality. Features:
- World tracking (6DOF)
- Scene understanding
- Face tracking
- Body tracking
- Image/object detection
- Light estimation
- Occlusion
- Collaboration with RealityKit for rendering

### 94. What is Combine framework?
Answer :- Combine is Apple's reactive programming framework. Provides Publishers (emit values over time) and Subscribers (receive values). Supports:
- Async event handling
- Declarative operator chains
- Memory management
- Backpressure
- Integration with SwiftUI

### 95. How does diffable data sources work?
Answer :- UITableViewDiffableDataSource and UICollectionViewDiffableDataSource manage table/collection data using snapshots. Benefits:
- Automatic animations
- No index path errors
- Thread-safe updates
- Declarative API
- Supports sections and items

### 96. Explain Background Modes
Answer :- Background modes allow apps to run when not in foreground:
- Audio playback
- Location updates
- VoIP
- Background fetch
- Remote notifications
- Background processing
- External accessory

Each has specific use cases and battery impact.

### 97. What is Scene Delegate and how is it different from App Delegate?
Answer :- Scene Delegate (iOS 13+) manages app UI lifecycle for multi-window support. App Delegate handles app lifecycle and setup. One App Delegate, multiple Scene Delegates for multiple windows. Scene delegate methods: scene(willConnectTo:), sceneDidBecomeActive, etc.

### 98. Explain CloudKit architecture
Answer :- CloudKit provides cloud storage with iCloud integration. Components:
- **Containers**: Isolated environments
- **Databases**: Public, private, shared
- **Records**: Basic data unit (like rows)
- **Zones**: Organize records, enable atomic commits
- **Subscriptions**: Push notifications for changes

Use for iCloud sync, not a replacement for backend.

### 99. What are Swift Package Manager and its advantages?
Answer :- SPM is Apple's dependency manager built into Xcode. Advantages:
- Native Xcode integration
- Cross-platform (macOS, Linux)
- Git-based
- No separate tools needed
- Binary dependencies support
- Version resolution
- Growing ecosystem

### 100. How do you approach app accessibility?
**Answer:**
- VoiceOver support (labels, hints, traits)
- Dynamic Type for text sizing
- Sufficient color contrast
- Test with accessibility inspector
- Keyboard navigation on iPad
- Reduce motion support
- Closed captions for video
- Semantic UI structure

---

## Additional Tips for Senior Interview

### Technical Leadership
- Be ready to discuss architecture decisions you've made
- Explain trade-offs and why you chose specific approaches
- Share examples of mentoring and code reviews
- Discuss how you've improved team processes

### System Design
- Think out loud during design questions
- Ask clarifying questions
- Consider scalability, performance, and edge cases
- Discuss trade-offs between different approaches

### Problem Solving
- Explain your thought process
- Start with simple solutions, then optimize
- Consider edge cases and error handling
- Write clean, readable code in coding exercises

### Communication
- Be concise but thorough
- Use examples from your experience
- Ask questions when unclear
- Admit when you don't know something

Good luck with your iOS interview preparation!
