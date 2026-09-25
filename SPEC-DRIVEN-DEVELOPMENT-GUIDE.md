# Spec-Driven Development Guide

<!-- markdownlint-disable MD013 -->

## OpenSpec and GitHub Spec Kit for Angular and .NET

> - **Audience:** Developers, tech leads, reviewers, and AI-coding-agent users
> - **Applies to:** Angular, .NET, and Angular + .NET repositories
> - **Last verified:** 2026-09-25

This guide defines a shared team approach for repositories that use either
[OpenSpec](https://openspec.dev/) or
[GitHub Spec Kit](https://github.github.com/spec-kit/). It does not require every repository to use the same framework. It standardizes the outcomes expected from both.

The name **OpenSpec** is used throughout this document; “OpecSpec” is treated as a typo for OpenSpec.

---

## 1. Team policy

1. **Use one spec framework per repository.** Do not run OpenSpec and Spec Kit for the same active feature. A deliberate, documented migration is the only exception.
2. **Keep specifications with the code.** Commit framework configuration, generated workflow skills, specifications, plans, tasks, and relevant code in version control.
3. **Treat approved artifacts as the source of intent.** Chat history is not a durable decision record.
4. **Separate requirements from implementation.** Define what and why first. Put Angular, .NET, database, and infrastructure decisions in the technical plan or design.
5. **Review before implementation.** A developer other than the author should review material changes to requirements, architecture, security, data, and public contracts.
6. **Keep work small and traceable.** Each implementation task should map to a requirement or scenario and have an observable completion check.
7. **Update the artifacts when reality changes.** Do not let code silently diverge from the accepted spec or plan.
8. **Require evidence before closure.** Checked tasks alone are not evidence; record the builds, tests, security checks, migrations, and manual verification performed.

### Repository declaration

Every repository should state its selected framework in `README.md` or `CONTRIBUTING.md`:

```md
## Specification workflow

- Framework: OpenSpec | GitHub Spec Kit
- Team instructions: ./SPEC-DRIVEN-DEVELOPMENT-GUIDE.md
- Project-specific rules: <path>
- Required workflow: short | full/production
- Required approvers: <roles or CODEOWNERS groups>
```

---

## 2. Identify the framework before working

| Check | OpenSpec | GitHub Spec Kit |
| --- | --- | --- |
| Main marker | `openspec/config.yaml` | `.specify/` |
| Durable project guidance | `openspec/config.yaml` and `openspec/specs/` | `.specify/memory/constitution.md` |
| Active feature/change | `openspec/changes/<change-name>/` | `specs/<feature-id-name>/` |
| Typical planning artifacts | `proposal.md`, delta `specs/`, `design.md`, `tasks.md` | `spec.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`, `tasks.md` |
| Completion model | Verify, merge deltas into main specs, archive change | Analyze, implement, converge, retain feature artifacts |
| Customization | Profiles, `config.yaml`, or a custom schema | Constitution, templates, extensions, presets, and workflows |

Before invoking a skill:

1. Inspect the repository markers above.
2. Read the repository’s agent instructions and framework configuration.
3. Inspect the actual Angular or .NET versions and commands; do not assume them.
4. Identify the active change or feature explicitly.
5. Check `git status` and preserve unrelated work.

If neither marker exists, do not initialize a framework without the repository owner’s approval.

---

## 3. Working with agent skills

Both frameworks install workflow instructions as agent skills. A skill is a repeatable procedure for a specific phase, such as proposing, planning, implementing, or verifying work.

### Skill usage rules

- Prefer the installed skill over an improvised prompt for a framework phase.
- Invoke one phase at a time and inspect its output before continuing.
- Name the target change or feature when more than one is active.
- Give requirements skills business behavior and constraints, not a preselected implementation.
- Give planning skills the verified stack, versions, architecture, and operational constraints.
- Give implementation skills a bounded phase or task range when the task list is large.
- Start implementation in a fresh agent session when practical; the versioned artifacts should provide the needed context.
- Stop when a requirement, contract, or destructive operation is ambiguous. Update the owning artifact before resuming.
- Never let a skill bypass code review, security policy, branch protection, or required human approval.
- Review generated skill changes after a CLI update just like dependency changes.

### Invocation names vary by coding agent

Use the exact names created by the repository’s selected integration. For example:

- OpenSpec may expose `openspec-propose` as a skill and `/opsx:propose`, `/openspec-propose`, or `$openspec-propose` as an invocation.
- Spec Kit may expose `speckit-specify` as a skill and `/speckit.specify`, `/speckit-specify`, or `$speckit-specify` as an invocation.

Do not copy command spelling from another IDE or agent without checking the installed files.

### Custom stack skills

Framework skills manage the specification lifecycle. Small project skills can complement them with stack-specific procedures, for example:

- `angular-quality-gate`: inspect workspace configuration; run formatting, linting, tests, production build, bundle checks, and accessibility checks.
- `dotnet-quality-gate`: inspect the solution and SDK policy; restore, build, test, format, validate migrations, and run integration tests.
- `api-contract-review`: compare the OpenAPI document, Angular client types, ASP.NET endpoints, authorization rules, and error shapes.
- `database-migration-review`: check compatibility, locking risk, rollback/roll-forward strategy, and deployment order.

A good custom skill should:

1. Have one job and a precise trigger.
2. Detect repository versions and scripts instead of hard-coding them.
3. State its inputs, outputs, allowed file changes, and stop conditions.
4. Reuse repository scripts rather than duplicating shell logic.
5. Produce concise evidence that can be pasted into a PR.
6. Fail safely when prerequisites are absent.
7. Avoid repeating or contradicting the OpenSpec configuration or Spec Kit constitution.

Place skills in the directory expected by the selected agent integration. Examples include `.agents/skills/`, `.github/skills/`, and `.claude/skills/`. Prefer one canonical source plus an installation/synchronization mechanism if the team supports multiple agents.

---

## 4. Equivalent workflow across frameworks

| Outcome | OpenSpec | GitHub Spec Kit |
| --- | --- | --- |
| Explore the problem | `openspec-explore` | Discuss first or use an approved assessment extension |
| Establish project principles | `openspec/config.yaml` context and rules | `speckit-constitution` |
| Specify the feature | `openspec-propose` | `speckit-specify` |
| Resolve ambiguity | Review, then `openspec-update-change` | `speckit-clarify` |
| Create technical design | Produced by propose and refined by update | `speckit-plan` |
| Check requirements quality | Human review or a project-specific checklist | `speckit-checklist` |
| Create executable tasks | Produced by propose and refined by update | `speckit-tasks` |
| Check cross-artifact consistency | Review and optional `openspec-verify-change` | `speckit-analyze` |
| Implement | `openspec-apply-change` | `speckit-implement` |
| Check code against intent | Optional `openspec-verify-change` | `speckit-converge` |
| Update durable specs and close | `openspec-archive-change` | Keep feature artifacts aligned and close through the team’s PR process |

The commands are not one-to-one internally. The table aligns review outcomes, not file formats.

---

## 5. Shared specification standard

Every production feature should address the following topics. Use “Not applicable” with a reason instead of silently omitting a topic.

### Requirements

- Problem, outcome, users, and business value
- Scope and explicit non-goals
- User journeys and independently testable acceptance scenarios
- Business rules, validation, and state transitions
- Empty, loading, success, partial-success, unauthorized, forbidden, conflict, and failure behavior
- Data classification, retention, privacy, and audit requirements
- Authentication and authorization rules
- Accessibility and localization expectations
- Performance and capacity targets with measurable thresholds
- Compatibility, rollout, feature flag, migration, and rollback expectations
- Observability and support expectations
- External dependencies, assumptions, risks, and unresolved questions

### Technical plan

- Verified framework, SDK, runtime, and package versions
- Existing patterns to reuse and deliberate deviations
- Component and service boundaries
- API and event contracts
- Data-model and migration strategy
- Security and threat considerations
- Caching, concurrency, idempotency, and failure recovery where relevant
- Logging, metrics, tracing, alerting, and health behavior
- Unit, integration, contract, end-to-end, performance, and accessibility test strategy
- Deployment order and rollback or roll-forward procedure

### Tasks

Each task should be small enough to review, name the files or subsystem affected when known, identify dependencies, link to a requirement/scenario, include its tests, and define completion evidence.

Prefer vertical slices that produce observable behavior. Avoid task lists that create all models, then all services, then all controllers or components without delivering a testable scenario.

### Definition of ready

A feature is ready to implement when:

- Scope and non-goals are explicit.
- Acceptance scenarios are objective and testable.
- Important errors and edge cases are covered.
- Security, accessibility, data, and operational concerns are resolved or recorded.
- Public contracts and compatibility expectations are clear.
- The technical plan matches the repository’s actual stack.
- Tasks cover requirements and required tests.
- Required reviewers have approved the artifacts.

### Definition of done

A feature is done when:

- Accepted requirements are implemented or explicitly deferred.
- Artifacts match the final implementation.
- Required automated checks pass.
- Security, accessibility, and operational checks pass.
- Database and contract compatibility have been verified.
- Deployment and rollback/roll-forward instructions are usable.
- Documentation and telemetry are updated.
- OpenSpec changes are verified and archived, or Spec Kit convergence reports no remaining gaps.

---

## 6. OpenSpec workflow

### OpenSpec setup

OpenSpec currently requires Node.js 20.19.0 or newer. Follow the
[official installation guide](https://openspec.dev/docs/installation), then initialize at the repository root:

```bash
npm install -g @fission-ai/openspec@latest
openspec --version
openspec init
```

Commit `openspec/` and the generated workflow files. Restart the coding agent if the skills are not discovered.

Refresh generated workflows after upgrading the CLI:

```bash
openspec update
```

Use `openspec config profile` to select core and optional workflows. For production repositories, enabling the optional verify workflow is recommended.

### Configure project context

Keep `openspec/config.yaml` concise. Include facts that should affect every change; do not copy documentation the agent can discover from the repository.

Example baseline:

```yaml
schema: spec-driven

context: |
  Inspect repository manifests before assuming Angular, .NET, Node, or SDK versions.
  Preserve existing architecture unless a reviewed design explicitly changes it.
  Public API and persisted-data changes require compatibility and rollout plans.
  Security, accessibility, tests, observability, and rollback are part of feature scope.

rules:
  proposal:
    - Include explicit non-goals, risks, rollout, and rollback.
  specs:
    - Write observable, testable scenarios including errors and authorization failures.
  design:
    - Record alternatives and explain consequential tradeoffs.
  tasks:
    - Map tasks to requirements and include tests in the same implementation slice.
    - End with repository quality gates and documentation updates.

operations:
  apply:
    guidance:
      - Do not mark a task complete until its checks pass.
      - Pause and update artifacts if implementation invalidates the design.
  archive:
    guidance:
      - Record verification evidence and deferred work before archiving.
```

Add a short stack section to `context` for an Angular or .NET repository, using the standards in sections 8 and 9. Avoid putting secrets, volatile version numbers, or lengthy tutorials in this file.

### Recommended lifecycle

1. **Explore** — Investigate the problem and codebase without changing code.
2. **Propose** — Create the proposal, spec deltas, design, and tasks.
3. **Review** — Review all artifacts. Use `openspec-update-change` to incorporate decisions.
4. **Validate** — Run `openspec validate` and resolve structural errors.
5. **Apply** — Implement tasks in order. Resume from the first unchecked task after interruption.
6. **Verify** — Use `openspec-verify-change` when installed and run the repository quality gates.
7. **Archive** — Archive only after tasks and verification are complete. Archiving updates the main specs and moves the completed change under `openspec/changes/archive/`.

Use `openspec-sync-specs` before archive only when the team deliberately needs main specs updated while a change remains active.

### Useful inspection commands

```bash
openspec list
openspec status --all
openspec show <change-name>
openspec validate <change-name>
```

### OpenSpec review focus

- `proposal.md`: value, scope, non-goals, risks, and affected capabilities
- Delta specs: correct `ADDED`, `MODIFIED`, and `REMOVED` behavior with scenarios
- `design.md`: boundaries, contracts, data, security, operations, and tradeoffs
- `tasks.md`: complete, ordered, testable, and traceable work
- Archive: implementation verified, deltas correct, and main specs trustworthy afterward

---

## 7. GitHub Spec Kit workflow

### Spec Kit setup

Follow the [official installation guide](https://github.github.com/spec-kit/installation.html). A common installation uses `uv`:

```bash
uv tool install specify-cli
specify version
```

For an existing repository, first commit or otherwise protect current work, then initialize for the team’s coding agent:

```bash
specify init --here --force --integration <agent-key>
```

Review and commit `.specify/`, the integration’s generated skills or commands, and related configuration. Use the
[existing-project guidance](https://github.github.com/spec-kit/guides/existing-projects.html) before adopting Spec Kit in a mature codebase.

### Establish the constitution

Run `speckit-constitution` once per repository and update it through review when engineering principles change. The constitution should contain enforceable rules, not aspirations.

Recommended principles:

1. Requirements before implementation
2. Contract and data compatibility
3. Security and privacy by design
4. Accessibility for user-facing behavior
5. Automated tests proportional to risk
6. Observable and operable production behavior
7. Small, reversible changes
8. Simplicity and consistency with existing architecture

Keep stack details in the constitution only when they are project-wide policy. Feature-specific choices belong in `plan.md`.

### Recommended production lifecycle

1. `speckit-constitution` — establish project principles once.
2. `speckit-specify` — define behavior and value without choosing the implementation.
3. `speckit-clarify` — resolve ambiguity; repeat with a different focus if needed.
4. `speckit-plan` — define Angular, .NET, data, and infrastructure choices.
5. `speckit-checklist` — generate focused requirements-quality review lists.
6. `speckit-tasks` — create dependency-ordered implementation tasks.
7. `speckit-analyze` — resolve cross-artifact conflicts before coding.
8. `speckit-implement` — implement all work or a named phase.
9. `speckit-converge` — compare implementation with artifacts and add missing tasks; repeat implementation and convergence until converged.

For a small, low-risk change, the team may use specify → plan → tasks → implement → converge. Do not omit clarification and analysis merely to accelerate a complex, public-contract, security-sensitive, or data-changing feature.

### Active-feature safety

Spec Kit resolves the active feature from `.specify/feature.json` unless `SPECIFY_FEATURE_DIRECTORY` overrides it. Changing Git branches alone does not necessarily change the active feature. Before running a skill:

1. Inspect `.specify/feature.json`.
2. Confirm the intended `specs/<feature-id-name>/` directory.
3. Check that generated or edited artifacts land in that directory.

### Spec Kit review focus

- `spec.md`: user value, scenarios, functional requirements, edge cases, and measurable success criteria
- `plan.md`: constitution gates, architecture, stack, security, data, and operational design
- `research.md`: evidence for unresolved technical decisions
- `data-model.md`: entities, validation, relationships, states, and migration implications
- `contracts/`: versioned APIs or events, errors, security, and compatibility
- `tasks.md`: dependency order, parallel-safe markers, scenario mapping, tests, and completion evidence
- `speckit-analyze`: clean before implementation
- `speckit-converge`: no remaining implementation gaps before closure

---

## 8. Angular best practices for specs and plans

Always inspect `package.json`, `angular.json`, `tsconfig*.json`, lint configuration, test setup, and existing feature patterns first. The repository’s supported Angular version and architecture take precedence over generic advice.

### Angular requirements should cover

- Routes, deep links, browser navigation, page titles, and access rules
- Loading, empty, success, stale, validation, partial, offline, and error states
- Keyboard navigation, focus behavior, semantic HTML, announcements, contrast, and target accessibility standard
- Responsive layouts and supported browsers or devices
- Localization, dates, numbers, time zones, and text expansion
- Client-side validation and authoritative server-side validation
- Performance budgets or measurable interaction/load goals
- Analytics and telemetry, including privacy expectations
- SSR, hydration, or SEO behavior when relevant

### Angular plans should cover

- Organize code by feature area; keep components focused and files small.
- Prefer the repository’s established standalone APIs. For new code on supported Angular versions, prefer standalone components over introducing new NgModules without a compatibility reason.
- Prefer `inject()` for dependency injection in new code when consistent with the project.
- Use signals for local synchronous state and derived values; use `computed()` for derivation. Avoid effects for propagating state.
- Keep RxJS where streams, cancellation, event composition, or existing APIs make it the clearer abstraction. Document signal/Observable boundaries.
- Keep business and data-access logic out of presentation components.
- Lazy-load non-primary routes where the measured loading tradeoff supports it; consider `@defer` for heavy non-critical UI.
- Use `OnPush` or the project’s supported change-detection strategy with immutable state transitions.
- Use typed forms and explicit validation/error-display behavior when forms are in scope.
- Define HTTP DTOs and error mapping explicitly. Do not treat TypeScript types as runtime validation.
- Rely on Angular template escaping and sanitization. Treat `bypassSecurityTrust*`, dynamic template construction, and direct DOM HTML injection as security-review triggers.
- Enforce authorization on the server; route guards and hidden UI are user-experience controls, not security boundaries.

### Angular testing strategy

- Pure unit tests for business rules and transformations
- Service tests for state and HTTP behavior, using Angular HTTP testing utilities
- Component DOM tests for rendering, user interaction, validation, and accessibility behavior
- Router tests with real route configuration and `RouterTestingHarness` where supported
- Contract tests for generated or hand-written API clients
- End-to-end tests for critical user journeys and authorization boundaries
- Accessibility checks plus manual keyboard/screen-reader review for high-impact flows
- Production build and bundle-budget checks

### Typical Angular quality gates

Use repository scripts rather than assuming these exact commands exist:

```bash
npm ci
npm run format:check
npm run lint
npm test -- --watch=false
npm run build -- --configuration production
npm run e2e
```

If the repository uses `pnpm`, Yarn, Bun, Nx, Vitest, Jest, or another runner, use its lockfile and documented scripts. Never introduce a second package manager or test runner as an incidental feature change.

---

## 9. .NET best practices for specs and plans

Inspect `global.json`, `Directory.Build.props`, `Directory.Packages.props`, solution files, project files, analyzers, editor configuration, test projects, and existing architectural boundaries first.

### .NET requirements should cover

- API, worker, desktop, library, or service behavior from the consumer’s perspective
- Authentication, authorization policy, tenant boundaries, and audit behavior
- Request validation, errors, status codes, idempotency, concurrency, and retry semantics
- Data consistency, retention, migrations, backfills, and compatibility
- Time zones, culture, decimal precision, identifiers, and null semantics
- Throughput, latency, memory, availability, and recovery targets where relevant
- Logs, metrics, traces, health, alerts, and diagnostic correlation
- Deployment order, configuration, feature flags, and rollback/roll-forward behavior

### .NET plans should cover

- Pin or honor the repository SDK policy and target-framework support policy.
- Keep nullable reference types enabled for new code; fix warnings rather than broadly suppressing them.
- Use dependency injection with intentional lifetimes. Avoid service locator patterns and calls to `BuildServiceProvider()` during registration.
- Use the options pattern for related configuration and validate critical configuration at startup.
- Use async APIs for I/O and propagate `CancellationToken` across cancellable boundaries.
- Keep domain/application logic separate from transport and persistence concerns at the level justified by the system’s complexity.
- Return a consistent error contract; ASP.NET Core APIs should normally use Problem Details where compatible with existing contracts.
- Perform authorization server-side at the endpoint and resource level.
- Use structured `ILogger` messages without secrets or sensitive personal data; preserve correlation across calls.
- Add OpenTelemetry traces, metrics, and logs where the service’s observability policy requires them.
- For EF Core, project only needed data, bound result sizes, avoid accidental N+1 queries, use no-tracking queries for read-only work where appropriate, and review generated migrations and SQL.
- Treat retries as an idempotency and load concern, not a universal error-handling mechanism.

### .NET testing strategy

- Unit tests for domain rules and pure application behavior
- Integration tests for ASP.NET Core endpoints with `WebApplicationFactory` where appropriate
- Persistence tests against the real database engine for query and migration-sensitive behavior
- Contract tests for public APIs, events, and Angular clients
- Authorization tests covering unauthenticated, forbidden, allowed, and cross-tenant cases
- Architecture tests only for boundaries that are deliberate team policy
- End-to-end tests for critical workflows
- Performance tests for explicitly stated latency, throughput, or allocation goals

### Typical .NET quality gates

Adapt these to the repository and CI configuration:

```bash
dotnet --info
dotnet restore
dotnet build --no-restore
dotnet test --no-build
dotnet format --verify-no-changes
```

Also run repository-specific analyzer, coverage, security, container, migration, and integration-test commands. Do not auto-apply or roll back a shared database migration as a routine local quality check.

---

## 10. Angular + .NET full-stack features

Treat the client/server contract as a first-class artifact, preferably in `contracts/` or another established repository location.

Specify:

- Endpoint or event purpose and versioning
- HTTP method and route, or event topic and type
- Request, response, and event schemas
- Required, optional, nullable, defaulted, and omitted fields
- Validation rules and Problem Details/error codes
- Authentication, authorization, and tenant behavior
- Pagination, sorting, filtering, concurrency, and idempotency
- Dates, time zones, enums, decimals, identifiers, and serialization conventions
- Compatibility and deprecation policy
- Cancellation, timeout, retry, and partial-failure behavior

Recommended task order:

1. Approve the contract and representative examples.
2. Add contract tests and server-side validation.
3. Implement the .NET behavior and persistence changes.
4. Generate or update the Angular client through the repository’s established process.
5. Implement Angular states and interactions.
6. Test authentication and authorization at both UX and server boundaries.
7. Run end-to-end, accessibility, observability, and failure-path checks.
8. Validate deployment order, backward compatibility, and rollback/roll-forward behavior.

The Angular and .NET tasks must reference the same contract. Do not allow each side to infer field names, null behavior, error shapes, or enum values independently.

---

## 11. Pull request expectations

Use this summary in feature PRs:

```md
## Specification

- Framework: OpenSpec | GitHub Spec Kit
- Change/feature: <name and relative link>
- Requirements approved by: <reviewer>
- Plan approved by: <reviewer>

## Delivered

- Scenarios: <IDs or links>
- Contracts/data changes: <summary or none>
- Rollout and rollback/roll-forward: <summary or none>

## Verification evidence

- Angular: <format/lint/test/build/e2e/a11y results or N/A>
- .NET: <build/test/format/integration/migration results or N/A>
- Security: <authorization/threat/dependency checks>
- Operations: <logs/metrics/traces/health checks>
- Manual verification: <what, environment, result>

## Deferred work or accepted deviations

- <item, owner, and tracking link, or none>
```

Reviewer checklist:

- [ ] Behavior is unambiguous and acceptance scenarios are testable.
- [ ] Code, tests, and artifacts agree.
- [ ] Public contracts and persisted data remain compatible or have an approved migration.
- [ ] Security and authorization are enforced at trusted boundaries.
- [ ] Accessibility and localization are addressed where relevant.
- [ ] Failure behavior, telemetry, rollout, and recovery are adequate.
- [ ] Verification evidence covers the risk of the change.
- [ ] No secrets, generated noise, unrelated refactors, or accidental framework mixing are present.

---

## 12. Portfolio governance and migration

A mixed portfolio is acceptable. Standardize governance and outcomes before attempting to standardize tools.

Maintain an inventory with:

| Repository | Stack | Framework | Version/update owner | Required workflow | CI spec checks | Last review |
| --- | --- | --- | --- | --- | --- | --- |
| Example UI | Angular | OpenSpec | Team A | Production | `openspec validate` | YYYY-MM-DD |
| Example API | .NET | Spec Kit | Team B | Production | artifact/link checks | YYYY-MM-DD |

### Common controls across all repositories

- Repository declares exactly one framework.
- Generated skills and framework configuration are versioned.
- Feature artifacts receive code review.
- Requirements, plan, tasks, implementation, and evidence remain traceable.
- CI runs structural validation where the framework provides it.
- Framework CLI and generated files have a named update owner and cadence.
- Teams use the same Definition of Ready, Definition of Done, and PR evidence format.

### Migration rules

1. Finish or freeze active work in the old framework.
2. Record which existing artifacts remain historical and which become the new baseline.
3. Initialize the new framework in a clean, reviewed PR.
4. Transfer only durable project principles and current system behavior; do not blindly convert generated workflow files.
5. Pilot one small feature and compare traceability, review effort, and developer experience.
6. Remove obsolete agent workflow files only after confirming the new integration works for all team agents.
7. Document the cutover date. Never maintain two live task/spec sets for the same feature.

---

## 13. Common anti-patterns

- Starting implementation from a one-line idea without acceptance scenarios
- Putting a preferred technical solution into the requirements before validating the problem
- Treating generated artifacts as correct without review
- Using both frameworks for the same feature
- Keeping important decisions only in chat
- Allowing code to diverge from specs because “the plan changed during coding”
- Creating one huge feature whose implementation exhausts the agent’s context
- Marking tasks complete without tests or evidence
- Editing generated workflow skills directly when a supported configuration mechanism exists
- Copying Angular or .NET version assumptions from another repository
- Treating Angular route guards as authorization
- Mocking every dependency and omitting integration or contract tests
- Ignoring accessibility, observability, migration, and rollback until the end
- Archiving or converging with known gaps but no explicit deferral record

---

## 14. Prompt starters

### Requirements prompt

```text
Specify <feature> for <users> so that <outcome>.

Include:
- in-scope and out-of-scope behavior;
- happy paths, validation, permissions, empty/loading/error states, and edge cases;
- accessibility, privacy/security, compatibility, performance, and observability needs;
- measurable acceptance scenarios and success criteria.

Do not choose Angular, .NET, database, or infrastructure implementation details yet.
Mark unresolved decisions explicitly instead of guessing.
```

### Angular plan prompt

```text
Plan this feature using the Angular version and conventions detected in the repository.
Cover feature boundaries, routes and lazy loading, components, state and RxJS/signal
boundaries, forms, API types and runtime validation, accessibility, security, tests,
bundle/performance impact, telemetry, rollout, and rollback. Reuse existing patterns
unless a reviewed tradeoff justifies a change.
```

### .NET plan prompt

```text
Plan this feature using the SDK, target framework, analyzers, and architecture detected
in the repository. Cover API contracts, validation and Problem Details, authorization,
DI lifetimes, cancellation, data model and migrations, concurrency/idempotency,
structured telemetry, tests, deployment order, compatibility, and rollback or
roll-forward. Reuse existing patterns unless a reviewed tradeoff justifies a change.
```

### Verification prompt

```text
Compare the implementation with every accepted requirement, scenario, contract,
design decision, and task. Report evidence, not assumptions. Identify missing,
partially implemented, or contradictory behavior; security and accessibility gaps;
untested failure paths; contract/data incompatibilities; and operational gaps.
Update the owning artifacts or add tasks for accepted remaining work. Do not declare
completion while unexplained gaps remain.
```

---

## 15. Official references

### OpenSpec

- [Installation](https://openspec.dev/docs/installation)
- [Project setup](https://openspec.dev/docs/setup)
- [Quickstart](https://openspec.dev/docs/quickstart)
- [Skills reference](https://openspec.dev/docs/skills)
- [Profiles](https://openspec.dev/docs/profiles)
- [Project configuration](https://openspec.dev/docs/project-config)
- [CLI reference](https://openspec.dev/docs/cli)

### GitHub Spec Kit

- [Documentation home](https://github.github.com/spec-kit/)
- [Spec-driven development quickstart](https://github.github.com/spec-kit/quickstart.html)
- [Agentic SDD command reference](https://github.github.com/spec-kit/reference/agentic-sdd.html)
- [Agent integrations](https://github.com/github/spec-kit/blob/main/docs/reference/integrations.md)
- [Adopting an existing codebase](https://github.github.com/spec-kit/guides/existing-projects.html)
- [Customization](https://github.github.com/spec-kit/guides/customization.html)
- [Handling complex features](https://github.github.com/spec-kit/concepts/complex-features.html)

### Angular

- [Angular style guide](https://angular.dev/style-guide)
- [Performance practices](https://angular.dev/best-practices/performance)
- [Security practices](https://angular.dev/best-practices/security)
- [Accessibility practices](https://angular.dev/best-practices/a11y)
- [Testing guide](https://angular.dev/guide/testing)

### .NET

- [.NET dependency injection guidelines](https://learn.microsoft.com/dotnet/core/extensions/dependency-injection/guidelines)
- [.NET options pattern](https://learn.microsoft.com/dotnet/core/extensions/options)
- [C# nullable reference types](https://learn.microsoft.com/dotnet/csharp/nullable-references)
- [ASP.NET Core integration tests](https://learn.microsoft.com/aspnet/core/test/integration-tests)
- [ASP.NET Core API error handling](https://learn.microsoft.com/aspnet/core/fundamentals/error-handling-api)
- [EF Core efficient querying](https://learn.microsoft.com/ef/core/performance/efficient-querying)
- [.NET observability with OpenTelemetry](https://learn.microsoft.com/dotnet/core/diagnostics/observability-with-otel)
