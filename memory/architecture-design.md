#memory/architecture #design #workflow

# Architecture & Design Best Practices

> [!NOTE] User Configuration
> Check [[config]] for your personal architecture preferences:
> - `languages`: Your preferred programming languages (in priority order)
> - `frameworks`: Your preferred frameworks by language
> - `database_relational`: Your preferred relational database
> - `database_nosql`: Your preferred NoSQL database
> - `preferred_license`: License preference for new projects

## Architecture Philosophy

- **Start simple, evolve as needed** - Don't over-engineer upfront
- **Solve today's problems** - Don't design for hypothetical future
- **Make it easy to change** - Flexibility over premature optimization
- **Document decisions** - Future maintainers will thank you
- **Consistency matters** - Follow established patterns

## Design Principles

### SOLID Principles

**Single Responsibility**
- Each class/module should have one reason to change
- Do one thing and do it well

**Open/Closed**
- Open for extension, closed for modification
- Use interfaces and abstractions

**Liskov Substitution**
- Subtypes should be substitutable for their base types
- Maintain behavioral consistency

**Interface Segregation**
- Many specific interfaces better than one general one
- Clients shouldn't depend on unused methods

**Dependency Inversion**
- Depend on abstractions, not concretions
- High-level modules shouldn't depend on low-level modules

### Other Key Principles

**DRY (Don't Repeat Yourself)**
- Extract common logic into reusable components
- But don't create abstractions too early

**KISS (Keep It Simple, Stupid)**
- Simplest solution that works is often the best
- Complexity is the enemy of maintainability

**YAGNI (You Aren't Gonna Need It)**
- Don't build features for hypothetical future needs
- Wait until you actually need it

**Separation of Concerns**
- Divide system into distinct features with minimal overlap
- Each part handles one aspect of functionality

## Architecture Patterns

### Layered Architecture

```
┌─────────────────────┐
│  Presentation Layer │  ← UI, Controllers, Views
├─────────────────────┤
│   Business Layer    │  ← Business Logic, Services
├─────────────────────┤
│  Persistence Layer  │  ← Data Access, Repositories
├─────────────────────┤
│    Database Layer   │  ← Database
└─────────────────────┘
```

**Pros**: Clear separation, easy to understand
**Cons**: Can become monolithic
**Use When**: Traditional CRUD applications

### Clean Architecture / Hexagonal

```
┌─────────────────────────────────┐
│  External (UI, DB, APIs)        │
│  ┌───────────────────────────┐  │
│  │  Adapters/Interfaces      │  │
│  │  ┌─────────────────────┐  │  │
│  │  │  Use Cases          │  │  │
│  │  │  ┌───────────────┐  │  │  │
│  │  │  │  Entities     │  │  │  │
│  │  │  │  (Core)       │  │  │  │
│  │  │  └───────────────┘  │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

**Pros**: Highly testable, technology independent
**Cons**: More complex, requires discipline
**Use When**: Complex business logic, long-term projects

### Microservices

```
Service A ←→ API Gateway ←→ Service B
   ↓                            ↓
Database A                  Database B
```

**Pros**: Independent deployment, scalability
**Cons**: Distributed system complexity
**Use When**: Large teams, need independent scaling

### Event-Driven

```
Producer → Event Bus → Consumer(s)
```

**Pros**: Loose coupling, scalability
**Cons**: Eventual consistency, debugging complexity
**Use When**: Async workflows, high scalability needs

## Design Patterns

### Creational Patterns

**Singleton**
```python
class Database:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```
**Use**: Single shared instance (configs, connections)

**Factory**
```python
def create_payment_processor(type):
    if type == "stripe":
        return StripeProcessor()
    elif type == "paypal":
        return PayPalProcessor()
```
**Use**: Object creation logic abstraction

**Builder**
```python
query = QueryBuilder()
    .select("name", "email")
    .from_table("users")
    .where("active", True)
    .build()
```
**Use**: Complex object construction

### Structural Patterns

**Adapter**
```python
class OldAPIAdapter:
    def __init__(self, old_api):
        self.old_api = old_api

    def new_method(self):
        return self.old_api.old_method()
```
**Use**: Make incompatible interfaces work together

**Decorator**
```python
@cache_result
@log_execution
def expensive_function():
    ...
```
**Use**: Add behavior without modifying original

**Repository**
```python
class UserRepository:
    def find_by_id(self, id): ...
    def find_by_email(self, email): ...
    def save(self, user): ...
```
**Use**: Abstract data access layer

### Behavioral Patterns

**Strategy**
```python
class PaymentContext:
    def __init__(self, strategy):
        self.strategy = strategy

    def pay(self, amount):
        return self.strategy.process(amount)
```
**Use**: Interchangeable algorithms

**Observer**
```python
class EventEmitter:
    def subscribe(self, event, callback): ...
    def emit(self, event, data): ...
```
**Use**: Notify multiple objects of changes

**Command**
```python
class Command:
    def execute(self): ...
    def undo(self): ...
```
**Use**: Encapsulate operations as objects

## Architectural Decision Records (ADRs)

### ADR Template

```markdown
# ADR-001: [Decision Title]

**Status**: [Proposed | Accepted | Deprecated | Superseded]
**Date**: YYYY-MM-DD
**Deciders**: [Names]
**Tags**: #adr #architecture

## Context

What is the issue we're addressing? What are the constraints?

## Decision

What did we decide to do?

## Consequences

### Positive
- Benefit 1
- Benefit 2

### Negative
- Tradeoff 1
- Tradeoff 2

### Neutral
- Other impact 1

## Alternatives Considered

### Option 1: [Name]
**Pros**:
**Cons**:
**Why rejected**:

### Option 2: [Name]
**Pros**:
**Cons**:
**Why rejected**:

## References

- [Link to discussion]
- [Related documentation]
```

### Example ADR

```markdown
# ADR-003: Use PostgreSQL for Primary Database

**Status**: Accepted
**Date**: 2025-01-15
**Deciders**: Backend Team

## Context

We need to choose a primary database for our application.
Requirements:
- ACID compliance
- Complex queries with joins
- Strong consistency
- Open source

## Decision

We will use PostgreSQL as our primary database.

## Consequences

### Positive
- Strong ACID compliance
- Rich feature set (JSON, full-text search)
- Excellent documentation and community
- Good performance for our use case

### Negative
- Requires more operational expertise than managed NoSQL
- Vertical scaling limitations (can be mitigated)

## Alternatives Considered

### Option 1: MongoDB
**Pros**: Flexible schema, easy to start
**Cons**: Weaker consistency guarantees, limited join support
**Why rejected**: Our data is highly relational

### Option 2: MySQL
**Pros**: Widely known, good performance
**Cons**: Fewer features than PostgreSQL
**Why rejected**: PostgreSQL's JSON support is superior
```

## System Design Checklist

### Scalability
- [ ] Can handle expected load
- [ ] Can scale horizontally
- [ ] Caching strategy defined
- [ ] Database indexing planned
- [ ] CDN for static assets

### Reliability
- [ ] Single points of failure identified
- [ ] Backup and recovery plan
- [ ] Monitoring and alerting
- [ ] Graceful degradation
- [ ] Retry logic for failures

### Security
- [ ] Authentication mechanism
- [ ] Authorization model
- [ ] Data encryption (at rest and in transit)
- [ ] Input validation
- [ ] Secrets management
- [ ] OWASP top 10 addressed

### Performance
- [ ] Response time requirements defined
- [ ] Database queries optimized
- [ ] Caching implemented
- [ ] Async processing for slow operations
- [ ] Load testing planned

### Maintainability
- [ ] Clear code organization
- [ ] Consistent patterns
- [ ] Documentation
- [ ] Logging strategy
- [ ] Error handling

## API Design

### RESTful API Best Practices

**Resource Naming**
```
GET    /users           # List users
GET    /users/123       # Get specific user
POST   /users           # Create user
PUT    /users/123       # Update user
DELETE /users/123       # Delete user

GET    /users/123/posts # Get user's posts
```

**Response Format**
```json
{
  "data": {
    "id": "123",
    "type": "user",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  },
  "meta": {
    "timestamp": "2025-01-15T10:00:00Z"
  }
}
```

**Error Format**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "details": [
      {
        "field": "email",
        "issue": "Must be valid email address"
      }
    ]
  }
}
```

**HTTP Status Codes**
- `200 OK` - Success
- `201 Created` - Resource created
- `204 No Content` - Success with no response body
- `400 Bad Request` - Client error
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Authenticated but not authorized
- `404 Not Found` - Resource doesn't exist
- `500 Internal Server Error` - Server error

### API Versioning

```
# URL versioning
/api/v1/users
/api/v2/users

# Header versioning
GET /api/users
Accept: application/vnd.api+json; version=1
```

## Database Design

### Normalization

**1NF**: Atomic values, no repeating groups
**2NF**: 1NF + no partial dependencies
**3NF**: 2NF + no transitive dependencies

**When to Denormalize**
- Read-heavy workloads
- Performance critical queries
- Data doesn't change often

### Indexing Strategy

```sql
-- Index foreign keys
CREATE INDEX idx_posts_user_id ON posts(user_id);

-- Composite index for common queries
CREATE INDEX idx_posts_user_status ON posts(user_id, status);

-- Partial index for filtered queries
CREATE INDEX idx_active_users ON users(email) WHERE active = true;
```

### Schema Versioning

- Use migration files
- Make changes backward compatible
- Test migrations on production-like data
- Have rollback plan

## Performance Optimization

### Caching Strategy

**Cache Levels**
```
Browser Cache (client-side)
    ↓
CDN Cache (edge)
    ↓
Application Cache (Redis)
    ↓
Database Query Cache
    ↓
Database
```

**Cache Invalidation**
- Time-based (TTL)
- Event-based (on updates)
- Manual (admin action)

### Query Optimization

```python
# ❌ N+1 Query Problem
users = User.all()
for user in users:
    posts = user.posts  # Separate query each time

# ✅ Eager Loading
users = User.all().with_("posts")
for user in users:
    posts = user.posts  # Already loaded
```

## Related

- [[documentation]] - For documenting architecture
- [[code-review]] - For reviewing architectural changes
- [[testing-qa]] - For testing architectural components
