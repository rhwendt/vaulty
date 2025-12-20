#memory/language #java #best-practices

# Java Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Java preferences:
> - `frameworks.java`: Preferred framework (Spring Boot, Quarkus, Micronaut, Jakarta EE)
> - `formatter_java`: Code formatter (google-java-format, Eclipse formatter)
> - `linter_java`: Linter (checkstyle, PMD, SpotBugs)
> - `test_framework_java`: Test framework (JUnit 5, TestNG, Spock)
> - `package_manager_java`: Build tool (Maven, Gradle)

## Java Philosophy

- **Write once, run anywhere (WORA)**
- **Strong static typing**
- **Object-oriented with functional features (Java 8+)**
- **Explicit over implicit**
- **Backward compatibility matters**

## Code Style & Formatting

### Naming Conventions

```java
// ✅ Good - Java naming conventions
public class UserService {  // PascalCase for classes
    private static final int MAX_USERS = 100;  // UPPER_CASE for constants
    
    private String userName;  // camelCase for fields
    
    public User getUserById(int userId) {  // camelCase for methods
        String localVariable = "test";  // camelCase for local variables
        return new User();
    }
}

// Package names: lowercase
package com.example.myapp.services;

// ❌ Bad
public class user_service {  // Wrong case
    public User Get_User_By_Id(int user_id) {  // Wrong case
        return null;
    }
}
```

### Modern Java Features (Java 8+)

**Streams API**
```java
// ✅ Good - Use Streams for collections
List<String> names = users.stream()
    .filter(user -> user.isActive())
    .map(User::getName)
    .sorted()
    .collect(Collectors.toList());

// ❌ Bad - Verbose imperative style
List<String> names = new ArrayList<>();
for (User user : users) {
    if (user.isActive()) {
        names.add(user.getName());
    }
}
Collections.sort(names);
```

**Optional Instead of Null**
```java
// ✅ Good - Use Optional to avoid null
public Optional<User> findUserById(Long id) {
    User user = repository.findById(id);
    return Optional.ofNullable(user);
}

// Usage
Optional<User> userOpt = findUserById(1L);
userOpt.ifPresent(user -> System.out.println(user.getName()));

String name = userOpt
    .map(User::getName)
    .orElse("Unknown");

// ❌ Bad - Returning null
public User findUserById(Long id) {
    return null;  // Don't do this
}
```

**Lambda Expressions**
```java
// ✅ Good - Use lambdas for functional interfaces
List<User> activeUsers = users.stream()
    .filter(user -> user.isActive())
    .collect(Collectors.toList());

// Method references
users.forEach(System.out::println);
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.toList());

// ❌ Bad - Anonymous classes (pre-Java 8)
Collections.sort(users, new Comparator<User>() {
    @Override
    public int compare(User u1, User u2) {
        return u1.getName().compareTo(u2.getName());
    }
});
```

**Records (Java 14+)**
```java
// ✅ Good - Use records for immutable data
public record User(Long id, String name, String email) {
    // Compact constructor for validation
    public User {
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }
}

// Automatic equals(), hashCode(), toString(), getters
User user = new User(1L, "John", "john@example.com");
System.out.println(user.name());  // Getter

// ❌ Bad - Boilerplate POJO
public class User {
    private final Long id;
    private final String name;
    private final String email;
    
    public User(Long id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }
    
    // ... getters, equals, hashCode, toString
}
```

**Var (Java 10+)**
```java
// ✅ Good - Use var for obvious types
var users = new ArrayList<User>();
var name = getUserName();
var config = Config.builder().build();

// ❌ Bad - Overusing var where type isn't obvious
var result = process();  // What type is this?

// ❌ Don't use var for primitives or when type aids readability
var count = 10;  // Just use: int count = 10;
```

## Error Handling

**Checked vs Unchecked Exceptions**
```java
// ✅ Good - Custom exceptions
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

public class ValidationException extends RuntimeException {
    private final List<String> errors;
    
    public ValidationException(List<String> errors) {
        super("Validation failed");
        this.errors = errors;
    }
    
    public List<String> getErrors() {
        return errors;
    }
}

// Usage
public User getUser(Long id) {
    return repository.findById(id)
        .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
}
```

**Try-with-Resources**
```java
// ✅ Good - Automatic resource management
public String readFile(String path) throws IOException {
    try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
        return reader.lines()
            .collect(Collectors.joining("\n"));
    }
    // Reader automatically closed
}

// Multiple resources
try (var input = new FileInputStream("input.txt");
     var output = new FileOutputStream("output.txt")) {
    // Use resources
}

// ❌ Bad - Manual resource management
BufferedReader reader = new BufferedReader(new FileReader(path));
try {
    return reader.readLine();
} finally {
    reader.close();
}
```

## Spring Boot Patterns

**Dependency Injection**
```java
// ✅ Good - Constructor injection (preferred)
@Service
public class UserService {
    private final UserRepository repository;
    private final EmailService emailService;
    
    public UserService(UserRepository repository, EmailService emailService) {
        this.repository = repository;
        this.emailService = emailService;
    }
}

// For single dependency, can use Lombok
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository repository;
}

// ❌ Bad - Field injection
@Service
public class UserService {
    @Autowired  // Avoid this
    private UserRepository repository;
}
```

**REST Controllers**
```java
// ✅ Good - RESTful controller
@RestController
@RequestMapping("/api/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;
    
    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<UserDTO> createUser(@Valid @RequestBody CreateUserRequest request) {
        UserDTO user = userService.create(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(user);
    }
    
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse(ex.getMessage()));
    }
}
```

**Configuration**
```java
// ✅ Good - Type-safe configuration
@Configuration
@ConfigurationProperties(prefix = "app")
@Data
public class AppConfig {
    private String name;
    private int maxConnections;
    private Duration timeout;
}

// application.yml
// app:
//   name: MyApp
//   max-connections: 100
//   timeout: 30s
```

## Testing with JUnit 5

**Test Structure**
```java
// ✅ Good - Modern JUnit 5 tests
@DisplayName("User Service Tests")
class UserServiceTest {
    
    @Mock
    private UserRepository repository;
    
    @InjectMocks
    private UserService service;
    
    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }
    
    @Test
    @DisplayName("Should create user successfully")
    void shouldCreateUser() {
        // Given
        CreateUserRequest request = new CreateUserRequest("John", "john@example.com");
        User savedUser = new User(1L, "John", "john@example.com");
        when(repository.save(any(User.class))).thenReturn(savedUser);
        
        // When
        User result = service.create(request);
        
        // Then
        assertThat(result.getId()).isEqualTo(1L);
        assertThat(result.getName()).isEqualTo("John");
        verify(repository).save(any(User.class));
    }
    
    @Test
    void shouldThrowExceptionWhenUserNotFound() {
        when(repository.findById(1L)).thenReturn(Optional.empty());
        
        assertThrows(UserNotFoundException.class, () -> service.getUser(1L));
    }
    
    @ParameterizedTest
    @ValueSource(strings = {"", "invalid", "@example.com"})
    void shouldRejectInvalidEmails(String email) {
        assertFalse(EmailValidator.isValid(email));
    }
}
```

**Integration Tests**
```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Autowired
    private ObjectMapper objectMapper;
    
    @Test
    void shouldCreateUserViaAPI() throws Exception {
        CreateUserRequest request = new CreateUserRequest("John", "john@example.com");
        
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.name").value("John"));
    }
}
```

## Package Organization

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── myapp/
│   │               ├── MyAppApplication.java
│   │               ├── config/
│   │               ├── controller/
│   │               ├── service/
│   │               ├── repository/
│   │               ├── model/
│   │               │   ├── entity/
│   │               │   └── dto/
│   │               └── exception/
│   └── resources/
│       ├── application.yml
│       └── application-prod.yml
└── test/
    └── java/
        └── com/
            └── example/
                └── myapp/
```

## Linting & Formatting

**Checkstyle Configuration**
```xml
<!-- checkstyle.xml -->
<module name="Checker">
    <module name="LineLength">
        <property name="max" value="120"/>
    </module>
    <module name="TreeWalker">
        <module name="NeedBraces"/>
        <module name="LeftCurly"/>
        <module name="RightCurly"/>
    </module>
</module>
```

**PMD Rules**
```xml
<ruleset>
    <rule ref="category/java/bestpractices.xml"/>
    <rule ref="category/java/codestyle.xml"/>
    <rule ref="category/java/design.xml"/>
</ruleset>
```

## Common Anti-Patterns

❌ **Raw Types**
```java
// ❌ Bad
List list = new ArrayList();
list.add("string");
list.add(123);

// ✅ Good
List<String> list = new ArrayList<>();
list.add("string");
```

❌ **String Concatenation in Loops**
```java
// ❌ Bad
String result = "";
for (String s : strings) {
    result += s;  // Creates new String each iteration
}

// ✅ Good
StringBuilder result = new StringBuilder();
for (String s : strings) {
    result.append(s);
}
```

❌ **Ignoring Exceptions**
```java
// ❌ Bad
try {
    riskyOperation();
} catch (Exception e) {
    // Ignored
}

// ✅ Good
try {
    riskyOperation();
} catch (SpecificException e) {
    logger.error("Operation failed", e);
    throw new ServiceException("Failed to process", e);
}
```

## Performance Tips

- Use `StringBuilder` for string concatenation
- Use primitive types when possible (int vs Integer)
- Use `EnumSet` and `EnumMap` for enum collections
- Prefer `ArrayList` over `LinkedList` for most cases
- Use lazy initialization when appropriate
- Profile with JProfiler or VisualVM before optimizing

## Resources

- [Java Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html)
- [Effective Java by Joshua Bloch](https://www.oreilly.com/library/view/effective-java/9780134686097/)
- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- [Spring Boot Best Practices](https://spring.io/guides)
