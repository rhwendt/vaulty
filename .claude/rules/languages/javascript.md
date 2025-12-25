#memory/language #javascript #best-practices

# JavaScript Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for JavaScript preferences:
> - `frameworks.javascript`: Preferred JS framework (React, Vue, Angular, Express, etc.)
> - `formatter_javascript`: Code formatter (prettier, standard)
> - `linter_javascript`: Linter (eslint, standard)
> - `test_framework_javascript`: Test framework (jest, mocha, vitest)
> - `package_manager_javascript`: Package manager (npm, yarn, pnpm)

## JavaScript Philosophy

- **"Write once, run anywhere"**: Cross-platform by design
- **Event-driven and asynchronous**: Non-blocking I/O for performance
- **Prototype-based**: Flexible object model with prototypal inheritance
- **First-class functions**: Functions as values enable functional programming
- **Dynamic and loosely typed**: Flexibility with runtime type checking

## Idiomatic JavaScript Patterns

### Modern Variable Declarations

**Const and Let**
```javascript
// ✅ Good - Use const by default
const API_URL = 'https://api.example.com';
const user = { name: 'Alice', age: 30 };

// Use let when reassignment is needed
let counter = 0;
counter++;

// ❌ Bad - Don't use var (function-scoped, hoisting issues)
var name = 'Bob';  // Don't do this in modern JS

// ✅ Good - Const doesn't mean immutable
const config = { theme: 'dark' };
config.theme = 'light';  // OK - object is mutable
config = {};  // ERROR - can't reassign
```

### Arrow Functions

**When to Use Arrow Functions**
```javascript
// ✅ Good - Arrow functions for callbacks
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);

// ✅ Good - Implicit return for single expressions
const square = x => x ** 2;
const greet = name => `Hello, ${name}!`;

// ✅ Good - Explicit return for multi-line
const processUser = user => {
    const fullName = `${user.first} ${user.last}`;
    return { ...user, fullName };
};

// ❌ Bad - Arrow functions for methods (loses 'this' context)
const obj = {
    name: 'Alice',
    greet: () => {
        console.log(`Hello, ${this.name}`);  // 'this' is undefined!
    }
};

// ✅ Good - Regular function or method shorthand for methods
const obj = {
    name: 'Alice',
    greet() {
        console.log(`Hello, ${this.name}`);  // Works correctly
    }
};
```

### Template Literals

**String Interpolation and Multi-line Strings**
```javascript
// ✅ Good - Template literals for interpolation
const name = 'Alice';
const age = 30;
const message = `Hello, ${name}! You are ${age} years old.`;

// ✅ Good - Multi-line strings
const html = `
    <div class="user">
        <h2>${name}</h2>
        <p>Age: ${age}</p>
    </div>
`;

// ✅ Good - Expression evaluation
const price = 19.99;
const total = `Total: $${(price * 1.2).toFixed(2)}`;

// ❌ Bad - String concatenation
const message = 'Hello, ' + name + '! You are ' + age + ' years old.';
```

### Destructuring

**Object and Array Destructuring**
```javascript
// ✅ Good - Object destructuring
const user = { name: 'Alice', age: 30, email: 'alice@example.com' };
const { name, age } = user;

// With renaming
const { name: userName, age: userAge } = user;

// With defaults
const { name, role = 'user' } = user;

// Nested destructuring
const { address: { city, country } } = user;

// ✅ Good - Array destructuring
const numbers = [1, 2, 3, 4, 5];
const [first, second, ...rest] = numbers;

// Skip elements
const [first, , third] = numbers;

// Swapping variables
let a = 1, b = 2;
[a, b] = [b, a];

// ✅ Good - Function parameter destructuring
function greet({ name, age }) {
    return `Hello, ${name}! You are ${age} years old.`;
}

greet(user);  // No need to access properties inside function
```

### Spread and Rest Operators

**Spread Operator**
```javascript
// ✅ Good - Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];

// Copying arrays
const copy = [...arr1];

// ✅ Good - Object spreading
const defaults = { theme: 'dark', lang: 'en' };
const userPrefs = { lang: 'fr', fontSize: 14 };
const config = { ...defaults, ...userPrefs };  // { theme: 'dark', lang: 'fr', fontSize: 14 }

// Copying objects
const userCopy = { ...user };

// Adding/overriding properties
const updatedUser = { ...user, age: 31, verified: true };

// ❌ Bad - Object.assign (spread is more readable)
const config = Object.assign({}, defaults, userPrefs);
```

**Rest Parameters**
```javascript
// ✅ Good - Rest parameters for variable arguments
function sum(...numbers) {
    return numbers.reduce((acc, n) => acc + n, 0);
}

sum(1, 2, 3, 4, 5);  // 15

// Mix regular and rest parameters
function logMessage(level, ...messages) {
    console.log(`[${level}]`, ...messages);
}

// ❌ Bad - Using arguments object
function sum() {
    return Array.from(arguments).reduce((acc, n) => acc + n, 0);
}
```

## Error Handling

### Try-Catch Patterns

**Synchronous Error Handling**
```javascript
// ✅ Good - Specific error handling
function parseJSON(jsonString) {
    try {
        return JSON.parse(jsonString);
    } catch (error) {
        if (error instanceof SyntaxError) {
            console.error('Invalid JSON:', error.message);
            return null;
        }
        throw error;  // Re-throw unexpected errors
    }
}

// ✅ Good - Finally for cleanup
function processFile(filename) {
    let file;
    try {
        file = openFile(filename);
        return processData(file.read());
    } catch (error) {
        console.error('Error processing file:', error);
        throw error;
    } finally {
        // Always runs, even if return or throw
        if (file) {
            file.close();
        }
    }
}
```

**Custom Errors**
```javascript
// ✅ Good - Custom error classes
class ValidationError extends Error {
    constructor(field, message) {
        super(`Validation failed for ${field}: ${message}`);
        this.name = 'ValidationError';
        this.field = field;
    }
}

class DatabaseError extends Error {
    constructor(message, query) {
        super(message);
        this.name = 'DatabaseError';
        this.query = query;
    }
}

// Usage
function validateUser(user) {
    if (!user.email) {
        throw new ValidationError('email', 'Email is required');
    }
    if (!user.email.includes('@')) {
        throw new ValidationError('email', 'Invalid email format');
    }
}

// Catching custom errors
try {
    validateUser(user);
} catch (error) {
    if (error instanceof ValidationError) {
        console.error(`Field error: ${error.field}`);
    } else {
        console.error('Unexpected error:', error);
    }
}
```

### Async Error Handling

**Promise Error Handling**
```javascript
// ✅ Good - Promise error handling with catch
fetchUser(userId)
    .then(user => processUser(user))
    .then(result => saveResult(result))
    .catch(error => {
        console.error('Error in promise chain:', error);
        // Handle error
    })
    .finally(() => {
        // Cleanup
        cleanup();
    });

// ❌ Bad - Unhandled promise rejection
fetchUser(userId).then(user => processUser(user));  // Missing .catch()
```

**Async/Await Error Handling**
```javascript
// ✅ Good - Try-catch with async/await
async function loadUserData(userId) {
    try {
        const user = await fetchUser(userId);
        const posts = await fetchUserPosts(user.id);
        return { user, posts };
    } catch (error) {
        console.error('Failed to load user data:', error);
        throw new Error(`User data load failed: ${error.message}`);
    }
}

// ✅ Good - Handle multiple errors differently
async function processData() {
    try {
        const data = await fetchData();
        return await process(data);
    } catch (error) {
        if (error.code === 'NETWORK_ERROR') {
            return retryFetch();
        }
        if (error.code === 'VALIDATION_ERROR') {
            return handleValidationError(error);
        }
        throw error;
    }
}

// ✅ Good - Promise.all with error handling
async function loadAllUsers(userIds) {
    try {
        const users = await Promise.all(
            userIds.map(id => fetchUser(id))
        );
        return users;
    } catch (error) {
        // If any promise fails, all fail
        console.error('Failed to load all users:', error);
        return [];
    }
}

// ✅ Good - Promise.allSettled for partial success
async function loadUsersWithPartialFailure(userIds) {
    const results = await Promise.allSettled(
        userIds.map(id => fetchUser(id))
    );

    const users = results
        .filter(result => result.status === 'fulfilled')
        .map(result => result.value);

    const errors = results
        .filter(result => result.status === 'rejected')
        .map(result => result.reason);

    return { users, errors };
}
```

## Code Organization

### Module System (ES6 Modules)

**Exports and Imports**
```javascript
// user.js - Named exports
export class User {
    constructor(name) {
        this.name = name;
    }
}

export function createUser(name) {
    return new User(name);
}

export const DEFAULT_ROLE = 'user';

// Default export
export default class UserService {
    // ...
}

// Importing
import UserService, { User, createUser, DEFAULT_ROLE } from './user.js';

// Import all
import * as UserModule from './user.js';

// Re-exporting
export { User, createUser } from './user.js';
export * from './user.js';
```

### File Organization

```
myproject/
├── src/
│   ├── components/
│   │   ├── Button.js
│   │   └── Input.js
│   ├── services/
│   │   ├── api.js
│   │   └── userService.js
│   ├── utils/
│   │   ├── validation.js
│   │   └── formatting.js
│   ├── models/
│   │   └── User.js
│   ├── config/
│   │   └── constants.js
│   └── index.js
├── tests/
│   ├── components/
│   └── services/
├── package.json
└── README.md
```

### Naming Conventions

```javascript
// ✅ Good - Descriptive names

// Constants: UPPER_SNAKE_CASE
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';

// Classes: PascalCase
class UserService {}
class DatabaseConnection {}

// Functions and variables: camelCase
function calculateTotal() {}
const userName = 'Alice';
let isAuthenticated = false;

// Private properties/methods: prefix with underscore
class User {
    constructor() {
        this._internalState = {};
    }

    _privateMethod() {
        // Internal use only
    }
}

// Boolean variables: use is/has/should prefix
const isActive = true;
const hasPermission = false;
const shouldUpdate = true;

// ❌ Bad - Unclear names
const a = 5;
const temp = getData();
const x = processUser();
```

## Language-Specific Features

### Promises and Async/Await

**Creating Promises**
```javascript
// ✅ Good - Creating a promise
function delay(ms) {
    return new Promise(resolve => {
        setTimeout(resolve, ms);
    });
}

function fetchData(url) {
    return new Promise((resolve, reject) => {
        fetch(url)
            .then(response => response.json())
            .then(data => resolve(data))
            .catch(error => reject(error));
    });
}

// ✅ Better - Using async/await
async function fetchData(url) {
    try {
        const response = await fetch(url);
        return await response.json();
    } catch (error) {
        throw new Error(`Failed to fetch: ${error.message}`);
    }
}
```

**Promise Combinators**
```javascript
// Promise.all - Wait for all promises (fail-fast)
const [users, posts, comments] = await Promise.all([
    fetchUsers(),
    fetchPosts(),
    fetchComments()
]);

// Promise.allSettled - Wait for all, get all results
const results = await Promise.allSettled([
    fetchUsers(),
    fetchPosts(),
    fetchComments()
]);

// Promise.race - First to complete wins
const result = await Promise.race([
    fetchFromPrimaryDB(),
    fetchFromSecondaryDB()
]);

// Promise.any - First successful promise wins
const result = await Promise.any([
    fetchFromServer1(),
    fetchFromServer2(),
    fetchFromServer3()
]);
```

### Closures and Scope

**Lexical Scope**
```javascript
// ✅ Good - Closures for private data
function createCounter() {
    let count = 0;  // Private variable

    return {
        increment() {
            return ++count;
        },
        decrement() {
            return --count;
        },
        getCount() {
            return count;
        }
    };
}

const counter = createCounter();
counter.increment();  // 1
counter.increment();  // 2
console.log(counter.count);  // undefined - private

// ✅ Good - Module pattern
const userModule = (function() {
    // Private
    const users = [];

    function validateUser(user) {
        return user.name && user.email;
    }

    // Public API
    return {
        addUser(user) {
            if (validateUser(user)) {
                users.push(user);
                return true;
            }
            return false;
        },
        getUsers() {
            return [...users];  // Return copy
        }
    };
})();
```

### this Binding

**Understanding this**
```javascript
// ✅ Good - Arrow functions preserve 'this'
class Timer {
    constructor() {
        this.seconds = 0;
    }

    start() {
        setInterval(() => {
            this.seconds++;  // 'this' refers to Timer instance
            console.log(this.seconds);
        }, 1000);
    }
}

// ❌ Bad - Regular function loses 'this'
class Timer {
    constructor() {
        this.seconds = 0;
    }

    start() {
        setInterval(function() {
            this.seconds++;  // 'this' is undefined or global object
        }, 1000);
    }
}

// ✅ Good - Explicit binding
const user = {
    name: 'Alice',
    greet() {
        console.log(`Hello, ${this.name}`);
    }
};

const greet = user.greet;
greet();  // Error: 'this' is undefined

// Solutions:
const boundGreet = user.greet.bind(user);
boundGreet();  // Works

user.greet.call(user);  // Works
user.greet.apply(user);  // Works
```

### Classes and Prototypes

**Modern Class Syntax**
```javascript
// ✅ Good - ES6 classes
class Animal {
    constructor(name) {
        this.name = name;
    }

    speak() {
        console.log(`${this.name} makes a sound`);
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);
        this.breed = breed;
    }

    speak() {
        console.log(`${this.name} barks`);
    }

    fetch() {
        console.log(`${this.name} fetches the ball`);
    }
}

// Static methods
class MathUtils {
    static square(x) {
        return x ** 2;
    }

    static cube(x) {
        return x ** 3;
    }
}

MathUtils.square(5);  // 25

// Private fields (ES2022)
class BankAccount {
    #balance = 0;  // Private field

    deposit(amount) {
        this.#balance += amount;
    }

    getBalance() {
        return this.#balance;
    }
}

const account = new BankAccount();
account.#balance;  // SyntaxError: Private field
```

## Testing Best Practices

### Jest Testing

**Basic Tests**
```javascript
// sum.test.js
const sum = require('./sum');

describe('sum function', () => {
    test('adds 1 + 2 to equal 3', () => {
        expect(sum(1, 2)).toBe(3);
    });

    test('adds negative numbers', () => {
        expect(sum(-1, -2)).toBe(-3);
    });

    test('handles zero', () => {
        expect(sum(0, 0)).toBe(0);
    });
});

// Parameterized tests
test.each([
    [1, 2, 3],
    [2, 3, 5],
    [-1, 1, 0],
    [0, 0, 0],
])('sum(%i, %i) equals %i', (a, b, expected) => {
    expect(sum(a, b)).toBe(expected);
});
```

**Async Tests**
```javascript
// ✅ Good - Testing promises
test('fetches user data', async () => {
    const data = await fetchUser(1);
    expect(data).toEqual({ id: 1, name: 'Alice' });
});

// Alternative: return promise
test('fetches user data', () => {
    return fetchUser(1).then(data => {
        expect(data).toEqual({ id: 1, name: 'Alice' });
    });
});

// Testing errors
test('throws error for invalid user', async () => {
    await expect(fetchUser(-1)).rejects.toThrow('Invalid user ID');
});
```

**Mocking**
```javascript
// ✅ Good - Mock functions
const mockCallback = jest.fn(x => x * 2);

[1, 2, 3].forEach(mockCallback);

expect(mockCallback).toHaveBeenCalledTimes(3);
expect(mockCallback).toHaveBeenCalledWith(1);
expect(mockCallback).toHaveBeenLastCalledWith(3);

// Mock return values
const mock = jest.fn();
mock
    .mockReturnValueOnce(10)
    .mockReturnValueOnce(20)
    .mockReturnValue(30);

console.log(mock(), mock(), mock(), mock());  // 10, 20, 30, 30

// Mock modules
jest.mock('./api');
const api = require('./api');

api.fetchUser.mockResolvedValue({ id: 1, name: 'Alice' });

test('processes user', async () => {
    const result = await processUser(1);
    expect(api.fetchUser).toHaveBeenCalledWith(1);
    expect(result).toBeDefined();
});
```

**Setup and Teardown**
```javascript
// Before/after hooks
beforeAll(() => {
    // Run once before all tests
    return initializeDatabase();
});

afterAll(() => {
    // Run once after all tests
    return closeDatabase();
});

beforeEach(() => {
    // Run before each test
    return clearDatabase();
});

afterEach(() => {
    // Run after each test
    return cleanupTest();
});

// Scoped to describe block
describe('User tests', () => {
    beforeEach(() => {
        // Only runs for tests in this describe block
        createTestUser();
    });

    test('creates user', () => {
        // test code
    });
});
```

## Common Patterns

### Module Pattern

```javascript
// ✅ Good - Revealing module pattern
const calculator = (function() {
    // Private
    let result = 0;

    function validate(n) {
        return typeof n === 'number' && !isNaN(n);
    }

    // Public API
    return {
        add(n) {
            if (!validate(n)) throw new Error('Invalid number');
            result += n;
            return this;
        },
        subtract(n) {
            if (!validate(n)) throw new Error('Invalid number');
            result -= n;
            return this;
        },
        getResult() {
            return result;
        },
        reset() {
            result = 0;
            return this;
        }
    };
})();

calculator.add(5).subtract(2).getResult();  // 3
```

### Observer Pattern

```javascript
// ✅ Good - Event emitter pattern
class EventEmitter {
    constructor() {
        this.events = {};
    }

    on(event, listener) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(listener);
        return this;
    }

    off(event, listenerToRemove) {
        if (!this.events[event]) return this;

        this.events[event] = this.events[event].filter(
            listener => listener !== listenerToRemove
        );
        return this;
    }

    emit(event, ...args) {
        if (!this.events[event]) return this;

        this.events[event].forEach(listener => {
            listener(...args);
        });
        return this;
    }

    once(event, listener) {
        const onceListener = (...args) => {
            this.off(event, onceListener);
            listener(...args);
        };
        return this.on(event, onceListener);
    }
}

// Usage
const emitter = new EventEmitter();
emitter.on('data', data => console.log('Received:', data));
emitter.emit('data', { id: 1, name: 'Alice' });
```

### Strategy Pattern

```javascript
// ✅ Good - Strategy pattern
class PaymentProcessor {
    constructor(strategy) {
        this.strategy = strategy;
    }

    setStrategy(strategy) {
        this.strategy = strategy;
    }

    processPayment(amount) {
        return this.strategy.process(amount);
    }
}

const creditCardStrategy = {
    process(amount) {
        console.log(`Processing $${amount} via Credit Card`);
        return { success: true, method: 'credit_card' };
    }
};

const paypalStrategy = {
    process(amount) {
        console.log(`Processing $${amount} via PayPal`);
        return { success: true, method: 'paypal' };
    }
};

// Usage
const processor = new PaymentProcessor(creditCardStrategy);
processor.processPayment(100);

processor.setStrategy(paypalStrategy);
processor.processPayment(50);
```

## Linting & Formatting

### ESLint Configuration

```javascript
// .eslintrc.js
module.exports = {
    env: {
        browser: true,
        es2021: true,
        node: true
    },
    extends: [
        'eslint:recommended',
        'plugin:react/recommended'  // If using React
    ],
    parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module'
    },
    rules: {
        'indent': ['error', 2],
        'linebreak-style': ['error', 'unix'],
        'quotes': ['error', 'single'],
        'semi': ['error', 'always'],
        'no-unused-vars': 'warn',
        'no-console': 'off'
    }
};
```

### Prettier Configuration

```javascript
// .prettierrc
{
    "semi": true,
    "singleQuote": true,
    "tabWidth": 2,
    "trailingComma": "es5",
    "printWidth": 80,
    "arrowParens": "avoid"
}
```

## Common Anti-Patterns

❌ **Callback Hell**
```javascript
// ❌ Bad - Nested callbacks
getData(function(a) {
    getMoreData(a, function(b) {
        getEvenMoreData(b, function(c) {
            getEvenEvenMoreData(c, function(d) {
                getFinalData(d, function(final) {
                    console.log(final);
                });
            });
        });
    });
});

// ✅ Good - Use promises or async/await
async function fetchAllData() {
    const a = await getData();
    const b = await getMoreData(a);
    const c = await getEvenMoreData(b);
    const d = await getEvenEvenMoreData(c);
    const final = await getFinalData(d);
    return final;
}
```

❌ **Modifying Array During Iteration**
```javascript
// ❌ Bad - Modifying array while iterating
const numbers = [1, 2, 3, 4, 5];
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] % 2 === 0) {
        numbers.splice(i, 1);  // Changes array length!
    }
}

// ✅ Good - Use filter
const numbers = [1, 2, 3, 4, 5];
const odds = numbers.filter(n => n % 2 !== 0);
```

❌ **Not Using Strict Equality**
```javascript
// ❌ Bad - Loose equality causes type coercion
if (value == null) { }  // Matches both null and undefined
if ('' == 0) { }  // true
if ('0' == 0) { }  // true

// ✅ Good - Use strict equality
if (value === null) { }
if (value === undefined) { }
if (value === null || value === undefined) { }
// Or use nullish check
if (value == null) { }  // Only acceptable for null/undefined check
```

❌ **Forgetting to Return in Array Methods**
```javascript
// ❌ Bad - No return in map
const doubled = [1, 2, 3].map(n => {
    n * 2;  // Missing return!
});
// doubled = [undefined, undefined, undefined]

// ✅ Good - Implicit or explicit return
const doubled = [1, 2, 3].map(n => n * 2);
// or
const doubled = [1, 2, 3].map(n => {
    return n * 2;
});
```

## Performance Tips

**Use Appropriate Data Structures**
```javascript
// ✅ Fast - Use Set for unique values
const uniqueIds = new Set([1, 2, 2, 3, 3, 4]);
uniqueIds.has(2);  // O(1)

// ✅ Fast - Use Map for key-value pairs
const userMap = new Map();
userMap.set(1, { name: 'Alice' });
userMap.get(1);  // O(1)

// ❌ Slow - Searching array
const users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];
users.find(u => u.id === 1);  // O(n)
```

**Debouncing and Throttling**
```javascript
// ✅ Good - Debounce expensive operations
function debounce(func, wait) {
    let timeout;
    return function(...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => func.apply(this, args), wait);
    };
}

const searchAPI = debounce(query => {
    fetch(`/api/search?q=${query}`);
}, 300);

// ✅ Good - Throttle frequent events
function throttle(func, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

const handleScroll = throttle(() => {
    console.log('Scrolled!');
}, 100);
```

**Avoid Memory Leaks**
```javascript
// ❌ Bad - Event listener not removed
element.addEventListener('click', handleClick);

// ✅ Good - Clean up listeners
element.addEventListener('click', handleClick);
// Later...
element.removeEventListener('click', handleClick);

// ✅ Good - Use AbortController (modern)
const controller = new AbortController();
element.addEventListener('click', handleClick, { signal: controller.signal });
// Later...
controller.abort();  // Removes all listeners
```

## Resources

- [MDN Web Docs - JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [JavaScript.info - The Modern JavaScript Tutorial](https://javascript.info/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)
- [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS)
- [Clean Code JavaScript](https://github.com/ryanmcdermott/clean-code-javascript)
