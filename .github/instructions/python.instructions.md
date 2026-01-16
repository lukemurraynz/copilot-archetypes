---
applyTo: "**/*.py"
description: 'Python development best practices following ISE Engineering Playbook guidelines'
---

# Python Code Instructions

Follow ISE Python Code Review Checklist and PEP 8 style guidelines.

**IMPORTANT**: Use the `iseplaybook` MCP server to get the latest Python best practices. Use `context7` MCP server for framework-specific documentation (FastAPI, Django, etc.). Do not assume—verify current guidance.

## Async/Await Governance

- Use `async`/`await` for I/O-bound work (HTTP, file, database).
- Avoid mixing threads with asyncio unless bridging legacy code.
- Always propagate cancellation (`asyncio.CancelledError`); do not swallow it.
- Use `asyncio.timeout()` / `asyncio.wait_for()` for timeouts.

## Code Style

- Use 4-space indentation
- Maximum line length: 88-120 characters (Black default: 88)
- Use snake_case for functions and variables
- Use PascalCase for classes
- Use UPPER_SNAKE_CASE for constants

## Type Hints

Use type hints for function signatures and complex variables:

```python
from typing import Optional, List, Dict, Any

def get_user(user_id: int) -> Optional[User]:
    """Retrieve a user by ID."""
    return user_repository.find(user_id)

def process_items(items: List[str]) -> Dict[str, Any]:
    """Process a list of items and return results."""
    return {item: len(item) for item in items}
```

## Docstrings

Use Google-style docstrings:

```python
def calculate_discount(price: float, discount_percent: float) -> float:
    """Calculate the discounted price.

    Args:
        price: The original price.
        discount_percent: The discount percentage (0-100).

    Returns:
        The discounted price.

    Raises:
        ValueError: If discount_percent is not between 0 and 100.

    Example:
        >>> calculate_discount(100.0, 20.0)
        80.0
    """
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount must be between 0 and 100")
    return price * (1 - discount_percent / 100)
```

## Classes

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    """Represents a user in the system."""
    
    id: int
    name: str
    email: str
    is_active: bool = True
    
    def __post_init__(self) -> None:
        """Validate user data after initialization."""
        if not self.email or '@' not in self.email:
            raise ValueError("Invalid email address")
```

## Error Handling

Use specific exception types:

```python
class UserNotFoundError(Exception):
    """Raised when a user is not found."""
    pass

class ValidationError(Exception):
    """Raised when validation fails."""
    pass

def get_user_or_fail(user_id: int) -> User:
    """Get a user or raise an error."""
    user = get_user(user_id)
    if user is None:
        raise UserNotFoundError(f"User {user_id} not found")
    return user
```

## Logging

Use the logging module with structured messages and correlation IDs:

```python
import logging

logger = logging.getLogger(__name__)

def process_order(order_id: str, correlation_id: str) -> None:
    """Process an order."""
    logger.info("Processing order", extra={"order_id": order_id, "correlation_id": correlation_id})
    try:
        # processing logic
        logger.info("Order processed successfully", extra={"order_id": order_id, "correlation_id": correlation_id})
    except Exception as e:
        logger.error("Failed to process order", extra={"order_id": order_id, "correlation_id": correlation_id}, exc_info=True)
        raise
```

## Async/Await

For asynchronous code:

```python
import asyncio
from typing import List

async def fetch_user(user_id: int) -> User:
    """Fetch a user asynchronously."""
    async with httpx.AsyncClient() as client:
        response = await client.get(f"/api/users/{user_id}")
        response.raise_for_status()
        return User(**response.json())

async def fetch_users(user_ids: List[int]) -> List[User]:
    """Fetch multiple users concurrently."""
    tasks = [fetch_user(uid) for uid in user_ids]
    return await asyncio.gather(*tasks)
```

## Testing

### pytest Best Practices

```python
import pytest
from unittest.mock import Mock, patch

class TestUserService:
    """Tests for UserService."""

    @pytest.fixture
    def service(self):
        """Create a test service instance."""
        return UserService(repository=Mock())

    def test_get_user_returns_user(self, service):
        """Test that get_user returns a user when found."""
        # Arrange
        expected_user = User(id=1, name="Test", email="test@example.com")
        service.repository.find.return_value = expected_user

        # Act
        result = service.get_user(1)

        # Assert
        assert result == expected_user
        service.repository.find.assert_called_once_with(1)

    def test_get_user_returns_none_when_not_found(self, service):
        """Test that get_user returns None when user not found."""
        service.repository.find.return_value = None
        assert service.get_user(999) is None
```

## Project Structure

```text
src/
  ├── __init__.py
  ├── main.py           # Application entry point
  ├── config.py         # Configuration management
  ├── models/           # Data models
  ├── services/         # Business logic
  ├── repositories/     # Data access
  ├── api/              # API endpoints
  └── utils/            # Utility functions
tests/
  ├── __init__.py
  ├── conftest.py       # pytest fixtures
  ├── unit/             # Unit tests
  └── integration/      # Integration tests
```

## Tools and Linting

Use these tools for code quality:

- **Black**: Code formatting
- **Flake8**: Linting
- **isort**: Import sorting
- **mypy**: Type checking
- **pytest**: Testing

```toml
# pyproject.toml
[tool.black]
line-length = 88

[tool.isort]
profile = "black"

[tool.mypy]
strict = true
```

## References

- [PEP 8 Style Guide](https://pep8.org/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [ISE Python Checklist](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/python/)
- [Python Documentation](https://docs.python.org/3/)
