---
name: system-design
description: Draft and refine frontend system designs before implementation, covering architecture, UI flows, component ownership, state, API contracts, routing, security, accessibility, performance, and validation. Use for new features or material frontend changes that should be reviewed and corrected before building. Do not implement until the user explicitly approves the design.
---

# Frontend System Design

Design a frontend feature before implementation. Ground the proposal in the existing repository, expose important decisions and tradeoffs, incorporate user corrections, and produce an approved implementation contract for `build`.

This is a pre-build workflow. It may inspect code and documentation, but it must not modify product files or begin implementation until the user explicitly approves the design or clearly says to proceed with building it. Silence is not approval.

## When to Use

Use this skill for:

- New pages, flows, or substantial features.
- API-backed UI with meaningful data, cache, state, or failure behavior.
- Material routing, state-management, authentication, real-time, or architectural changes.
- Work where component ownership, reuse, responsive behavior, or interaction flow needs agreement.
- Any request that explicitly asks to design, plan, or agree on an approach before implementation.

A small, well-specified fix or local styling change does not require a full system design unless the user asks for one.

## Design Workflow

1. Establish the problem, desired outcome, users, constraints, supplied design or API contracts, and explicit non-goals.
2. Inspect repository instructions, the installed stack, routes, page architecture, nearest comparable features, shared components, API services, types, state, tests, and relevant consumers.
3. Load only the domain companion skills needed to evaluate the design.
4. Separate verified repository facts, user requirements, assumptions, recommendations, and unresolved decisions.
5. Draft the smallest complete design that satisfies the goal while preserving established architecture and behavior.
6. Present alternatives where a choice materially affects complexity, UX, compatibility, performance, or future work. Recommend one and explain the tradeoff.
7. Ask focused questions only for decisions that would materially change the design and cannot be safely represented as explicit assumptions.
8. Incorporate the user's corrections into a revised draft. Preserve rejected alternatives or changed decisions in a short decision log when that context will help implementation.
9. Request explicit approval. Do not transition to `build` until approval is given.
10. After approval, hand the approved design to `build` as the implementation contract. If implementation evidence later contradicts the design, pause and revise the design rather than silently diverging.

## Companion Skills

Use companions in design mode: inspect evidence and shape decisions, but do not edit product code.

- **`frontend-architecture-review`:** Page ownership, colocation, reuse, module boundaries, abstractions, and dependencies.
- **`frontend-typescript-review`:** Domain models, component props, API contracts, nullability, schemas, and public module contracts.
- **`react-correctness-review`:** State ownership, effects, context, forms, routing behavior, asynchronous flows, and real-time synchronization.
- **`frontend-ui-review`:** Information hierarchy, interaction states, responsive behavior, accessibility, dialogs, forms, and design-system fit.
- **`api-integration`:** Endpoints, request and response shapes, query identity, caching, mutations, invalidation, pagination, races, and failure states.
- **`nextjs-app-router-review`:** Route structure, Server and Client boundaries, actions, handlers, caching, metadata, and redirects.
- **`frontend-i18n-review`:** Translation boundaries, locale routing, interpolation, plurals, and localized metadata.
- **`frontend-security-review`:** Trust boundaries, authorization, unsafe sinks, secrets, CSRF, and serialization.
- **`frontend-performance-seo-review`:** Bundle boundaries, rendering and network cost, images, Core Web Vitals, indexing, canonicals, and shareability.
- **`frontend-testing-review`:** Behavioral acceptance criteria, regression coverage, repository test utilities, and validation scope.

If a companion is unavailable, use verified repository patterns and authoritative documentation for the installed version, and mark the limitation.

## Required Design Content

Adapt the depth to the feature, but address every section that materially applies.

### Context and Scope

- Problem statement and intended user outcome.
- In-scope and out-of-scope behavior.
- Verified constraints, dependencies, and assumptions.
- Existing codebase precedents that the design follows, named precisely.

### User and UI Flow

- Entry points, primary path, alternate paths, and completion state.
- Loading, empty, filtered-empty, error, retry, pending, disabled, success, and confirmation behavior where applicable.
- Navigation, URL and browser-history behavior, form preservation, scrolling, and responsive changes.
- Accessibility semantics, keyboard behavior, focus management, announcements, and touch behavior.

### Architecture and Ownership

- Route and page ownership.
- Component hierarchy and responsibility boundaries.
- Feature-local versus shared components, hooks, types, and utilities.
- Server and Client Component boundaries when applicable.
- State ownership and source-of-truth decisions: URL, server cache, form state, local state, context, or store.

### Data and API Contracts

- Exact known method, endpoint, route parameters, query parameters, payload, and response shape.
- Unknown or externally owned contract details that require confirmation.
- Query keys, transformations, pagination, caching, freshness, invalidation, optimistic behavior, rollback, and real-time synchronization.
- Failure behavior, retained data, retry policy, cancellation, duplicate-action prevention, and concurrency considerations.

### Cross-Cutting Concerns

- Type contracts and runtime validation boundaries.
- Authentication, authorization, sensitive data, unsafe content, and other relevant security boundaries.
- Localization and locale-aware routing or metadata.
- Performance, bundle, image, rendering, network, and SEO implications.
- Backward compatibility, migration, rollout, feature-flag, or fallback needs when applicable.

### Validation and Delivery

- Testable acceptance criteria stated as observable behavior.
- Focused unit, component, integration, browser, or manual checks appropriate to the change.
- Type-check, lint, build, accessibility, responsive, performance, or SEO verification when relevant.
- Implementation sequence with dependencies and safe checkpoints, without writing the implementation itself.

## Draft Format

Use a concise Markdown design with these headings, omitting only sections that clearly do not apply:

```md
# <Feature> Frontend System Design

**Status:** Draft | Revised | Approved

## Problem and Outcome
## Scope and Non-Goals
## Existing System Evidence
## Proposed User Flow
## Proposed Architecture
## Component and State Ownership
## API and Data Contracts
## UI States and Accessibility
## Security, Performance, SEO, and i18n
## Alternatives and Tradeoffs
## Risks and Open Questions
## Acceptance Criteria
## Validation Strategy
## Implementation Sequence
## Decision Log
```

Use a small flow, sequence, or hierarchy diagram only when it makes relationships materially clearer than prose.

## Revision and Approval Rules

- Label the first proposal `Draft`; do not describe it as final or approved.
- Apply user corrections directly and label the next version `Revised`.
- Clearly identify changed decisions, newly resolved questions, and remaining blockers without repeating the entire conversation.
- Do not defend an earlier proposal merely for consistency. Prefer stronger repository evidence and the user's clarified requirements.
- Mark a design `Approved` only after explicit user approval.
- Approval authorizes the design as an implementation contract but does not broaden the requested scope or authorize unrelated changes.
- After approval, use `build` to implement and validate the approved design.
