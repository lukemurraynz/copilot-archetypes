---
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
description: 'TypeScript and JavaScript development best practices following ISE Engineering Playbook guidelines'
---

# TypeScript/JavaScript Code Instructions

Follow ISE JavaScript/TypeScript Code Review Checklist and modern JavaScript best practices.

**Source of truth**: the ISE checklist plus the referenced servers (ISE Playbook + Context7) are authoritative. This file is a profile/overlay that tightens or relaxes guidance for this repo.

**Conflict resolution**: if these instructions conflict with lint rules or ISE guidance, prefer the lint configuration first, then ISE guidance. Open a follow-up to align this document.

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
- Prefer `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, and `noImplicitOverride` when compatible with the target runtime/tooling
- Define explicit types for function parameters and return values on public APIs; prefer inference for obvious locals to reduce noise
- Use interfaces for object shapes, types for unions/primitives
- Avoid `any` - use `unknown` if type is truly unknown
- Prefer discriminated unions for state modeling (e.g., `loading | error | success`)
- Add explicit return types on exported functions
- Use `readonly` for immutable data structures

- When using `unknown`, narrow with type guards before access and avoid unsafe casts

### Type Inference vs Explicit Types

- Prefer inference for obvious locals and callback parameters when the type is clear from assignment
- Require explicit types for module/public boundaries (exports, public class methods, external callbacks)

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

Use functional components with hooks (no class components). Prefer explicit props typing over `React.FC`; do not rely on `React.FC` for implicit `children`—type children explicitly when needed:

```tsx
interface UserCardProps {
  user: User;
  onSelect?: (id: string) => void;
}

const UserCard = ({ user, onSelect }: UserCardProps) => {
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
- Prefer stable callbacks/objects; avoid effect-driven derived state when the value can be computed during render
- Stabilize props passed to memoized components to avoid render storms
- Avoid business-logic-heavy `useEffect` in components; push data access and orchestration into hooks/services

#### Avoid duplicated state

```tsx
// ❌ Avoid
const [items, setItems] = useState<Item[]>([]);
const [itemsCount, setItemsCount] = useState(0);

useEffect(() => {
  setItemsCount(items.length);
}, [items]);

// ✅ Prefer
const [items, setItems] = useState<Item[]>([]);
const itemsCount = items.length;
```

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

### Domain-level error modeling

- Prefer discriminated unions (e.g., `Result<Ok, Err>`) for expected failures in core logic
- Reserve throwing for truly exceptional/unrecoverable cases

### Logging strategy

- In production code, prefer centralized logging/telemetry abstractions over raw `console.*`

## Fluent UI v9

- Use a single `FluentProvider` at the app root.
- Prefer token-first theming and define light/dark/high-contrast themes.
- Use Griffel `makeStyles()` + `mergeClasses()`; avoid DOM-coupled selectors.
- Prefer Fluent UI components for accessibility instead of custom div-based controls.

- Slots/overrides are supported through the Fluent UI slots model; follow slot APIs for component customization (Fluent UI slots docs).
- Prefer package-level imports from `@fluentui/react-components` as shown in Fluent UI v9 examples; avoid deep imports unless documented.
- Tree-shaking is bundler-dependent; verify your bundler's ES module/tree-shaking configuration rather than assuming deep import benefits.

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

### JavaScript usage

- Prefer `.ts`/`.tsx` for new code
- For legacy `.js`/`.jsx`, enable `checkJs` where feasible and migrate incrementally

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
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('calls onSelect when clicked', async () => {
  const onSelect = jest.fn();
  render(<UserCard user={mockUser} onSelect={onSelect} />);

  await userEvent.click(screen.getByRole('button', { name: /select/i }));

  expect(onSelect).toHaveBeenCalledWith(mockUser.id);
});
```

- Prefer role-based queries (`getByRole`, `findByRole`) and `userEvent` for user flows.
- Avoid `fireEvent` for user interactions unless you are testing low-level events.

### Test isolation and mocking

- Prefer lightweight module mocks/spies over global `jest.mock` when possible
- For hooks, use React Testing Library and a hook test utility if adopted by the repo

### Type-safe tests

- Keep TS strictness in tests equal to app code
- Avoid `any` in tests; use helper types or factories to model fixtures safely

## Performance Hygiene

- Avoid inline object/function creation in hot paths; hoist or memoize.
- Stabilize props to memoized components to prevent unnecessary re-renders.

## Accessibility

- Use accessible labels/aria attributes on interactive controls.
- Manage focus for dialogs/menus (focus trap, initial focus, restore on close).

## Linting and Formatting

Use ESLint and Prettier:

```json
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:testing-library/recommended",
    "plugin:jest-dom/recommended"
  ]
}
```

- Keep the codebase lint-clean; do not add `eslint-disable` without justification and a comment

### Prettier integration

- Choose one flow and document it: ESLint runs Prettier (`eslint-plugin-prettier`) **or** Prettier runs separately

### tsconfig posture

- Prefer a canonical base config with `"strict": true` and explicit additions
- Document any exceptions (e.g., libraries vs apps)

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true
  }
}
```

### Generics and utility types

- Prefer standard utility types (`Partial`, `Pick`, `Omit`, `Record`) over custom helpers
- Use generics for reusable abstractions; avoid over-complex conditional types in app code

### Module boundary types

- Export stable interface types at module boundaries
- Avoid leaking implementation types (e.g., DB row types directly to UI)

### Enums vs unions

- Prefer string/literal union types for domain modeling
- Use enums only for interop with external systems or protocols

### Monorepo layout

- In workspaces, keep per-package `src/` boundaries and shared types/config in dedicated packages to avoid duplication

## References

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [React Documentation](https://react.dev/)
- [ISE TypeScript Checklist](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/javascript-and-typescript/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
