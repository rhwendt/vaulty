#memory/testing #qa #workflow

# Testing & QA Best Practices

## Testing Philosophy

- **Test behavior, not implementation** - Tests should verify what code does, not how
- **Write tests first (TDD)** - Or at least alongside code
- **Keep tests simple** - Tests should be easier to understand than the code
- **Fast feedback** - Tests should run quickly
- **Reliable** - Tests should not be flaky

## Types of Testing

### 1. Unit Tests
**Purpose**: Test individual functions/methods in isolation

**Characteristics**:
- Fast (milliseconds)
- No external dependencies
- Use mocks/stubs for dependencies
- Test one thing at a time

**Example**:
```python
def test_calculate_discount():
    # Given
    price = 100
    discount_percent = 20

    # When
    result = calculate_discount(price, discount_percent)

    # Then
    assert result == 80
```

### 2. Integration Tests
**Purpose**: Test how components work together

**Characteristics**:
- Slower than unit tests
- May use test database
- Test interaction between modules
- Verify contracts between components

**Example**:
```python
def test_user_registration_flow():
    # Test database interaction + email service
    user = register_user("test@example.com", "password123")
    assert user.is_active == False
    assert email_was_sent(user.email)
```

### 3. End-to-End Tests
**Purpose**: Test complete user workflows

**Characteristics**:
- Slowest tests
- Test full system
- Simulate real user behavior
- Catch integration issues

**Example**:
```python
def test_complete_purchase_flow():
    browser.visit("/products")
    browser.click("Add to Cart")
    browser.fill_in("card_number", "4242424242424242")
    browser.click("Complete Purchase")
    assert browser.has_text("Order Confirmed")
```

### 4. Manual Testing
**Purpose**: Exploratory testing, UX validation

**When to use**:
- New features before release
- Complex user workflows
- Visual/UX verification
- Edge cases not covered by automation

## Testing Pyramid

```
     /\
    /E2E\      ← Fewer, slower, expensive
   /------\
  /INTEG.  \   ← Medium number, moderate speed
 /----------\
/ UNIT TESTS \ ← Many, fast, cheap
--------------
```

**Guideline**:
- 70% Unit tests
- 20% Integration tests
- 10% E2E tests

## Test Structure

### AAA Pattern (Arrange-Act-Assert)

```python
def test_user_creation():
    # Arrange - Set up test data
    username = "testuser"
    email = "test@example.com"

    # Act - Perform the action
    user = User.create(username, email)

    # Assert - Verify the outcome
    assert user.username == username
    assert user.email == email
    assert user.id is not None
```

### Given-When-Then (BDD Style)

```python
def test_applying_discount():
    # Given - Initial state
    cart = ShoppingCart()
    cart.add_item(Item(price=100))

    # When - Action occurs
    cart.apply_discount_code("SAVE20")

    # Then - Expected outcome
    assert cart.total == 80
```

## Test Naming

**Pattern**: `test_<what>_<condition>_<expected_result>`

**Examples**:
```python
test_user_login_with_valid_credentials_succeeds()
test_user_login_with_invalid_password_fails()
test_calculate_total_with_empty_cart_returns_zero()
test_apply_discount_with_expired_code_raises_error()
```

## What to Test

### ✅ DO Test
- **Business logic**: Core functionality
- **Edge cases**: Empty inputs, null values, boundary conditions
- **Error handling**: Exceptions, validation failures
- **State changes**: Database updates, file modifications
- **Public APIs**: Interfaces used by other code
- **Bug fixes**: Write test first to reproduce, then fix

### ⚠️ Test Carefully
- **External services**: Use mocks/stubs
- **UI details**: Focus on behavior, not CSS
- **Private methods**: Test through public interface

### ❌ Don't Test
- **Framework code**: Trust the framework
- **Language features**: Trust the language
- **Trivial code**: Getters/setters with no logic
- **Third-party libraries**: Trust well-tested libraries

## Test Quality

### Good Tests Are:

**FIRST**
- **Fast**: Run quickly (< 1 second for unit tests)
- **Independent**: Don't depend on other tests
- **Repeatable**: Same result every time
- **Self-validating**: Pass or fail clearly
- **Timely**: Written with or before code

### Test Smells

❌ **Flaky Tests**: Pass sometimes, fail others
- Caused by: timing issues, shared state, external dependencies
- Fix: Use mocks, remove sleeps, clean state

❌ **Slow Tests**: Take too long to run
- Caused by: DB access, network calls, file I/O
- Fix: Use mocks, test databases, optimize

❌ **Brittle Tests**: Break when implementation changes
- Caused by: Testing implementation details
- Fix: Test behavior and outcomes

❌ **Unclear Tests**: Hard to understand what's being tested
- Caused by: Poor naming, complex setup
- Fix: Use AAA pattern, descriptive names

## Mocking and Stubbing

### When to Mock
- External APIs
- Databases (for unit tests)
- File systems
- Time/date functions
- Random number generators

### Mock Example
```python
from unittest.mock import Mock, patch

def test_send_notification():
    # Mock external email service
    mock_email = Mock()

    with patch('app.email_service', mock_email):
        notify_user("user@example.com", "Hello")

        mock_email.send.assert_called_once_with(
            to="user@example.com",
            body="Hello"
        )
```

## Test Coverage

### Coverage Goals
- **Minimum**: 70% overall
- **Critical paths**: 90-100%
- **New code**: 80-100%

### Coverage ≠ Quality
- 100% coverage doesn't mean bug-free
- Test quality matters more than quantity
- Focus on testing important behaviors

### Checking Coverage
```bash
# Python
pytest --cov=app --cov-report=html

# JavaScript
npm test -- --coverage

# Go
go test -cover ./...
```

## Testing Checklist

### Before Committing
- [ ] All tests pass locally
- [ ] New code has tests
- [ ] Tests cover edge cases
- [ ] No commented-out tests
- [ ] No skipped tests without reason
- [ ] Tests are fast and independent

### In Code Review
- [ ] Tests verify behavior, not implementation
- [ ] Test names are descriptive
- [ ] Mocks are used appropriately
- [ ] Tests follow AAA pattern
- [ ] No duplicated test code

## Common Testing Patterns

### Testing Exceptions
```python
def test_divide_by_zero_raises_error():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)
```

### Testing Async Code
```python
async def test_async_function():
    result = await fetch_data()
    assert result is not None
```

### Parameterized Tests
```python
@pytest.mark.parametrize("input,expected", [
    (0, 0),
    (1, 1),
    (-1, 1),
    (5, 25),
])
def test_square(input, expected):
    assert square(input) == expected
```

### Testing Database Changes
```python
def test_create_user(db_session):
    user = User(username="test")
    db_session.add(user)
    db_session.commit()

    # Verify in database
    saved_user = db_session.query(User).filter_by(username="test").first()
    assert saved_user is not None
    assert saved_user.username == "test"
```

## CI/CD Integration

### Run Tests Automatically
- On every commit
- On pull requests
- Before deployment
- On schedule (nightly for slow tests)

### Test Stages
```
1. Lint/Format checks (seconds)
2. Unit tests (seconds to minutes)
3. Integration tests (minutes)
4. E2E tests (minutes to hours)
```

### Fail Fast
- Run fastest tests first
- Stop on first failure in CI
- Parallelize when possible

## QA Process

### Manual QA Checklist
- [ ] Feature works as specified
- [ ] Edge cases handled gracefully
- [ ] Error messages are clear
- [ ] UI is responsive and accessible
- [ ] Works across browsers (if web)
- [ ] Works on different devices/screen sizes
- [ ] Performance is acceptable
- [ ] No console errors or warnings

### Bug Reporting Template
```markdown
## Bug Description
Brief description of the issue

## Steps to Reproduce
1. Step 1
2. Step 2
3. Step 3

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS:
- Browser/Version:
- App Version:

## Screenshots
[If applicable]

## Additional Context
Any other relevant information
```

## Related

- [[code-review]] - For review practices
- [[git-workflow]] - For committing test code
- [[deployment]] - For testing in deployment pipeline
