---
paths: "**/*.rs"
---

#memory/language #rust #best-practices

# Rust Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Rust preferences:
> - `frameworks.rust`: Preferred Rust framework (Actix, Rocket, Axum, Tokio, etc.)
> - `formatter_rust`: Code formatter (rustfmt)
> - `linter_rust`: Linter (clippy)
> - `test_framework_rust`: Test framework (built-in test, cargo-nextest)
> - `package_manager_rust`: Package manager (cargo)

## Rust Philosophy

- **Memory safety without garbage collection**: Ownership system ensures safety at compile-time
- **Zero-cost abstractions**: High-level features with no runtime overhead
- **Fearless concurrency**: Type system prevents data races
- **Explicit over implicit**: No hidden control flow or allocations
- **"If it compiles, it works"**: Strong guarantees from the compiler

## Idiomatic Rust Patterns

### Ownership and Borrowing

**Understanding Ownership**
```rust
// ✅ Good - Understanding ownership transfer
fn process_string(s: String) {
    println!("{}", s);
    // s is dropped here
}

fn main() {
    let message = String::from("Hello");
    process_string(message);
    // message is no longer valid here - ownership moved
    // println!("{}", message); // ERROR
}

// ✅ Good - Borrowing instead of ownership transfer
fn process_string(s: &String) {
    println!("{}", s);
    // s is borrowed, not owned
}

fn main() {
    let message = String::from("Hello");
    process_string(&message);
    println!("{}", message); // OK - still valid
}

// ✅ Good - Mutable borrowing
fn append_world(s: &mut String) {
    s.push_str(", World!");
}

fn main() {
    let mut message = String::from("Hello");
    append_world(&mut message);
    println!("{}", message); // "Hello, World!"
}
```

**Borrowing Rules**
```rust
// ✅ Good - Multiple immutable borrows
let s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} and {}", r1, r2); // OK

// ❌ Bad - Can't have mutable and immutable borrows together
let mut s = String::from("hello");
let r1 = &s;
let r2 = &mut s; // ERROR
println!("{} {}", r1, r2);

// ✅ Good - Borrowing scope ends before mutable borrow
let mut s = String::from("hello");
let r1 = &s;
println!("{}", r1);
// r1 is no longer used after this point

let r2 = &mut s; // OK - r1's scope has ended
r2.push_str(" world");
```

### Error Handling with Result and Option

**Result Type**
```rust
use std::fs::File;
use std::io::{self, Read};

// ✅ Good - Propagating errors with ?
fn read_file(path: &str) -> io::Result<String> {
    let mut file = File::open(path)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}

// ✅ Good - Custom error types
use std::fmt;

#[derive(Debug)]
enum ConfigError {
    IoError(io::Error),
    ParseError(String),
    ValidationError(String),
}

impl fmt::Display for ConfigError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            ConfigError::IoError(e) => write!(f, "IO error: {}", e),
            ConfigError::ParseError(msg) => write!(f, "Parse error: {}", msg),
            ConfigError::ValidationError(msg) => write!(f, "Validation error: {}", msg),
        }
    }
}

impl From<io::Error> for ConfigError {
    fn from(error: io::Error) -> Self {
        ConfigError::IoError(error)
    }
}

fn load_config(path: &str) -> Result<Config, ConfigError> {
    let contents = std::fs::read_to_string(path)?;
    let config: Config = serde_json::from_str(&contents)
        .map_err(|e| ConfigError::ParseError(e.to_string()))?;

    if !config.is_valid() {
        return Err(ConfigError::ValidationError("Invalid config".to_string()));
    }

    Ok(config)
}

// ✅ Good - Using match for error handling
match read_file("config.txt") {
    Ok(contents) => println!("File contents: {}", contents),
    Err(e) => eprintln!("Error reading file: {}", e),
}
```

**Option Type**
```rust
// ✅ Good - Using Option
fn find_user(id: u32) -> Option<User> {
    // Return Some(user) if found, None otherwise
    users.get(&id).cloned()
}

// ✅ Good - Pattern matching on Option
match find_user(1) {
    Some(user) => println!("Found user: {}", user.name),
    None => println!("User not found"),
}

// ✅ Good - Using combinators
let user_name = find_user(1)
    .map(|u| u.name)
    .unwrap_or_else(|| "Unknown".to_string());

// ✅ Good - Converting Option to Result
fn get_user(id: u32) -> Result<User, String> {
    find_user(id).ok_or_else(|| format!("User {} not found", id))
}

// ✅ Good - Early return with ?
fn process_user(id: u32) -> Option<String> {
    let user = find_user(id)?;
    let profile = user.get_profile()?;
    Some(profile.display_name)
}
```

### Pattern Matching

**Exhaustive Matching**
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

// ✅ Good - Exhaustive pattern matching
fn process_message(msg: Message) {
    match msg {
        Message::Quit => {
            println!("Quitting");
        }
        Message::Move { x, y } => {
            println!("Moving to ({}, {})", x, y);
        }
        Message::Write(text) => {
            println!("Writing: {}", text);
        }
        Message::ChangeColor(r, g, b) => {
            println!("Changing color to RGB({}, {}, {})", r, g, b);
        }
    }
}

// ✅ Good - Pattern matching with guards
fn categorize_number(n: i32) -> &'static str {
    match n {
        n if n < 0 => "negative",
        0 => "zero",
        n if n < 10 => "single digit",
        n if n < 100 => "double digit",
        _ => "large number",
    }
}

// ✅ Good - Destructuring in match
struct Point {
    x: i32,
    y: i32,
}

fn describe_point(point: Point) {
    match point {
        Point { x: 0, y: 0 } => println!("Origin"),
        Point { x: 0, y } => println!("On Y-axis at {}", y),
        Point { x, y: 0 } => println!("On X-axis at {}", x),
        Point { x, y } => println!("At ({}, {})", x, y),
    }
}
```

**If Let and While Let**
```rust
// ✅ Good - if let for single pattern
if let Some(user) = find_user(1) {
    println!("Found: {}", user.name);
}

// Equivalent to:
match find_user(1) {
    Some(user) => println!("Found: {}", user.name),
    None => {}
}

// ✅ Good - while let for iteration
let mut stack = vec![1, 2, 3];
while let Some(value) = stack.pop() {
    println!("Popped: {}", value);
}
```

### Traits and Generics

**Defining and Implementing Traits**
```rust
// ✅ Good - Define trait
trait Summary {
    fn summarize(&self) -> String;

    // Default implementation
    fn summarize_long(&self) -> String {
        format!("(Read more from {}...)", self.summarize())
    }
}

struct Article {
    title: String,
    author: String,
    content: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{} by {}", self.title, self.author)
    }
}

// ✅ Good - Generic functions with trait bounds
fn print_summary<T: Summary>(item: &T) {
    println!("Summary: {}", item.summarize());
}

// ✅ Good - Multiple trait bounds
use std::fmt::Display;

fn print_and_summarize<T: Display + Summary>(item: &T) {
    println!("{}: {}", item, item.summarize());
}

// ✅ Good - Where clause for complex bounds
fn complex_function<T, U>(t: &T, u: &U) -> String
where
    T: Display + Clone,
    U: Clone + Debug,
{
    format!("{}", t)
}

// ✅ Good - Associated types
trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
}

struct Counter {
    count: u32,
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;
        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```

**Generic Types**
```rust
// ✅ Good - Generic struct
struct Container<T> {
    value: T,
}

impl<T> Container<T> {
    fn new(value: T) -> Self {
        Container { value }
    }

    fn get(&self) -> &T {
        &self.value
    }
}

// ✅ Good - Implementing methods for specific types
impl Container<String> {
    fn len(&self) -> usize {
        self.value.len()
    }
}

// ✅ Good - Generic enums
enum Result<T, E> {
    Ok(T),
    Err(E),
}

enum Option<T> {
    Some(T),
    None,
}
```

### Lifetimes

**Lifetime Annotations**
```rust
// ✅ Good - Explicit lifetime annotations
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

// ✅ Good - Lifetime in structs
struct ImportantExcerpt<'a> {
    part: &'a str,
}

impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }

    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention: {}", announcement);
        self.part
    }
}

// ✅ Good - Multiple lifetime parameters
fn announce<'a, 'b>(x: &'a str, y: &'b str) -> &'a str
where
    'b: 'a,  // 'b outlives 'a
{
    println!("Announcement: {}", y);
    x
}

// ✅ Good - Static lifetime
fn get_default() -> &'static str {
    "default value"
}
```

## Concurrency

### Threads and Message Passing

**Spawning Threads**
```rust
use std::thread;
use std::time::Duration;

// ✅ Good - Spawning threads
let handle = thread::spawn(|| {
    for i in 1..10 {
        println!("Number {} from spawned thread", i);
        thread::sleep(Duration::from_millis(1));
    }
});

handle.join().unwrap();

// ✅ Good - Moving ownership into threads
let v = vec![1, 2, 3];

let handle = thread::spawn(move || {
    println!("Vector: {:?}", v);
});

handle.join().unwrap();
// v is no longer accessible here
```

**Message Passing with Channels**
```rust
use std::sync::mpsc;
use std::thread;

// ✅ Good - Using channels for communication
let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    let val = String::from("hello");
    tx.send(val).unwrap();
});

let received = rx.recv().unwrap();
println!("Received: {}", received);

// ✅ Good - Multiple producers
let (tx, rx) = mpsc::channel();

let tx1 = tx.clone();
thread::spawn(move || {
    tx.send("Message from thread 1").unwrap();
});

thread::spawn(move || {
    tx1.send("Message from thread 2").unwrap();
});

for received in rx {
    println!("Got: {}", received);
}
```

**Shared State with Mutex and Arc**
```rust
use std::sync::{Arc, Mutex};
use std::thread;

// ✅ Good - Shared state with Arc and Mutex
let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

### Async/Await

**Async Functions**
```rust
use tokio;

// ✅ Good - Async function
async fn fetch_user(id: u32) -> Result<User, Error> {
    let response = reqwest::get(&format!("https://api.example.com/users/{}", id))
        .await?;
    let user = response.json().await?;
    Ok(user)
}

// ✅ Good - Using async in main
#[tokio::main]
async fn main() -> Result<(), Error> {
    let user = fetch_user(1).await?;
    println!("User: {:?}", user);
    Ok(())
}

// ✅ Good - Concurrent async operations
async fn fetch_multiple_users(ids: Vec<u32>) -> Vec<Result<User, Error>> {
    let futures: Vec<_> = ids.into_iter()
        .map(|id| fetch_user(id))
        .collect();

    futures::future::join_all(futures).await
}

// ✅ Good - Select between futures
use tokio::time::{sleep, Duration};

async fn race_operations() -> String {
    tokio::select! {
        _ = sleep(Duration::from_secs(5)) => {
            "Timeout".to_string()
        }
        result = fetch_user(1) => {
            format!("Got user: {:?}", result)
        }
    }
}
```

## Code Organization

### Module System

```rust
// src/lib.rs or src/main.rs
mod models;
mod services;
mod utils;

pub use models::User;
pub use services::UserService;

// src/models/mod.rs
mod user;
mod post;

pub use user::User;
pub use post::Post;

// src/models/user.rs
#[derive(Debug, Clone)]
pub struct User {
    pub id: u32,
    pub name: String,
    pub email: String,
}

impl User {
    pub fn new(id: u32, name: String, email: String) -> Self {
        User { id, name, email }
    }
}

// src/services/user_service.rs
use crate::models::User;

pub struct UserService {
    users: Vec<User>,
}

impl UserService {
    pub fn new() -> Self {
        UserService { users: Vec::new() }
    }

    pub fn add_user(&mut self, user: User) {
        self.users.push(user);
    }
}
```

### Project Structure

```
myproject/
├── Cargo.toml
├── Cargo.lock
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── user.rs
│   │   └── post.rs
│   ├── services/
│   │   ├── mod.rs
│   │   └── user_service.rs
│   └── utils/
│       ├── mod.rs
│       └── validation.rs
├── tests/
│   ├── integration_test.rs
│   └── common/
│       └── mod.rs
└── examples/
    └── example1.rs
```

### Naming Conventions

```rust
// ✅ Good - Rust naming conventions

// Types and traits: PascalCase
struct UserAccount {}
trait Drawable {}
enum Status {}

// Functions and variables: snake_case
fn calculate_total() {}
let user_name = "Alice";

// Constants and statics: SCREAMING_SNAKE_CASE
const MAX_POINTS: u32 = 100_000;
static GLOBAL_CONFIG: Config = Config::new();

// Module names: snake_case
mod user_service;
mod api_client;

// Lifetime parameters: lowercase, short
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {}

// Generic type parameters: Single uppercase letter or PascalCase
fn process<T>(value: T) {}
fn merge<TSource, TTarget>(s: TSource, t: TTarget) {}
```

## Testing Best Practices

### Unit Tests

```rust
// ✅ Good - Unit tests in same file
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_addition() {
        assert_eq!(2 + 2, 4);
    }

    #[test]
    fn test_user_creation() {
        let user = User::new(1, "Alice".to_string(), "alice@example.com".to_string());
        assert_eq!(user.name, "Alice");
        assert_eq!(user.email, "alice@example.com");
    }

    #[test]
    #[should_panic(expected = "Invalid email")]
    fn test_invalid_email() {
        validate_email("invalid");
    }

    #[test]
    fn test_result() -> Result<(), String> {
        let result = divide(10, 2)?;
        assert_eq!(result, 5);
        Ok(())
    }
}

// ✅ Good - Testing with Result
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_divide_success() {
        assert_eq!(divide(10, 2), Ok(5));
    }

    #[test]
    fn test_divide_by_zero() {
        assert!(divide(10, 0).is_err());
    }
}
```

### Integration Tests

```rust
// tests/integration_test.rs
use myproject::UserService;

#[test]
fn test_user_service() {
    let mut service = UserService::new();
    let user = User::new(1, "Alice".to_string(), "alice@example.com".to_string());

    service.add_user(user);

    assert_eq!(service.user_count(), 1);
}
```

### Benchmarking

```rust
#![feature(test)]
extern crate test;

#[cfg(test)]
mod benches {
    use super::*;
    use test::Bencher;

    #[bench]
    fn bench_process(b: &mut Bencher) {
        b.iter(|| {
            process_data(test::black_box(1000))
        });
    }
}
```

## Common Patterns

### Builder Pattern

```rust
// ✅ Good - Builder pattern
#[derive(Debug, Default)]
struct User {
    id: u32,
    name: String,
    email: String,
    age: Option<u32>,
    address: Option<String>,
}

struct UserBuilder {
    id: u32,
    name: String,
    email: String,
    age: Option<u32>,
    address: Option<String>,
}

impl UserBuilder {
    fn new(id: u32, name: String, email: String) -> Self {
        UserBuilder {
            id,
            name,
            email,
            age: None,
            address: None,
        }
    }

    fn age(mut self, age: u32) -> Self {
        self.age = Some(age);
        self
    }

    fn address(mut self, address: String) -> Self {
        self.address = Some(address);
        self
    }

    fn build(self) -> User {
        User {
            id: self.id,
            name: self.name,
            email: self.email,
            age: self.age,
            address: self.address,
        }
    }
}

// Usage
let user = UserBuilder::new(1, "Alice".to_string(), "alice@example.com".to_string())
    .age(30)
    .address("123 Main St".to_string())
    .build();
```

### Newtype Pattern

```rust
// ✅ Good - Newtype pattern for type safety
struct UserId(u32);
struct PostId(u32);

fn get_user(id: UserId) -> User {
    // Can't accidentally pass PostId
}

// Can't mix up IDs
let user_id = UserId(1);
let post_id = PostId(1);

get_user(user_id);  // OK
// get_user(post_id);  // Compile error!
```

### Type State Pattern

```rust
// ✅ Good - Type state pattern for compile-time state validation
struct Locked;
struct Unlocked;

struct Door<State> {
    _state: PhantomData<State>,
}

impl Door<Locked> {
    fn unlock(self) -> Door<Unlocked> {
        println!("Door unlocked");
        Door { _state: PhantomData }
    }
}

impl Door<Unlocked> {
    fn lock(self) -> Door<Locked> {
        println!("Door locked");
        Door { _state: PhantomData }
    }

    fn open(&self) {
        println!("Door opened");
    }
}

// Usage
let door = Door::<Locked> { _state: PhantomData };
let door = door.unlock();
door.open();
let door = door.lock();
// door.open();  // Compile error - door is locked!
```

## Linting & Formatting

### Clippy (Linter)

```bash
# Run clippy
cargo clippy

# Run clippy with all warnings as errors
cargo clippy -- -D warnings

# Auto-fix some issues
cargo clippy --fix
```

### Rustfmt (Formatter)

```bash
# Format all files
cargo fmt

# Check formatting without modifying
cargo fmt -- --check
```

### Configuration (rustfmt.toml)

```toml
max_width = 100
hard_tabs = false
tab_spaces = 4
newline_style = "Unix"
use_small_heuristics = "Default"
reorder_imports = true
reorder_modules = true
remove_nested_parens = true
```

## Common Anti-Patterns

❌ **Unnecessary Cloning**
```rust
// ❌ Bad - Unnecessary clone
fn process(data: &Vec<i32>) -> Vec<i32> {
    data.clone()  // Expensive!
}

// ✅ Good - Borrow or return reference
fn process(data: &[i32]) -> &[i32] {
    data
}
```

❌ **Using unwrap() Unnecessarily**
```rust
// ❌ Bad - Will panic on None
let user = find_user(id).unwrap();

// ✅ Good - Handle Option properly
let user = match find_user(id) {
    Some(user) => user,
    None => return Err("User not found"),
};

// ✅ Good - Use ? operator
let user = find_user(id).ok_or("User not found")?;
```

❌ **String Allocation When Not Needed**
```rust
// ❌ Bad - Allocates unnecessarily
fn greet(name: String) {
    println!("Hello, {}!", name);
}

greet("Alice".to_string());

// ✅ Good - Use string slice
fn greet(name: &str) {
    println!("Hello, {}!", name);
}

greet("Alice");
```

❌ **Ignoring Compiler Warnings**
```rust
// ❌ Bad - Unused variables, mutations
let data = fetch_data();  // Warning: unused variable
let mut count = 0;  // Warning: variable does not need to be mutable

// ✅ Good - Fix warnings
let _data = fetch_data();  // Prefix with _ if intentionally unused
let count = 0;  // Remove mut if not mutated
```

## Performance Tips

**Use String Slices (&str) Instead of String**
```rust
// ✅ Fast - Use &str for read-only strings
fn process(s: &str) {
    println!("{}", s);
}

// ❌ Slow - Unnecessary allocation
fn process(s: String) {
    println!("{}", s);
}
```

**Preallocate Vectors**
```rust
// ✅ Fast - Preallocate capacity
let mut vec = Vec::with_capacity(1000);
for i in 0..1000 {
    vec.push(i);
}

// ❌ Slower - Multiple reallocations
let mut vec = Vec::new();
for i in 0..1000 {
    vec.push(i);
}
```

**Use Iterators Instead of Loops**
```rust
// ✅ Fast - Iterator chains are optimized
let sum: i32 = numbers
    .iter()
    .filter(|&&x| x % 2 == 0)
    .map(|&x| x * 2)
    .sum();

// ❌ Slower - Manual loops
let mut sum = 0;
for &num in &numbers {
    if num % 2 == 0 {
        sum += num * 2;
    }
}
```

**Avoid Unnecessary Allocations**
```rust
// ✅ Fast - Reuse buffer
let mut buffer = String::new();
for line in lines {
    buffer.clear();
    buffer.push_str(line);
    process(&buffer);
}

// ❌ Slow - New allocation each iteration
for line in lines {
    let buffer = String::from(line);
    process(&buffer);
}
```

## Resources

- [The Rust Programming Language Book](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Clippy Lints](https://rust-lang.github.io/rust-clippy/)
- [Awesome Rust](https://github.com/rust-unofficial/awesome-rust)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
