# AI-Assisted SDD Best Practices for Angular and .NET
## OpenSpec + GitHub Spec Kit + Agent Skills + Repository Instructions

> **Purpose**
>
> This document defines a common team approach for AI-assisted software development across repositories that use **OpenSpec**, **GitHub Spec Kit**, **Angular**, and **.NET / ASP.NET Core**.
>
> The goal is **not** to force every repository to use the same SDD tool. The goal is to make engineering principles, AI instructions, skills, verification steps, and quality gates consistent across the organization.

---

## 1. Recommended Mental Model

Treat AI-assisted development as several independent layers.

```text
Business Requirement
        |
        v
+---------------------------+
| SDD Tool                  |
| OpenSpec OR GitHub SpecKit|
+-------------+-------------+
              |
              v
+---------------------------+
| Repository Instructions   |
| AGENTS.md / Constitution  |
| OpenSpec config           |
+-------------+-------------+
              |
              v
+---------------------------+
| Agent Skills              |
| Angular / .NET / company  |
+-------------+-------------+
              |
              v
+---------------------------+
| Coding Agent              |
| Codex / Copilot / Claude  |
| Gemini / Cursor / etc.    |
+-------------+-------------+
              |
              v
+---------------------------+
| Tooling / MCP / CLI       |
| ng / dotnet / tests       |
+-------------+-------------+
              |
              v
+---------------------------+
| Verification              |
| build + test + lint       |
| spec consistency          |
+---------------------------+
```

| Layer | Responsibility |
|---|---|
| SDD | Defines what is being built and why |
| Repository rules | Defines non-negotiable engineering constraints |
| Skills | Gives the agent specialized implementation knowledge |
| Agent | Performs analysis and implementation |
| Tooling | Gives the agent access to build/test/framework tools |
| Verification | Proves the result satisfies the specification and project rules |

---

# 2. Team Rule: One SDD System per Repository

A repository should normally use **either OpenSpec or GitHub Spec Kit as its primary SDD system**.

Avoid running both systems as parallel sources of truth in the same repository unless the team is performing a deliberate migration.

Bad:

```text
openspec/specs/user-login
.specify/specs/001-user-login

Both describe the same feature.
```

This creates risks such as:

- conflicting requirements;
- duplicated tasks;
- unclear ownership;
- conflicting architectural instructions;
- agents reading stale specifications;
- developers not knowing which artifact is authoritative.

Recommended:

```text
Repository A -> Angular + OpenSpec
Repository B -> Angular + GitHub Spec Kit
Repository C -> .NET + OpenSpec
Repository D -> .NET + GitHub Spec Kit
```

The **engineering standards remain common**, even though the SDD workflow differs.

---

# 3. Shared Repository Instructions

Every AI-enabled repository should have a clear repository-level instruction file.

Recommended:

```text
AGENTS.md
```

Use it for rules that must apply regardless of whether the developer is currently using OpenSpec, Spec Kit, or manually asking an AI agent to change code.

Typical content:

```markdown
# Repository Engineering Rules

## General

- Preserve the existing architecture unless a specification explicitly changes it.
- Prefer small, reviewable changes.
- Do not introduce new frameworks or libraries without justification.
- Do not modify generated files manually.
- Do not weaken existing tests to make a feature pass.
- Do not remove validation, authorization, logging, or error handling without explicit requirements.

## Before completing a task

- Build the affected projects.
- Run relevant tests.
- Run lint/static analysis.
- Verify the implementation against the current feature specification.
```

Think of `AGENTS.md` as:

> **How agents must behave in this repository.**

---

# 4. Skills vs Repository Rules

Do not put everything into a skill.

## Put it in `AGENTS.md` / Constitution / OpenSpec config when:

The rule is mandatory for almost every change.

Examples:

- Controllers must not expose database entities.
- Angular components must not make direct HTTP calls.
- All production code must have tests.
- The project uses Clean Architecture.
- Nullable reference types must remain enabled.
- New Angular code should use modern standalone patterns.

## Put it in a Skill when:

The agent needs specialized reusable knowledge for a specific type of task.

Examples:

```text
angular-developer
company-angular-design-system
company-api-contracts
dotnet-domain-modeling
database-migrations
observability
security-review
```

Skills should contain **focused expertise**, not the entire project constitution.

---

# 5. Recommended Skill Organization

Where supported by the coding agent:

```text
.agents/
└── skills/
    ├── angular-developer/
    │   └── SKILL.md
    ├── company-angular/
    │   └── SKILL.md
    ├── company-dotnet/
    │   └── SKILL.md
    ├── company-api/
    │   └── SKILL.md
    └── testing-strategy/
        └── SKILL.md
```

Do not duplicate large amounts of official framework documentation.

Prefer:

```text
Official framework skill
        +
Small company-specific skill
```

instead of a huge custom framework skill copied from documentation. This reduces stale instructions.

---

# 6. Angular Projects

## 6.1 Use the Official Angular Agent Skills

The Angular team publishes official Agent Skills.

Install them with:

```bash
npx skills add https://github.com/angular/skills
```

The most important general skill is:

```text
angular-developer
```

It provides current Angular guidance for areas such as components, services, Signals, dependency injection, routing, forms, HTTP, SSR, accessibility, testing, and Angular CLI.

Do not recreate the complete Angular framework guidance in a company skill unless necessary.

---

## 6.2 Recommended Angular Repository Rules

Example:

```markdown
## Angular

- Follow the Angular version used by this repository.
- Prefer modern standalone Angular APIs.
- Do not introduce NgModules unless required by the existing architecture or a dependency.
- Prefer Signals for local reactive state.
- Use `computed()` for derived Signal state.
- Use `effect()` only for real side effects.
- Prefer modern Angular template control flow such as `@if`, `@for`, and `@switch`.
- Keep components focused on presentation and orchestration.
- Move reusable business logic into services, stores, facades, or domain functions.
- Components must not contain duplicated API/data transformation logic.
- Do not call `HttpClient` directly from presentation components.
- Preserve accessibility and keyboard behavior.
- New behavior requires relevant automated tests.
```

These are good candidates for `AGENTS.md`.

---

## 6.3 Angular AI Configuration

Angular CLI provides AI-oriented configuration support.

Where appropriate:

```bash
ng generate ai-config
```

Use the configuration generated for the coding agent used by the repository.

---

## 6.4 Angular MCP

Angular provides an Angular CLI MCP server.

Typical command:

```bash
npx @angular/cli mcp
```

The exact client configuration depends on the coding agent.

Think of the separation as:

```text
Angular Skill
    -> framework knowledge and instructions

Angular MCP / CLI
    -> inspect, build, test, and operate on the workspace
```

---

## 6.5 Angular Verification

An AI-generated Angular feature should not be considered complete merely because code was generated.

Typical verification:

```bash
npm ci
npm run lint
npm test
npm run build
```

Or the repository's equivalent commands.

Verify:

- compilation;
- linting;
- unit tests;
- integration/component tests where relevant;
- accessibility impact;
- routing;
- loading/error/empty states;
- API type compatibility.

---

# 7. .NET Projects

## 7.1 Recommended .NET Repository Rules

Example:

```markdown
## .NET

- Follow the .NET version configured by the repository.
- Keep nullable reference types enabled.
- Respect existing analyzers and `.editorconfig`.
- Prefer explicit, testable dependencies through dependency injection.
- Keep controllers/endpoints thin.
- Business rules belong in application/domain services, handlers, or domain objects.
- Data access must not leak into API controllers.
- Do not expose persistence/domain entities directly through public API contracts.
- Use DTOs/request/response contracts at API boundaries.
- Use asynchronous APIs for I/O operations when appropriate.
- Propagate `CancellationToken` through request and I/O boundaries where appropriate.
- Preserve structured logging and existing observability conventions.
- New behavior requires relevant automated tests.
```

Microsoft's ASP.NET Core guidance explicitly uses DTOs to avoid over-posting and accidental exposure of model properties.

---

## 7.2 API Boundary Rule

For controller-based projects:

```text
HTTP Request
    |
    v
Request DTO
    |
    v
Controller
    |
    v
Application / Domain
    |
    v
Persistence
```

Response:

```text
Domain/Application result
    |
    v
Response DTO
    |
    v
HTTP Response
```

Avoid exposing persistence entities directly from endpoints.

Prefer explicit contracts such as:

```csharp
public async Task<ActionResult<UserResponse>> Get(...)
```

The exact DTO naming convention should follow the existing project.

---

## 7.3 .NET Static Analysis

Use repository-level configuration such as:

```text
.editorconfig
Directory.Build.props
Directory.Packages.props
global.json
```

The coding agent should respect existing analyzers instead of bypassing them.

Typical checks:

```bash
dotnet restore
dotnet build
dotnet test
dotnet format --verify-no-changes
```

Use only the commands appropriate for the repository.

---

## 7.4 .NET Tests

Depending on the project, an AI-generated change may need:

- unit tests;
- application/service tests;
- integration tests;
- API tests;
- persistence tests;
- architecture tests.

Tests should verify behavior rather than implementation details whenever possible.

Do not allow the coding agent to "fix" failing tests by simply removing assertions or skipping tests unless the specification explicitly requires the test behavior to change.

---

# 8. OpenSpec Projects

OpenSpec uses skills to drive its SDD workflow.

The repository normally contains:

```text
openspec/
├── config.yaml
├── specs/
└── changes/
```

OpenSpec skills should own the OpenSpec workflow.

Do not manually edit generated OpenSpec agent skills to add Angular or .NET instructions.

Instead use:

```text
OpenSpec-managed skills
+
AGENTS.md
+
openspec/config.yaml
+
framework/company skills
```

---

## 8.1 OpenSpec `config.yaml`

Use `openspec/config.yaml` for important project context that should affect OpenSpec planning and implementation.

### Angular example

```yaml
schema: spec-driven

context: |
  This repository contains an Angular application.

  Engineering constraints:
  - Follow AGENTS.md.
  - Use the Angular version configured by the repository.
  - Follow standalone Angular patterns.
  - Keep HTTP access outside presentation components.
  - Preserve accessibility.
  - Add tests for new behavior.
```

### .NET example

```yaml
schema: spec-driven

context: |
  This repository contains a .NET application.

  Engineering constraints:
  - Follow AGENTS.md.
  - Preserve the existing architecture.
  - Keep API controllers/endpoints thin.
  - Never expose persistence entities as API contracts.
  - Use request/response DTOs at HTTP boundaries.
  - Preserve nullable reference type safety.
  - Add automated tests for new behavior.
```

Keep this concise. Do not paste the entire `AGENTS.md` into `config.yaml`.

---

## 8.2 Recommended OpenSpec Feature Flow

A good conceptual flow is:

```text
Explore / discuss
        |
        v
Proposal
        |
        v
Specification
        |
        v
Design
        |
        v
Tasks
        |
        v
Implementation
        |
        v
Verification
        |
        v
Archive
```

The precise skills/commands depend on the installed OpenSpec profile and agent integration.

---

## 8.3 OpenSpec Verification

Verification should include both:

### Specification verification

Does the implementation satisfy:

- requirements;
- scenarios;
- edge cases;
- acceptance criteria?

### Engineering verification

Does it pass:

```text
build
tests
lint/static analysis
architecture rules
repository instructions
```

---

# 9. GitHub Spec Kit Projects

GitHub Spec Kit uses an agentic SDD workflow.

Current core flow:

```text
constitution
    |
    v
specify
    |
    v
plan
    |
    v
tasks
    |
    v
implement
    |
    v
converge
```

For important or ambiguous production features, also use:

```text
clarify
checklist
analyze
```

A fuller workflow is:

```text
constitution
    |
    v
specify
    |
    v
clarify
    |
    v
plan
    |
    v
checklist
    |
    v
tasks
    |
    v
analyze
    |
    v
implement
    |
    v
converge
```

---

# 10. Spec Kit Constitution

The Spec Kit constitution is the best location for **non-negotiable project principles**.

The constitution should describe principles, not feature requirements.

Good .NET principle:

```markdown
### API Contract Isolation

Public HTTP endpoints MUST use explicit request and response contracts.

Persistence and domain entities MUST NOT be exposed directly through controllers.

Rationale: API contracts must be evolvable independently of persistence models.
```

Good Angular principle:

```markdown
### Angular Architecture

Presentation components MUST NOT directly implement API access or reusable business rules.

Shared business behavior MUST live in appropriate services, stores, facades, or domain functions.
```

Avoid putting feature-specific requirements into the constitution.

---

# 11. Spec Kit: Specify vs Plan

## `/speckit-specify`

Describe:

```text
WHAT
WHY
USER BEHAVIOR
ACCEPTANCE CRITERIA
EDGE CASES
```

Avoid prematurely specifying implementation details.

Example:

```text
Users must be able to filter orders by status.

The selected filters must remain visible.

If there are no matching orders, an empty-state message must be displayed.
```

Do not start with:

```text
Create OrderFilterComponent using signals and HttpClient.
```

That is implementation detail.

## `/speckit-plan`

Use the plan stage for:

```text
architecture
Angular patterns
.NET patterns
database choices
API contracts
technical dependencies
migration strategy
testing approach
```

Example:

```text
Use Angular Signals for local filter state.
Use the existing OrdersApiService for remote calls.
Add a new GET query parameter to the ASP.NET Core API.
Expose a new OrderSummaryResponse DTO.
```

---

# 12. Spec Kit Quality Gates

## Clarify

Use when requirements contain ambiguity, for example:

- authorization behavior is unclear;
- pagination rules are unclear;
- empty states are not defined;
- API compatibility is uncertain;
- multiple user roles behave differently.

## Checklist

Use checklists for important requirement-quality or engineering concerns:

```text
Security
Accessibility
API compatibility
Database migration safety
Observability
Performance
Backward compatibility
```

## Analyze

Run before implementation for non-trivial changes to detect inconsistencies between:

```text
spec.md
plan.md
tasks.md
constitution
```

## Converge

Use convergence after implementation.

Conceptually:

```text
Specification
      |
      +---- compare ----> Implementation
      |
      +---- compare ----> Tasks
      |
      +---- compare ----> Plan
```

If missing work is discovered, return to implementation and converge again.

---

# 13. Mapping OpenSpec and Spec Kit Concepts

Teams do not need identical filenames to have identical engineering discipline.

| Concept | OpenSpec | GitHub Spec Kit |
|---|---|---|
| Project principles | `config.yaml` + repository instructions | constitution + repository instructions |
| Feature intent | proposal/spec artifacts | `spec.md` |
| Technical design | design artifact | `plan.md` |
| Work breakdown | task artifact | `tasks.md` |
| Implementation | apply workflow | implement |
| Verification | verify workflow | converge |
| Agent specialization | skills | skills / agent integration |
| Framework rules | `AGENTS.md` + skills | `AGENTS.md` + constitution + skills |

> **Standardize the meaning of the stages, not necessarily the SDD product.**

---

# 14. Recommended Shared Team Workflow

For any substantial feature:

```text
1. Understand the requirement
2. Create/refine the specification
3. Resolve ambiguity
4. Create the technical plan
5. Validate the plan against project principles
6. Generate implementation tasks
7. Check specification/plan/task consistency
8. Implement in small increments
9. Build and test continuously
10. Verify the final implementation against the specification
11. Review the diff as a human
12. Merge only after normal CI/review gates pass
```

This works with both OpenSpec and Spec Kit.

---

# 15. Agent Execution Rules

## Before editing

The agent should inspect:

```text
AGENTS.md
SDD artifacts for the active feature
package.json / angular.json
*.csproj / *.sln
.editorconfig
existing neighboring implementation
existing tests
```

Do not assume a generic architecture when the repository already establishes one.

## During implementation

Prefer:

```text
small coherent change
    ->
build
    ->
test
    ->
next change
```

over generating many files and running verification only at the end.

## Before completion

The agent should report:

```text
Files changed
Requirements implemented
Tests added/updated
Build/test/lint status
Known limitations
Any remaining spec gaps
```

---

# 16. Skills Strategy for Angular + .NET Teams

A useful shared skill portfolio may look like:

```text
.agents/skills/
├── angular-developer/          # official Angular skill
├── company-angular/
├── company-dotnet/
├── company-api-contracts/
├── company-security/
├── company-observability/
└── testing-strategy/
```

## `company-angular`

Only company/project-specific Angular rules, for example:

```text
design system usage
folder conventions
state-management rules
shared UI libraries
company accessibility rules
analytics integration
```

## `company-dotnet`

Examples:

```text
layer boundaries
CQRS conventions
error/result model
logging conventions
repository/unit-of-work rules
domain event patterns
```

## `company-api-contracts`

Examples:

```text
DTO naming
pagination shape
ProblemDetails conventions
versioning strategy
OpenAPI conventions
error codes
```

---

# 17. Avoid Skill Explosion

Do not create one skill for every small rule.

Bad:

```text
use-records/
use-async/
angular-signals/
angular-if/
angular-for/
angular-inject/
controller-dto/
```

Prefer coherent domains:

```text
angular-developer
company-angular
company-dotnet
company-api-contracts
```

Skills should be easy for agents to discover and easy for humans to maintain.

---

# 18. Do Not Duplicate the Same Rule Everywhere

Avoid maintaining five copies of:

```text
Controllers must use DTOs.
```

Choose one authoritative location for the rule and reference it elsewhere.

Suggested authority:

```text
AGENTS.md
    |
    +--> summarized in OpenSpec context

or

Spec Kit constitution
    |
    +--> reinforced by AGENTS.md
```

The team should know where a rule is officially owned.

---

# 19. Recommended Authority Hierarchy

A useful hierarchy is:

```text
1. Security/compliance requirements
2. Repository constitution / mandatory rules
3. Active feature specification
4. Technical plan
5. Repository architecture
6. Framework/company skills
7. General model knowledge
```

When instructions conflict, higher-level project requirements should normally win unless the specification explicitly changes the architecture through normal team review.

---

# 20. Source-of-Truth Rules

For a feature under development:

## OpenSpec repository

Primary feature truth:

```text
active OpenSpec change/spec artifacts
```

## Spec Kit repository

Primary feature truth:

```text
spec.md
plan.md
tasks.md
```

Do not allow a ticket description, old chat discussion, AI chat history, or stale README to silently override the active reviewed specification.

If there is a conflict, update the specification first.

---

# 21. Prompting Pattern

Instead of:

```text
Implement user registration.
```

Prefer:

```text
Use the active SDD feature artifacts as the source of truth.

Follow AGENTS.md and all repository engineering rules.

Use the relevant framework skills.

Inspect the existing architecture before editing.

Implement tasks incrementally.

After each meaningful phase, run the appropriate build/tests.

Do not consider the feature complete until the implementation has been
verified against the specification.
```

This pattern works with either OpenSpec or Spec Kit.

---

# 22. Angular + .NET Feature Example

Requirement:

```text
Users can search customers by name.
```

## Specification

```text
- Users can enter part of a customer name.
- Results update after a short input delay.
- Searches shorter than the configured minimum do not call the API.
- Loading, no-results, and server-error states are visible.
- Previous requests must not overwrite newer results.
```

## Technical plan

```text
Angular:
- use the existing customer feature architecture;
- use Signals for local UI state where appropriate;
- use the existing API service abstraction;
- cancel/supersede stale requests;
- preserve accessibility.

.NET:
- add/extend the customer search endpoint;
- use explicit response DTOs;
- validate query parameters;
- apply cancellation;
- add API/application tests.
```

The specification says **what must happen**.

The plan says **how this repository will implement it**.

---

# 23. CI Is Part of the Agent Harness

AI instructions are not enough.

Repository CI should enforce important rules.

```text
Pull Request
   |
   +--> Angular lint
   +--> Angular tests
   +--> Angular build
   |
   +--> dotnet build
   +--> dotnet test
   +--> format/analyzers
   |
   +--> security/dependency checks
```

The strongest rule is one that both:

```text
AI is instructed to follow
        +
CI can verify
```

---

# 24. Human Review Remains Mandatory

SDD and AI verification reduce risk but do not replace engineering review.

Reviewers should examine:

```text
spec correctness
architecture impact
security
public API changes
database migrations
backward compatibility
test quality
unnecessary complexity
generated-code noise
```

The coding agent should assist the review, not become the final authority.

---

# 25. Anti-Patterns

## AI-first implementation

Bad:

```text
prompt -> code -> specification created afterwards
```

The specification should guide implementation, not document whatever the agent already produced.

## Giant repository prompt

Do not maintain a single giant instruction file containing framework documentation, team rules, feature requirements, deployment steps, and every coding convention.

Separate:

```text
AGENTS.md
skills
SDD artifacts
tool configuration
```

## Framework knowledge copied into SDD artifacts

Do not fill every feature spec with explanations of Angular Signals, dependency injection, or ASP.NET Core controllers. That belongs in framework skills or engineering instructions.

## Blind agent refactoring

Do not let an agent upgrade Angular, replace a state-management library, change .NET architecture, replace a test framework, or rewrite shared infrastructure merely because it considers another approach cleaner.

Architectural changes need explicit scope.

## Skipping verification

Bad:

```text
Agent says "implementation complete"
```

Good:

```text
Specification satisfied
+
build succeeds
+
tests succeed
+
lint/analyzers succeed
+
diff reviewed
```

---

# 26. Suggested Repository Templates

## Angular + OpenSpec

```text
repo/
├── AGENTS.md
├── openspec/
│   ├── config.yaml
│   ├── specs/
│   └── changes/
├── .agents/
│   └── skills/
│       ├── angular-developer/
│       └── company-angular/
├── src/
├── angular.json
├── package.json
└── tsconfig.json
```

## Angular + Spec Kit

```text
repo/
├── AGENTS.md
├── .specify/
├── specs/
├── .agents/
│   └── skills/
│       ├── angular-developer/
│       └── company-angular/
├── src/
├── angular.json
└── package.json
```

## .NET + OpenSpec

```text
repo/
├── AGENTS.md
├── openspec/
│   ├── config.yaml
│   ├── specs/
│   └── changes/
├── .agents/
│   └── skills/
│       ├── company-dotnet/
│       └── company-api-contracts/
├── src/
├── tests/
└── Solution.sln
```

## .NET + Spec Kit

```text
repo/
├── AGENTS.md
├── .specify/
├── specs/
├── .agents/
│   └── skills/
│       ├── company-dotnet/
│       └── company-api-contracts/
├── src/
├── tests/
└── Solution.sln
```

Exact generated folders can vary by tool version and agent integration. Preserve the structure generated by the installed SDD tool rather than manually forcing these examples.

---

# 27. Team Adoption Checklist

For each repository:

- [ ] Identify whether the primary SDD tool is OpenSpec or Spec Kit.
- [ ] Do not maintain duplicate feature specifications in both tools.
- [ ] Add or review `AGENTS.md`.
- [ ] Define mandatory architecture rules.
- [ ] Keep framework-specific knowledge in skills where appropriate.
- [ ] Install the official Angular skill in Angular repositories where the agent supports skills.
- [ ] Add small company-specific skills only where they add real value.
- [ ] Configure Angular MCP where useful and supported.
- [ ] Ensure Angular build/test/lint commands work locally and in CI.
- [ ] Ensure .NET build/test/analyzer commands work locally and in CI.
- [ ] Use DTOs/contracts at .NET API boundaries.
- [ ] Keep Angular HTTP/domain concerns outside presentation components.
- [ ] Use OpenSpec verification or Spec Kit convergence before feature completion.
- [ ] Require normal human PR review.
- [ ] Treat CI as part of the AI engineering harness.

---

# 28. Definition of Done for AI-Assisted Features

## Requirements

- [ ] The implementation satisfies the active specification.
- [ ] Acceptance scenarios are covered.
- [ ] Important edge cases are addressed.

## Architecture

- [ ] Repository rules are respected.
- [ ] Framework conventions are respected.
- [ ] No unnecessary architectural changes were introduced.

## Quality

- [ ] Build succeeds.
- [ ] Relevant automated tests succeed.
- [ ] Lint/static analysis succeeds.
- [ ] New behavior has appropriate tests.

## Security and API

- [ ] Authorization/validation behavior is preserved or explicitly changed.
- [ ] Sensitive/internal fields are not accidentally exposed.
- [ ] API compatibility impact has been reviewed.

## SDD

For OpenSpec:

- [ ] The change has been verified against its OpenSpec artifacts.

For Spec Kit:

- [ ] Relevant analysis was performed for non-trivial features.
- [ ] Implementation and specification have converged.

## Review

- [ ] The generated diff has been reviewed by a developer.
- [ ] CI passes.

---

# 29. Recommended Organizational Standard

The organization can standardize the following without forcing one SDD product:

```text
              SHARED ENGINEERING STANDARD

                     AGENTS.md
                         |
        +----------------+----------------+
        |                                 |
        v                                 v
     Angular                            .NET
 official skill                 company .NET skills
        |                                 |
        +----------------+----------------+
                         |
                Repository + CI rules
                         |
          +--------------+--------------+
          |                             |
          v                             v
      OpenSpec                    GitHub Spec Kit
          |                             |
          +--------------+--------------+
                         |
                         v
                  Coding Agent
                         |
                         v
                Build / Test / Verify
```

This gives teams flexibility while keeping quality and engineering discipline consistent.

---

# 30. References

## OpenSpec

- OpenSpec Skills: https://openspec.dev/docs/skills
- OpenSpec documentation: https://openspec.dev/

## GitHub Spec Kit

- Repository: https://github.com/github/spec-kit
- Quick Start: https://github.com/github/spec-kit/blob/main/docs/quickstart.md
- Agentic SDD reference: https://github.com/github/spec-kit/blob/main/docs/reference/agentic-sdd.md

## Angular

- Angular Agent Skills: https://angular.dev/ai/agent-skills
- Angular MCP: https://angular.dev/ai/mcp
- Angular AI documentation: https://angular.dev/ai

## .NET / ASP.NET Core

- C# coding conventions: https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions
- Nullable reference types: https://learn.microsoft.com/dotnet/csharp/nullable-references
- ASP.NET Core Web APIs: https://learn.microsoft.com/aspnet/core/web-api/
- ASP.NET Core DTO guidance: https://learn.microsoft.com/aspnet/core/tutorials/min-web-api

---

# 31. Final Principle

> **Specifications tell the agent what must be true.**
>
> **Repository rules tell the agent what it must never violate.**
>
> **Skills teach the agent how to work effectively in the technology.**
>
> **Tools let the agent act.**
>
> **Builds, tests, verification, CI, and human review determine whether the result is acceptable.**

This principle applies equally to:

```text
Angular + OpenSpec
Angular + GitHub Spec Kit
.NET + OpenSpec
.NET + GitHub Spec Kit
```
