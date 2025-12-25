---
paths: "**/*.php"
---

#memory/language #php #best-practices

# PHP Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for PHP preferences:
> - `frameworks.php`: Preferred framework (Laravel, Symfony, CodeIgniter, Slim)
> - `formatter_php`: Code formatter (PHP-CS-Fixer, phpfmt)
> - `linter_php`: Linter (PHPStan, Psalm, PHP_CodeSniffer)
> - `test_framework_php`: Test framework (PHPUnit, Pest, Codeception)
> - `package_manager_php`: Package manager (Composer)

## PHP Philosophy (Modern PHP 7.4+/8+)

- **Strict types for safety**
- **Type declarations for clarity**
- **Composer for dependency management**
- **PSR standards for interoperability**
- **Modern OOP with strong typing**

## Code Style (PSR-12)

### Naming Conventions

```php
<?php

// ✅ Good - PSR-12 compliant
declare(strict_types=1);

namespace App\Services;

class UserService  // PascalCase for classes
{
    private const MAX_USERS = 100;  // UPPER_CASE for constants
    
    private string $connectionString;  // camelCase for properties
    
    public function getUserById(int $userId): ?User  // camelCase for methods
    {
        $localVariable = 'test';  // camelCase for variables
        return new User();
    }
}

// ❌ Bad
class user_service  // Wrong case
{
    function Get_User_By_Id($user_id)  // Wrong case
    {
        return null;
    }
}
```

### Type Declarations (PHP 7.4+/8+)

```php
<?php
declare(strict_types=1);

// ✅ Good - Strict types and declarations
class UserService
{
    public function __construct(
        private UserRepository $repository,  // PHP 8 constructor properties
        private LoggerInterface $logger,
    ) {}
    
    public function findById(int $id): ?User
    {
        return $this->repository->find($id);
    }
    
    public function getActiveUsers(): array
    {
        return $this->repository->findBy(['active' => true]);
    }
    
    // Union types (PHP 8)
    public function process(int|string $id): User
    {
        // ...
    }
}

// ❌ Bad - No type declarations
class UserService
{
    public function findById($id)  // No types
    {
        return $this->repository->find($id);
    }
}
```

### Nullable Types & Null Safe Operator

```php
// ✅ Good - Nullable types and null safe operator
public function getUserEmail(int $userId): ?string
{
    $user = $this->findById($userId);
    
    // PHP 8 null safe operator
    return $user?->getEmail();
}

// Traditional null check
if ($user !== null) {
    return $user->getEmail();
}
return null;
```

### Named Arguments (PHP 8)

```php
// ✅ Good - Named arguments for clarity
$user = new User(
    id: 1,
    name: 'John',
    email: 'john@example.com',
    isActive: true,
);

// Skip optional parameters
createUser(
    name: 'John',
    email: 'john@example.com',
    // Skip other optional params
);
```

### Match Expression (PHP 8)

```php
// ✅ Good - Match expression (strict comparison)
$message = match ($status) {
    'pending' => 'Order is pending',
    'approved' => 'Order approved',
    'rejected' => 'Order rejected',
    default => 'Unknown status',
};

// Pattern matching with conditions
$price = match (true) {
    $age < 18 => 10,
    $age < 65 => 20,
    default => 15,
};

// ❌ Bad - Switch with fall-through
switch ($status) {
    case 'pending':
        $message = 'Order is pending';
        break;  // Easy to forget
    default:
        $message = 'Unknown';
}
```

## Error Handling

```php
// ✅ Good - Custom exceptions
class UserNotFoundException extends \Exception
{
    public function __construct(int $userId)
    {
        parent::__construct("User not found: {$userId}");
    }
}

class ValidationException extends \Exception
{
    public function __construct(
        string $message,
        private array $errors = [],
    ) {
        parent::__construct($message);
    }
    
    public function getErrors(): array
    {
        return $this->errors;
    }
}

// Usage
public function getUser(int $id): User
{
    $user = $this->repository->find($id);
    
    if ($user === null) {
        throw new UserNotFoundException($id);
    }
    
    return $user;
}

// Try-catch
try {
    $user = $this->getUser(1);
} catch (UserNotFoundException $e) {
    $this->logger->error($e->getMessage());
    throw $e;
}
```

## Laravel Best Practices

### Eloquent Models

```php
// ✅ Good - Eloquent model
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class User extends Model
{
    protected $fillable = ['name', 'email'];
    protected $hidden = ['password'];
    
    protected $casts = [
        'email_verified_at' => 'datetime',
        'is_active' => 'boolean',
    ];
    
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }
    
    // Accessor (PHP 8+ attribute)
    #[Attribute]
    public function getFullNameAttribute(): string
    {
        return "{$this->first_name} {$this->last_name}";
    }
}
```

### Controllers

```php
// ✅ Good - Resourceful controller
namespace App\Http\Controllers;

use App\Http\Requests\CreateUserRequest;
use App\Services\UserService;
use Illuminate\Http\JsonResponse;

class UserController extends Controller
{
    public function __construct(
        private UserService $userService,
    ) {}
    
    public function show(int $id): JsonResponse
    {
        $user = $this->userService->findById($id);
        
        if ($user === null) {
            return response()->json(['error' => 'Not found'], 404);
        }
        
        return response()->json($user);
    }
    
    public function store(CreateUserRequest $request): JsonResponse
    {
        $user = $this->userService->create($request->validated());
        
        return response()->json($user, 201);
    }
}
```

### Service Layer

```php
// ✅ Good - Service layer for business logic
namespace App\Services;

use App\Models\User;
use App\Repositories\UserRepository;
use Illuminate\Support\Facades\DB;

class UserService
{
    public function __construct(
        private UserRepository $repository,
    ) {}
    
    public function create(array $data): User
    {
        return DB::transaction(function () use ($data) {
            $user = $this->repository->create($data);
            
            // Additional business logic
            event(new UserCreated($user));
            
            return $user;
        });
    }
}
```

## Testing with PHPUnit

```php
// ✅ Good - PHPUnit tests
namespace Tests\Feature;

use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserServiceTest extends TestCase
{
    use RefreshDatabase;
    
    public function test_can_create_user(): void
    {
        // Arrange
        $data = [
            'name' => 'John Doe',
            'email' => 'john@example.com',
        ];
        
        // Act
        $user = $this->userService->create($data);
        
        // Assert
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
        $this->assertEquals('John Doe', $user->name);
    }
    
    public function test_throws_exception_when_user_not_found(): void
    {
        $this->expectException(UserNotFoundException::class);
        
        $this->userService->getUser(999);
    }
}
```

## Static Analysis (PHPStan/Psalm)

```php
// PHPStan configuration (phpstan.neon)
parameters:
    level: 8  // Strictest level
    paths:
        - src
        - tests

// Use PHPDoc for additional type info
/**
 * @param array<int, User> $users
 * @return array<string, int>
 */
function processUsers(array $users): array
{
    $result = [];
    foreach ($users as $user) {
        $result[$user->name] = $user->id;
    }
    return $result;
}
```

## Code Formatting (PHP-CS-Fixer)

```php
// .php-cs-fixer.php
<?php

$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__)
    ->exclude('vendor');

return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => true,
        'no_unused_imports' => true,
    ])
    ->setFinder($finder);
```

## Common Anti-Patterns

❌ **Not Using Strict Types**
```php
// ❌ Bad
function add($a, $b) {
    return $a + $b;
}
add('5', 3);  // Returns 8, but should error

// ✅ Good
declare(strict_types=1);

function add(int $a, int $b): int {
    return $a + $b;
}
```

❌ **Using Old Array Syntax**
```php
// ❌ Bad
$users = array(1, 2, 3);

// ✅ Good
$users = [1, 2, 3];
```

❌ **Not Validating Input**
```php
// ❌ Bad
public function store(Request $request)
{
    User::create($request->all());  // No validation!
}

// ✅ Good
public function store(CreateUserRequest $request)
{
    User::create($request->validated());
}
```

## Performance Tips

- Use opcache in production
- Use eager loading to avoid N+1 queries
- Cache database queries
- Use queue for long-running tasks
- Profile with Blackfire or Xdebug

## Resources

- [PSR-12: Extended Coding Style](https://www.php-fig.org/psr/psr-12/)
- [PHP: The Right Way](https://phptherightway.com/)
- [Laravel Best Practices](https://github.com/alexeymezenin/laravel-best-practices)
