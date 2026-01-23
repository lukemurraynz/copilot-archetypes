---
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
description: "TypeScript and JavaScript best practices aligned with ISE Engineering Playbook and Fluent UI v9 (2026)"
---

# TypeScript/JavaScript Copilot Instructions

Follow the ISE JavaScript/TypeScript Code Review Checklist and modern JavaScript best practices.

## Authority and conflicts

- **Source of truth**: ISE checklist and referenced servers (ISE Playbook + Context7) are authoritative; this file is a repo-specific overlay.
- If these instructions conflict with lint rules or ISE guidance, prefer in this order:
  1. Project lint configuration (ESLint, TypeScript)
  2. ISE guidance
  3. This file (open a follow-up PR to reconcile)
- Always use the `iseplaybook` MCP server for the latest TypeScript guidance and `context7` for React/Node/framework specifics; do not guess, verify.

## General code style

- Use 2-space indentation.
- Prefer `const` over `let`; avoid `var`.
- Prefer arrow functions for callbacks and inline functions.
- Use template literals for string interpolation and multi-line strings.
- Follow the existing semicolon style; ESLint is the arbiter.

## TypeScript best practices

### Types and configuration

- Enable `"strict": true` in `tsconfig.json` and prefer:
  - `"noUncheckedIndexedAccess": true`
  - `"exactOptionalPropertyTypes": true`
  - `"noImplicitOverride": true`
  - `"verbatimModuleSyntax": true`

```jsonc
// tsconfig.json (2026 baseline)
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "verbatimModuleSyntax": true,
  },
}
```

Prefer .ts/.tsx for new code; for legacy .js/.jsx, enable checkJs where feasible and migrate incrementally.

### Type design

Prefer type aliases over interface for most object shapes (simpler, supports unions natively); reserve interface for extension-heavy cases.

Avoid any; use unknown when the type is truly unknown, then narrow via type guards before access.

Prefer discriminated unions for domain state (for example: loading | error | success).

Add explicit types on:

- Exported functions
- Public class methods
- Module/public boundaries and external callbacks

Prefer inference for obvious locals and callback parameters to reduce noise.

Use readonly on immutable properties/collections when appropriate.

```ts
// ✅ Good: explicit boundary types, inferred locals, type alias
type User = {
  readonly id: string;
  name: string;
  email: string;
};

function getUser(id: string): Promise<User | undefined> {
  const normalizedId = id.trim();
  // implementation
  return Promise.resolve(undefined);
}

// ❌ Avoid: untyped boundaries
function getUserLegacy(id: any): any {
  // implementation
}
```

#### #### Type guards

Use user-defined type guards to safely narrow unknown and complex unions.

```ts
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" && obj !== null && "id" in obj && "name" in obj
  );
}
```

### Generics, utilities, and boundaries

Prefer standard utilities (Partial, Pick, Omit, Record, Readonly) over custom helpers.

Use generics for reusable abstractions, but avoid overly complex conditional types in app code.

Expose stable interface types at module boundaries; avoid leaking persistence/transport-layer shapes directly to UI.

Prefer string/literal unions for domain modeling; use enum only when required for interop or protocol constraints.

### React and Fluent UI v9

#### Components and hooks

Use functional components with hooks; avoid class components.

Type props explicitly; do not rely on React.FC for children. Type children explicitly when needed.

Avoid business-logic-heavy useEffect in components; prefer extracting logic into custom hooks/services.

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
    <button type="button" onClick={handleClick}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </button>
  );
};
```

Use useState for local state; avoid duplicated derived state (derive counts/flags from source state in render).

Treat exhaustive-deps warnings as correctness bugs; fix dependency arrays instead of disabling the rule.

Use useMemo/useCallback and stable props for memoized components in hot paths.

```tsx
// ❌ Avoid duplicated derived state
const [items, setItems] = useState<Item[]>([]);
const [itemsCount, setItemsCount] = useState(0);

useEffect(() => {
  setItemsCount(items.length);
}, [items]);

// ✅ Prefer derived values
const [items, setItems] = useState<Item[]>([]);
const itemsCount = items.length;
```

Fluent UI v9 usage (2026)
Use a single FluentProvider at the app root with token-based theming (light/dark/high-contrast). Set dir="rtl" or dir="ltr" for automatic RTL support.

Prefer imports from @fluentui/react-components and @fluentui/react-theme; avoid undocumented deep imports.

Use Griffel makeStyles() and mergeClasses() for styling; prefer slots API over CSS class selectors for child customization.

Use Fluent UI controls instead of custom div/span for interactive components to get accessibility and theming by default.

```tsx
import {
  FluentProvider,
  webLightTheme,
  tokens,
  Button,
  makeStyles,
  mergeClasses,
} from "@fluentui/react-components";

// Token-first theming
const useRootStyles = makeStyles({
  root: {
    color: tokens.colorNeutralForeground1,
    padding: tokens.spacingMedium,
  },
});

const App = () => {
  const styles = useRootStyles();

  return (
    <FluentProvider theme={webLightTheme} dir="ltr">
      <div className={styles.root}>
        <Button appearance="primary">Hello World</Button>
      </div>
    </FluentProvider>
  );
};
```

```tsx
// ✅ Slots API for customization (preferred over CSS selectors)
const CustomButton = () => (
  <Button icon={<span className="myIcon">★</span>} appearance="primary" />
);
```

Error handling and async patterns
Use async/await with explicit error paths; check response.ok and model domain errors explicitly.[web:3][web:5]

```ts
async function fetchJson<T>(url: string): Promise<T> {
  const response = await fetch(url);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }

  return response.json() as Promise<T>;
}
```

Prefer discriminated unions or Result<Ok, Err>-style types for expected failures in core logic; reserve throwing for unexpected/unrecoverable conditions.

In production code, prefer centralized logging/telemetry abstractions over `console.*`.

Testing
Use Jest/Vitest for unit tests and React Testing Library for UI tests.

Keep TypeScript strictness in tests equal to app code; avoid any in tests.

```ts
describe("UserService", () => {
  it("fetches user by id", async () => {
    const mockUser: User = {
      id: "1",
      name: "Test User",
      email: "test@example.com",
    };
    jest.spyOn(api, "getUser").mockResolvedValue(mockUser);

    const result = await service.getUser("1");

    expect(result).toEqual(mockUser);
  });
});
```

tsx

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

it("calls onSelect when clicked", async () => {
  const onSelect = vi.fn();
  render(<UserCard user={mockUser} onSelect={onSelect} />);

  await userEvent.click(screen.getByRole("button", { name: /test user/i }));

  expect(onSelect).toHaveBeenCalledWith(mockUser.id);
});
```

Prefer role-based queries (getByRole/findByRole) and userEvent for realistic user flows.

Prefer focused module mocks/spies over broad global jest.mock where possible.

Performance and accessibility
Avoid creating new objects/functions inline in hot paths; hoist or memoize.

Stabilize props passed to memoized components and lists to limit re-renders.

Ensure interactive elements have accessible roles, labels, and appropriate ARIA attributes; manage focus correctly for dialogs/menus.

Use Fluent UI slots API and color (not fill) for icon styling; automatic RTL via FluentProvider dir prop.

Linting, formatting, and monorepo posture
Use ESLint with TypeScript, React, hooks, a11y, and testing plugins as a baseline.

```json
// .eslintrc.json (2026 baseline)
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:testing-library/react",
    "plugin:jest-dom/recommended"
  ]
}
```

Keep the codebase lint-clean; only add eslint-disable with justification and a clear TODO.

Integrate Prettier either via ESLint (eslint-plugin-prettier) or as a separate formatter; choose one flow and document it.

In monorepos, keep per-package src/ boundaries and shared types/config in dedicated packages to avoid duplication.

Module organization

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

References
TypeScript docs[https://www.typescriptlang.org/docs/]

React docs[https://react.dev/]

ISE JavaScript/TypeScript Code Reviews[https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/javascript-and-typescript/]

Fluent UI React v9 [https://react.fluentui.dev/]
