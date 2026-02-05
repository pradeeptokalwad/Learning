# SOLID Principles for iOS Developers

## Introduction

SOLID principles are five design rules that help you write better, cleaner, and more maintainable code. They're especially important in iOS development because apps grow complex over time. Following SOLID makes your code easier to understand, test, and change without breaking things.

Think of them as best practices that prevent common mistakes and make your codebase sustainable as your team and app grow.

---

## 1. Single Responsibility Principle (SRP)
       Each class should do only one thing and do it well. If a class has too many jobs, it becomes hard to understand and maintain.

### Why it matters
When you need to change how one feature works, you only touch one class. You won't accidentally break other features that rely on the same class.

### Bad Example - Violating SRP
```swift
// This view controller does too many things!
class UserViewController: UIViewController {
    func fetchUserFromAPI() { 
        // Network call logic
        let url = URL(string: "https://api.example.com/user")!
        URLSession.shared.dataTask(with: url) { data, _, _ in
            // Parse and handle response
        }.resume()
    }
    
    func saveUserToDatabase() { 
        // Database work
        let context = persistentContainer.viewContext
        // Save logic
    }
    
    func updateUI() { 
        // Display logic
        nameLabel.text = user.name
        emailLabel.text = user.email
    }
    
    func validateEmail(_ email: String) -> Bool { 
        // Validation logic
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        return NSPredicate(format: "SELF MATCHES %@", emailRegex).evaluate(with: email)
    }
    
    func exportUserToCSV() {
        // Export logic
    }
}
```

**Problems:**
- Too many responsibilities in one class
- Hard to test individual features
- Changes to networking affect UI code
- Violates separation of concerns

### Good Example - Following SRP
```swift
// Each class has one clear responsibility

// ONLY handles UI
class UserViewController: UIViewController {
    private let networkService: NetworkService
    private let databaseService: DatabaseService
    private let emailValidator: EmailValidator
    
    init(networkService: NetworkService, 
         databaseService: DatabaseService,
         emailValidator: EmailValidator) {
        self.networkService = networkService
        self.databaseService = databaseService
        self.emailValidator = emailValidator
        super.init(nibName: nil, bundle: nil)
    }
    
    func loadUser() {
        networkService.fetchUser { [weak self] user in
            self?.updateUI(with: user)
        }
    }
    
    private func updateUI(with user: User) {
        nameLabel.text = user.name
        emailLabel.text = user.email
    }
}

// ONLY handles networking
class NetworkService {
    func fetchUser(completion: @escaping (User) -> Void) {
        let url = URL(string: "https://api.example.com/user")!
        URLSession.shared.dataTask(with: url) { data, _, _ in
            // Parse and return user
            if let data = data {
                let user = try? JSONDecoder().decode(User.self, from: data)
                completion(user ?? User())
            }
        }.resume()
    }
}

// ONLY handles database
class DatabaseService {
    func saveUser(_ user: User) {
        let context = persistentContainer.viewContext
        // Save logic
    }
    
    func fetchUser() -> User? {
        // Fetch logic
        return nil
    }
}

// ONLY validates emails
class EmailValidator {
    func validate(_ email: String) -> Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        return NSPredicate(format: "SELF MATCHES %@", emailRegex).evaluate(with: email)
    }
}

// ONLY handles export
class UserExporter {
    func exportToCSV(_ user: User) -> String {
        return "\(user.name),\(user.email)"
    }
}
```

**Benefits:**
- Each class has one reason to change
- Easy to test in isolation
- Easy to understand what each class does
- Can reuse components in other places

### Real-World iOS Examples
- **UIViewController**: Only handles UI, not networking or business logic
- **URLSession**: Only handles network requests
- **Core Data Context**: Only handles database operations
- **NotificationCenter**: Only handles notifications

---

## 2. Open/Closed Principle (OCP)
       Your code should be open for extension but closed for modification. This means you can add new features without changing existing code.

### Why it matters
Adding new features doesn't risk breaking existing, working code. You extend behavior instead of modifying it.

### Bad Example - Violating OCP
```swift
// Every time you add a new payment method, you modify this class
class PaymentProcessor {
    func process(type: String, amount: Double) {
        if type == "CreditCard" {
            print("Processing credit card payment of \(amount)")
            // Credit card logic
            connectToCreditCardGateway()
            validateCardNumber()
            chargeCard()
        } else if type == "PayPal" {
            print("Processing PayPal payment of \(amount)")
            // PayPal logic
            connectToPayPal()
            authenticateUser()
            transferFunds()
        } else if type == "ApplePay" {
            print("Processing Apple Pay payment of \(amount)")
            // Apple Pay logic
            requestApplePayAuthentication()
            processApplePayment()
        } else if type == "GooglePay" {
            print("Processing Google Pay payment of \(amount)")
            // Keep adding more if-else blocks...
        }
        // Each new payment method requires modifying this class!
    }
}
```

**Problems:**
- Must modify existing code to add features
- Growing if-else chain
- Risk breaking existing payment methods
- Hard to test individual payment types

### Good Example - Following OCP
```swift
// Define a protocol (contract)
protocol PaymentMethod {
    var name: String { get }
    func process(amount: Double)
}

// Each payment method is its own class
class CreditCardPayment: PaymentMethod {
    let name = "Credit Card"
    
    func process(amount: Double) {
        print("Processing credit card payment of \(amount)")
        connectToCreditCardGateway()
        validateCardNumber()
        chargeCard()
    }
    
    private func connectToCreditCardGateway() { }
    private func validateCardNumber() { }
    private func chargeCard() { }
}

class PayPalPayment: PaymentMethod {
    let name = "PayPal"
    
    func process(amount: Double) {
        print("Processing PayPal payment of \(amount)")
        connectToPayPal()
        authenticateUser()
        transferFunds()
    }
    
    private func connectToPayPal() { }
    private func authenticateUser() { }
    private func transferFunds() { }
}

class ApplePayPayment: PaymentMethod {
    let name = "Apple Pay"
    
    func process(amount: Double) {
        print("Processing Apple Pay payment of \(amount)")
        requestApplePayAuthentication()
        processApplePayment()
    }
    
    private func requestApplePayAuthentication() { }
    private func processApplePayment() { }
}

// Easy to add new payment method - just create new class
class GooglePayPayment: PaymentMethod {
    let name = "Google Pay"
    
    func process(amount: Double) {
        print("Processing Google Pay payment of \(amount)")
        // Google Pay specific logic
    }
}

// Processor doesn't need to change when adding new payment methods
class PaymentProcessor {
    func process(method: PaymentMethod, amount: Double) {
        print("Processing \(method.name)...")
        method.process(amount: amount)
    }
}

// Usage
let processor = PaymentProcessor()
processor.process(method: CreditCardPayment(), amount: 100.0)
processor.process(method: ApplePayPayment(), amount: 50.0)
processor.process(method: GooglePayPayment(), amount: 75.0)  // New method, no changes to processor!
```

**Benefits:**
- Add new features by creating new classes
- Existing code remains unchanged and stable
- Each payment type is isolated and testable
- No risk of breaking existing functionality

### Real-World iOS Examples
- **UITableViewCell subclasses**: Extend UITableViewCell without modifying it
- **Custom UIView subclasses**: Add new views without changing UIView
- **URLSessionTask types**: Data, upload, download tasks extend base functionality
- **Protocol extensions**: Add functionality to protocols without modifying conforming types

---

## 3. Liskov Substitution Principle (LSP)
       If you have a parent class, you should be able to replace it with any child class without breaking your app. Child classes must work the same way as the parent.

### Why it matters
Your code won't crash unexpectedly when you use different subclasses. Everything behaves predictably, and you can safely use polymorphism.

### Bad Example - Violating LSP
```swift
class Bird {
    func fly() {
        print("Flying high in the sky!")
    }
    
    func eat() {
        print("Eating food")
    }
}

class Sparrow: Bird {
    override func fly() {
        print("Sparrow flying fast!")
    }
}

class Penguin: Bird {
    override func fly() {
        // Penguins can't fly - this breaks the parent class contract!
        fatalError("Penguins can't fly!")
    }
}

// This will crash if bird is a Penguin
func makeBirdFly(bird: Bird) {
    bird.fly()  // Expects all birds to fly
}

let sparrow = Sparrow()
makeBirdFly(bird: sparrow)  // Works fine

let penguin = Penguin()
makeBirdFly(bird: penguin)  // CRASH! 💥
```

**Problems:**
- Child class doesn't work like parent
- Unexpected crashes
- Can't safely substitute child for parent
- Violates expectations

### Good Example - Following LSP
```swift
// Use protocols to define actual capabilities
protocol Bird {
    func eat()
    func move()
}

protocol Flyable {
    func fly()
}

class Sparrow: Bird, Flyable {
    func eat() {
        print("Sparrow eating seeds")
    }
    
    func move() {
        fly()
    }
    
    func fly() {
        print("Sparrow flying!")
    }
}

class Penguin: Bird {
    func eat() {
        print("Penguin eating fish")
    }
    
    func move() {
        swim()
    }
    
    func swim() {
        print("Penguin swimming!")
    }
}

// Works with any bird type
func feedBird(_ bird: Bird) {
    bird.eat()  // Safe for all birds
}

func moveBird(_ bird: Bird) {
    bird.move()  // Safe for all birds (each moves their own way)
}

// Only call fly on birds that can actually fly
func makeFlyableFly(_ flyable: Flyable) {
    flyable.fly()
}

// Usage - all work correctly
let sparrow = Sparrow()
feedBird(sparrow)
moveBird(sparrow)
makeFlyableFly(sparrow)

let penguin = Penguin()
feedBird(penguin)
moveBird(penguin)
// makeFlyableFly(penguin)  // Won't compile - penguin isn't Flyable
```

**Benefits:**
- No unexpected crashes
- Clear contracts about what each type can do
- Can safely substitute types
- Compiler catches mistakes

### Real-World iOS Examples
```swift
// Bad - Violates LSP
class CustomTextField: UITextField {
    override func becomeFirstResponder() -> Bool {
        return false  // Breaks expectation that text fields can become first responder
    }
}

// Good - Follows LSP
class ReadOnlyTextField: UITextField {
    override init(frame: CGRect) {
        super.init(frame: frame)
        isUserInteractionEnabled = false  // Properly disables interaction
    }
}

// All UIView subclasses should work interchangeably
func addViewToScreen(_ view: UIView) {
    view.backgroundColor = .white
    // Should work for any UIView subclass
}
```

---

## 4. Interface Segregation Principle (ISP)
       Don't force classes to implement methods they don't need. Keep protocols small and focused. It's better to have many small protocols than one giant protocol.

### Why it matters
Classes only implement what they actually need, making code cleaner and easier to understand. No more empty method stubs.

### Bad Example - Violating ISP
```swift
// Too many responsibilities in one protocol
protocol Worker {
    func writeCode()
    func testCode()
    func designUI()
    func managePeople()
    func doAccounting()
    func marketProduct()
}

// This class is forced to implement methods it doesn't use
class Developer: Worker {
    func writeCode() {
        print("Writing Swift code...")
    }
    
    func testCode() {
        print("Testing code...")
    }
    
    // Forced to implement these even though they don't make sense for a developer
    func designUI() { 
        // Empty - developers don't always design
    }
    
    func managePeople() { 
        // Empty - not all developers manage people
    }
    
    func doAccounting() { 
        // Empty - definitely not a developer's job
    }
    
    func marketProduct() { 
        // Empty - also not a developer's job
    }
}

class Designer: Worker {
    func designUI() {
        print("Designing beautiful UI...")
    }
    
    // Forced to implement all these
    func writeCode() { }
    func testCode() { }
    func managePeople() { }
    func doAccounting() { }
    func marketProduct() { }
}
```

**Problems:**
- Classes implement methods they don't need
- Lots of empty implementations
- Confusing interface
- Hard to understand what a class actually does

### Good Example - Following ISP
```swift
// Small, focused protocols
protocol Codable {
    func writeCode()
}

protocol Testable {
    func testCode()
}

protocol Designer {
    func designUI()
}

protocol Manager {
    func managePeople()
}

protocol Accountant {
    func doAccounting()
}

protocol Marketer {
    func marketProduct()
}

// Only implement what you need
class SoftwareDeveloper: Codable, Testable {
    func writeCode() {
        print("Writing Swift code...")
    }
    
    func testCode() {
        print("Testing code...")
    }
}

class UIDesigner: Designer {
    func designUI() {
        print("Designing beautiful UI...")
    }
}

class TechLead: Codable, Testable, Manager {
    func writeCode() {
        print("Writing code...")
    }
    
    func testCode() {
        print("Testing code...")
    }
    
    func managePeople() {
        print("Managing team...")
    }
}

class FullStackDeveloper: Codable, Testable, Designer {
    func writeCode() {
        print("Writing code...")
    }
    
    func testCode() {
        print("Testing code...")
    }
    
    func designUI() {
        print("Designing UI...")
    }
}
```

**Benefits:**
- Clean, focused interfaces
- No empty method implementations
- Clear about what each class can do
- Easy to create different combinations

### Real-World iOS Examples
```swift
// Good - iOS protocols are focused
protocol UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
}

protocol UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)
}

// Your class can implement just what it needs
class MyViewController: UIViewController, UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 10
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        return UITableViewCell()
    }
    // Not forced to implement delegate methods if not needed
}

// Another example - focused protocols
protocol Downloadable {
    func download()
}

protocol Cacheable {
    func cache()
}

protocol Shareable {
    func share()
}

class Image: Downloadable, Cacheable {
    func download() { print("Downloading image") }
    func cache() { print("Caching image") }
}

class Video: Downloadable, Shareable {
    func download() { print("Downloading video") }
    func share() { print("Sharing video") }
}
```

---

## 5. Dependency Inversion Principle (DIP)
       High-level code shouldn't depend on low-level code. Both should depend on abstractions (protocols). This means your view controller shouldn't know the exact details of how data is fetched—it just knows that it can be fetched.

### Why it matters
You can easily swap implementations (network vs local), test your code with mock objects, and change data sources without touching the view controller.

### Bad Example - Violating DIP
```swift
// Hard dependency on specific implementation
class UserViewController: UIViewController {
    let apiClient = APIClient()  // Directly depends on concrete class
    
    func loadUser() {
        apiClient.fetchUser { user in
            self.nameLabel.text = user.name
        }
    }
}

class APIClient {
    func fetchUser(completion: @escaping (User) -> Void) {
        // Fetch from network
        let url = URL(string: "https://api.example.com/user")!
        URLSession.shared.dataTask(with: url) { data, _, _ in
            // Parse user
        }.resume()
    }
}
```

**Problems:**
- Can't swap APIClient for another implementation
- Hard to test (always hits real network)
- Tightly coupled to specific implementation
- Can't switch to local database or cache easily

### Good Example - Following DIP
```swift
// Define what you need, not how to do it
protocol UserRepository {
    func fetchUser(completion: @escaping (User) -> Void)
}

// Network implementation
class NetworkUserRepository: UserRepository {
    func fetchUser(completion: @escaping (User) -> Void) {
        let url = URL(string: "https://api.example.com/user")!
        URLSession.shared.dataTask(with: url) { data, _, _ in
            if let data = data,
               let user = try? JSONDecoder().decode(User.self, from: data) {
                completion(user)
            }
        }.resume()
    }
}

// Local storage implementation
class LocalUserRepository: UserRepository {
    func fetchUser(completion: @escaping (User) -> Void) {
        // Fetch from Core Data or local file
        let user = fetchFromDatabase()
        completion(user)
    }
    
    private func fetchFromDatabase() -> User {
        // Database logic
        return User(name: "Local User", email: "local@example.com")
    }
}

// Mock for testing
class MockUserRepository: UserRepository {
    var userToReturn: User?
    
    func fetchUser(completion: @escaping (User) -> Void) {
        completion(userToReturn ?? User(name: "Mock User", email: "mock@test.com"))
    }
}

// View controller depends on the protocol, not the implementation
class UserViewController: UIViewController {
    let repository: UserRepository  // Abstraction, not concrete type
    
    init(repository: UserRepository) {
        self.repository = repository
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    func loadUser() {
        repository.fetchUser { [weak self] user in
            DispatchQueue.main.async {
                self?.nameLabel.text = user.name
                self?.emailLabel.text = user.email
            }
        }
    }
}

// Easy to switch implementations
let networkVC = UserViewController(repository: NetworkUserRepository())
let localVC = UserViewController(repository: LocalUserRepository())
let testVC = UserViewController(repository: MockUserRepository())

// Can switch based on conditions
class RepositoryFactory {
    static func createRepository() -> UserRepository {
        if isOffline {
            return LocalUserRepository()
        } else {
            return NetworkUserRepository()
        }
    }
}
```

**Benefits:**
- Easy to test with mocks
- Can swap implementations easily
- Loose coupling
- Flexible and maintainable

### Real-World iOS Examples
```swift
// Good dependency inversion in iOS
protocol ImageLoader {
    func loadImage(from url: URL, completion: @escaping (UIImage?) -> Void)
}

protocol DataStorage {
    func save(_ data: Data, for key: String)
    func load(for key: String) -> Data?
}

protocol LocationProvider {
    func getCurrentLocation(completion: @escaping (CLLocation?) -> Void)
}

// View controller depends on abstractions
class ProfileViewController: UIViewController {
    let imageLoader: ImageLoader
    let storage: DataStorage
    let locationProvider: LocationProvider
    
    init(imageLoader: ImageLoader, storage: DataStorage, locationProvider: LocationProvider) {
        self.imageLoader = imageLoader
        self.storage = storage
        self.locationProvider = locationProvider
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("Use init with dependencies")
    }
}
```

---

## Combining SOLID Principles - Complete Example

Here's a real-world iOS example that uses all SOLID principles together:

```swift
// PROTOCOLS - Interface Segregation & Dependency Inversion
protocol ImageLoader {
    func loadImage(from url: URL, completion: @escaping (UIImage?) -> Void)
}

protocol ImageCache {
    func image(for url: URL) -> UIImage?
    func store(_ image: UIImage, for url: URL)
}

protocol ImageProcessor {
    func resize(_ image: UIImage, to size: CGSize) -> UIImage
}

// IMPLEMENTATIONS - Open/Closed & Single Responsibility

// Single Responsibility: Only loads images from network
class NetworkImageLoader: ImageLoader {
    func loadImage(from url: URL, completion: @escaping (UIImage?) -> Void) {
        URLSession.shared.dataTask(with: url) { data, _, _ in
            let image = data.flatMap { UIImage(data: $0) }
            DispatchQueue.main.async {
                completion(image)
            }
        }.resume()
    }
}

// Single Responsibility: Only handles memory caching
class MemoryImageCache: ImageCache {
    private var cache = NSCache<NSURL, UIImage>()
    
    func image(for url: URL) -> UIImage? {
        return cache.object(forKey: url as NSURL)
    }
    
    func store(_ image: UIImage, for url: URL) {
        cache.setObject(image, forKey: url as NSURL)
    }
}

// Single Responsibility: Only processes images
class BasicImageProcessor: ImageProcessor {
    func resize(_ image: UIImage, to size: CGSize) -> UIImage {
        UIGraphicsBeginImageContextWithOptions(size, false, 0.0)
        image.draw(in: CGRect(origin: .zero, size: size))
        let resizedImage = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        return resizedImage ?? image
    }
}

// COORDINATOR - Single Responsibility: Coordinates image loading
// Depends on abstractions (DIP)
class ImageLoadingCoordinator {
    private let loader: ImageLoader
    private let cache: ImageCache
    private let processor: ImageProcessor
    
    init(loader: ImageLoader, cache: ImageCache, processor: ImageProcessor) {
        self.loader = loader
        self.cache = cache
        self.processor = processor
    }
    
    func loadImage(from url: URL, size: CGSize? = nil, completion: @escaping (UIImage?) -> Void) {
        // Check cache first
        if let cachedImage = cache.image(for: url) {
            let processedImage = size.map { processor.resize(cachedImage, to: $0) } ?? cachedImage
            completion(processedImage)
            return
        }
        
        // Load from network
        loader.loadImage(from: url) { [weak self] image in
            guard let self = self, let image = image else {
                completion(nil)
                return
            }
            
            // Cache the image
            self.cache.store(image, for: url)
            
            // Process if needed
            let processedImage = size.map { self.processor.resize(image, to: $0) } ?? image
            completion(processedImage)
        }
    }
}

// VIEW CONTROLLER - Single Responsibility: Only handles UI
// Depends on abstractions (DIP)
class ImageGalleryViewController: UIViewController {
    private let imageCoordinator: ImageLoadingCoordinator
    private let collectionView: UICollectionView
    
    init(imageCoordinator: ImageLoadingCoordinator) {
        self.imageCoordinator = imageCoordinator
        let layout = UICollectionViewFlowLayout()
        self.collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("Use init with coordinator")
    }
    
    func loadImage(at url: URL, into imageView: UIImageView) {
        imageCoordinator.loadImage(from: url, size: imageView.bounds.size) { image in
            imageView.image = image
        }
    }
}

// USAGE - Easy to configure and extend
let loader = NetworkImageLoader()
let cache = MemoryImageCache()
let processor = BasicImageProcessor()

let coordinator = ImageLoadingCoordinator(
    loader: loader,
    cache: cache,
    processor: processor
)

let viewController = ImageGalleryViewController(imageCoordinator: coordinator)

// Easy to add new implementations (Open/Closed)
class DiskImageCache: ImageCache {
    func image(for url: URL) -> UIImage? {
        // Load from disk
        return nil
    }
    
    func store(_ image: UIImage, for url: URL) {
        // Save to disk
    }
}

// Can swap implementations easily
let diskCache = DiskImageCache()
let newCoordinator = ImageLoadingCoordinator(
    loader: loader,
    cache: diskCache,  // Different cache implementation
    processor: processor
)
```

**How this example uses SOLID:**

1. **SRP**: Each class has one job (load, cache, process, coordinate, display UI)
2. **OCP**: Can add new image loaders, caches, or processors without modifying existing code
3. **LSP**: Any ImageLoader works the same way, any ImageCache works the same way
4. **ISP**: Small, focused protocols (ImageLoader, ImageCache, ImageProcessor)
5. **DIP**: Everything depends on protocols, not concrete implementations

---

## Common SOLID Violations in iOS

### 1. Massive View Controllers (Violates SRP)
```swift
// DON'T DO THIS
class MassiveViewController: UIViewController {
    // Networking
    func fetchData() { }
    
    // Data processing
    func parseJSON() { }
    func transformData() { }
    
    // Business logic
    func calculateTotal() { }
    func validateInput() { }
    
    // UI updates
    func updateLabels() { }
    func configureTableView() { }
    
    // Database
    func saveToDatabase() { }
    
    // Analytics
    func trackEvent() { }
}

// DO THIS
class ViewController: UIViewController {
    let networkService: NetworkService
    let dataProcessor: DataProcessor
    let calculator: Calculator
    let validator: Validator
    let databaseService: DatabaseService
    let analyticsService: AnalyticsService
    
    // Only UI-related code here
}
```

### 2. Hard-coded Dependencies (Violates DIP)
```swift
// DON'T DO THIS
class ViewController {
    let api = APIManager()  // Hard to test or swap
    let database = CoreDataManager()  // Tightly coupled
}

// DO THIS
class ViewController {
    let repository: DataRepository  // Protocol
    
    init(repository: DataRepository) {
        self.repository = repository
    }
}
```

### 3. Fat Protocols (Violates ISP)
```swift
// DON'T DO THIS
protocol DataSourceDelegate {
    func didUpdate()
    func didDelete()
    func didCreate()
    func didSync()
    func didFail(with error: Error)
    func didLoadMore()
    func didRefresh()
}

// DO THIS
protocol DataUpdateDelegate {
    func didUpdate()
}

protocol DataDeletionDelegate {
    func didDelete()
}

protocol DataSyncDelegate {
    func didSync()
}
```

### 4. Type Checking with If-Else (Violates OCP)
```swift
// DON'T DO THIS
func handle(notification type: String) {
    if type == "email" {
        sendEmail()
    } else if type == "push" {
        sendPush()
    } else if type == "sms" {
        sendSMS()
    }
    // Adding new types requires modifying this
}

// DO THIS
protocol NotificationSender {
    func send()
}

class EmailNotification: NotificationSender {
    func send() { /* Email logic */ }
}
class PushNotification: NotificationSender {
    func send() { /* Push logic */ }
}

func handle(notification: NotificationSender) {
    notification.send()
}
```

### 5. Breaking Parent Contracts (Violates LSP)
```swift
// DON'T DO THIS
class CustomButton: UIButton {
    override func setTitle(_ title: String?, for state: UIControl.State) {
        fatalError("Use setAttributedTitle instead")  // Breaks parent contract
    }
}

// DO THIS
class CustomButton: UIButton {
    override func setTitle(_ title: String?, for state: UIControl.State) {
        // Properly handle title setting
        super.setTitle(title?.uppercased(), for: state)
    }
}
```

---

## Explaining SOLID to Junior Developers

Use simple analogies to make SOLID principles easy to understand:

### Single Responsibility (SRP)
**Analogy:** "A chef cooks, a waiter serves, and a cashier handles payment. Don't make one person do all three jobs."

**iOS Example:** "Don't put networking, database, and UI code all in the view controller. Each should be a separate class."

### Open/Closed (OCP)
**Analogy:** "You can add new apps to your iPhone without changing iOS. iOS is open for extension, closed for modification."

**iOS Example:** "Use protocols so you can add new payment methods without changing the payment processor class."

### Liskov Substitution (LSP)
**Analogy:** "Any USB-C cable should work with any USB-C port. If a cable requires special handling, it breaks the contract."

**iOS Example:** "Any subclass of UITableViewCell should work in a table view. Don't create a cell that crashes when used normally."

### Interface Segregation (ISP)
**Analogy:** "Don't force everyone to learn the entire iPhone settings menu. People only need to know the settings they use."

**iOS Example:** "Instead of one huge delegate protocol with 20 methods, create small protocols so classes only implement what they need."

### Dependency Inversion (DIP)
**Analogy:** "Your car doesn't care what brand of gas you use—it just needs fuel. The car depends on 'fuel' (abstraction), not 'Shell Premium' (concrete implementation)."

**iOS Example:** "Your view controller should depend on a 'UserRepository' protocol, not on 'NetworkAPIClient'. This way you can swap in a mock for testing."

---

## Benefits of Following SOLID

1. **Easier Testing** - Mock dependencies, test in isolation
2. **Better Maintainability** - Changes are localized, less risk of breaking things
3. **Improved Readability** - Clear responsibilities, easier to understand
4. **Flexibility** - Easy to swap implementations
5. **Reusability** - Components can be used in multiple places
6. **Scalability** - Codebase can grow without becoming unmanageable
7. **Team Collaboration** - Clear boundaries make it easier for multiple developers to work together

---

## Quick Reference

| Principle | What to Do | What to Avoid |
|-----------|-----------|---------------|
| **SRP** | One class, one job | Massive classes doing everything |
| **OCP** | Extend with new classes | Modify existing code for new features |
| **LSP** | Subclasses work like parents | Subclasses that break parent contracts |
| **ISP** | Small, focused protocols | Giant protocols with unused methods |
| **DIP** | Depend on protocols | Depend on concrete classes |

---

## Conclusion

SOLID principles aren't just academic concepts—they're practical guidelines that make iOS development easier and more sustainable. Start applying them gradually:

1. **Start with SRP**: Break down massive view controllers
2. **Use protocols**: Enable DIP and ISP
3. **Think before subclassing**: Ensure LSP compliance
4. **Extend, don't modify**: Follow OCP when adding features

Remember: SOLID principles are guidelines, not strict rules. Use judgment to apply them where they add value. The goal is maintainable, testable code—not perfect adherence to principles for their own sake.

Happy coding! 🚀
