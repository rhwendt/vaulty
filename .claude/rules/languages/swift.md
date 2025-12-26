---
paths: "**/*.swift"
---

#memory/language #swift #best-practices

# Swift Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Swift preferences:
> - `frameworks.swift`: Preferred framework (SwiftUI, UIKit, Vapor for backend)
> - `formatter_swift`: Code formatter (SwiftFormat, swift-format)
> - `linter_swift`: Linter (SwiftLint)
> - `test_framework_swift`: Test framework (XCTest, Quick & Nimble)
> - `package_manager_swift`: Package manager (Swift Package Manager, CocoaPods, Carthage)

## Swift Philosophy

- **Safety first (type-safe and memory-safe)**
- **Clear over clever**
- **Protocol-oriented programming**
- **Value types (structs) preferred over reference types (classes)**
- **Optionals instead of null pointers**

## Code Style & Formatting

### Naming Conventions

```swift
// ✅ Good - Swift naming conventions
class UserService {  // PascalCase for types (classes, structs, enums, protocols)
    static let maxUsers = 100  // camelCase for constants (static let)
    
    private var userCount: Int  // camelCase for properties
    
    func getUserById(_ userId: Int) -> User? {  // camelCase for methods
        let localVariable = "test"  // camelCase for variables
        return User()
    }
}

protocol UserRepositoryProtocol {  // PascalCase, often suffixed with Protocol
    func findUser(by id: Int) -> User?
}

enum UserStatus {  // PascalCase for enums
    case active  // camelCase for cases
    case inactive
    case pending
}

// ❌ Bad
class user_service {  // Wrong case
    func Get_User_By_Id(_ user_id: Int) -> User? {  // Wrong case
        return nil
    }
}
```

### Optionals (The Right Way)

```swift
// ✅ Good - Proper optional handling
func findUser(id: Int) -> User? {
    return repository.find(id)
}

// Optional binding
if let user = findUser(id: 1) {
    print(user.name)
}

// Guard for early exit
guard let user = findUser(id: 1) else {
    return
}
print(user.name)

// Nil coalescing
let name = user?.name ?? "Unknown"

// Optional chaining
let email = user?.profile?.email

// Force unwrapping (only when 100% certain)
let user = findUser(id: 1)!  // ⚠️ Use sparingly

// ❌ Bad - Force unwrapping everywhere
let user = findUser(id: 1)!
print(user!.name!)  // Don't do this
```

### Value Types vs Reference Types

```swift
// ✅ Good - Use struct for value types
struct User {  // Prefer struct for data models
    let id: Int
    var name: String
    var email: String
}

// Use class when you need:
// - Inheritance
// - Reference semantics
// - Deinitialization
class UserService {  // Class for services/managers
    private let repository: UserRepository
    
    init(repository: UserRepository) {
        self.repository = repository
    }
}
```

### Protocol-Oriented Programming

```swift
// ✅ Good - Protocol-oriented approach
protocol UserRepository {
    func find(id: Int) -> User?
    func save(_ user: User) throws
}

struct InMemoryUserRepository: UserRepository {
    private var users: [Int: User] = [:]
    
    func find(id: Int) -> User? {
        return users[id]
    }
    
    mutating func save(_ user: User) throws {
        users[user.id] = user
    }
}

// Protocol extensions for default behavior
extension UserRepository {
    func findOrThrow(id: Int) throws -> User {
        guard let user = find(id: id) else {
            throw UserError.notFound(id: id)
        }
        return user
    }
}
```

### Modern Swift Features

**Property Wrappers**
```swift
// ✅ Good - Property wrappers for common patterns
@propertyWrapper
struct Clamped<Value: Comparable> {
    private var value: Value
    private let range: ClosedRange<Value>
    
    var wrappedValue: Value {
        get { value }
        set { value = min(max(newValue, range.lowerBound), range.upperBound) }
    }
    
    init(wrappedValue: Value, _ range: ClosedRange<Value>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
}

struct User {
    @Clamped(0...120) var age: Int = 0
}
```

**Result Type**
```swift
// ✅ Good - Result type for operations that can fail
func fetchUser(id: Int) -> Result<User, Error> {
    do {
        let user = try repository.find(id: id)
        return .success(user)
    } catch {
        return .failure(error)
    }
}

// Using result
switch fetchUser(id: 1) {
case .success(let user):
    print(user.name)
case .failure(let error):
    print("Error: \(error)")
}
```

**Async/Await (Swift 5.5+)**
```swift
// ✅ Good - Modern async/await
func fetchUser(id: Int) async throws -> User {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data)
}

// Usage
Task {
    do {
        let user = try await fetchUser(id: 1)
        print(user.name)
    } catch {
        print("Error: \(error)")
    }
}

// Parallel async
async let user1 = fetchUser(id: 1)
async let user2 = fetchUser(id: 2)
let users = try await [user1, user2]
```

## Error Handling

```swift
// ✅ Good - Custom errors with enum
enum UserError: Error {
    case notFound(id: Int)
    case invalidEmail(String)
    case saveFailed(underlying: Error)
}

// Throwing functions
func getUser(id: Int) throws -> User {
    guard let user = repository.find(id: id) else {
        throw UserError.notFound(id: id)
    }
    return user
}

// Do-catch
do {
    let user = try getUser(id: 1)
    print(user.name)
} catch UserError.notFound(let id) {
    print("User \(id) not found")
} catch {
    print("Unexpected error: \(error)")
}

// Try? and try!
let user = try? getUser(id: 1)  // Returns nil on error
let user = try! getUser(id: 1)  // Crashes on error (use sparingly)
```

## SwiftUI Best Practices

```swift
// ✅ Good - SwiftUI view
struct UserListView: View {
    @StateObject private var viewModel = UserListViewModel()
    @State private var searchText = ""
    
    var body: some View {
        NavigationView {
            List(viewModel.filteredUsers) { user in
                NavigationLink(destination: UserDetailView(user: user)) {
                    UserRow(user: user)
                }
            }
            .searchable(text: $searchText)
            .navigationTitle("Users")
            .task {
                await viewModel.loadUsers()
            }
        }
    }
}

// ViewModel
@MainActor
class UserListViewModel: ObservableObject {
    @Published var users: [User] = []
    @Published var isLoading = false
    
    func loadUsers() async {
        isLoading = true
        defer { isLoading = false }
        
        do {
            users = try await userService.fetchUsers()
        } catch {
            // Handle error
        }
    }
}

// Extract subviews
struct UserRow: View {
    let user: User
    
    var body: some View {
        HStack {
            Image(systemName: "person.circle")
            Text(user.name)
            Spacer()
            if user.isActive {
                Image(systemName: "checkmark.circle.fill")
                    .foregroundColor(.green)
            }
        }
    }
}
```

## Testing with XCTest

```swift
// ✅ Good - XCTest tests
import XCTest
@testable import MyApp

class UserServiceTests: XCTestCase {
    var sut: UserService!
    var mockRepository: MockUserRepository!
    
    override func setUp() {
        super.setUp()
        mockRepository = MockUserRepository()
        sut = UserService(repository: mockRepository)
    }
    
    override func tearDown() {
        sut = nil
        mockRepository = nil
        super.tearDown()
    }
    
    func testGetUser_WhenUserExists_ReturnsUser() throws {
        // Given
        let expectedUser = User(id: 1, name: "John", email: "john@example.com")
        mockRepository.users[1] = expectedUser
        
        // When
        let user = try sut.getUser(id: 1)
        
        // Then
        XCTAssertEqual(user.name, "John")
        XCTAssertEqual(user.email, "john@example.com")
    }
    
    func testGetUser_WhenUserNotFound_ThrowsError() {
        // When/Then
        XCTAssertThrowsError(try sut.getUser(id: 999)) { error in
            XCTAssertEqual(error as? UserError, .notFound(id: 999))
        }
    }
    
    // Async test
    func testFetchUser_Success() async throws {
        let user = try await sut.fetchUser(id: 1)
        XCTAssertNotNil(user)
    }
}

// Mock
class MockUserRepository: UserRepository {
    var users: [Int: User] = [:]
    
    func find(id: Int) -> User? {
        return users[id]
    }
}
```

## Memory Management

```swift
// ✅ Good - Avoid retain cycles with weak/unowned
class UserViewController {
    private let service: UserService
    
    func loadUser() {
        service.fetchUser(id: 1) { [weak self] result in
            guard let self = self else { return }
            
            switch result {
            case .success(let user):
                self.updateUI(with: user)
            case .failure(let error):
                self.showError(error)
            }
        }
    }
}

// Use unowned when reference will never be nil
class Child {
    unowned let parent: Parent
    
    init(parent: Parent) {
        self.parent = parent
    }
}
```

## Code Quality (SwiftLint)

```yaml
# .swiftlint.yml
disabled_rules:
  - trailing_whitespace
opt_in_rules:
  - empty_count
  - explicit_init
  - explicit_type_interface
included:
  - Sources
excluded:
  - Pods
  - Generated
line_length: 120
```

```bash
# Run SwiftLint
swiftlint

# Auto-correct issues
swiftlint --fix
```

## Common Anti-Patterns

❌ **Force Unwrapping Everywhere**
```swift
// ❌ Bad
let user = users.first!
let name = user.name!

// ✅ Good
guard let user = users.first else { return }
let name = user.name ?? "Unknown"
```

❌ **Massive View Controllers**
```swift
// ❌ Bad - God class
class UserViewController: UIViewController {
    // 1000+ lines of code
}

// ✅ Good - Separate concerns
class UserViewController: UIViewController {
    private let viewModel: UserViewModel
    private let coordinator: UserCoordinator
    // Focused responsibilities
}
```

❌ **Ignoring Errors**
```swift
// ❌ Bad
try? riskyOperation()  // Error silently ignored

// ✅ Good
do {
    try riskyOperation()
} catch {
    logger.error("Operation failed: \(error)")
}
```

## Performance Tips

- Use value types (structs) when possible
- Use `lazy` for expensive properties
- Use `@autoclosure` for delayed evaluation
- Profile with Instruments before optimizing
- Use copy-on-write for large collections

## Resources

- [Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/)
- [Swift Style Guide by raywenderlich.com](https://github.com/raywenderlich/swift-style-guide)
- [Swift Documentation](https://docs.swift.org/swift-book/)
- [SwiftLint](https://github.com/realm/SwiftLint)
