#memory/language #ruby #best-practices

# Ruby Language Best Practices

> [!NOTE] User Configuration
> Check [[config]] for Ruby preferences:
> - `frameworks.ruby`: Preferred framework (Rails, Sinatra, Hanami)
> - `formatter_ruby`: Code formatter (RuboCop, Prettier for Ruby)
> - `linter_ruby`: Linter (RuboCop, Reek)
> - `test_framework_ruby`: Test framework (RSpec, Minitest, Test::Unit)
> - `package_manager_ruby`: Package manager (Bundler/gem)

## Ruby Philosophy

- **Optimized for programmer happiness**
- **Principle of Least Surprise (POLS)**
- **DRY (Don't Repeat Yourself)**
- **Convention over Configuration**
- **Everything is an object**
- **Duck typing**

## Code Style (RuboCop Community Style Guide)

### Naming Conventions

```ruby
# ✅ Good - Ruby naming conventions
class UserService  # PascalCase for classes and modules
  MAX_USERS = 100  # UPPER_CASE for constants
  
  attr_reader :user_count  # snake_case for methods and variables
  
  def initialize
    @user_count = 0  # @snake_case for instance variables
    @@total_services = 0  # @@snake_case for class variables (avoid these)
  end
  
  def get_user_by_id(user_id)  # snake_case for methods
    local_variable = 'test'  # snake_case for local variables
  end
  
  def active?  # Predicate methods end with ?
    @active
  end
  
  def save!  # Dangerous methods end with !
    # Raises exception on failure
  end
end

# ❌ Bad
class user_service  # Wrong case
  def GetUserById(userId)  # Wrong case
  end
end
```

### Ruby Idioms

**Use Ruby's Expressive Syntax**
```ruby
# ✅ Good - Ruby idioms
users.each { |user| puts user.name }  # Single-line blocks use {}
users.each do |user|  # Multi-line blocks use do...end
  process_user(user)
  log_activity(user)
end

# Guard clauses
return unless user.active?
return if user.nil?

# Symbol to proc
names = users.map(&:name)  # Instead of users.map { |u| u.name }

# Safe navigation operator
user&.profile&.email  # Returns nil if any part is nil

# ❌ Bad
users.each do |user| puts user.name end  # Don't use do...end for one-liners
for user in users  # Avoid for loops, use each
  puts user.name
end
```

**String Interpolation**
```ruby
# ✅ Good - Use interpolation
name = "John"
greeting = "Hello, #{name}!"

# ❌ Bad - String concatenation
greeting = "Hello, " + name + "!"
```

**Hash Syntax**
```ruby
# ✅ Good - Modern hash syntax
user = { name: 'John', email: 'john@example.com' }
user = { 'name' => 'John' } if key.include?('.')  # Hashrocket for string keys

# Access
user[:name]

# ❌ Bad - Old hash syntax (unless required)
user = { :name => 'John', :email => 'john@example.com' }
```

## Object-Oriented Programming

**Classes and Modules**
```ruby
# ✅ Good - Ruby OOP
class User
  attr_reader :id, :name
  attr_accessor :email
  
  def initialize(id:, name:, email:)
    @id = id
    @name = name
    @email = email
  end
  
  def active?
    !@deactivated_at
  end
  
  def to_s
    "User(#{id}, #{name})"
  end
  
  private
  
  def validate_email
    raise ArgumentError, 'Invalid email' unless email.include?('@')
  end
end

# Modules for mixins
module Timestampable
  def created_at
    @created_at ||= Time.now
  end
end

class Post
  include Timestampable
end
```

**Duck Typing**
```ruby
# ✅ Good - Duck typing (if it quacks like a duck...)
def process_payment(processor)
  # Any object with a process method works
  processor.process(amount)
end

class StripeProcessor
  def process(amount)
    # Stripe implementation
  end
end

class PayPalProcessor
  def process(amount)
    # PayPal implementation
  end
end
```

## Blocks, Procs, and Lambdas

```ruby
# ✅ Good - Understanding blocks, procs, lambdas

# Blocks
def with_timing
  start = Time.now
  yield
  puts "Took #{Time.now - start}s"
end

with_timing { expensive_operation }

# Procs (don't check argument count)
multiply = Proc.new { |x, y| x * y }
multiply.call(5, 3)  # => 15

# Lambdas (check argument count, return differently)
multiply = ->(x, y) { x * y }
multiply.call(5, 3)  # => 15

# Passing blocks explicitly
def execute(&block)
  block.call if block_given?
end
```

## Error Handling

```ruby
# ✅ Good - Ruby exception handling
class UserNotFoundError < StandardError
  attr_reader :user_id
  
  def initialize(user_id)
    @user_id = user_id
    super("User not found: #{user_id}")
  end
end

def get_user(id)
  user = User.find_by(id: id)
  raise UserNotFoundError, id unless user
  user
end

# Rescue specific exceptions
begin
  user = get_user(1)
  process_user(user)
rescue UserNotFoundError => e
  logger.error("User not found: #{e.user_id}")
  raise
rescue StandardError => e
  logger.error("Unexpected error: #{e.message}")
  raise
end

# Inline rescue for simple cases
user = find_user(id) rescue nil

# Ensure for cleanup
begin
  file = File.open('data.txt')
  # Process file
ensure
  file.close if file
end
```

## Rails Best Practices

**Models**
```ruby
# ✅ Good - Rails model
class User < ApplicationRecord
  # Associations
  has_many :posts, dependent: :destroy
  belongs_to :organization
  
  # Validations
  validates :email, presence: true, uniqueness: true
  validates :name, length: { minimum: 2 }
  
  # Scopes
  scope :active, -> { where(active: true) }
  scope :recent, -> { where('created_at > ?', 1.week.ago) }
  
  # Callbacks
  before_save :normalize_email
  
  # Class methods
  def self.find_by_email(email)
    find_by(email: email.downcase)
  end
  
  # Instance methods
  def full_name
    "#{first_name} #{last_name}"
  end
  
  private
  
  def normalize_email
    self.email = email.downcase.strip
  end
end
```

**Controllers**
```ruby
# ✅ Good - Skinny controllers
class UsersController < ApplicationController
  before_action :set_user, only: [:show, :update, :destroy]
  
  def index
    @users = User.active.page(params[:page])
    render json: @users
  end
  
  def show
    render json: @user
  end
  
  def create
    @user = UserService.new.create(user_params)
    
    if @user.persisted?
      render json: @user, status: :created
    else
      render json: { errors: @user.errors }, status: :unprocessable_entity
    end
  end
  
  private
  
  def set_user
    @user = User.find(params[:id])
  end
  
  def user_params
    params.require(:user).permit(:name, :email)
  end
end
```

**Service Objects**
```ruby
# ✅ Good - Service objects for complex logic
class UserCreationService
  def initialize(user_params)
    @user_params = user_params
  end
  
  def call
    ActiveRecord::Base.transaction do
      user = User.create!(@user_params)
      send_welcome_email(user)
      notify_admin(user)
      user
    end
  rescue ActiveRecord::RecordInvalid => e
    Rails.logger.error("User creation failed: #{e.message}")
    raise
  end
  
  private
  
  def send_welcome_email(user)
    UserMailer.welcome(user).deliver_later
  end
  
  def notify_admin(user)
    AdminNotifier.new_user(user).deliver_later
  end
end
```

## Testing with RSpec

```ruby
# ✅ Good - RSpec tests
RSpec.describe UserService do
  describe '#create' do
    let(:valid_params) { { name: 'John', email: 'john@example.com' } }
    
    context 'with valid parameters' do
      it 'creates a user' do
        user = described_class.new.create(valid_params)
        
        expect(user).to be_persisted
        expect(user.name).to eq('John')
      end
      
      it 'sends welcome email' do
        expect(UserMailer).to receive(:welcome).and_return(double(deliver_later: true))
        
        described_class.new.create(valid_params)
      end
    end
    
    context 'with invalid parameters' do
      let(:invalid_params) { { name: '', email: 'invalid' } }
      
      it 'raises validation error' do
        expect {
          described_class.new.create(invalid_params)
        }.to raise_error(ActiveRecord::RecordInvalid)
      end
    end
  end
end

# Feature tests
RSpec.describe 'User management', type: :feature do
  it 'allows creating a new user' do
    visit new_user_path
    
    fill_in 'Name', with: 'John Doe'
    fill_in 'Email', with: 'john@example.com'
    click_button 'Create User'
    
    expect(page).to have_content('User created successfully')
  end
end
```

## Metaprogramming (Use Sparingly)

```ruby
# ✅ Good - Metaprogramming when it adds value
class DynamicFinder
  def self.method_missing(method_name, *args)
    if method_name.to_s.start_with?('find_by_')
      attribute = method_name.to_s.sub('find_by_', '')
      find_by(attribute => args.first)
    else
      super
    end
  end
  
  def self.respond_to_missing?(method_name, include_private = false)
    method_name.to_s.start_with?('find_by_') || super
  end
end

# ⚠️ Use with caution - can make code hard to understand
```

## Code Quality (RuboCop)

```yaml
# .rubocop.yml
AllCops:
  NewCops: enable
  TargetRubyVersion: 3.2

Style/StringLiterals:
  EnforcedStyle: single_quotes

Metrics/LineLength:
  Max: 120

Metrics/MethodLength:
  Max: 15
```

```bash
# Run RuboCop
rubocop

# Auto-correct issues
rubocop -a
```

## Common Anti-Patterns

❌ **Long Method Chains**
```ruby
# ❌ Bad
users.select(&:active?).map(&:email).sort.uniq.join(', ')

# ✅ Good - Break it up
active_users = users.select(&:active?)
emails = active_users.map(&:email)
unique_emails = emails.uniq.sort
unique_emails.join(', ')
```

❌ **Not Using Symbols**
```ruby
# ❌ Bad - Strings as hash keys (unless dynamic)
user = { 'name' => 'John', 'email' => 'john@example.com' }

# ✅ Good
user = { name: 'John', email: 'john@example.com' }
```

❌ **Modifying Frozen Strings**
```ruby
# ❌ Bad (Ruby 3+, strings are frozen by default)
greeting = "Hello"
greeting << " World"  # Might error in frozen string mode

# ✅ Good
greeting = "Hello"
greeting += " World"
# Or
greeting = "#{greeting} World"
```

## Performance Tips

- Use `find_each` instead of `each` for large datasets
- Use `pluck` instead of `map` for database queries
- Avoid N+1 queries (use `includes`, `joins`, `eager_load`)
- Use background jobs for slow operations
- Profile with `rack-mini-profiler` or `bullet`

## Resources

- [Ruby Style Guide](https://rubystyle.guide/)
- [Rails Guides](https://guides.rubyonrails.org/)
- [Effective Testing with RSpec](https://pragprog.com/titles/rspec3/effective-testing-with-rspec-3/)
- [RuboCop](https://rubocop.org/)
