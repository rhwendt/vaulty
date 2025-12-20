#memory/language #go #best-practices

# Go Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Go preferences:
> - `frameworks.go`: Preferred Go framework (Gin, Echo, Fiber, etc.)
> - `formatter_go`: Code formatter (gofmt, goimports)
> - `linter_go`: Linter (golangci-lint, staticcheck, go vet)
> - `test_framework_go`: Test framework (testing, testify, ginkgo)
> - `package_manager_go`: Package manager (go mod)

## Go Philosophy

- **Simplicity over cleverness**: Clear, straightforward code
- **Explicit over implicit**: Be explicit about errors, dependencies
- **Composition over inheritance**: Use interfaces and embedding
- **Concurrency built-in**: Goroutines and channels are first-class
- **"A little copying is better than a little dependency"**

## Idiomatic Go Patterns

### Error Handling

**Standard Error Handling**
```go
// ✅ Good - Check every error immediately
func ReadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("failed to read config: %w", err)
    }

    var config Config
    err = json.Unmarshal(data, &config)
    if err != nil {
        return nil, fmt.Errorf("failed to parse config: %w", err)
    }

    return &config, nil
}

// ❌ Bad - Ignoring errors
func ReadConfig(path string) *Config {
    data, _ := os.ReadFile(path)  // DON'T DO THIS
    var config Config
    json.Unmarshal(data, &config)
    return &config
}
```

**Error Wrapping (Go 1.13+)**
```go
// Use %w to wrap errors for error chain
if err != nil {
    return fmt.Errorf("process user %d: %w", userID, err)
}

// Check for specific errors
if errors.Is(err, os.ErrNotExist) {
    // Handle file not found
}

// Check for error types
var pathErr *os.PathError
if errors.As(err, &pathErr) {
    // Handle path error
}
```

**Custom Errors**
```go
// Define sentinel errors at package level
var (
    ErrUserNotFound = errors.New("user not found")
    ErrInvalidInput = errors.New("invalid input")
)

// Custom error types
type ValidationError struct {
    Field string
    Issue string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation failed for %s: %s", e.Field, e.Issue)
}
```

### Interfaces

**Small, Focused Interfaces**
```go
// ✅ Good - Small, single-purpose interfaces
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type ReadWriter interface {
    Reader
    Writer
}

// ❌ Bad - Large, multi-purpose interface
type FileHandler interface {
    Open() error
    Close() error
    Read(p []byte) (int, error)
    Write(p []byte) (int, error)
    Seek(offset int64, whence int) (int64, error)
    Stat() (FileInfo, error)
    // Too many methods!
}
```

**Accept Interfaces, Return Structs**
```go
// ✅ Good - Accept interface, return concrete type
func ProcessData(r io.Reader) (*Result, error) {
    // r can be any reader: file, network, buffer, etc.
    data, err := io.ReadAll(r)
    if err != nil {
        return nil, err
    }
    return &Result{Data: data}, nil
}

// Usage allows flexibility
file, _ := os.Open("data.txt")
result, _ := ProcessData(file)

buffer := bytes.NewBuffer([]byte("test"))
result, _ = ProcessData(buffer)
```

### Struct Embedding

**Composition via Embedding**
```go
// ✅ Good - Composition through embedding
type Logger struct {
    prefix string
}

func (l *Logger) Log(msg string) {
    fmt.Printf("[%s] %s\n", l.prefix, msg)
}

type Service struct {
    Logger  // Embedded - Service "has-a" Logger
    db *Database
}

func (s *Service) Process() {
    s.Log("Processing...")  // Can call Logger methods directly
}

// Usage
svc := &Service{
    Logger: Logger{prefix: "SERVICE"},
    db: db,
}
```

### Concurrency Patterns

**Goroutines and Channels**
```go
// ✅ Pattern: Fan-out, Fan-in
func fanOut(input <-chan int, workers int) []<-chan int {
    channels := make([]<-chan int, workers)
    for i := 0; i < workers; i++ {
        channels[i] = worker(input)
    }
    return channels
}

func worker(input <-chan int) <-chan int {
    output := make(chan int)
    go func() {
        defer close(output)
        for n := range input {
            output <- process(n)
        }
    }()
    return output
}

func fanIn(channels ...<-chan int) <-chan int {
    output := make(chan int)
    var wg sync.WaitGroup

    for _, ch := range channels {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for n := range c {
                output <- n
            }
        }(ch)
    }

    go func() {
        wg.Wait()
        close(output)
    }()

    return output
}
```

**Context for Cancellation**
```go
// ✅ Good - Always use context for cancellation
func ProcessWithTimeout(ctx context.Context, data []byte) error {
    // Create timeout context
    ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
    defer cancel()

    resultCh := make(chan error, 1)

    go func() {
        resultCh <- doWork(data)
    }()

    select {
    case err := <-resultCh:
        return err
    case <-ctx.Done():
        return fmt.Errorf("processing timeout: %w", ctx.Err())
    }
}
```

**Sync Primitives**
```go
// Use sync.WaitGroup for waiting on goroutines
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
    wg.Add(1)
    go func(id int) {
        defer wg.Done()
        process(id)
    }(i)
}
wg.Wait()

// Use sync.Mutex for protecting shared state
type Counter struct {
    mu    sync.Mutex
    count int
}

func (c *Counter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

// Use sync.Once for one-time initialization
var (
    instance *Database
    once     sync.Once
)

func GetDatabase() *Database {
    once.Do(func() {
        instance = &Database{/* init */}
    })
    return instance
}
```

### Defer, Panic, Recover

**Defer for Cleanup**
```go
// ✅ Good - Use defer for cleanup
func WriteToFile(path string, data []byte) error {
    f, err := os.Create(path)
    if err != nil {
        return err
    }
    defer f.Close()  // Always closes, even if Write fails

    _, err = f.Write(data)
    return err
}

// Multiple defers execute in LIFO order
func processLayers() {
    defer fmt.Println("3. Cleanup outer")
    defer fmt.Println("2. Cleanup middle")
    defer fmt.Println("1. Cleanup inner")
    // Prints: 1, 2, 3
}
```

**Panic and Recover**
```go
// ⚠️ Use panic only for truly unrecoverable errors
func MustCompile(pattern string) *regexp.Regexp {
    re, err := regexp.Compile(pattern)
    if err != nil {
        panic(err)  // OK in init() or for config errors
    }
    return re
}

// Use recover in servers to prevent crash
func safeHandler(w http.ResponseWriter, r *http.Request) {
    defer func() {
        if err := recover(); err != nil {
            log.Printf("panic: %v", err)
            http.Error(w, "Internal Server Error", 500)
        }
    }()

    handler(w, r)
}
```

## Package Organization

### Project Structure
```
myproject/
├── cmd/
│   └── myapp/
│       └── main.go           # Application entry point
├── internal/                 # Private packages
│   ├── user/
│   │   ├── user.go          # Domain model
│   │   ├── repository.go    # Data access
│   │   └── service.go       # Business logic
│   └── auth/
│       └── auth.go
├── pkg/                      # Public packages
│   └── api/
│       └── client.go
├── go.mod
└── go.sum
```

### Naming Conventions
```go
// Package names: short, lowercase, no underscores
package user  // ✅ Good
package user_service  // ❌ Bad

// Exported names: PascalCase
type UserService struct {}
func NewUserService() *UserService {}

// Unexported names: camelCase
type userImpl struct {}
func newUserImpl() *userImpl {}

// Interface names: usually end in -er
type Reader interface {}
type Writer interface {}
type Validator interface {}

// Don't stutter names
user.NewUserService()  // ❌ Bad - stutters with package name
user.NewService()      // ✅ Good
```

## Testing

### Table-Driven Tests
```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive numbers", 2, 3, 5},
        {"negative numbers", -2, -3, -5},
        {"mixed", 5, -3, 2},
        {"zeros", 0, 0, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### Test Helpers
```go
func TestUserService(t *testing.T) {
    // Use t.Helper() for test helpers
    setup := func(t *testing.T) (*UserService, func()) {
        t.Helper()
        db := setupTestDB(t)
        svc := NewUserService(db)
        cleanup := func() {
            db.Close()
        }
        return svc, cleanup
    }

    t.Run("create user", func(t *testing.T) {
        svc, cleanup := setup(t)
        defer cleanup()

        user, err := svc.Create("test@example.com")
        if err != nil {
            t.Fatalf("unexpected error: %v", err)
        }
        if user.Email != "test@example.com" {
            t.Errorf("got email %s, want test@example.com", user.Email)
        }
    })
}
```

### Mocking with Interfaces
```go
// Define interface
type UserRepository interface {
    FindByID(id int) (*User, error)
}

// Mock implementation for testing
type MockUserRepository struct {
    FindByIDFunc func(id int) (*User, error)
}

func (m *MockUserRepository) FindByID(id int) (*User, error) {
    return m.FindByIDFunc(id)
}

// Test
func TestGetUser(t *testing.T) {
    mock := &MockUserRepository{
        FindByIDFunc: func(id int) (*User, error) {
            return &User{ID: id, Name: "Test"}, nil
        },
    }

    svc := NewUserService(mock)
    user, err := svc.GetUser(1)
    // assertions...
}
```

## Common Patterns

### Functional Options Pattern
```go
type Server struct {
    host    string
    port    int
    timeout time.Duration
}

type Option func(*Server)

func WithHost(host string) Option {
    return func(s *Server) {
        s.host = host
    }
}

func WithPort(port int) Option {
    return func(s *Server) {
        s.port = port
    }
}

func WithTimeout(timeout time.Duration) Option {
    return func(s *Server) {
        s.timeout = timeout
    }
}

func NewServer(opts ...Option) *Server {
    // Defaults
    s := &Server{
        host:    "localhost",
        port:    8080,
        timeout: 30 * time.Second,
    }

    // Apply options
    for _, opt := range opts {
        opt(s)
    }

    return s
}

// Usage
server := NewServer(
    WithHost("0.0.0.0"),
    WithPort(9000),
)
```

### Context Propagation
```go
// ✅ Always pass context as first parameter
func ProcessRequest(ctx context.Context, userID int) error {
    // Pass context down the call chain
    user, err := fetchUser(ctx, userID)
    if err != nil {
        return err
    }

    return saveUser(ctx, user)
}

// Store request-scoped values in context
type contextKey string

const userIDKey contextKey = "userID"

func WithUserID(ctx context.Context, userID int) context.Context {
    return context.WithValue(ctx, userIDKey, userID)
}

func GetUserID(ctx context.Context) (int, bool) {
    userID, ok := ctx.Value(userIDKey).(int)
    return userID, ok
}
```

## Common Anti-Patterns to Avoid

❌ **Goroutine Leaks**
```go
// Bad - goroutine never exits
func leak() {
    ch := make(chan int)
    go func() {
        for {
            ch <- 1  // Blocks forever if nobody reads
        }
    }()
}

// Good - use context for cancellation
func noLeak(ctx context.Context) {
    ch := make(chan int)
    go func() {
        for {
            select {
            case ch <- 1:
            case <-ctx.Done():
                return  // Exit when context cancelled
            }
        }
    }()
}
```

❌ **Not Checking Error Before Using Value**
```go
// Bad
user, err := getUser(id)
fmt.Println(user.Name)  // May panic if err != nil
if err != nil {
    return err
}

// Good
user, err := getUser(id)
if err != nil {
    return err
}
fmt.Println(user.Name)
```

❌ **Pointer to Loop Variable**
```go
// Bad - all goroutines will use last value
for _, item := range items {
    go func() {
        process(item)  // WRONG - all see last item
    }()
}

// Good - pass value to goroutine
for _, item := range items {
    go func(i Item) {
        process(i)
    }(item)
}
```

## Performance Tips

- Use `strings.Builder` for string concatenation
- Preallocate slices when size is known: `make([]int, 0, capacity)`
- Use `sync.Pool` for frequently allocated objects
- Profile with `pprof` before optimizing
- Use buffered channels when appropriate
- Avoid unnecessary pointer indirection

## Resources

- [Effective Go](https://go.dev/doc/effective_go)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)
