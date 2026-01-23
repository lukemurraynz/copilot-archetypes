---
applyTo: "**/*.cs"
description: "C# and .NET development best practices (ISE-aligned) with DDD-lite architectural guardrails"
---
# C# Code Instructions (ISE + DDD-lite Guardrails)

## How to Use This File

Follow repository conventions first (.editorconfig, analyzers, Directory.Build.props, Directory.Packages.props).

Apply the rules in this file next.

### Use MCP servers (only when needed)

- **iseplaybook** → ISE C# and engineering standards
- **context7** → .NET APIs and version-specific behavior
- **microsoft-learn** → official Microsoft guidance

### Precedence order

- Repo standards → this file → MCP servers

If the repository is not layered (e.g., vertical slices or minimal APIs), apply only the guardrails that fit and do not force structure.

## Default Architecture Posture (New Projects)

Default to **DDD-lite** unless the repository clearly uses another style.

### DDD-lite means

- Strong layer and dependency boundaries
- Business invariants in the domain
- Minimal abstractions
- No mandatory CQRS, UoW, sagas, or eventing

### DDD-lite does not mean

- Heavy ceremony
- Over-abstracting “just in case”
- Premature bounded contexts or event sourcing

## C# Expert Agent Profile
You are an expert C#/.NET developer.

You produce clean, secure, readable, maintainable code that:

- Matches existing project patterns
- Uses modern .NET appropriately
- Avoids unnecessary architectural changes

### When Invoked

- Identify app type, TFM, C# version, nullable status
- Scan for violations before proposing refactors
- Introduce patterns (CQRS, UoW, sagas, etc.) only if:
  - The repo already uses them, or
  - The user explicitly requests them
- Prefer minimal, safe diffs
- Improve performance only with evidence or clear justification

## Required Preflight (Before Any Code Changes)

### Platform Checks

- Target Framework Moniker (TFM)
- C# language version
- global.json
- `<Nullable>enable</Nullable>`
- Directory.Build.props / Directory.Packages.props

Do not change TFM, SDK, or language version unless explicitly requested.

### Architecture Preflight (DDD-lite)

#### Briefly state:

- Core domain concept(s) involved (use ubiquitous language)
- Layer(s) impacted: Domain / Application / Infrastructure / Presentation
- Where invariants will live (domain method or value object)
- Integration/eventing needs (if unknown, do not introduce them)
- External/third-party dependencies & resilience needs: scan for external dependencies in Preflight; introduce resilience libraries only with evidence of need to avoid over-engineering.

If the repo clearly uses a different architecture, adapt—do not force DDD.

## DDD-lite Layering Rules

### Domain Layer

#### Allowed

- Entities, aggregates, value objects
- Domain services (stateless, multi-aggregate logic)
- Domain exceptions and invariants

#### Not Allowed

- EF Core (DbContext, IQueryable)
- HTTP, SQL, messaging clients
- Logging, configuration, DI container usage
- File system, environment access

#### Hard guardrails

- No serialization concerns in Domain (no `[Json*]` attributes, no DTOs).
- No I/O in Domain. Domain methods must not perform network/disk/database calls.

### Application Layer

#### Allowed

- Use-case orchestration
- Input validation
- Authorization and workflow decisions
- Calling domain methods
- Ports (interfaces) for persistence and external systems

#### Not Allowed

- Business invariants duplicated from Domain
- Persistence or transport concerns

#### Hard guardrails

- Application code must not expose EF types (DbContext, EF entities, IQueryable) to callers.
- Do not return IQueryable from repositories/services. Materialize or return explicit read models.

### Infrastructure Layer

- EF Core mappings and repositories
- External adapters (HTTP, messaging, file system)
- Caching, telemetry wiring

#### Optional Resilience (Infrastructure Layer)

- For external calls (HTTP, DB), prefer `Microsoft.Extensions.Http.Resilience` (NET8+) and add `builder.AddStandardResilienceHandler()` on `IHttpClientBuilder` — includes retry, circuit breaker, timeout, and rate limiting with sensible defaults.
- Customize policies per service criticality.
- Configure resilience in Infrastructure setup only; never in Domain or Application.
- Scan for external dependencies in Preflight; introduce resilience policies only when evidence shows it's needed to avoid over-engineering.

### Presentation Layer

- Controllers/endpoints/UI
- AuthN/AuthZ
- DTO mapping and validation
- ProblemDetails and error shaping

## Recommended Folder Structure (New Layered Projects)

```text
src/
  Project.Domain/
  Project.Application/
  Project.Infrastructure/
  Project.Api/
```

This is a recommendation, not a requirement.

## Domain Modeling Defaults

### Entities & Aggregates

- Use classes with identity-based equality
- Protect invariants with methods (Activate(), Cancel())
- Avoid public setters
- Expose collections as `IReadOnlyCollection<T>`
- Prefer factories when invariants exist

### Value Objects

- Use records
- Immutable
- Validate on creation

### Aggregate Boundaries

- Reference other aggregates by ID, not object reference
- Keep aggregates small and consistent

### Domain Services

Use only when logic:

- Is domain logic, and
- Spans multiple aggregates

## Domain Purity (DDD-lite)

Domain methods should be side-effect free except mutating their own state.

If domain events are used, Domain may record events, but publishing is handled outside the Domain.

## Data Mapping & Serialization

Mapping between DTOs and domain objects happens in Presentation or Application, never in Domain.

Domain types must not depend on serialization frameworks or attributes.

Prefer explicit DTOs/contracts at boundaries (API/UI), and explicit domain types internally.

## Time, Randomness, and Determinism

To keep code testable and deterministic:

- Prefer TimeProvider (or a small port like IClock) over scattered `DateTime.UtcNow`.
- Avoid scattering `Guid.NewGuid()` / randomness across domain logic when it matters for tests; inject via a port when determinism is useful.
- If the repo already uses a standard approach (e.g., `TimeProvider.System`, `IIdGenerator`), follow it.

## Code Design Rules

Introduce interfaces only for:

- DI seams
- Ports (repositories, gateways)
- Public APIs
- Testing

- Do not wrap existing abstractions
- Visibility: `private` > `internal` > `protected` > `public`
- Default to `internal` unless a type is intended as public API
- Do not edit auto-generated code
- Comments explain WHY, not what
- Reuse existing methods
- XML docs for public APIs
- User-facing strings → resources
- Keep naming consistent

## API Surface & Compatibility

- Avoid making domain types public unless you are building a reusable package.

**If building a public API or reusable library:**

- Prefer additive, backwards-compatible changes
- Avoid breaking changes without explicit approval and a migration plan

## Naming Conventions

- Private fields: `_logger`
- Constants: `MaxRetryCount`
- Interfaces: `IUserService`
- Async methods: `GetUserAsync`
- Parameters: `userId`, `cancellationToken`
- Properties: `FirstName`

## Error Handling

- Use `ArgumentNullException.ThrowIfNull(user);`

- Example:

```csharp
if (string.IsNullOrWhiteSpace(name))
    throw new ArgumentException("Name cannot be empty", nameof(name));

throw new InvalidOperationException("Invalid state");
```

- Guard early
- Use precise exception types
- Log and rethrow at boundaries
- Never swallow exceptions

Domain Exceptions
Use domain-specific exceptions for business rule violations

Do not throw infrastructure exceptions from Domain

## Results vs Exceptions

**Use exceptions for:**

- Invariant violations
- Unexpected failures

For expected validation failures at boundaries (API/UI), prefer:

- `ProblemDetails` (RFC7807) for HTTP APIs
- Explicit result types only if the repo already uses them (do not introduce new result frameworks by default)

Modern C# Features (When TFM Allows)
File-scoped namespaces

Raw string literals

Switch expressions

Ranges and indices

Records for DTOs/events

Records vs Classes
Records → immutable DTOs/events (value equality)

Classes → entities/aggregates (identity)

No mutable fields in records

Async & Performance
Always

Async suffix for async methods

Pass CancellationToken

Avoid sync-over-async

Avoid Task.Run in ASP.NET Core

Avoid fire-and-forget without handling

public Task<User> GetUserAsync(int id) =>
    _repository.FindAsync(id);
## Libraries

- Use ConfigureAwait(false) only in reusable libraries

## Dependency Injection & Ports

**Lifetimes:**

- Singleton → stateless/shared
- Scoped → per request
- Transient → lightweight

- Do not capture scoped services in singletons
- Validate critical options on startup
- Ports are allowed to preserve boundaries

## Production & Observability

- No hard-coded secrets
- Input validation everywhere
- `IHttpClientFactory` + retries (idempotent only)
- `ProblemDetails` (RFC7807)
- Correlation IDs
- Structured logging (no secrets/PII)

**Optional:**

- OpenTelemetry
- Health/readiness checks

## Security

- Validate all inputs
- Avoid untrusted deserialization
- Use managed identity/configuration for secrets
- SSRF protection via allowlists
- For regulated domains: explicitly note audit, auth, and privacy decisions

## Testing

- Test project: `[Project].Tests`
- Mirror production structure
- Naming: `Method_Condition_ExpectedResult`
- Arrange-Act-Assert
- One behavior per test
- Parallel-safe
- Test through public APIs
- Mock externals only

Example:

```csharp
[Fact]
public async Task GetUserAsync_WhenExists_ReturnsUser()
{
    // Arrange

    // Act

    // Assert
}
```
## Coverage

- Follow repo gates
- Prioritize Domain and critical paths

Example tooling:

```bash
dotnet tool install -g dotnet-coverage
dotnet-coverage collect -f cobertura -o coverage.xml dotnet test
```
Analyzers & Style
Respect .editorconfig

Treat warnings as errors in CI

Use var when obvious

Follow Microsoft C# conventions

## Earned Patterns (Do NOT Introduce by Default)

Only introduce when the repo already uses them or the user explicitly requests them:

- CQRS / Mediator frameworks
- Unit of Work
- Sagas / process managers
- Outbox / event-driven integration
- Multiple bounded contexts

Always explain trade-offs.

Final Self-Check (Before Responding)
Before delivering changes, verify:

Boundaries respected (Domain is pure; no EF/HTTP/logging/config/DI in Domain)

No IQueryable or EF entities leaked through Application/ports

Invariants enforced in Domain (not controllers/handlers)

Mapping/serialization kept out of Domain

Time/randomness handled deterministically where it matters

No unnecessary patterns introduced

Tests added or updated

Minimal, safe diffs

Repo conventions followed

If any item cannot be confirmed, ask for clarification before proceeding.
