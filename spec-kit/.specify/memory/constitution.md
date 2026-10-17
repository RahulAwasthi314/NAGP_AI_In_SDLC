<!--
SYNC IMPACT REPORT
==================
Version Change: Draft Template → 1.0.0 (Ratified)
Ratification Date: 2026-10-17
Last Amended Date: 2026-10-17

Modified Principles:
- [PRINCIPLE_1_NAME] → I. Architectural Integrity & Clean Vertical Slices
- [PRINCIPLE_2_NAME] → II. Modern .NET 10 & C# 13 Backend Standards
- [PRINCIPLE_3_NAME] → III. Modern Angular 22 Frontend Standards (Signals-First & Standalone)
- [PRINCIPLE_4_NAME] → IV. Test-First Quality & Automated Verification (xUnit & Vitest)
- [PRINCIPLE_5_NAME] → V. Strict Typing, API Contracts & Observability (OpenAPI & TypeScript)

Added Sections:
- Technology Stack Specifications & Best Practices Standards
- Development Workflow, Review Process & Quality Gates

Removed Sections:
- Placeholder template guidelines

Follow-up TODOs:
- None. All bracketed tokens have been resolved with concrete, version-aligned rules.
-->

# Invoice Approval System Constitution

## Core Principles

### I. Architectural Integrity & Clean Vertical Slices
The solution architecture MUST maintain a strict separation of concerns across both backend and frontend layers.
- Business domain models and core rules MUST remain independent of external frameworks, transport protocols, UI components, and database drivers.
- In `InvoiceApprovalBackend`, dependencies MUST flow inward toward the domain layer. Direct coupling between API endpoints and persistence implementation details is prohibited; domain contracts, commands, queries, and interfaces MUST mediate interactions.
- In `InvoiceApprovalFrontend`, UI presentation components MUST remain decoupled from network transport and business orchestration by isolating API services and state management stores.
- Circular dependencies between layers, projects, or feature modules are strictly forbidden.

### II. Modern .NET 10 & C# 13 Backend Standards
Backend services built in `InvoiceApprovalBackend` MUST follow contemporary .NET 10 and C# 13 idioms, ensuring high performance, memory efficiency, and readability.
- **Language & Compiler Features**: Nullable reference types (`<Nullable>enable</Nullable>`) and implicit usings MUST be strictly enforced with zero warnings. Use primary constructors for dependency injection, `record` types for immutable Data Transfer Objects (DTOs) and domain events, pattern matching, collection expressions (`[...]`), and file-scoped namespaces.
- **Asynchronous Execution**: All I/O operations (database queries, network requests, file access) MUST be fully asynchronous using `async`/`await`. Endpoints and repository methods MUST accept and propagate a `CancellationToken`. Synchronous blocking calls (`.Result`, `.Wait()`, `Thread.Sleep()`) are strictly forbidden.
- **Minimal APIs & Typed Results**: API endpoints MUST utilize ASP.NET Core Minimal APIs or strongly-typed controllers with explicit endpoint names and metadata. Return types MUST use strongly-typed results (e.g., `TypedResults.Ok<T>()`, `TypedResults.NotFound()`, `TypedResults.BadRequest()`).
- **Standardized Error Handling**: APIs MUST adhere to RFC 7807 Problem Details for all client-facing errors via `builder.Services.AddProblemDetails()`. Raw exceptions and stack traces MUST never leak into HTTP responses.
- **Dependency Injection**: Register all dependencies with appropriate lifecycles (`Transient`, `Scoped`, or `Singleton`). The Service Locator antipattern (manually querying `IServiceProvider` inside application services) is prohibited. Configuration settings MUST be bound using strongly-typed options (`IOptions<T>` / `IOptionsSnapshot<T>`).

### III. Modern Angular 22 Frontend Standards (Signals-First & Standalone)
Frontend applications built in `InvoiceApprovalFrontend` MUST leverage Angular 22 and TypeScript modern best practices for reactivity, performance, and modularity.
- **Standalone Architecture**: 100% of components, directives, and pipes MUST be Standalone (`standalone: true` or Angular default). Legacy `NgModule` definitions are strictly prohibited.
- **Reactivity with Angular Signals**: Component state and derived values MUST be managed primarily using Angular Signals (`signal()`, `computed()`, `effect()`). RxJS is reserved for asynchronous event streams and complex transformations (e.g., debouncing, cancellation). Manual subscriptions in components MUST be avoided in favor of `toSignal()`, and any persistent subscriptions MUST use `takeUntilDestroyed()`.
- **Modern Control Flow**: Templates MUST use built-in control flow blocks (`@if`, `@else`, `@for ... track`, `@switch`, `@case`, `@empty`, `@defer`). Legacy structural directives (`*ngIf`, `*ngFor`, `*ngSwitch`) are strictly prohibited.
- **Change Detection & Performance**: All components MUST configure `changeDetection: ChangeDetectionStrategy.OnPush`. Non-critical UI elements (such as secondary modals, chart widgets, and below-the-fold content) MUST leverage `@defer` blocks to optimize initial bundle delivery and time-to-interactive.
- **Dependency Injection & Routing**: Dependency injection in components and services MUST utilize the modern `inject()` function instead of constructor parameter injection. Routing MUST use functional guards (`CanActivateFn`) and lazy loading via `loadComponent` or `loadChildren`.
- **Typed Reactive Forms**: Form processing MUST use strictly-typed Reactive Forms (`FormGroup`, `FormControl`, `NonNullableFormBuilder`). Two-way template-driven `[(ngModel)]` bindings are prohibited for core business forms.

### IV. Test-First Quality & Automated Verification (xUnit & Vitest)
Quality is maintained through comprehensive automated testing that validates business functionality prior to delivery.
- **Backend Testing**: `InvoiceApprovalBackend` MUST be covered by automated test suites using xUnit, FluentAssertions, and NSubstitute/Moq for unit tests. Integration tests MUST use `Microsoft.AspNetCore.Mvc.Testing` (`WebApplicationFactory`) to validate end-to-end API contracts, route bindings, and HTTP status codes.
- **Frontend Testing**: `InvoiceApprovalFrontend` unit and component tests MUST run via Vitest (`ng test`) with `provideHttpClientTesting()` and Angular component testing harnesses. Tests MUST verify signal mutations, template control flow conditions, and user interaction outcomes.
- **Deterministic & Isolated Tests**: Unit tests MUST execute deterministically without external network or persistent database dependencies. All tests MUST pass before code is merged into the trunk.

### V. Strict Typing, API Contracts & Observability (OpenAPI & TypeScript)
Cross-boundary contracts and runtime diagnostics MUST be unambiguous, strongly typed, and observable.
- **OpenAPI / REST Contract**: The backend MUST expose comprehensive OpenAPI definitions generated via `Microsoft.AspNetCore.OpenApi` (`builder.Services.AddOpenApi()`, `app.MapOpenApi()`).
- **End-to-End Type Safety**: TypeScript definitions on the frontend MUST precisely mirror backend DTOs. The TypeScript compiler MUST enforce strict mode; usage of `any` is prohibited (`unknown` with type narrowing MUST be used when data shapes are dynamic).
- **Structured Logging**: Backend application code MUST use structured logging via `ILogger<T>` with parameterized message templates. Logging sensitive credentials, personal identifiable information (PII), or confidential invoice financial details in plain text is strictly forbidden.
- **Health Checks & Resilience**: Backend services MUST provide health check endpoints (`/healthz`, `/readyz`). Frontend services MUST gracefully handle HTTP failure codes (e.g., 401, 403, 404, 500) and present actionable user feedback.

## Technology Stack Specifications & Best Practices Standards

### Backend Specifications
- **Target Framework**: .NET 10.0 (`net10.0`)
- **Language Version**: C# 13
- **Web API Engine**: ASP.NET Core Minimal APIs / Controller endpoints
- **API Documentation**: Microsoft.AspNetCore.OpenApi (v10.0.0-preview+)
- **Code Style & Formatting**: `.editorconfig` enforced; Roslyn analyzers enabled; `dotnet format` compliant
- **Security**: Mandatory HTTPS redirection (`app.UseHttpsRedirection()`), secure CORS policy definition, input sanitization, and parameterized queries for persistence

### Frontend Specifications
- **Framework**: Angular 22 (v22.2.x)
- **Language**: TypeScript (v6.0.x) with `strict: true` and `noImplicitAny: true`
- **Testing Engine**: Vitest (v5.0.x) with Angular CLI test runner
- **Styling**: Modern CSS3 utilizing CSS Custom Properties (variables), Flexbox/Grid, and BEM/scoped conventions
- **Code Quality**: Prettier (v3.8.x) formatting with `.prettierrc` configuration

## Development Workflow, Review Process & Quality Gates

### Specification-Driven Development
1. **Spec First**: Features and architectural changes MUST begin with a specification and technical plan generated and approved through the Spec Kit workflow (`.specify/`).
2. **Acceptance Criteria**: Every user story or feature specification MUST include clear, testable acceptance criteria before code authoring begins.

### Pull Request & Review Gates
- **Build & Format Gate**: Code MUST build cleanly with zero compiler warnings and pass Prettier (`npm run prettier --check` / Prettier CLI) and .NET format checks.
- **Testing Gate**: 100% of unit and integration test suites MUST execute cleanly in local and CI environments.
- **Code Review**: Peer reviews MUST specifically verify compliance with this Constitution, focusing on Signal usage in Angular, async/cancellation token propagation in .NET, and strict boundary typing.

## Governance

This Constitution serves as the single source of truth for architectural standards, technical rules, and implementation practices for the Invoice Approval System.
- **Supremacy**: The rules laid out in this Constitution supersede individual preferences, tribal knowledge, and ad-hoc conventions. Code that violates these tenets MUST NOT be merged.
- **Amendment Process**: Amendments to this document require:
  1. A formal proposal detailing technical motivation, impact assessment, and migration path.
  2. Review and consensus among project technical leads and contributors.
  3. Incremental update of the Constitution version using Semantic Versioning (MAJOR for breaking changes or principle removals, MINOR for new principles or significant guidance expansions, PATCH for clarifications and typo fixes).
- **Compliance Verification**: All pull requests, code reviews, and automated specification audits MUST reference this Constitution to ensure continuous alignment across the lifecycle.

**Version**: 1.0.0 | **Ratified**: 2026-10-17 | **Last Amended**: 2026-10-17
