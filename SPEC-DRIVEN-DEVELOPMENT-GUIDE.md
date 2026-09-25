# Spec-Driven Development Guide

<!-- markdownlint-disable MD013 -->

## OpenSpec and GitHub Spec Kit for Angular and .NET

> - **Audience:** Developers, tech leads, reviewers, and AI-coding-agent users
> - **Applies to:** Angular, .NET, and Angular + .NET repositories
> - **Last verified:** 2026-09-25
> - **Owner:** The team or engineering-governance group named by each adopting repository
> - **Review cadence:** Quarterly and after material OpenSpec, Spec Kit, Angular, or .NET workflow changes

This guide defines a shared team approach for repositories that use either
[OpenSpec](https://openspec.dev/) or
[GitHub Spec Kit](https://github.github.com/spec-kit/). It does not require every repository to use the same framework. It standardizes the outcomes expected from both.

The name **OpenSpec** is used throughout this document; “OpecSpec” is treated as a typo for OpenSpec.

---

## Operating model

Treat AI-assisted development as a set of cooperating layers. Each layer has a distinct responsibility and should have one clear source of truth.

```text
Business requirement
        |
        v
Specification framework
(OpenSpec or GitHub Spec Kit)
        |
        v
Repository policy and feature artifacts
        |
        v
Framework and company skills
        |
        v
Coding agent and development tools
        |
        v
Build, test, verification, CI, and human review
```

| Layer | Responsibility |
| --- | --- |
| Specification framework | Defines the lifecycle for deciding what to build and why |
| Repository policy | Defines non-negotiable engineering and operational constraints |
| Feature artifacts | Define accepted behavior, design decisions, tasks, and evidence |
| Skills | Supply focused, reusable implementation or review expertise |
| Coding agent | Analyzes, plans, implements, and reports evidence within the approved boundaries |
| Tooling | Inspects and operates the workspace through repository scripts, CLIs, or MCP servers |
| Verification | Demonstrates that the implementation satisfies the specification and repository policy |

Specifications tell the agent what must be true. Repository policy states what must not be violated. Skills teach specialized procedures. Tools let the agent act. Automated evidence and human review determine whether the result is acceptable.

---

## 1. Team policy

1. **Use one authoritative spec framework per bounded project.** A monorepo may contain independently governed projects, but the same active feature must never be managed by both OpenSpec and Spec Kit. A deliberate, documented migration is the only exception.
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

For a monorepo containing independently governed applications or services, make the declaration in each project root and document how cross-project features choose one coordinating source of truth.

### Workflow proportional to risk

The artifact depth and review gates should match the risk of the change:

| Level | Typical work | Minimum workflow |
| --- | --- | --- |
| Light | Documentation, internal refactoring, or a small reversible bug fix with no contract or data impact | Brief accepted behavior, scoped plan/tasks, relevant checks, and implementation verification |
| Standard | Normal product behavior or a contained Angular/.NET feature | Specification, plan, tasks, implementation, convergence/verification, and PR evidence |
| High risk | Security, authorization, payments, public contracts, regulated data, migrations, concurrency, or major architecture | Full workflow with clarification, threat/data/compatibility review, explicit rollout and recovery, required approvers, and complete evidence |

Skipping an artifact because it is genuinely unnecessary is acceptable only when the reason is recorded. Do not classify work as light merely to bypass a required review.

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

### Angular skills with OpenSpec or GitHub Spec Kit

The [official Angular skills](https://github.com/angular/skills) complement a
specification framework; they do not replace it. The framework skill owns the
change lifecycle and artifacts, while `angular-developer` supplies
Angular-specific implementation guidance. Use the Angular skill during planning
and implementation, but keep the feature's scope, acceptance criteria, and task
status in **either** OpenSpec **or** Spec Kit. Never use both frameworks to
manage the same active feature.

The Angular repository currently provides these skills:

- `angular-developer` for Angular architecture, implementation, testing,
  accessibility, routing, forms, signals, HTTP, and CLI guidance.
- `angular-new-app` for creating a modern Angular application with the Angular
  CLI.

Install the skills at project scope from the repository root. Do not add `-g`:
the team should commit project skills so every collaborator uses the same
guidance.

#### GitHub Copilot

Install the Angular skills for GitHub Copilot:

```bash
npx skills add https://github.com/angular/skills \
  --agent github-copilot \
  --skill '*' \
  --yes
```

This command installs `angular-*` skills under `.agents/skills/`. GitHub
Copilot loads project skills from `.github/skills/`, `.agents/skills/`, and
`.claude/skills/`, so it can use the Angular skills alongside framework skills
that OpenSpec or Spec Kit generates in `.github/skills/`. See [GitHub Copilot
agent skills](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills)
for the supported locations and skill format.

Choose one specification framework and initialize its Copilot integration once:

```bash
# OpenSpec
openspec init --tools github-copilot

# GitHub Spec Kit, in Copilot skills mode, on macOS or Linux
specify init --here --force --integration copilot \
  --integration-options="--skills" --script sh
```

For the Spec Kit command, use `--script ps` on Windows. `--force` is only for
initializing or recovering an existing, non-empty repository after its current
work has been committed or otherwise protected; do not run it as routine
project setup.

Expected project layout:

```text
.agents/skills/angular-developer/SKILL.md
.agents/skills/angular-new-app/SKILL.md
.github/skills/openspec-*/SKILL.md     # when using OpenSpec
.github/skills/speckit-*/SKILL.md      # when using Spec Kit in skills mode
```

Example OpenSpec flow in Copilot:

```text
/opsx-propose Add an accessible Angular account-preferences page. Use the
angular-developer skill for the Angular design and implementation guidance.

# Review the generated proposal, specs, design, and tasks first.

/opsx-apply-change add-account-preferences
Use angular-developer. Implement only the approved unchecked tasks and run the
repository's Angular quality gates.
```

Example Spec Kit flow in Copilot:

```text
/speckit-specify Add an accessible Angular account-preferences page. Use the
angular-developer skill for Angular-specific guidance.

/speckit-plan Use angular-developer and the repository's established patterns.
/speckit-tasks

# Review the artifacts before implementation.

/speckit-implement Use angular-developer. Implement only the approved tasks
and run the repository's Angular quality gates.
/speckit-converge
```

OpenSpec writes Copilot prompt files as `opsx-*`; Spec Kit's Copilot skills mode
uses `speckit-*`. If a command is not shown by the installed Copilot client,
inspect `.github/prompts/` or `.github/skills/` and use the generated name. A
new Copilot session discovers project skills automatically; reload skills or
restart the client when its documentation requires it.

#### Codex

Install the same Angular skills for Codex:

```bash
npx skills add https://github.com/angular/skills \
  --agent codex \
  --skill '*' \
  --yes
```

For Codex, the project skill directory is `.agents/skills/`. It is also the
directory used by OpenSpec and Spec Kit's Codex integrations, so all relevant
skills share one tree:

```text
.agents/skills/angular-developer/SKILL.md
.agents/skills/angular-new-app/SKILL.md
.agents/skills/openspec-*/SKILL.md     # when using OpenSpec
.agents/skills/speckit-*/SKILL.md      # when using Spec Kit
```

Initialize the selected framework for Codex once:

```bash
# OpenSpec
openspec init --tools codex

# GitHub Spec Kit, in an existing repository on macOS or Linux
specify init --here --force --integration codex --script sh
```

Example OpenSpec flow in Codex:

```text
$openspec-propose Add an accessible Angular account-preferences page. Use the
angular-developer skill for Angular-specific guidance.

# Review the generated artifacts before continuing.

$openspec-apply-change add-account-preferences
Use angular-developer. Implement only approved unchecked tasks and run the
repository's Angular quality gates.
```

Example Spec Kit flow in Codex:

```text
$speckit-specify Add an accessible Angular account-preferences page. Use the
angular-developer skill.
$speckit-plan Use angular-developer and existing repository patterns.
$speckit-tasks

# Review the artifacts before continuing.

$speckit-implement Use angular-developer. Implement only approved tasks and
run the repository's Angular quality gates.
$speckit-converge
```

Verify either installation before beginning work:

```bash
find .agents/skills .github/skills -maxdepth 2 -name SKILL.md -print 2>/dev/null | sort
```

The command should show `angular-*` and only the selected framework's skills.
OpenSpec manages only its `openspec-*` directories, and framework updates must
not be used to overwrite the Angular skill directories.

### Rule ownership and authority

Do not copy the same instruction into every artifact. Give each type of information one authoritative home:

| Information | Authoritative location |
| --- | --- |
| Agent behavior that applies to nearly every change | `AGENTS.md` or the repository's equivalent instruction file |
| Non-negotiable engineering principles | Spec Kit constitution or reviewed repository policy |
| OpenSpec-wide planning and artifact rules | `openspec/config.yaml` |
| Feature behavior, scope, and acceptance scenarios | Active feature specification |
| Architecture and implementation choices | Reviewed design or technical plan |
| Reusable specialized procedure | Focused framework or company skill |
| Mechanically enforceable requirement | Repository scripts and CI configuration |

Examples of repository-wide rules include keeping API contracts separate from persistence entities, preserving nullable-reference-type safety, keeping reusable business logic outside presentation components, and requiring tests for new production behavior. Skills should explain specialized procedures such as migration review, observability design, or use of a company design system; they should not duplicate the entire constitution.

When instructions conflict, use this precedence unless an approved governance process states otherwise:

1. Legal, security, privacy, and compliance obligations
2. Repository constitution and mandatory policy
3. Approved active feature specification
4. Approved technical plan or design
5. Established repository architecture and conventions
6. Framework and company skills
7. General model knowledge

A lower-level artifact must not silently override a higher-level one. If an approved feature needs an exception to repository policy or architecture, record and approve that exception in the owning artifact before implementation continues.

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

Avoid skill explosion. Prefer coherent capabilities such as `company-angular`, `company-dotnet`, `company-api-contracts`, and `testing-strategy` over tiny skills such as `use-async`, `angular-signals`, or `controller-dto`. A skill should be easy for an agent to discover and for a human owner to maintain.

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

### Agent implementation discipline

Before editing, the agent should inspect the active SDD artifacts, repository instructions, manifests and project files, analyzer and formatter configuration, neighboring implementation, existing tests, and the current working-tree state. It must preserve unrelated work and follow the architecture actually present in the repository.

During implementation, prefer a small coherent change followed by the relevant build or test over generating a large batch of files and verifying only at the end. If implementation evidence invalidates an accepted requirement or design decision, pause that portion of the work and update the owning artifact through the normal review process.

Before declaring completion, report:

- Files and subsystems changed
- Requirements and scenarios implemented
- Tests added or updated
- Build, test, lint, analyzer, and other quality-gate results
- Known limitations and accepted deviations
- Remaining specification, implementation, or operational gaps

---

## 6. OpenSpec workflow

### OpenSpec setup

OpenSpec currently requires Node.js 20.19.0 or newer. Follow the
[official installation guide](https://openspec.dev/docs/installation), then initialize at the repository root:

```bash
npm install -g @fission-ai/openspec@latest
openspec --version

# Select the coding-agent integration used by this repository.
openspec init --tools github-copilot

# Or, for a Codex repository:
openspec init --tools codex
```

The `@latest` command is appropriate for evaluating the current release. For repeatable team onboarding and CI, record and install an approved version, assign an update owner, and review generated workflow changes before adopting an upgrade.

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

For repeatable team environments, pin the approved Spec Kit release through the team's chosen installation mechanism. Upgrade the CLI and generated integration files in a dedicated, reviewed change.

For an existing repository, first commit or otherwise protect current work, then initialize for the team’s coding agent. The following examples use skills-based integrations for GitHub Copilot and Codex:

```bash
# GitHub Copilot on macOS or Linux
specify init --here --force --integration copilot \
  --integration-options="--skills" --script sh

# Codex on macOS or Linux
specify init --here --force --integration codex --script sh
```

Review and commit `.specify/`, the integration’s generated skills or commands, and related configuration. Use the
[existing-project guidance](https://github.github.com/spec-kit/guides/existing-projects.html) before adopting Spec Kit in a mature codebase.
Use `--script ps` on Windows. `--force` acknowledges the merge warning in a non-empty directory; it is not a routine update command. See [Angular skills with OpenSpec or GitHub Spec Kit](#angular-skills-with-openspec-or-github-spec-kit) for the matching Angular-skill installation and invocation examples.

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

### Angular agent tooling

The Angular team publishes [official Agent Skills](https://github.com/angular/skills).
Where the selected coding agent supports skills, install or synchronize them
using the repository's approved dependency/update process. For team-safe,
agent-specific commands and end-to-end OpenSpec and Spec Kit examples for
GitHub Copilot and Codex, see [Angular skills with OpenSpec or GitHub Spec
Kit](#angular-skills-with-openspec-or-github-spec-kit). In particular, pass
`--agent github-copilot` or `--agent codex` instead of relying on an interactive
target selection.

The general-purpose `angular-developer` skill covers modern Angular architecture and APIs. Combine it with small company-specific skills rather than copying Angular documentation into a large custom skill.

On Angular CLI versions that provide the schematic, generate agent-specific instructions and configuration with:

```bash
ng generate ai-config
```

The Angular CLI also provides an MCP server for supported agent hosts:

```bash
npx @angular/cli mcp
```

Skills provide framework guidance; the CLI or MCP server provides workspace inspection and execution tools. Neither replaces repository review, tests, CI, or explicit approval for architectural changes.

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

Treat CI as part of the agent harness, not as a final administrative step. Important instructions should be backed by automated checks wherever practical: Angular formatting/linting/tests/builds, .NET formatting/analyzers/tests/builds, specification validation, contract checks, and security or dependency checks. Human review remains mandatory for correctness, architecture, risk, and judgment that automation cannot establish.

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

## 15. Repository and CI baseline

Use the structure generated by the selected framework and agent integration. A representative combined repository looks like:

```text
repo/
├── AGENTS.md
├── README.md or CONTRIBUTING.md
├── openspec/                    # OpenSpec
│   ├── config.yaml
│   ├── specs/
│   └── changes/
├── .specify/                    # Spec Kit alternative
├── specs/                       # Spec Kit feature artifacts
├── .agents/skills/              # when supported by the agent
├── src/
├── tests/
└── CI configuration
```

The OpenSpec and Spec Kit paths in this example are alternatives for one bounded project, not a recommendation to initialize both. Exact generated directories differ by tool version and integration; preserve the installed tool's structure instead of forcing the example manually.

### Minimal repository-agent baseline

A repository instruction file should remain concise and enforce durable behavior. Adapt this baseline rather than copying framework tutorials into it:

```md
# Repository engineering rules

- Preserve the existing architecture unless an approved specification and design change it.
- Prefer small, reviewable changes.
- Do not introduce frameworks or dependencies without justification and review.
- Do not edit generated files when a supported generator or source artifact owns them.
- Do not weaken, skip, or remove tests merely to make a change pass.
- Do not remove validation, authorization, logging, observability, or error handling without an explicit requirement.
- Preserve unrelated working-tree changes.

Before completing work:

- Verify the implementation against the active feature artifacts.
- Build the affected projects.
- Run the relevant automated tests.
- Run formatting, linting, analyzers, and repository-specific checks.
- Report evidence, limitations, deviations, and remaining gaps.
```

### CI expectations

At minimum, CI should run the repository-equivalent checks for the affected stack:

| Area | Typical checks |
| --- | --- |
| Specification | Artifact structure, links/traceability, and `openspec validate` where applicable |
| Angular | Lockfile install, formatting, linting, tests, production build, bundle budgets, and critical e2e/a11y checks |
| .NET | Restore, formatting/analyzers, build, unit/integration tests, migration review checks, and architecture rules where policy requires them |
| Contracts | OpenAPI/event-schema compatibility and client/server contract tests |
| Security | Dependency, secret, static-analysis, authorization, and policy-required scans |

Do not claim that CI proves complete feature correctness. It supplies repeatable evidence; convergence/verification and human review still compare the delivered behavior with the accepted intent.

---

## 16. Official references

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
- [Using Spec Kit in a monorepo](https://github.github.com/spec-kit/guides/monorepo.html)
- [Customization](https://github.github.com/spec-kit/guides/customization.html)
- [Handling complex features](https://github.github.com/spec-kit/concepts/complex-features.html)

### Angular

- [Angular Agent Skills](https://angular.dev/ai/agent-skills)
- [Angular CLI MCP server](https://angular.dev/ai/mcp)
- [Angular AI configuration schematic](https://angular.dev/cli/generate/ai-config)
- [Angular style guide](https://angular.dev/style-guide)
- [Performance practices](https://angular.dev/best-practices/performance)
- [Security practices](https://angular.dev/best-practices/security)
- [Accessibility practices](https://angular.dev/best-practices/a11y)
- [Testing guide](https://angular.dev/guide/testing)

### .NET

- [C# coding conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [.NET dependency injection guidelines](https://learn.microsoft.com/dotnet/core/extensions/dependency-injection/guidelines)
- [.NET options pattern](https://learn.microsoft.com/dotnet/core/extensions/options)
- [C# nullable reference types](https://learn.microsoft.com/dotnet/csharp/nullable-references)
- [ASP.NET Core web APIs](https://learn.microsoft.com/aspnet/core/web-api/)
- [ASP.NET Core integration tests](https://learn.microsoft.com/aspnet/core/test/integration-tests)
- [ASP.NET Core API error handling](https://learn.microsoft.com/aspnet/core/fundamentals/error-handling-api)
- [EF Core efficient querying](https://learn.microsoft.com/ef/core/performance/efficient-querying)
- [.NET observability with OpenTelemetry](https://learn.microsoft.com/dotnet/core/diagnostics/observability-with-otel)
