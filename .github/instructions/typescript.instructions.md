---
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
description: 'TypeScript and JavaScript development best practices following ISE Engineering Playbook guidelines'
---

# TypeScript/JavaScript Code Instructions

Follow ISE JavaScript/TypeScript Code Review Checklist and modern JavaScript best practices.

**IMPORTANT**: Use the `iseplaybook` MCP server to get the latest TypeScript best practices. Use `context7` MCP server for React, Node.js, and framework-specific documentation. Do not assume—verify current guidance.

## Code Style

- Use 2-space indentation
- Prefer `const` over `let`, avoid `var`
- Use arrow functions for callbacks
- Use template literals for string interpolation
- Semicolons: consistent with project style (ESLint enforced)

## TypeScript Best Practices

### Type Definitions

- Enable strict mode in `tsconfig.json`
- Define explicit types for function parameters and return values
- Use interfaces for object shapes, types for unions/primitives
- Avoid `any` - use `unknown` if type is truly unknown
- Prefer discriminated unions for state modeling (e.g., `loading | error | success`)
- Add explicit return types on exported functions
- Use `readonly` for immutable data structures

```typescript
// ✅ Good
interface User {
  id: string;
  name: string;
  email: string;
}

function getUser(id: string): Promise<User | undefined> {
  // implementation
}

// ❌ Avoid
function getUser(id: any): any {
  // implementation
}
```

### Type Guards

```typescript
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'name' in obj
  );
}
```

## React Best Practices

### Functional Components

Use functional components with hooks (no class components):

```tsx
interface UserCardProps {
  user: User;
  onSelect?: (id: string) => void;
}

const UserCard: React.FC<UserCardProps> = ({ user, onSelect }) => {
  const handleClick = () => {
    onSelect?.(user.id);
  };

  return (
    <div onClick={handleClick}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
};
```

### Hooks

- Use `useState` for local state
- Use `useEffect` with proper dependency arrays
- Create custom hooks for reusable logic
- Use `useMemo` and `useCallback` for performance optimization
- Treat `exhaustive-deps` warnings as correctness bugs; fix dependencies instead of disabling
- Stabilize props passed to memoized components to avoid render storms

```tsx
const useUser = (id: string) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    let cancelled = false;

    const fetchUser = async () => {
      try {
        const data = await api.getUser(id);
        if (!cancelled) setUser(data);
      } catch (err) {
        if (!cancelled) setError(err as Error);
      } finally {
        if (!cancelled) setLoading(false);
      }
    };

    fetchUser();
    return () => { cancelled = true; };
  }, [id]);

  return { user, loading, error };
};
```

## Error Handling

### Async/Await

```typescript
async function fetchData<T>(url: string): Promise<T> {
  const response = await fetch(url);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  
  return response.json();
}
```

### Try/Catch Patterns

```typescript
try {
  const data = await fetchData<User[]>('/api/users');
  return data;
} catch (error) {
  console.error('Failed to fetch users:', error);
  throw error; // Re-throw or handle appropriately
}
```

## Module Organization

```text
src/
  ├── components/     # Reusable UI components
  │   ├── Button/
  │   │   ├── Button.tsx
  │   │   ├── Button.test.tsx
  │   │   └── index.ts
  ├── hooks/          # Custom React hooks
  ├── pages/          # Page-level components
  ├── services/       # API and external services
  ├── types/          # TypeScript type definitions
  └── utils/          # Utility functions
```

## Testing

### Unit Tests with Jest/Vitest

```typescript
describe('UserService', () => {
  it('should fetch user by id', async () => {
    // Arrange
    const mockUser = { id: '1', name: 'Test User' };
    jest.spyOn(api, 'getUser').mockResolvedValue(mockUser);

    // Act
    const result = await service.getUser('1');

    // Assert
    expect(result).toEqual(mockUser);
  });
});
```

### React Testing Library

```tsx
import { render, screen, fireEvent } from '@testing-library/react';

test('calls onSelect when clicked', () => {
  const onSelect = jest.fn();
  render(<UserCard user={mockUser} onSelect={onSelect} />);

  fireEvent.click(screen.getByRole('button'));

  expect(onSelect).toHaveBeenCalledWith(mockUser.id);
});
```

## Linting and Formatting

Use ESLint and Prettier:

```json
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended"
  ]
}
```

## References

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [React Documentation](https://react.dev/)
- [ISE TypeScript Checklist](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/javascript-and-typescript/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
