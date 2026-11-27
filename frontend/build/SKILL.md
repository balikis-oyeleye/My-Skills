---
name: build
description: Build or modify frontend features in React, Next.js, and TypeScript, including UI implementation, responsive behavior, forms, state, API integration, localization, and tests. Use when asked to create, implement, fix, refactor, or connect frontend functionality. Do not use for findings-only code reviews.
---

# Frontend Build

Implement frontend work as an experienced product engineer. Match the repository's architecture and established patterns, preserve existing behavior outside the requested scope, and finish with proportionate validation.

This is the top-level implementation skill. Use `code-review` instead when the user asks only for findings or evaluation without changes.

For a new feature, material architectural change, or any request that asks for design-first work, use `system-design` before implementation. Do not begin building until the user explicitly approves the draft. Small, well-specified fixes can proceed directly unless the user requests a design.

## Build Modes

Identify the work before editing:

- **UI build:** Components, pages, layouts, forms, interactions, responsive behavior, accessibility, styling, and design-system integration.
- **API integration:** HTTP services, contracts, queries, mutations, cache behavior, request states, pagination, and error handling.
- **Combined feature:** API-backed UI where the data contract, state transitions, and visible experience must be implemented together.
- **Frontend refactor or fix:** Behavior-preserving restructuring or a focused correction to existing frontend functionality.

Most product work can involve more than one mode. Treat the API and visible UI as one user flow when they depend on each other.

## Workflow

1. Establish the requested outcome, constraints, supplied design or API contract, and definition of done.
2. Determine whether the task requires `system-design`. If it does and no approved design exists, draft the design and stop for user correction or approval.
3. Treat an approved design as the implementation contract. If repository evidence conflicts with it, pause and revise the design rather than silently diverging.
4. Read repository instructions and inspect the installed stack, relevant route or page, nearest comparable implementation, shared components, types, utilities, tests, and consumers.
5. Select only the companion skills relevant to the work.
6. Implement the smallest complete change that satisfies the approved design or clearly scoped request. Preserve unrelated user changes and avoid speculative abstractions or dependencies.
7. Exercise the affected user flow and inspect the final diff for incomplete handlers, stale state, contract mismatches, accessibility regressions, and unintended scope.
8. Run focused tests first, then appropriate type checking, linting, build, or browser verification in proportion to the change and repository conventions.
9. Report the completed behavior, important implementation decisions, checks that actually ran, and any remaining limitations.

Do not invent API fields, design behavior, business rules, fallback values, routes, or components. Investigate repository evidence first. Ask only when a missing decision would materially change the implementation and cannot be safely inferred.

## Companion Skills

Load only companions implicated by the task. A build request explicitly places them in implementation mode: apply their constraints while changing code, rather than returning review findings.

- **`frontend-architecture-review`:** Ownership, colocation, reuse, abstractions, module boundaries, and dependencies.
- **`frontend-typescript-review`:** Props, contracts, narrowing, schemas, nullability, exported APIs, and safe external boundaries.
- **`react-correctness-review`:** Components, hooks, effects, context, state, forms, routing, asynchronous interactions, and real-time behavior.
- **`frontend-ui-review`:** Design-system usage, Tailwind or CSS, interaction states, responsive behavior, accessibility, dialogs, forms, and visible feedback.
- **`api-integration`:** HTTP services, TanStack Query, response transformations, mutations, invalidation, pagination, request races, and API-backed rendering states.
- **`nextjs-app-router-review`:** App Router boundaries, route UX, Server Actions, Route Handlers, caching, revalidation, metadata, and redirects.
- **`frontend-i18n-review`:** Translation keys, namespaces, interpolation, plurals, locale routing, and localized metadata.
- **`frontend-security-review`:** Trust boundaries, unsafe sinks, authorization, secrets, CSRF, and SSR serialization.
- **`frontend-performance-seo-review`:** Bundle and render cost, images, network behavior, Core Web Vitals, indexing, canonical URLs, and share metadata.
- **`frontend-testing-review`:** Meaningful behavioral coverage and repository-specific test utilities.

If a companion is unavailable, follow verified repository patterns and authoritative documentation for the installed version, then state the limitation.

`system-design` is a prerequisite workflow, not an implementation companion. Use it to establish and approve material design decisions before loading the build companions above.

## UI Implementation Contract

- Build from the supplied requirements or design and compare against the nearest established product pattern before creating new UI primitives.
- Keep feature-owned components close to the page or feature that owns them. Promote code to shared modules only when actual reuse justifies it.
- Implement applicable loading, empty, filtered-empty, error, retry, pending, disabled, success, and confirmation states.
- Use semantic controls, accessible names, visible focus, keyboard behavior, appropriate dialog focus management, and responsive layouts without horizontal overflow.
- Preserve the intended interaction order, navigation and history behavior, form values, scrolling, and feedback.
- Route user-facing copy through the project's localization system when one exists.

## API Integration Contract

- Treat the supplied method, endpoint, parameters, payload, and response example as the contract. Confirm uncertainty rather than fabricating fields.
- Reuse request helpers, serializers, query keys, service structure, types, and comparable hooks already present in the repository.
- Keep transport, cached server state, consumer transformations, and local UI state in their appropriate layers.
- Ensure every value affecting fetched data participates in query identity and guard requests whose required inputs are unavailable.
- Prevent duplicate mutations, update or invalidate the correct cache scope, preserve usable data during recoverable failures, and expose retry where appropriate.
- Connect the API result to the complete visible flow; a service or hook alone is not a finished feature when the request includes UI behavior.

## Validation and Handoff

Validation should prove the changed behavior, not merely that files compile.

- Use the repository's existing commands and shared test utilities.
- Prefer focused behavioral tests for the affected flow; do not add tests or tooling when the project explicitly excludes them.
- For visual or interaction work, verify the relevant viewport and interaction path in a real browser when available and proportionate.
- Run broader checks after focused checks when their cost is justified.
- Report environment failures separately from product failures and never claim an unexecuted check passed.

In the final handoff, lead with what now works. Summarize important files or decisions, list validation results, and mention only genuine remaining work or limitations.
