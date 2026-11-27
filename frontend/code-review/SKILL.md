---
name: code-review
description: Orchestrate frontend React, Next.js, and TypeScript reviews across architecture, API integration, correctness, security, UI, accessibility, performance, SEO, i18n, and testing. Use for pull requests, diffs, snippets, existing implementations, debugging, or production-readiness reviews. Not intended for backend, database, or infrastructure reviews.
---

# Frontend Code Review

Review frontend code as an experienced teammate familiar with the project's conventions. Prioritize demonstrable bugs, incomplete functionality, regressions, and maintainability over subjective preferences.

This is the coordinating skill. Keep review scope, investigation, severity, and reporting here. Load only the companion skills relevant to the changed code; do not load every companion by default.

## Scope

Review React, Next.js, and TypeScript frontend applications, including components, hooks, client-side state, routing, styling, localization, browser-side API services, Server Actions, route handlers, authentication integration, real-time listeners, and frontend tests.

Inspect backend contracts only as needed to verify frontend behavior. Do not review server implementations, database schemas, or infrastructure. For mixed changes, review the frontend work and report external dependencies or contract uncertainties separately.

Do not edit files for a review-only request. When the user explicitly asks to implement fixes as part of the same task, use `build` for the authorized implementation after establishing the review findings.

### Isolated snippets

When the user supplies a snippet without requesting repository-context review:

- Use only the supplied snippet and do not infer product requirements, framework context, or missing surrounding behavior.
- Apply framework-specific rules only when the snippet identifies that framework.
- Treat placeholder-looking handlers, mock data, and stubs as confirmed issues only when the user says the snippet is complete production code.
- Put unverified concerns under **Context-dependent considerations** and label them as conditional. They must not block merging.
- If no verified issue exists, say `Safe to merge based on the supplied snippet`.

## Workflow

1. Establish the intended behavior, review scope, requirements, and appropriate diff base.
2. Read applicable repository instructions, architecture conventions, and testing standards, including `API_TESTING_STANDARDS` when present.
3. Inspect related implementations, types, utilities, design tokens, tests, and consumers before judging the change.
4. Select and apply the relevant companion skills below. For a broad audit, use the routing checklist in [references/checklist.md](references/checklist.md).
5. Trace affected user flows, data contracts, state transitions, URL and history behavior, and cross-module dependencies. Compare behavior before and after the change.
6. Run relevant tests, type checks, and lint checks when available and proportionate. Report only checks that actually ran and their outcomes.
7. Report actionable findings using the output format below.

Do not assume requirements, contracts, components, types, or business rules. Investigate available evidence first. Keep unresolved questions separate from confirmed findings and continue reviewing independent issues where possible.

## Companion Skills

Use a companion when its trigger applies. Apply all companions in review mode: report findings and recommendations, but do not edit files unless the user separately requests implementation.

- **`frontend-architecture-review`:** Use for module ownership, colocation, abstractions, reuse, naming, imports, exports, dead code, and dependency placement.
- **`frontend-typescript-review`:** Use for type modeling, narrowing, nullability, assertions, generics, exported contracts, runtime-schema alignment, and compiler-option claims.
- **`api-integration`:** Use for HTTP services, TanStack Query queries or mutations, query keys, cache updates, pagination, response transformation, loading and error states, and API-facing types.
- **`react-correctness-review`:** Use for hooks, effects, context, local or shared state, routing and browser history, asynchronous interactions, real-time synchronization, functional completeness, and runtime performance.
- **`nextjs-app-router-review`:** Use for App Router Server and Client Component boundaries, Cache Components, route UX, Server Actions, Route Handlers, Proxy, metadata, and redirects.
- **`frontend-security-review`:** Use for unsafe HTML or URL sinks, authorization boundaries, secrets, CSRF, SSR serialization, and Content Security Policy.
- **`frontend-ui-review`:** Use for design-system consistency, Tailwind and CSS tokens, accessibility, responsive layout, interaction feedback, confirmation flows, and visible UI behavior.
- **`frontend-i18n-review`:** Use for locale key integrity, namespaces, fallbacks, interpolation, plurals, locale routing, and localized metadata.
- **`frontend-performance-seo-review`:** Use for bundles, rendering and network performance, Core Web Vitals, images, metadata, canonical URLs, indexing, redirects, and shareability.
- **`frontend-testing-review`:** Use when tests change, behavior changes require regression coverage, or the repository's test standards affect the review.

When multiple areas apply, use all relevant companions and combine their results into one review. Do not duplicate the same underlying issue across companions.

If a named companion is unavailable, continue using repository evidence and authoritative documentation, and state the limitation.

### Related workflow

Use **`system-design`** to draft and correct a new feature or material architectural change before implementation. It must receive explicit approval before handing work to `build`.

Use **`build`** when the user asks to create, implement, fix, refactor, or integrate frontend functionality. For a combined review-and-fix request, use this skill to establish evidence and `build` to make and validate the requested changes.

Use **`pr-description-generator`** only when the user explicitly asks for a pull request description. It is not part of an ordinary code review and must not be loaded merely because the review target is a PR.

## Cross-Cutting Review Checks

- Trace visible actions to working handlers and intended data sources. Flag no-op callbacks, production placeholders, dummy data, incomplete workflows, and missing failure handling.
- Preserve established behavior unless a change is required or approved. Verify validation, defaults, resets, modal transitions, navigation, scrolling, and repeated interactions against intended behavior.
- When routing or tab state changes, inspect history semantics, deep links, browser back and forward behavior, route-dependent effects, and scroll behavior.
- Establish a concrete reproduction or code path before calling a suspected issue a bug.
- Separate regressions introduced by the change from pre-existing or out-of-scope problems. Recommend follow-up work instead of silently expanding scope.

## Review Output

Read and follow [references/output-format.md](references/output-format.md) unless the user requests a different format. PR and branch-diff reviews follow changed-file order; broad audits and snippet reviews group findings by severity.

Keep unresolved questions separate from confirmed findings. List pre-existing or out-of-scope issues separately. Report checks performed, results, and review limitations. If no actionable findings remain, say so; do not manufacture issues or claim merge readiness when required checks are unavailable.

When a technical conclusion depends on version-sensitive framework behavior, use the installed version and authoritative documentation. [references/sources.md](references/sources.md) contains starting points; read only the relevant section.
