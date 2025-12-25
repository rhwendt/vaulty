#memory/language #typescript #best-practices

# TypeScript Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for TypeScript preferences:
> - `frameworks.typescript`: Preferred TS framework (React, Angular, NestJS, Next.js, etc.)
> - `formatter_typescript`: Code formatter (prettier, dprint)
> - `linter_typescript`: Linter (eslint with typescript-eslint)
> - `test_framework_typescript`: Test framework (jest, vitest, mocha)
> - `package_manager_typescript`: Package manager (npm, yarn, pnpm)

## TypeScript Philosophy

- **Type safety without sacrificing JavaScript flexibility**: Gradual typing system
- **Compile-time error detection**: Catch bugs before runtime
- **Enhanced IDE support**: Better autocomplete, refactoring, and navigation
- **JavaScript superset**: All valid JavaScript is valid TypeScript
- **Scales from small to large projects**: Incremental adoption possible

## Idiomatic TypeScript Patterns

### Type Annotations and Inference

**Let TypeScript Infer When Obvious**
```typescript
// ✅ Good - Let TypeScript infer obvious types
const count = 5;  // inferred as number
const message = 'Hello';  // inferred as string
const isActive = true;  // inferred as boolean

const numbers = [1, 2, 3];  // inferred as number[]
const user = { name: 'Alice', age: 30 };  // inferred as { name: string, age: number }

// ✅ Good - Annotate when inference isn't clear
function getUser(id: number): User {
    // Return type annotation helps catch errors
    return fetchUser(id);
}

// Function parameters always need annotations
function greet(name: string, age: number): void {
    console.log(`Hello ${name}, you are ${age} years old`);
}

// ❌ Bad - Redundant type annotations
const count: number = 5;
const message: string = 'Hello';
```

### Interface vs Type

**When to Use Each**
```typescript
// ✅ Good - Use interface for object shapes
interface User {
    id: number;
    name: string;
    email: string;
}

// Interfaces can be extended
interface Admin extends User {
    role: string;
    permissions: string[];
}

// Interfaces can be merged (declaration merging)
interface Window {
    myCustomProperty: string;
}

// ✅ Good - Use type for unions, intersections, and primitives
type ID = string | number;
type Status = 'pending' | 'active' | 'inactive';

type Point = { x: number; y: number };
type Shape = Circle | Square | Triangle;

// Intersection types
type Admin = User & { role: string; permissions: string[] };

// Type aliases for complex types
type AsyncFunction<T> = (...args: any[]) => Promise<T>;

// ✅ Guideline: Prefer interface for public API, type for internal usage
```

### Advanced Type Features

**Union and Intersection Types**
```typescript
// ✅ Good - Union types
type Result<T> =
    | { success: true; data: T }
    | { success: false; error: string };

function handleResult<T>(result: Result<T>): void {
    if (result.success) {
        console.log('Data:', result.data);  // TypeScript knows data exists
    } else {
        console.log('Error:', result.error);  // TypeScript knows error exists
    }
}

// ✅ Good - Discriminated unions
type Shape =
    | { kind: 'circle'; radius: number }
    | { kind: 'square'; size: number }
    | { kind: 'rectangle'; width: number; height: number };

function getArea(shape: Shape): number {
    switch (shape.kind) {
        case 'circle':
            return Math.PI * shape.radius ** 2;
        case 'square':
            return shape.size ** 2;
        case 'rectangle':
            return shape.width * shape.height;
    }
}

// ✅ Good - Intersection types
type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type User = {
    id: number;
    name: string;
} & Timestamped;

const user: User = {
    id: 1,
    name: 'Alice',
    createdAt: new Date(),
    updatedAt: new Date(),
};
```

**Generics**
```typescript
// ✅ Good - Generic functions
function identity<T>(value: T): T {
    return value;
}

const num = identity(5);  // T inferred as number
const str = identity('hello');  // T inferred as string

// ✅ Good - Generic constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const user = { name: 'Alice', age: 30 };
const name = getProperty(user, 'name');  // Type: string
const age = getProperty(user, 'age');  // Type: number

// ✅ Good - Generic interfaces
interface Repository<T> {
    getById(id: number): Promise<T>;
    getAll(): Promise<T[]>;
    create(item: T): Promise<T>;
    update(id: number, item: Partial<T>): Promise<T>;
    delete(id: number): Promise<void>;
}

class UserRepository implements Repository<User> {
    async getById(id: number): Promise<User> {
        // implementation
    }
    // ... other methods
}

// ✅ Good - Generic constraints with default
function merge<T extends object = {}, U extends object = {}>(
    obj1: T,
    obj2: U
): T & U {
    return { ...obj1, ...obj2 };
}
```

**Utility Types**
```typescript
interface User {
    id: number;
    name: string;
    email: string;
    age: number;
    address?: string;
}

// ✅ Good - Partial makes all properties optional
type PartialUser = Partial<User>;
function updateUser(id: number, updates: Partial<User>): void {
    // Can update any subset of properties
}

// ✅ Good - Required makes all properties required
type RequiredUser = Required<User>;  // address is now required

// ✅ Good - Pick selects specific properties
type UserPreview = Pick<User, 'id' | 'name'>;

// ✅ Good - Omit excludes specific properties
type UserWithoutEmail = Omit<User, 'email'>;

// ✅ Good - Record creates object type
type UserRole = 'admin' | 'user' | 'guest';
type RolePermissions = Record<UserRole, string[]>;

const permissions: RolePermissions = {
    admin: ['read', 'write', 'delete'],
    user: ['read', 'write'],
    guest: ['read'],
};

// ✅ Good - ReturnType extracts function return type
function getUser() {
    return { id: 1, name: 'Alice' };
}

type User = ReturnType<typeof getUser>;  // { id: number, name: string }

// ✅ Good - Readonly makes properties immutable
type ReadonlyUser = Readonly<User>;

const user: ReadonlyUser = { id: 1, name: 'Alice', email: 'alice@example.com', age: 30 };
user.name = 'Bob';  // Error: Cannot assign to 'name' because it is a read-only property
```

### Type Guards and Narrowing

**Built-in Type Guards**
```typescript
// ✅ Good - typeof type guards
function process(value: string | number): void {
    if (typeof value === 'string') {
        console.log(value.toUpperCase());  // TypeScript knows it's string
    } else {
        console.log(value.toFixed(2));  // TypeScript knows it's number
    }
}

// ✅ Good - instanceof type guards
class Dog {
    bark() { console.log('Woof!'); }
}

class Cat {
    meow() { console.log('Meow!'); }
}

function makeSound(animal: Dog | Cat): void {
    if (animal instanceof Dog) {
        animal.bark();
    } else {
        animal.meow();
    }
}

// ✅ Good - in operator type guard
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird): void {
    if ('swim' in animal) {
        animal.swim();
    } else {
        animal.fly();
    }
}
```

**Custom Type Guards**
```typescript
// ✅ Good - User-defined type guards
interface User {
    type: 'user';
    name: string;
}

interface Admin {
    type: 'admin';
    name: string;
    permissions: string[];
}

function isAdmin(user: User | Admin): user is Admin {
    return user.type === 'admin';
}

function greet(user: User | Admin): void {
    if (isAdmin(user)) {
        console.log(`Admin ${user.name} with permissions: ${user.permissions.join(', ')}`);
    } else {
        console.log(`User ${user.name}`);
    }
}

// ✅ Good - Assertion functions
function assertIsDefined<T>(value: T): asserts value is NonNullable<T> {
    if (value === undefined || value === null) {
        throw new Error('Value must be defined');
    }
}

function processUser(user: User | null): void {
    assertIsDefined(user);
    // TypeScript knows user is not null here
    console.log(user.name);
}
```

## Error Handling

### Type-Safe Error Handling

**Result Types**
```typescript
// ✅ Good - Result type for error handling
type Result<T, E = Error> =
    | { ok: true; value: T }
    | { ok: false; error: E };

function parseJSON<T>(json: string): Result<T> {
    try {
        const value = JSON.parse(json) as T;
        return { ok: true, value };
    } catch (error) {
        return { ok: false, error: error as Error };
    }
}

// Usage
const result = parseJSON<User>('{"name": "Alice"}');
if (result.ok) {
    console.log(result.value.name);
} else {
    console.error(result.error.message);
}
```

**Custom Error Classes**
```typescript
// ✅ Good - Type-safe custom errors
class ValidationError extends Error {
    constructor(
        public field: string,
        message: string
    ) {
        super(message);
        this.name = 'ValidationError';
    }
}

class DatabaseError extends Error {
    constructor(
        public query: string,
        message: string
    ) {
        super(message);
        this.name = 'DatabaseError';
    }
}

function validateUser(user: unknown): User {
    if (typeof user !== 'object' || user === null) {
        throw new ValidationError('user', 'User must be an object');
    }

    const u = user as Record<string, unknown>;

    if (typeof u.name !== 'string') {
        throw new ValidationError('name', 'Name must be a string');
    }

    return u as User;
}

// Catching typed errors
try {
    validateUser(data);
} catch (error) {
    if (error instanceof ValidationError) {
        console.error(`Validation failed for ${error.field}: ${error.message}`);
    } else {
        throw error;
    }
}
```

**Async Error Handling**
```typescript
// ✅ Good - Type-safe async/await
async function fetchUser(id: number): Promise<User> {
    try {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) {
            throw new Error(`HTTP error: ${response.status}`);
        }
        const data: unknown = await response.json();
        return validateUser(data);
    } catch (error) {
        if (error instanceof ValidationError) {
            throw new Error(`Invalid user data: ${error.message}`);
        }
        throw error;
    }
}

// ✅ Good - Result type with async
async function fetchUserSafe(id: number): Promise<Result<User>> {
    try {
        const user = await fetchUser(id);
        return { ok: true, value: user };
    } catch (error) {
        return { ok: false, error: error as Error };
    }
}
```

## Code Organization

### Project Structure

```
myproject/
├── src/
│   ├── types/
│   │   ├── index.ts
│   │   ├── user.ts
│   │   └── api.ts
│   ├── services/
│   │   ├── UserService.ts
│   │   └── ApiService.ts
│   ├── utils/
│   │   ├── validation.ts
│   │   └── formatting.ts
│   ├── models/
│   │   └── User.ts
│   ├── config/
│   │   └── constants.ts
│   └── index.ts
├── tests/
│   ├── services/
│   └── utils/
├── tsconfig.json
├── package.json
└── README.md
```

### Module Organization

```typescript
// types/user.ts - Export types
export interface User {
    id: number;
    name: string;
    email: string;
}

export type UserRole = 'admin' | 'user' | 'guest';

export interface CreateUserDTO {
    name: string;
    email: string;
    role?: UserRole;
}

// types/index.ts - Barrel export
export * from './user';
export * from './api';

// services/UserService.ts - Implementation
import { User, CreateUserDTO } from '../types';

export class UserService {
    async getUser(id: number): Promise<User> {
        // implementation
    }

    async createUser(data: CreateUserDTO): Promise<User> {
        // implementation
    }
}

// index.ts - Main exports
export { UserService } from './services/UserService';
export * from './types';
```

### Naming Conventions

```typescript
// ✅ Good - Clear naming conventions

// Interfaces and Types: PascalCase
interface UserRepository {}
type UserStatus = 'active' | 'inactive';

// Classes: PascalCase
class UserService {}

// Functions and variables: camelCase
function getUserById(id: number): User {}
const userName = 'Alice';

// Constants: UPPER_SNAKE_CASE
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';

// Enum: PascalCase for enum, UPPER_CASE for values
enum UserRole {
    ADMIN = 'ADMIN',
    USER = 'USER',
    GUEST = 'GUEST',
}

// Generic type parameters: Single uppercase letter or PascalCase
function identity<T>(value: T): T {}
function merge<TSource, TTarget>(source: TSource, target: TTarget) {}

// Private properties: prefix with #or underscore
class User {
    #privateField: string;
    private _internalState: number;

    public name: string;

    private _privateMethod(): void {}
}
```

## Language-Specific Features

### Strict Mode Configuration

```json
// tsconfig.json - ✅ Good strict configuration
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "ESNext",
        "lib": ["ES2020", "DOM"],
        "outDir": "./dist",
        "rootDir": "./src",

        // Strict Type Checking
        "strict": true,
        "noImplicitAny": true,
        "strictNullChecks": true,
        "strictFunctionTypes": true,
        "strictBindCallApply": true,
        "strictPropertyInitialization": true,
        "noImplicitThis": true,
        "alwaysStrict": true,

        // Additional Checks
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noImplicitReturns": true,
        "noFallthroughCasesInSwitch": true,
        "noUncheckedIndexedAccess": true,

        // Module Resolution
        "moduleResolution": "node",
        "esModuleInterop": true,
        "resolveJsonModule": true,
        "isolatedModules": true,

        // Other
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules", "dist"]
}
```

### Enums vs Union Types

```typescript
// ✅ Good - Use const enum for compile-time constants
const enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT',
}

function move(direction: Direction): void {
    // ...
}

move(Direction.Up);

// ✅ Better - Use union types (more flexible)
type Direction = 'UP' | 'DOWN' | 'LEFT' | 'RIGHT';

function move(direction: Direction): void {
    // ...
}

move('UP');  // Simpler, no enum import needed

// ✅ Good - Use as const for readonly objects
const DIRECTIONS = {
    Up: 'UP',
    Down: 'DOWN',
    Left: 'LEFT',
    Right: 'RIGHT',
} as const;

type Direction = typeof DIRECTIONS[keyof typeof DIRECTIONS];
```

### Decorators (Experimental)

```typescript
// ✅ Good - Class decorators
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}

@sealed
class User {
    constructor(public name: string) {}
}

// ✅ Good - Method decorators
function log(
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
) {
    const originalMethod = descriptor.value;

    descriptor.value = function (...args: any[]) {
        console.log(`Calling ${propertyKey} with`, args);
        const result = originalMethod.apply(this, args);
        console.log(`Result:`, result);
        return result;
    };

    return descriptor;
}

class Calculator {
    @log
    add(a: number, b: number): number {
        return a + b;
    }
}

// ✅ Good - Property decorators
function readonly(target: any, propertyKey: string) {
    Object.defineProperty(target, propertyKey, {
        writable: false,
    });
}

class User {
    @readonly
    id: number;

    constructor(id: number) {
        this.id = id;
    }
}
```

### Mapped Types

```typescript
// ✅ Good - Create variations of types
type Nullable<T> = {
    [P in keyof T]: T[P] | null;
};

interface User {
    id: number;
    name: string;
    email: string;
}

type NullableUser = Nullable<User>;
// { id: number | null; name: string | null; email: string | null }

// ✅ Good - Conditional mapped types
type NonNullableProps<T> = {
    [P in keyof T]: NonNullable<T[P]>;
};

// ✅ Good - Template literal types
type EventName<T extends string> = `on${Capitalize<T>}`;

type ClickEvent = EventName<'click'>;  // "onClick"
type MouseEvent = EventName<'mouseMove'>;  // "onMouseMove"

// ✅ Good - Key remapping in mapped types
type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface Person {
    name: string;
    age: number;
}

type PersonGetters = Getters<Person>;
// { getName: () => string; getAge: () => number }
```

## Testing Best Practices

### Type-Safe Testing with Jest

```typescript
// user.test.ts
import { UserService } from './UserService';
import { User } from './types';

describe('UserService', () => {
    let service: UserService;

    beforeEach(() => {
        service = new UserService();
    });

    test('creates user with valid data', async () => {
        const userData = {
            name: 'Alice',
            email: 'alice@example.com',
        };

        const user = await service.createUser(userData);

        expect(user).toMatchObject<Partial<User>>({
            name: 'Alice',
            email: 'alice@example.com',
        });
        expect(user.id).toBeDefined();
    });

    test('throws error for invalid email', async () => {
        const userData = {
            name: 'Bob',
            email: 'invalid',
        };

        await expect(service.createUser(userData))
            .rejects
            .toThrow(ValidationError);
    });
});

// Type-safe mock
const mockUser: User = {
    id: 1,
    name: 'Test User',
    email: 'test@example.com',
    age: 30,
};
```

### Type-Safe Mocking

```typescript
// ✅ Good - Type-safe mock functions
import { jest } from '@jest/globals';

interface Database {
    query<T>(sql: string): Promise<T[]>;
    execute(sql: string): Promise<void>;
}

const mockDatabase: jest.Mocked<Database> = {
    query: jest.fn(),
    execute: jest.fn(),
};

// TypeScript ensures we implement all methods
mockDatabase.query.mockResolvedValue([mockUser]);

// ✅ Good - Partial mocks
const partialUser: Partial<User> = {
    name: 'Alice',
    email: 'alice@example.com',
};

// ✅ Good - Mock type from implementation
type MockFunction<T extends (...args: any[]) => any> = jest.Mock<
    ReturnType<T>,
    Parameters<T>
>;

const mockFetch: MockFunction<typeof fetch> = jest.fn();
```

## Common Patterns

### Builder Pattern with Fluent API

```typescript
// ✅ Good - Type-safe builder
class UserBuilder {
    private user: Partial<User> = {};

    setName(name: string): this {
        this.user.name = name;
        return this;
    }

    setEmail(email: string): this {
        this.user.email = email;
        return this;
    }

    setAge(age: number): this {
        this.user.age = age;
        return this;
    }

    build(): User {
        if (!this.user.name || !this.user.email) {
            throw new Error('Name and email are required');
        }

        return {
            id: Math.random(),
            name: this.user.name,
            email: this.user.email,
            age: this.user.age ?? 0,
        };
    }
}

// Usage
const user = new UserBuilder()
    .setName('Alice')
    .setEmail('alice@example.com')
    .setAge(30)
    .build();
```

### Repository Pattern

```typescript
// ✅ Good - Generic repository interface
interface Repository<T, ID = number> {
    findById(id: ID): Promise<T | null>;
    findAll(): Promise<T[]>;
    create(item: Omit<T, 'id'>): Promise<T>;
    update(id: ID, item: Partial<T>): Promise<T>;
    delete(id: ID): Promise<void>;
}

class UserRepository implements Repository<User> {
    async findById(id: number): Promise<User | null> {
        // implementation
    }

    async findAll(): Promise<User[]> {
        // implementation
    }

    async create(data: Omit<User, 'id'>): Promise<User> {
        // implementation
    }

    async update(id: number, data: Partial<User>): Promise<User> {
        // implementation
    }

    async delete(id: number): Promise<void> {
        // implementation
    }

    // Additional user-specific methods
    async findByEmail(email: string): Promise<User | null> {
        // implementation
    }
}
```

### Dependency Injection

```typescript
// ✅ Good - Constructor injection with interfaces
interface IUserRepository {
    findById(id: number): Promise<User | null>;
}

interface IEmailService {
    sendEmail(to: string, subject: string, body: string): Promise<void>;
}

class UserService {
    constructor(
        private readonly userRepository: IUserRepository,
        private readonly emailService: IEmailService
    ) {}

    async notifyUser(userId: number, message: string): Promise<void> {
        const user = await this.userRepository.findById(userId);
        if (!user) {
            throw new Error('User not found');
        }

        await this.emailService.sendEmail(
            user.email,
            'Notification',
            message
        );
    }
}

// Easy to test with mocks
const mockRepo: IUserRepository = {
    findById: jest.fn().mockResolvedValue(mockUser),
};

const mockEmail: IEmailService = {
    sendEmail: jest.fn().mockResolvedValue(undefined),
};

const service = new UserService(mockRepo, mockEmail);
```

## Linting & Formatting

### ESLint Configuration

```javascript
// .eslintrc.js
module.exports = {
    parser: '@typescript-eslint/parser',
    parserOptions: {
        project: './tsconfig.json',
        ecmaVersion: 2020,
        sourceType: 'module',
    },
    extends: [
        'eslint:recommended',
        'plugin:@typescript-eslint/recommended',
        'plugin:@typescript-eslint/recommended-requiring-type-checking',
        'prettier',  // Must be last
    ],
    plugins: ['@typescript-eslint'],
    rules: {
        '@typescript-eslint/explicit-function-return-type': 'warn',
        '@typescript-eslint/no-explicit-any': 'error',
        '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
        '@typescript-eslint/no-floating-promises': 'error',
        '@typescript-eslint/strict-boolean-expressions': 'warn',
    },
};
```

## Common Anti-Patterns

❌ **Using `any` Type**
```typescript
// ❌ Bad - Defeats purpose of TypeScript
function process(data: any): any {
    return data.value;
}

// ✅ Good - Use proper types or unknown
function process<T>(data: T): T {
    return data;
}

// ✅ Good - Use unknown for truly unknown types
function process(data: unknown): string {
    if (typeof data === 'object' && data !== null && 'value' in data) {
        return String(data.value);
    }
    return '';
}
```

❌ **Type Assertions Instead of Type Guards**
```typescript
// ❌ Bad - Unsafe type assertion
const user = getUserData() as User;
console.log(user.name.toUpperCase());  // May crash!

// ✅ Good - Validate with type guard
function isUser(data: unknown): data is User {
    return (
        typeof data === 'object' &&
        data !== null &&
        'name' in data &&
        typeof data.name === 'string'
    );
}

const userData = getUserData();
if (isUser(userData)) {
    console.log(userData.name.toUpperCase());
}
```

❌ **Not Handling Null/Undefined**
```typescript
// ❌ Bad - Assuming value exists (with strictNullChecks)
function getLength(str: string | null): number {
    return str.length;  // Error with strictNullChecks
}

// ✅ Good - Handle null/undefined
function getLength(str: string | null): number {
    return str?.length ?? 0;
}

// ✅ Good - Non-null assertion when you're certain
function getLength(str: string | null): number {
    // Only use ! when you're absolutely sure
    return str!.length;
}
```

❌ **Overusing Enums**
```typescript
// ❌ Questionable - Enums add runtime code
enum Status {
    Active,
    Inactive,
    Pending,
}

// ✅ Better - Union types (no runtime cost)
type Status = 'Active' | 'Inactive' | 'Pending';

// ✅ Good - Const assertions
const STATUS = {
    Active: 'Active',
    Inactive: 'Inactive',
    Pending: 'Pending',
} as const;

type Status = typeof STATUS[keyof typeof STATUS];
```

## Performance Tips

**Avoid Expensive Type Computations**
```typescript
// ❌ Slow - Complex recursive type
type DeepReadonly<T> = {
    readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// ✅ Better - Use built-in or simpler types
type Readonly<T> = { readonly [P in keyof T]: T[P] };
```

**Use Type Aliases for Complex Types**
```typescript
// ❌ Bad - Repeated complex type
function process(
    callback: (data: { id: number; name: string; items: Array<{ id: number; value: string }> }) => void
): void {}

// ✅ Good - Named type
type UserData = {
    id: number;
    name: string;
    items: Array<{ id: number; value: string }>;
};

function process(callback: (data: UserData) => void): void {}
```

**Leverage Type Inference**
```typescript
// ❌ Bad - Unnecessary explicit types slow compilation
const users: Array<User> = getUsers().map((u: User): User => {
    return { ...u, active: true };
});

// ✅ Good - Let TypeScript infer
const users = getUsers().map(u => ({
    ...u,
    active: true,
}));
```

## Resources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Type Challenges](https://github.com/type-challenges/type-challenges)
- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)
- [Clean Code TypeScript](https://github.com/labs42io/clean-code-typescript)
- [Effective TypeScript](https://effectivetypescript.com/)
