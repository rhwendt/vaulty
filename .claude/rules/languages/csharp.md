---
paths: "**/*.cs"
---

#memory/language #csharp #best-practices

# C# Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for C# preferences:
> - `frameworks.csharp`: Preferred framework (ASP.NET Core, .NET MAUI)
> - `formatter_csharp`: Code formatter (dotnet format, csharpier)
> - `linter_csharp`: Linter (Roslyn analyzers, StyleCop)
> - `test_framework_csharp`: Test framework (xUnit, NUnit, MSTest)
> - `package_manager_csharp`: Package manager (NuGet via .NET CLI)

## C# Philosophy

- **Type-safe and modern object-oriented**
- **LINQ for expressive data queries**
- **Async/await for asynchronous programming**
- **Strongly typed with type inference (var)**
- **Cross-platform with .NET Core/.NET 6+**

## Code Style & Formatting

### Naming Conventions

```csharp
// ✅ Good - C# naming conventions
public class UserService  // PascalCase for classes
{
    private const int MaxUsers = 100;  // PascalCase for constants
    private readonly string _connectionString;  // _camelCase for private fields
    
    public string UserName { get; set; }  // PascalCase for properties
    
    public User GetUserById(int userId)  // PascalCase for methods
    {
        var localVariable = "test";  // camelCase for local variables
        return new User();
    }
}

// Namespace: PascalCase
namespace MyApp.Services;

// ❌ Bad
public class user_service  // Wrong case
{
    private string connection_string;  // Should be _connectionString
    
    public User get_user_by_id(int user_id)  // Wrong case
    {
        return null;
    }
}
```

### Modern C# Features

**Nullable Reference Types (C# 8+)**
```csharp
#nullable enable

// ✅ Good - Explicit nullability
public class UserService
{
    public User? FindUserById(int id)  // May return null
    {
        return _repository.Find(id);
    }
    
    public User GetUserById(int id)  // Never returns null
    {
        return _repository.Find(id) 
            ?? throw new NotFoundException($"User {id} not found");
    }
    
    public void ProcessUser(User user)  // Parameter cannot be null
    {
        Console.WriteLine(user.Name);  // No null check needed
    }
}
```

**Pattern Matching (C# 7-10)**
```csharp
// ✅ Good - Modern pattern matching
public string GetDescription(object obj) => obj switch
{
    User { IsActive: true } => "Active user",
    User { IsActive: false } => "Inactive user",
    Admin admin => $"Admin: {admin.Name}",
    null => "No object",
    _ => "Unknown"
};

// Property patterns
if (user is { Age: > 18, IsActive: true })
{
    Console.WriteLine("Adult active user");
}

// Type patterns
if (obj is string { Length: > 0 } text)
{
    Console.WriteLine(text);
}
```

**Records (C# 9+)**
```csharp
// ✅ Good - Use records for immutable data
public record User(int Id, string Name, string Email)
{
    // Validation
    public User
    {
        if (string.IsNullOrWhiteSpace(Email))
            throw new ArgumentException("Email required");
    }
}

// With expressions for copying
var user = new User(1, "John", "john@example.com");
var updated = user with { Name = "Jane" };

// Record classes for mutable records
public record class MutableUser
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
}
```

**Init-Only Properties (C# 9+)**
```csharp
// ✅ Good - Immutable objects with init
public class User
{
    public int Id { get; init; }
    public string Name { get; init; } = "";
    
    public User()
    {
        Id = 0;
        Name = "";
    }
}

// Usage
var user = new User 
{ 
    Id = 1, 
    Name = "John" 
};
// user.Id = 2;  // Compile error - cannot modify after initialization
```

**Global Usings (C# 10+)**
```csharp
// GlobalUsings.cs - Implicit usings for entire project
global using System;
global using System.Collections.Generic;
global using System.Linq;
global using System.Threading.Tasks;
global using Microsoft.Extensions.Logging;
```

## LINQ (Language Integrated Query)

```csharp
// ✅ Good - Expressive LINQ queries
var activeAdults = users
    .Where(u => u.IsActive)
    .Where(u => u.Age >= 18)
    .OrderBy(u => u.Name)
    .Select(u => new { u.Id, u.Name })
    .ToList();

// Query syntax
var results = from user in users
              where user.IsActive && user.Age >= 18
              orderby user.Name
              select new { user.Id, user.Name };

// Common LINQ operations
var count = users.Count(u => u.IsActive);
var firstUser = users.FirstOrDefault(u => u.Id == 1);
var exists = users.Any(u => u.Email == "test@example.com");
var allActive = users.All(u => u.IsActive);
var grouped = users.GroupBy(u => u.City);
```

## Async/Await

```csharp
// ✅ Good - Async/await pattern
public async Task<User?> GetUserAsync(int id)
{
    using var client = new HttpClient();
    var response = await client.GetAsync($"/api/users/{id}");
    response.EnsureSuccessStatusCode();
    return await response.Content.ReadFromJsonAsync<User>();
}

// Async LINQ
var users = await _context.Users
    .Where(u => u.IsActive)
    .ToListAsync();

// Task.WhenAll for parallel operations
var tasks = userIds.Select(id => GetUserAsync(id));
var users = await Task.WhenAll(tasks);

// ❌ Bad - Blocking on async code
var user = GetUserAsync(1).Result;  // Don't do this - can cause deadlocks
var user2 = GetUserAsync(2).GetAwaiter().GetResult();  // Also bad
```

## Error Handling

```csharp
// ✅ Good - Custom exceptions
public class UserNotFoundException : Exception
{
    public int UserId { get; }
    
    public UserNotFoundException(int userId) 
        : base($"User {userId} not found")
    {
        UserId = userId;
    }
}

// Try-catch with specific exceptions
try
{
    var user = await GetUserAsync(id);
    await ProcessUserAsync(user);
}
catch (UserNotFoundException ex)
{
    _logger.LogError(ex, "User not found: {UserId}", ex.UserId);
    throw;
}
catch (HttpRequestException ex)
{
    _logger.LogError(ex, "Network error");
    throw new ServiceException("Failed to fetch user", ex);
}

// Using statement for IDisposable
using var connection = new SqlConnection(connectionString);
await connection.OpenAsync();
// Automatically disposed at end of scope
```

## Dependency Injection (ASP.NET Core)

```csharp
// ✅ Good - Constructor injection
public class UserController : ControllerBase
{
    private readonly IUserService _userService;
    private readonly ILogger<UserController> _logger;
    
    public UserController(
        IUserService userService,
        ILogger<UserController> logger)
    {
        _userService = userService;
        _logger = logger;
    }
    
    [HttpGet("{id}")]
    public async Task<ActionResult<UserDto>> GetUser(int id)
    {
        var user = await _userService.GetByIdAsync(id);
        if (user is null)
            return NotFound();
        
        return Ok(user);
    }
}

// Service registration
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddSingleton<ICacheService, CacheService>();
builder.Services.AddTransient<IEmailService, EmailService>();
```

## Testing with xUnit

```csharp
// ✅ Good - Modern xUnit tests
public class UserServiceTests
{
    private readonly Mock<IUserRepository> _repositoryMock;
    private readonly UserService _service;
    
    public UserServiceTests()
    {
        _repositoryMock = new Mock<IUserRepository>();
        _service = new UserService(_repositoryMock.Object);
    }
    
    [Fact]
    public async Task GetUserAsync_WhenUserExists_ReturnsUser()
    {
        // Arrange
        var userId = 1;
        var expectedUser = new User(userId, "John", "john@example.com");
        _repositoryMock
            .Setup(r => r.GetByIdAsync(userId))
            .ReturnsAsync(expectedUser);
        
        // Act
        var result = await _service.GetUserAsync(userId);
        
        // Assert
        Assert.NotNull(result);
        Assert.Equal("John", result.Name);
        _repositoryMock.Verify(r => r.GetByIdAsync(userId), Times.Once);
    }
    
    [Theory]
    [InlineData("")]
    [InlineData("invalid")]
    [InlineData("@example.com")]
    public void IsValidEmail_WithInvalidEmail_ReturnsFalse(string email)
    {
        var result = EmailValidator.IsValid(email);
        Assert.False(result);
    }
}

// FluentAssertions for readable assertions
result.Should().NotBeNull();
result.Name.Should().Be("John");
users.Should().HaveCount(3);
```

## Project Structure

```
MyApp/
├── src/
│   ├── MyApp.Api/
│   │   ├── Controllers/
│   │   ├── Middleware/
│   │   ├── Program.cs
│   │   └── appsettings.json
│   ├── MyApp.Core/
│   │   ├── Entities/
│   │   ├── Interfaces/
│   │   └── Services/
│   └── MyApp.Infrastructure/
│       ├── Data/
│       └── Repositories/
└── tests/
    ├── MyApp.Api.Tests/
    └── MyApp.Core.Tests/
```

## Code Analysis & Linting

**EditorConfig**
```ini
[*.cs]
# Indentation
indent_size = 4
indent_style = space

# New line preferences
end_of_line = crlf
insert_final_newline = true

# Naming conventions
dotnet_naming_rule.private_fields_underscored.symbols = private_fields
dotnet_naming_rule.private_fields_underscored.style = underscored
dotnet_naming_rule.private_fields_underscored.severity = warning

# Code style
csharp_prefer_braces = true:warning
csharp_using_directive_placement = outside_namespace:warning
```

**dotnet format**
```bash
# Format code
dotnet format

# Check formatting without modifying
dotnet format --verify-no-changes
```

## Common Anti-Patterns

❌ **String Concatenation in Loops**
```csharp
// ❌ Bad
string result = "";
foreach (var s in strings)
{
    result += s;
}

// ✅ Good
var result = string.Join("", strings);
// Or
var sb = new StringBuilder();
foreach (var s in strings)
{
    sb.Append(s);
}
```

❌ **Catching Generic Exceptions**
```csharp
// ❌ Bad
try
{
    DoSomething();
}
catch (Exception ex)  // Too broad
{
    // Handle
}

// ✅ Good
try
{
    DoSomething();
}
catch (ArgumentException ex)
{
    // Handle specific exception
}
catch (InvalidOperationException ex)
{
    // Handle different specific exception
}
```

❌ **Not Disposing Resources**
```csharp
// ❌ Bad
var stream = File.OpenRead("file.txt");
// ... use stream ...
stream.Dispose();  // Might not execute if exception

// ✅ Good
using var stream = File.OpenRead("file.txt");
// ... use stream ...
// Automatically disposed
```

## Performance Tips

- Use `Span<T>` and `Memory<T>` for performance-critical code
- Use `ValueTask<T>` for frequently called async methods
- Use `struct` for small, immutable types
- Use `readonly struct` when possible
- Use `ArrayPool<T>` for temporary arrays
- Profile with BenchmarkDotNet before optimizing

## Resources

- [C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [.NET Design Guidelines](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/)
- [ASP.NET Core Best Practices](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/best-practices)
