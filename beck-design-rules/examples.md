# Beck Design Rules — Examples

## Example 1: Passes the Tests First

**Bad** — Refactoring without test coverage:
```python
# No tests exist for this function, but we "improve" the design anyway
def calculate_total(items):
    return sum(i.price * i.qty for i in items)  # refactored from a loop
```

**Good** — Add tests before changing anything:
```python
def test_calculate_total():
    items = [Item(price=10, qty=2), Item(price=5, qty=3)]
    assert calculate_total(items) == 35

def test_calculate_total_empty():
    assert calculate_total([]) == 0

# NOW refactor with confidence
def calculate_total(items):
    return sum(i.price * i.qty for i in items)
```

## Example 2: Reveals Intention

**Bad** — Unclear intent:
```python
def proc(d, f):
    return [x for x in d if f(x)]
```

**Good** — Names reveal purpose:
```python
def filter_active_users(users, is_active):
    return [user for user in users if is_active(user)]
```

## Example 3: No Duplication

**Bad** — Repeated logic across handlers:
```python
def create_user(data):
    if not data.get("email"):
        raise ValueError("Email required")
    if "@" not in data["email"]:
        raise ValueError("Invalid email")
    # ... create logic

def update_user(data):
    if not data.get("email"):
        raise ValueError("Email required")
    if "@" not in data["email"]:
        raise ValueError("Invalid email")
    # ... update logic
```

**Good** — Extract shared validation:
```python
def validate_email(email):
    if not email:
        raise ValueError("Email required")
    if "@" not in email:
        raise ValueError("Invalid email")

def create_user(data):
    validate_email(data.get("email"))
    # ... create logic

def update_user(data):
    validate_email(data.get("email"))
    # ... update logic
```

## Example 4: Fewest Elements

**Bad** — Speculative abstraction:
```python
class UserRepositoryInterface(ABC):
    @abstractmethod
    def find(self, id): ...

class UserRepositoryFactory:
    def create(self, config):
        return PostgresUserRepository(config)

class PostgresUserRepository(UserRepositoryInterface):
    def find(self, id):
        return self.db.query("SELECT * FROM users WHERE id = %s", id)
```

**Good** — Only what's needed now:
```python
class UserRepository:
    def find(self, id):
        return self.db.query("SELECT * FROM users WHERE id = %s", id)
```

## Example 5: Rules 2 and 3 Reinforcing Each Other

Sometimes removing duplication *improves* clarity:

**Before** — Duplicated and unclear:
```python
if user.role == "admin":
    log.info(f"Admin {user.name} accessed {resource}")
    grant_access(user, resource)
elif user.role == "editor":
    log.info(f"Editor {user.name} accessed {resource}")
    grant_access(user, resource)
```

**After** — No duplication, clearer intent:
```python
if user.role in AUTHORIZED_ROLES:
    log.info(f"{user.role.title()} {user.name} accessed {resource}")
    grant_access(user, resource)
```
