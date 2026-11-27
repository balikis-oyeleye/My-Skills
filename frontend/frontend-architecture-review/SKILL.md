---
name: frontend-architecture-review
description: Review frontend architecture, module ownership, colocation, reuse, abstractions, naming, dependencies, and dead code. Use for targeted architecture reviews or as a companion to the code-review skill.
---

# Frontend Architecture Review

Review whether frontend code fits the repository's existing architecture without introducing unnecessary duplication, indirection, or misplaced ownership. Base findings on actual consumers and local conventions.

When used from `code-review`, return findings to that skill's severity and output format. Review only unless implementation is explicitly requested.

## Architecture and Colocation

- Keep feature-owned components, tables, columns, cards, modals, dropdowns, skeletons, and creation stages near the owning feature, commonly under its `_components` directory.
- Put a component in a shared directory only when it has genuine cross-module use. Do not generalize unrelated features merely because their current shapes look similar.
- Organize by responsibility rather than only by the current caller. For example, an SSE listener used by a context provider but not implementing context belongs in the API or listener layer.
- Keep constants, helpers, types, and fixtures in the repository's established locations.
- Flatten unnecessary single-file directories while preserving route entry points and module boundaries the repository requires.
- When files move or are renamed, verify filenames, imports, exports, public entry points, and corresponding test paths.

## Reuse and Simplicity

- Search for existing components, hooks, helpers, types, and patterns before accepting a new implementation.
- Extend a reusable component only when the new behavior belongs to its responsibility and existing consumers remain compatible.
- When two complete components have different product or API requirements, extract only the genuinely shared part instead of forcing a highly configurable abstraction.
- Flag wrappers that only forward props or arguments unless they preserve a provider, adaptation boundary, stable public API, or another demonstrated responsibility.
- Detect repeated calls or computations within one execution path. Consolidate only when inputs, state, ordering, side effects, and configuration are equivalent. Do not introduce memoization without an actual lifecycle or performance need.
- Simplify duplicated branches and redundant conditions only after verifying the underlying data invariants.
- Prefer named handlers for multi-step actions and clear conditionals for side effects.
- Flag unused production code, obsolete mocks, dead branches, accidental console statements, stray utility classes, and `cn()` around purely static classes. Preserve intentional fixtures and approved logging.

## Dependencies

- Evaluate new dependencies against the actual workload, installed framework versions, bundle impact, maintenance, reliability, and security.
- Verify suspected dependency defects against the installed version or source rather than package reputation.
- Prefer the repository's existing dependency or platform capability when it already satisfies the requirement.

## Review Boundaries

Treat architectural differences as findings only when they create a concrete correctness, ownership, reuse, testing, or maintenance problem or violate an established project rule. Keep subjective preferences out of the findings.
