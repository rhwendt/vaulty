#memory/language #cpp #cplusplus #best-practices

# C++ Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for C++ preferences:
> - `frameworks.cpp`: Preferred framework (Qt, Boost, POCO)
> - `formatter_cpp`: Code formatter (clang-format)
> - `linter_cpp`: Linter (clang-tidy, cppcheck)
> - `test_framework_cpp`: Test framework (Google Test, Catch2, Boost.Test)
> - `package_manager_cpp`: Package manager (Conan, vcpkg, CMake)

## C++ Philosophy

- **Zero-overhead abstraction**
- **RAII (Resource Acquisition Is Initialization)**
- **Deterministic destruction**
- **"Don't pay for what you don't use"**
- **Modern C++ (C++11/14/17/20) preferred**

## Modern C++ Features (C++11 and later)

### Smart Pointers (Never use raw new/delete)

```cpp
// ✅ Good - Smart pointers manage memory automatically
#include <memory>

// unique_ptr for exclusive ownership
auto user = std::make_unique<User>("John");
processUser(std::move(user));  // Transfer ownership

// shared_ptr for shared ownership
auto config = std::make_shared<Config>();
auto service1 = std::make_shared<Service>(config);
auto service2 = std::make_shared<Service>(config);  // Both share config

// weak_ptr to break circular references
class Node {
    std::shared_ptr<Node> next;
    std::weak_ptr<Node> prev;  // Weak to avoid cycles
};

// ❌ Bad - Raw pointers and manual memory management
User* user = new User("John");
delete user;  // Easy to forget, causes memory leaks
```

### Auto Type Deduction

```cpp
// ✅ Good - Use auto for obvious types
auto users = std::vector<User>{};
auto config = loadConfig();
auto it = map.find(key);

for (const auto& user : users) {  // Range-based for loop
    std::cout << user.getName() << '\n';
}

// ❌ Bad - Verbose type specifications
std::vector<User>::iterator it = users.begin();
std::map<std::string, int>::const_iterator mapIt = map.find(key);
```

### Move Semantics

```cpp
// ✅ Good - Use move semantics for efficiency
class Buffer {
    std::vector<char> data_;
public:
    // Move constructor
    Buffer(Buffer&& other) noexcept 
        : data_(std::move(other.data_)) {}
    
    // Move assignment
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            data_ = std::move(other.data_);
        }
        return *this;
    }
};

// Usage
Buffer b1;
Buffer b2 = std::move(b1);  // Move, not copy

std::vector<User> users;
users.push_back(std::move(user));  // Move into vector
```

### Lambda Expressions

```cpp
// ✅ Good - Lambdas for inline functions
std::vector<int> numbers = {1, 2, 3, 4, 5};

// Capture by value
auto sum = std::accumulate(numbers.begin(), numbers.end(), 0,
    [](int acc, int n) { return acc + n; });

// Capture by reference
int threshold = 3;
auto count = std::count_if(numbers.begin(), numbers.end(),
    [&threshold](int n) { return n > threshold; });

// Generic lambda (C++14)
auto print = [](const auto& value) {
    std::cout << value << '\n';
};
```

### constexpr and const

```cpp
// ✅ Good - Use constexpr for compile-time constants
constexpr int MAX_SIZE = 100;
constexpr double PI = 3.14159;

// constexpr functions (evaluated at compile time when possible)
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

constexpr int result = factorial(5);  // Computed at compile time

// Use const for runtime constants
const std::string getName() {
    return "John";
}

// const member functions
class User {
public:
    std::string getName() const { return name_; }  // Doesn't modify object
private:
    std::string name_;
};
```

## RAII (Resource Acquisition Is Initialization)

```cpp
// ✅ Good - RAII for resource management
class FileHandle {
    FILE* file_;
public:
    explicit FileHandle(const char* filename) 
        : file_(std::fopen(filename, "r")) {
        if (!file_) {
            throw std::runtime_error("Failed to open file");
        }
    }
    
    ~FileHandle() {
        if (file_) {
            std::fclose(file_);
        }
    }
    
    // Delete copy, allow move
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
    FileHandle(FileHandle&& other) noexcept : file_(other.file_) {
        other.file_ = nullptr;
    }
    
    FILE* get() const { return file_; }
};

// Usage - file automatically closed
{
    FileHandle file("data.txt");
    // Use file.get()
}  // File closed here

// Use standard RAII types
std::lock_guard<std::mutex> lock(mutex_);  // Auto-unlocks
std::unique_lock<std::mutex> lock(mutex_);  // More flexible
```

## Error Handling

```cpp
// ✅ Good - Exceptions for error handling
class UserNotFoundException : public std::runtime_error {
public:
    explicit UserNotFoundException(int id)
        : std::runtime_error("User not found: " + std::to_string(id)) {}
};

User getUser(int id) {
    if (auto user = findUser(id)) {
        return *user;
    }
    throw UserNotFoundException(id);
}

// Using noexcept for performance
void swap(User& a, User& b) noexcept {
    std::swap(a, b);
}

// std::optional for optional values (C++17)
std::optional<User> findUser(int id) {
    if (/* found */) {
        return User{id, "John"};
    }
    return std::nullopt;
}

// Usage
if (auto user = findUser(1)) {
    std::cout << user->getName() << '\n';
}
```

## STL Containers & Algorithms

```cpp
// ✅ Good - Use STL containers and algorithms
#include <vector>
#include <algorithm>
#include <ranges>  // C++20

std::vector<int> numbers = {5, 2, 8, 1, 9};

// Sorting
std::sort(numbers.begin(), numbers.end());
std::sort(numbers.begin(), numbers.end(), std::greater<>());

// Searching
auto it = std::find(numbers.begin(), numbers.end(), 5);
if (it != numbers.end()) {
    std::cout << "Found: " << *it << '\n';
}

// Transforming
std::vector<int> doubled;
std::transform(numbers.begin(), numbers.end(), 
               std::back_inserter(doubled),
               [](int n) { return n * 2; });

// C++20 Ranges
auto even = numbers | std::views::filter([](int n) { return n % 2 == 0; })
                    | std::views::transform([](int n) { return n * 2; });
```

## Naming Conventions

```cpp
// ✅ Good - Common C++ naming conventions
class UserService {  // PascalCase for classes
public:
    void processUser(int userId);  // camelCase for functions
    
    int getUserCount() const { return userCount_; }
    void setUserCount(int count) { userCount_ = count; }
    
private:
    int userCount_;  // camelCase_ with underscore for members
    static const int MAX_USERS = 100;  // UPPER_CASE for constants
};

namespace myapp {  // lowercase for namespaces
    // ...
}

// ❌ Bad
class user_service {  // Wrong case
    int m_user_count;  // Hungarian notation (outdated)
};
```

## Testing with Google Test

```cpp
#include <gtest/gtest.h>

// ✅ Good - Structured tests
class UserServiceTest : public ::testing::Test {
protected:
    void SetUp() override {
        service_ = std::make_unique<UserService>();
    }
    
    std::unique_ptr<UserService> service_;
};

TEST_F(UserServiceTest, CreateUserSuccess) {
    // Arrange
    auto request = UserRequest{"John", "john@example.com"};
    
    // Act
    auto user = service_->createUser(request);
    
    // Assert
    EXPECT_EQ(user.getName(), "John");
    EXPECT_EQ(user.getEmail(), "john@example.com");
}

TEST_F(UserServiceTest, CreateUserInvalidEmail) {
    auto request = UserRequest{"John", "invalid"};
    
    EXPECT_THROW(service_->createUser(request), ValidationException);
}

// Parameterized tests
class EmailValidationTest : public ::testing::TestWithParam<std::string> {};

TEST_P(EmailValidationTest, InvalidEmails) {
    EXPECT_FALSE(isValidEmail(GetParam()));
}

INSTANTIATE_TEST_SUITE_P(
    InvalidEmailTests,
    EmailValidationTest,
    ::testing::Values("", "invalid", "@example.com"));
```

## Project Structure

```
MyProject/
├── include/
│   └── myproject/
│       ├── user.hpp
│       └── service.hpp
├── src/
│   ├── user.cpp
│   └── service.cpp
├── tests/
│   └── user_test.cpp
├── CMakeLists.txt
└── README.md
```

## Code Formatting (clang-format)

```yaml
# .clang-format
BasedOnStyle: Google
IndentWidth: 4
ColumnLimit: 100
PointerAlignment: Left
```

```bash
# Format code
clang-format -i src/*.cpp include/**/*.hpp

# Check formatting
clang-format --dry-run --Werror src/*.cpp
```

## Common Anti-Patterns

❌ **Manual Memory Management**
```cpp
// ❌ Bad
void process() {
    User* user = new User();
    // ... use user ...
    delete user;  // Easy to forget
}

// ✅ Good
void process() {
    auto user = std::make_unique<User>();
    // ... use user ...
}  // Automatically deleted
```

❌ **Not Using const**
```cpp
// ❌ Bad
void printUser(User user) {  // Copies user
    std::cout << user.getName();
}

// ✅ Good
void printUser(const User& user) {  // Const reference, no copy
    std::cout << user.getName();
}
```

❌ **Using C-style casts**
```cpp
// ❌ Bad
int* ptr = (int*)malloc(sizeof(int));

// ✅ Good
auto ptr = static_cast<int*>(voidPtr);
auto derived = dynamic_cast<Derived*>(base);
auto constCast = const_cast<int*>(constPtr);  // Use sparingly
```

## Performance Tips

- Use `const&` for function parameters (avoid copies)
- Use `std::move` for large objects
- Reserve vector capacity if size is known
- Use `emplace_back` instead of `push_back` for in-place construction
- Profile with perf, gprof, or Valgrind
- Use `[[nodiscard]]` attribute for important return values

## Resources

- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/)
- [Effective Modern C++ by Scott Meyers](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/)
- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
