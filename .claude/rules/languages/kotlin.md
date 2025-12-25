---
paths: "**/*.kt, **/*.kts"
---

#memory/language #kotlin #best-practices

# Kotlin Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Kotlin preferences:
> - `frameworks.kotlin`: Preferred framework (Spring Boot, Ktor, Android Jetpack Compose)
> - `formatter_kotlin`: Code formatter (ktlint, kotlinter)
> - `linter_kotlin`: Linter (ktlint, detekt)
> - `test_framework_kotlin`: Test framework (JUnit 5, Kotest, Spek)
> - `package_manager_kotlin`: Build tool (Gradle, Maven)

## Kotlin Philosophy

- **Concise and expressive**
- **Null safety built-in**
- **Functional and object-oriented**
- **100% interoperable with Java**
- **Pragmatic over dogmatic**

## Code Style & Formatting

### Naming Conventions

```kotlin
// ✅ Good - Kotlin naming conventions
class UserService {  // PascalCase for classes
    companion object {
        const val MAX_USERS = 100  // UPPER_CASE for compile-time constants
        val defaultName = "Unknown"  // camelCase for runtime constants
    }
    
    private val userCount: Int = 0  // camelCase for properties
    
    fun getUserById(userId: Int): User? {  // camelCase for functions
        val localVariable = "test"  // camelCase for variables
        return User()
    }
}

interface UserRepository {  // PascalCase for interfaces (no "I" prefix)
    fun findById(id: Int): User?
}

sealed class Result {  // PascalCase for sealed classes
    data class Success(val data: String) : Result()
    data class Error(val exception: Exception) : Result()
}

// ❌ Bad
class user_service {  // Wrong case
    fun Get_User_By_Id(user_id: Int): User? {  // Wrong case
        return null
    }
}
```

### Null Safety

```kotlin
// ✅ Good - Leverage Kotlin's null safety
fun findUser(id: Int): User? {  // Nullable return type
    return repository.find(id)
}

// Safe call operator
val email = user?.email

// Elvis operator
val name = user?.name ?: "Unknown"

// Safe cast
val admin = user as? Admin

// Let function for null checks
user?.let { u ->
    println(u.name)
}

// Require/check for validation
fun processUser(user: User?) {
    requireNotNull(user) { "User cannot be null" }
    check(user.isActive) { "User must be active" }
}

// ❌ Bad - Force not-null assertion (use sparingly)
val user = findUser(1)!!  // Can throw NullPointerException
```

### Data Classes

```kotlin
// ✅ Good - Use data classes for DTOs
data class User(
    val id: Int,
    val name: String,
    val email: String,
    val isActive: Boolean = true  // Default parameter
) {
    // Additional methods
    fun displayName(): String = name.uppercase()
}

// Automatic equals(), hashCode(), toString(), copy()
val user1 = User(1, "John", "john@example.com")
val user2 = user1.copy(name = "Jane")  // Copy with modification

// Destructuring
val (id, name, email) = user1
```

### Sealed Classes for Type-Safe States

```kotlin
// ✅ Good - Sealed classes for restricted hierarchies
sealed class NetworkResult<out T> {
    data class Success<T>(val data: T) : NetworkResult<T>()
    data class Error(val exception: Exception) : NetworkResult<Nothing>()
    object Loading : NetworkResult<Nothing>()
}

// Exhaustive when expression
fun handleResult(result: NetworkResult<User>) {
    when (result) {
        is NetworkResult.Success -> println(result.data.name)
        is NetworkResult.Error -> println("Error: ${result.exception}")
        is NetworkResult.Loading -> println("Loading...")
    }  // Compiler ensures all cases covered
}
```

### Extension Functions

```kotlin
// ✅ Good - Extension functions for utility methods
fun String.isValidEmail(): Boolean {
    return this.contains("@") && this.contains(".")
}

fun <T> List<T>.secondOrNull(): T? {
    return if (this.size >= 2) this[1] else null
}

// Usage
val isValid = "test@example.com".isValidEmail()
val second = listOf(1, 2, 3).secondOrNull()
```

### Higher-Order Functions & Lambdas

```kotlin
// ✅ Good - Functional programming patterns
val numbers = listOf(1, 2, 3, 4, 5)

// Map, filter, reduce
val doubled = numbers.map { it * 2 }
val evens = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, n -> acc + n }

// Function types
fun processUsers(users: List<User>, predicate: (User) -> Boolean): List<User> {
    return users.filter(predicate)
}

val activeUsers = processUsers(users) { it.isActive }

// Trailing lambda syntax
users.filter { it.isActive }
    .map { it.name }
    .forEach { println(it) }
```

### Coroutines for Async Programming

```kotlin
// ✅ Good - Coroutines for asynchronous code
suspend fun fetchUser(id: Int): User {
    return withContext(Dispatchers.IO) {
        api.getUser(id)
    }
}

// Launching coroutines
fun loadUsers() {
    viewModelScope.launch {
        try {
            val users = fetchUsers()
            _usersState.value = users
        } catch (e: Exception) {
            _errorState.value = e.message
        }
    }
}

// Parallel execution
suspend fun loadMultipleUsers(ids: List<Int>): List<User> {
    return coroutineScope {
        ids.map { id ->
            async { fetchUser(id) }
        }.awaitAll()
    }
}

// Flows for streams
fun observeUsers(): Flow<List<User>> = flow {
    while (true) {
        val users = fetchUsers()
        emit(users)
        delay(5000)
    }
}
```

## Object-Oriented Programming

**Companion Objects**
```kotlin
// ✅ Good - Companion objects for static members
class User(val id: Int, val name: String) {
    companion object {
        const val DEFAULT_NAME = "Unknown"
        
        fun create(name: String): User {
            return User(generateId(), name)
        }
        
        private fun generateId(): Int = Random.nextInt()
    }
}

// Usage
val user = User.create("John")
```

**Delegation**
```kotlin
// ✅ Good - Property delegation
class User {
    val lazyValue: String by lazy {
        println("Computed!")
        "Hello"
    }
    
    var observedValue: String by Delegates.observable("Initial") { 
        _, old, new -> println("$old -> $new")
    }
}

// Class delegation
interface Repository {
    fun save(data: String)
}

class LoggingRepository(private val repository: Repository) : Repository by repository {
    override fun save(data: String) {
        println("Saving: $data")
        repository.save(data)
    }
}
```

## Spring Boot / Ktor Patterns

**Spring Boot Service**
```kotlin
// ✅ Good - Spring Boot with Kotlin
@Service
class UserService(
    private val repository: UserRepository,
    private val emailService: EmailService
) {
    fun createUser(request: CreateUserRequest): User {
        val user = User(
            name = request.name,
            email = request.email
        )
        
        return repository.save(user).also {
            emailService.sendWelcome(it)
        }
    }
    
    fun findById(id: Int): User? {
        return repository.findByIdOrNull(id)
    }
}

@RestController
@RequestMapping("/api/users")
class UserController(private val service: UserService) {
    
    @GetMapping("/{id}")
    fun getUser(@PathVariable id: Int): ResponseEntity<User> {
        return service.findById(id)
            ?.let { ResponseEntity.ok(it) }
            ?: ResponseEntity.notFound().build()
    }
    
    @PostMapping
    fun createUser(@Valid @RequestBody request: CreateUserRequest): User {
        return service.createUser(request)
    }
}
```

**Ktor Application**
```kotlin
// ✅ Good - Ktor with coroutines
fun Application.module() {
    routing {
        route("/api/users") {
            get("/{id}") {
                val id = call.parameters["id"]?.toIntOrNull()
                    ?: return@get call.respond(HttpStatusCode.BadRequest)
                
                val user = userService.findById(id)
                    ?: return@get call.respond(HttpStatusCode.NotFound)
                
                call.respond(user)
            }
            
            post {
                val request = call.receive<CreateUserRequest>()
                val user = userService.createUser(request)
                call.respond(HttpStatusCode.Created, user)
            }
        }
    }
}
```

## Testing with Kotest / JUnit

```kotlin
// ✅ Good - Kotest tests
class UserServiceTest : StringSpec({
    val repository = mockk<UserRepository>()
    val service = UserService(repository)
    
    "should create user successfully" {
        // Given
        val request = CreateUserRequest("John", "john@example.com")
        val savedUser = User(1, "John", "john@example.com")
        every { repository.save(any()) } returns savedUser
        
        // When
        val result = service.createUser(request)
        
        // Then
        result.name shouldBe "John"
        verify { repository.save(any()) }
    }
    
    "should throw exception when user not found" {
        every { repository.findById(999) } returns null
        
        shouldThrow<UserNotFoundException> {
            service.getUserOrThrow(999)
        }
    }
})

// JUnit 5 style
@Test
fun `should create user successfully`() {
    // Given
    val request = CreateUserRequest("John", "john@example.com")
    
    // When
    val result = service.createUser(request)
    
    // Then
    assertEquals("John", result.name)
}
```

## Code Quality (ktlint / detekt)

**ktlint configuration**
```kotlin
// .editorconfig
[*.{kt,kts}]
indent_size = 4
max_line_length = 120
insert_final_newline = true
```

```bash
# Run ktlint
./gradlew ktlintCheck

# Auto-format
./gradlew ktlintFormat
```

**detekt configuration**
```yaml
# detekt.yml
complexity:
  LongMethod:
    threshold: 30
  TooManyFunctions:
    threshold: 20
```

## Common Anti-Patterns

❌ **Not Using Scope Functions**
```kotlin
// ❌ Bad
val user = User()
user.name = "John"
user.email = "john@example.com"
return user

// ✅ Good
return User().apply {
    name = "John"
    email = "john@example.com"
}
```

❌ **Not Using Sealed Classes for States**
```kotlin
// ❌ Bad
data class State(
    val isLoading: Boolean,
    val data: List<User>?,
    val error: String?
)

// ✅ Good
sealed class State {
    object Loading : State()
    data class Success(val data: List<User>) : State()
    data class Error(val message: String) : State()
}
```

❌ **Using !! Operator**
```kotlin
// ❌ Bad
val user = findUser(1)!!  // Can crash

// ✅ Good
val user = findUser(1) ?: return
// Or
val user = requireNotNull(findUser(1)) { "User required" }
```

## Performance Tips

- Use `inline` functions for higher-order functions
- Use `sequence` for lazy collection operations
- Use `data class` for better equals()/hashCode()
- Profile with Android Studio Profiler or IntelliJ Profiler
- Use `@JvmStatic` for frequent Java interop

## Resources

- [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html)
- [Kotlin Style Guide by Android](https://developer.android.com/kotlin/style-guide)
- [Effective Kotlin by Marcin Moskała](https://kt.academy/book/effectivekotlin)
- [ktlint](https://github.com/pinterest/ktlint)
