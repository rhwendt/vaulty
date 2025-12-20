#memory/language #python #best-practices

# Python Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Python preferences:
> - `frameworks.python`: Preferred Python framework (Django, Flask, FastAPI, etc.)
> - `formatter_python`: Code formatter (black, autopep8, yapf)
> - `linter_python`: Linter (pylint, flake8, ruff)
> - `test_framework_python`: Test framework (pytest, unittest, nose)
> - `package_manager_python`: Package manager (pip, poetry, pipenv)

## Python Philosophy

- **"There should be one-- and preferably only one --obvious way to do it"** (The Zen of Python)
- **Readability counts**: Clear, readable code over clever one-liners
- **Explicit is better than implicit**: Be clear about what your code does
- **Simple is better than complex**: Favor simplicity in design
- **Batteries included**: Rich standard library for common tasks

## Idiomatic Python Patterns

### List Comprehensions and Generator Expressions

**List Comprehensions**
```python
# ✅ Good - Clear list comprehension
squares = [x**2 for x in range(10)]
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# ✅ Good - Nested comprehensions (when readable)
matrix = [[i*j for j in range(5)] for i in range(5)]

# ❌ Bad - Too complex, hard to read
result = [x*y for x in range(10) if x % 2 == 0 for y in range(10) if y % 3 == 0 if x*y > 20]

# Better - Break it down
evens = [x for x in range(10) if x % 2 == 0]
threes = [y for y in range(10) if y % 3 == 0]
result = [x*y for x in evens for y in threes if x*y > 20]
```

**Generator Expressions**
```python
# ✅ Good - Use generators for large datasets
total = sum(x**2 for x in range(1000000))  # Memory efficient

# ❌ Bad - Creates entire list in memory
total = sum([x**2 for x in range(1000000)])
```

### Context Managers

**Using Context Managers**
```python
# ✅ Good - Always use context managers for resources
with open('file.txt', 'r') as f:
    data = f.read()
# File automatically closed

# Multiple context managers
with open('input.txt', 'r') as infile, open('output.txt', 'w') as outfile:
    outfile.write(infile.read())

# ❌ Bad - Manual resource management
f = open('file.txt', 'r')
data = f.read()
f.close()  # Easy to forget, won't run if exception occurs
```

**Creating Custom Context Managers**
```python
from contextlib import contextmanager

# ✅ Method 1: Using decorator
@contextmanager
def timer(name):
    import time
    start = time.time()
    try:
        yield
    finally:
        elapsed = time.time() - start
        print(f"{name} took {elapsed:.2f}s")

# Usage
with timer("Database query"):
    # Do work
    pass

# ✅ Method 2: Using class
class DatabaseConnection:
    def __enter__(self):
        self.conn = create_connection()
        return self.conn

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.conn.close()
        return False  # Don't suppress exceptions

# Usage
with DatabaseConnection() as conn:
    conn.execute("SELECT * FROM users")
```

### Decorators

**Function Decorators**
```python
# ✅ Good - Simple decorator
def timing_decorator(func):
    import time
    from functools import wraps

    @wraps(func)  # Preserves function metadata
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.2f}s")
        return result
    return wrapper

@timing_decorator
def slow_function():
    import time
    time.sleep(1)

# ✅ Good - Decorator with arguments
def repeat(times):
    def decorator(func):
        from functools import wraps
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(times=3)
def greet(name):
    print(f"Hello {name}")
```

**Class Decorators**
```python
# Property decorators
class User:
    def __init__(self, first_name, last_name):
        self._first_name = first_name
        self._last_name = last_name

    @property
    def full_name(self):
        return f"{self._first_name} {self._last_name}"

    @full_name.setter
    def full_name(self, value):
        self._first_name, self._last_name = value.split(' ', 1)

    @staticmethod
    def is_valid_name(name):
        return bool(name and name.strip())

    @classmethod
    def from_string(cls, name_string):
        first, last = name_string.split(' ', 1)
        return cls(first, last)
```

### Pythonic Iteration

**Enumerate Instead of Range**
```python
# ✅ Good - Use enumerate
items = ['a', 'b', 'c']
for idx, item in enumerate(items):
    print(f"{idx}: {item}")

# ❌ Bad - Manual indexing
for i in range(len(items)):
    print(f"{i}: {items[i]}")
```

**Zip for Parallel Iteration**
```python
# ✅ Good - Zip multiple iterables
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]
for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# Dictionary from two lists
user_dict = dict(zip(names, ages))
```

**Itertools for Advanced Iteration**
```python
from itertools import chain, islice, combinations, groupby

# Chain multiple iterables
all_items = chain([1, 2, 3], [4, 5, 6], [7, 8, 9])

# Slice an iterator
first_five = islice(range(100), 5)

# Combinations
pairs = combinations([1, 2, 3, 4], 2)  # (1,2), (1,3), (1,4), (2,3), (2,4), (3,4)

# Group consecutive items
data = [('A', 1), ('A', 2), ('B', 3), ('B', 4)]
for key, group in groupby(data, key=lambda x: x[0]):
    print(key, list(group))
```

## Error Handling

### Exception Handling Best Practices

**Specific Exception Handling**
```python
# ✅ Good - Catch specific exceptions
try:
    value = int(user_input)
    result = 10 / value
except ValueError:
    print("Invalid number format")
except ZeroDivisionError:
    print("Cannot divide by zero")
except Exception as e:
    print(f"Unexpected error: {e}")
    raise  # Re-raise unexpected exceptions

# ❌ Bad - Bare except catches everything
try:
    do_something()
except:  # Catches KeyboardInterrupt, SystemExit, etc.
    pass
```

**Using Finally and Else**
```python
# ✅ Good - Complete exception handling
try:
    file = open('data.txt', 'r')
    data = file.read()
except FileNotFoundError:
    print("File not found")
    data = None
except IOError as e:
    print(f"IO error: {e}")
    data = None
else:
    # Runs if no exception occurred
    print("File read successfully")
finally:
    # Always runs
    if 'file' in locals():
        file.close()
```

**Custom Exceptions**
```python
# ✅ Good - Custom exception hierarchy
class ApplicationError(Exception):
    """Base exception for application"""
    pass

class ValidationError(ApplicationError):
    """Raised when validation fails"""
    def __init__(self, field, message):
        self.field = field
        self.message = message
        super().__init__(f"Validation error in {field}: {message}")

class DatabaseError(ApplicationError):
    """Raised when database operation fails"""
    pass

# Usage
def validate_user(user):
    if not user.email:
        raise ValidationError("email", "Email is required")
    if '@' not in user.email:
        raise ValidationError("email", "Invalid email format")
```

**Context Manager for Exception Handling**
```python
from contextlib import suppress

# ✅ Good - Suppress specific exceptions when appropriate
with suppress(FileNotFoundError):
    os.remove('temp_file.txt')

# Equivalent to:
try:
    os.remove('temp_file.txt')
except FileNotFoundError:
    pass
```

## Code Organization

### Package Structure

```
myproject/
├── src/
│   └── myproject/
│       ├── __init__.py
│       ├── models/
│       │   ├── __init__.py
│       │   └── user.py
│       ├── services/
│       │   ├── __init__.py
│       │   └── user_service.py
│       ├── api/
│       │   ├── __init__.py
│       │   └── routes.py
│       └── utils/
│           ├── __init__.py
│           └── helpers.py
├── tests/
│   ├── __init__.py
│   ├── test_models.py
│   └── test_services.py
├── pyproject.toml
├── setup.py
└── README.md
```

### Naming Conventions

```python
# Module names: lowercase with underscores
import user_service  # ✅ Good
import UserService   # ❌ Bad

# Class names: PascalCase
class UserService:  # ✅ Good
    pass

class user_service:  # ❌ Bad
    pass

# Function/method names: lowercase with underscores
def get_user_by_id(user_id):  # ✅ Good
    pass

def getUserById(user_id):  # ❌ Bad (camelCase not Pythonic)
    pass

# Constants: UPPERCASE with underscores
MAX_CONNECTIONS = 100  # ✅ Good
max_connections = 100  # ❌ Bad

# Private/internal: prefix with underscore
class User:
    def __init__(self):
        self._internal_state = {}  # "Private" attribute
        self.__really_private = {}  # Name mangled

    def public_method(self):
        pass

    def _internal_method(self):  # Internal use
        pass
```

### Import Organization

```python
# ✅ Good - Organized imports
# Standard library imports
import os
import sys
from pathlib import Path

# Third-party imports
import numpy as np
import pandas as pd
from flask import Flask, request

# Local application imports
from myproject.models import User
from myproject.services import UserService

# ❌ Bad - Wildcard imports
from os import *  # Don't do this
from myproject.models import *  # Don't do this
```

## Language-Specific Features

### Type Hints (Python 3.5+)

**Basic Type Hints**
```python
from typing import List, Dict, Optional, Union, Tuple, Callable

# ✅ Good - Type hints for clarity
def process_users(users: List[str]) -> Dict[str, int]:
    return {user: len(user) for user in users}

def find_user(user_id: int) -> Optional[User]:
    """Returns User or None if not found"""
    user = db.query(User).filter_by(id=user_id).first()
    return user

def calculate(value: Union[int, float]) -> float:
    return float(value) * 1.5

# Function types
def apply_operation(value: int, operation: Callable[[int], int]) -> int:
    return operation(value)
```

**Advanced Type Hints**
```python
from typing import TypeVar, Generic, Protocol

# Generic types
T = TypeVar('T')

class Container(Generic[T]):
    def __init__(self) -> None:
        self._items: List[T] = []

    def add(self, item: T) -> None:
        self._items.append(item)

    def get(self, index: int) -> T:
        return self._items[index]

# Protocols (structural subtyping)
class Drawable(Protocol):
    def draw(self) -> None: ...

def render(obj: Drawable) -> None:
    obj.draw()
```

### Data Classes (Python 3.7+)

```python
from dataclasses import dataclass, field
from typing import List

# ✅ Good - Use dataclasses for data containers
@dataclass
class User:
    id: int
    name: str
    email: str
    tags: List[str] = field(default_factory=list)
    is_active: bool = True

    def __post_init__(self):
        # Validation after initialization
        if '@' not in self.email:
            raise ValueError("Invalid email")

# Automatic __init__, __repr__, __eq__, etc.
user = User(id=1, name="Alice", email="alice@example.com")
print(user)  # User(id=1, name='Alice', email='alice@example.com', tags=[], is_active=True)

# ❌ Bad - Manual boilerplate
class UserOld:
    def __init__(self, id, name, email, tags=None, is_active=True):
        self.id = id
        self.name = name
        self.email = email
        self.tags = tags or []
        self.is_active = is_active

    def __repr__(self):
        return f"User(id={self.id}, name={self.name}...)"

    def __eq__(self, other):
        # ... lots of boilerplate
        pass
```

### Async/Await (Python 3.5+)

```python
import asyncio
import aiohttp

# ✅ Good - Async functions for I/O bound tasks
async def fetch_url(url: str) -> str:
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()

async def fetch_multiple(urls: List[str]) -> List[str]:
    tasks = [fetch_url(url) for url in urls]
    return await asyncio.gather(*tasks)

# Running async code
async def main():
    urls = ['http://example.com', 'http://example.org']
    results = await fetch_multiple(urls)
    print(results)

# Python 3.7+
asyncio.run(main())

# Async context managers
class AsyncDatabase:
    async def __aenter__(self):
        self.connection = await create_connection()
        return self.connection

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        await self.connection.close()

async def query_db():
    async with AsyncDatabase() as db:
        result = await db.execute("SELECT * FROM users")
```

### Pattern Matching (Python 3.10+)

```python
# ✅ Good - Pattern matching for complex conditionals
def handle_command(command):
    match command.split():
        case ["quit"]:
            return "Exiting..."
        case ["load", filename]:
            return f"Loading {filename}"
        case ["save", filename]:
            return f"Saving {filename}"
        case ["set", key, value]:
            return f"Setting {key} to {value}"
        case _:
            return "Unknown command"

# Pattern matching with types
def process_value(value):
    match value:
        case int(x) if x > 0:
            return f"Positive integer: {x}"
        case int(x) if x < 0:
            return f"Negative integer: {x}"
        case str(s) if s:
            return f"Non-empty string: {s}"
        case []:
            return "Empty list"
        case [x, y]:
            return f"Pair: {x}, {y}"
        case _:
            return "Something else"
```

## Testing Best Practices

### Using pytest

**Basic Tests**
```python
# test_user.py
import pytest
from myproject.models import User

def test_user_creation():
    user = User(id=1, name="Alice", email="alice@example.com")
    assert user.id == 1
    assert user.name == "Alice"
    assert user.email == "alice@example.com"

def test_user_validation():
    with pytest.raises(ValueError, match="Invalid email"):
        User(id=1, name="Bob", email="invalid")

# Parametrized tests
@pytest.mark.parametrize("email,expected", [
    ("test@example.com", True),
    ("invalid", False),
    ("@example.com", False),
    ("test@", False),
])
def test_email_validation(email, expected):
    result = is_valid_email(email)
    assert result == expected
```

**Fixtures**
```python
# conftest.py or test file
@pytest.fixture
def user():
    """Provides a test user"""
    return User(id=1, name="Test User", email="test@example.com")

@pytest.fixture
def database():
    """Provides a test database"""
    db = create_test_database()
    yield db
    db.cleanup()  # Teardown

@pytest.fixture(scope="module")
def expensive_resource():
    """Created once per module"""
    resource = create_expensive_resource()
    yield resource
    resource.cleanup()

# Using fixtures
def test_user_save(user, database):
    database.save(user)
    retrieved = database.get(user.id)
    assert retrieved == user
```

**Mocking and Patching**
```python
from unittest.mock import Mock, patch, MagicMock

# Mock objects
def test_user_service():
    mock_db = Mock()
    mock_db.get_user.return_value = User(id=1, name="Alice")

    service = UserService(mock_db)
    user = service.get_user(1)

    assert user.name == "Alice"
    mock_db.get_user.assert_called_once_with(1)

# Patching
@patch('myproject.services.requests.get')
def test_fetch_user_data(mock_get):
    mock_response = Mock()
    mock_response.json.return_value = {'name': 'Alice'}
    mock_get.return_value = mock_response

    result = fetch_user_data(1)

    assert result['name'] == 'Alice'
    mock_get.assert_called_once()

# Context manager for patching
def test_with_patch():
    with patch('os.path.exists') as mock_exists:
        mock_exists.return_value = True
        assert check_file_exists('test.txt') == True
```

## Common Patterns

### Singleton Pattern

```python
# ✅ Good - Using decorator
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class Database:
    def __init__(self):
        self.connection = None

# ✅ Good - Using __new__
class Logger:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```

### Factory Pattern

```python
# ✅ Good - Factory pattern
class Animal:
    def speak(self):
        raise NotImplementedError

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    @staticmethod
    def create_animal(animal_type: str) -> Animal:
        animals = {
            'dog': Dog,
            'cat': Cat,
        }
        animal_class = animals.get(animal_type.lower())
        if animal_class is None:
            raise ValueError(f"Unknown animal type: {animal_type}")
        return animal_class()

# Usage
animal = AnimalFactory.create_animal('dog')
print(animal.speak())  # "Woof!"
```

### Builder Pattern

```python
# ✅ Good - Builder pattern with method chaining
class QueryBuilder:
    def __init__(self):
        self._select = []
        self._from = None
        self._where = []
        self._limit = None

    def select(self, *fields):
        self._select.extend(fields)
        return self

    def from_table(self, table):
        self._from = table
        return self

    def where(self, condition):
        self._where.append(condition)
        return self

    def limit(self, n):
        self._limit = n
        return self

    def build(self):
        query = f"SELECT {', '.join(self._select)} FROM {self._from}"
        if self._where:
            query += f" WHERE {' AND '.join(self._where)}"
        if self._limit:
            query += f" LIMIT {self._limit}"
        return query

# Usage
query = (QueryBuilder()
         .select('id', 'name', 'email')
         .from_table('users')
         .where('age > 18')
         .where('is_active = true')
         .limit(10)
         .build())
```

## Linting & Formatting

### Black (Code Formatter)

```bash
# Format all Python files
black .

# Check without modifying
black --check .

# Format specific file
black myfile.py
```

### Ruff (Fast Linter)

```bash
# Lint all files
ruff check .

# Auto-fix issues
ruff check --fix .

# Check specific rules
ruff check --select E,F,I .
```

### pylint

```bash
# Lint file
pylint myfile.py

# Generate config
pylint --generate-rcfile > .pylintrc
```

### Configuration Example (pyproject.toml)

```toml
[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'

[tool.ruff]
line-length = 88
select = ["E", "F", "I", "N", "W"]
ignore = ["E501"]  # Line too long

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
```

## Common Anti-Patterns

❌ **Mutable Default Arguments**
```python
# ❌ Bad - Mutable default argument
def add_item(item, items=[]):
    items.append(item)
    return items

# Problem: The list persists across calls
add_item(1)  # [1]
add_item(2)  # [1, 2] - UNEXPECTED!

# ✅ Good - Use None and create new list
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

❌ **Using isinstance() for Type Checking**
```python
# ❌ Bad - Too specific type checking
def process(value):
    if isinstance(value, list):
        return sum(value)

# ✅ Good - Duck typing
def process(value):
    try:
        return sum(value)
    except TypeError:
        return value
```

❌ **Not Using __name__ == "__main__"**
```python
# ❌ Bad - Code runs on import
def main():
    print("Running application")

main()  # Runs even when imported

# ✅ Good - Only runs when executed directly
def main():
    print("Running application")

if __name__ == "__main__":
    main()
```

❌ **String Concatenation in Loops**
```python
# ❌ Bad - Inefficient string concatenation
result = ""
for item in items:
    result += str(item) + ","

# ✅ Good - Use join
result = ",".join(str(item) for item in items)
```

❌ **Not Using enumerate()**
```python
# ❌ Bad - Manual index tracking
for i in range(len(items)):
    print(f"{i}: {items[i]}")

# ✅ Good - Use enumerate
for i, item in enumerate(items):
    print(f"{i}: {item}")
```

## Performance Tips

**Use Built-in Functions**
```python
# ✅ Fast - Built-in functions are implemented in C
result = sum(numbers)
maximum = max(numbers)
minimum = min(numbers)

# ❌ Slow - Python loops
result = 0
for n in numbers:
    result += n
```

**List Comprehensions vs Loops**
```python
# ✅ Fast - List comprehension
squares = [x**2 for x in range(1000)]

# ❌ Slower - Loop with append
squares = []
for x in range(1000):
    squares.append(x**2)
```

**Use Local Variables**
```python
# ✅ Fast - Local variable lookup is faster
def process_items(items):
    upper = str.upper  # Cache method
    result = []
    for item in items:
        result.append(upper(item))
    return result

# ❌ Slower - Repeated attribute lookup
def process_items(items):
    result = []
    for item in items:
        result.append(str.upper(item))
    return result
```

**Use Sets for Membership Tests**
```python
# ✅ Fast - O(1) lookup
valid_ids = {1, 2, 3, 4, 5}
if user_id in valid_ids:
    process()

# ❌ Slow - O(n) lookup
valid_ids = [1, 2, 3, 4, 5]
if user_id in valid_ids:
    process()
```

**Profile Before Optimizing**
```python
import cProfile
import pstats

# Profile your code
cProfile.run('main()', 'profile_stats')

# Analyze results
stats = pstats.Stats('profile_stats')
stats.sort_stats('cumulative')
stats.print_stats(10)

# Or use line_profiler for line-by-line profiling
# @profile decorator
```

## Resources

- [PEP 8 - Style Guide for Python Code](https://peps.python.org/pep-0008/)
- [PEP 20 - The Zen of Python](https://peps.python.org/pep-0020/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [The Hitchhiker's Guide to Python](https://docs.python-guide.org/)
- [Real Python Tutorials](https://realpython.com/)
- [Python Patterns](https://python-patterns.guide/)
