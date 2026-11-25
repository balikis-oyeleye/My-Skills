---
name: frontend-code-review
description: Review frontend React and TypeScript code for correctness, regressions, architecture, reuse, API integration, state synchronization, performance, design consistency, and meaningful test coverage. Use when reviewing pull requests, diffs, staged or uncommitted changes, existing frontend implementations, or frontend code before submission. Not intended for backend, database, or infrastructure reviews.
---

# Frontend Code Review

## Overview

Review frontend code as an experienced teammate familiar with the project's conventions. Prioritize demonstrable bugs, incomplete functionality, regressions, and maintainability over subjective preferences. Evaluate the implementation in the context of its consumers, existing architecture, and intended product behavior.

**Scope:** React and TypeScript frontend applications, including UI components, hooks, client-side state, routing, styling, localization, browser-side API services, authentication integration, real-time listeners, and frontend tests. Inspect backend contracts only as needed to verify frontend behavior; do not review server implementations, database schemas, or infrastructure. For mixed PRs, review the frontend changes and report external dependencies or contract uncertainties separately.

## Review Workflow

1. Establish the frontend review scope, intended behavior, and relevant requirements. Inspect the diff against the appropriate base when available.
2. Read applicable project instructions, architecture conventions, testing standards (including `API_TESTING_STANDARDS` if present), and related skills. Inspect existing implementations, types, utilities, design tokens, tests, and consumers before recommending changes.
3. Trace affected user flows, data contracts, state transitions, and cross-module dependencies. Compare behavior before and after the change.
4. Apply the core guidelines below. Run relevant tests, type checks, and lint checks when available and appropriate; report what actually ran and its outcome.
5. Report actionable findings using the output format below. Review only unless implementation is explicitly requested.

**Do not assume.** Investigate available evidence first. If requirements, API contracts, design intent, or project conventions remain unclear, ask focused questions before making decisions that depend on them. Continue reviewing independent issues where possible. Do not invent existing components, types, test requirements, or business rules.

## Core Guidelines

### Architecture and colocation

- Keep feature-specific components, tables, column definitions, cards, modals, dropdowns, skeletons, and creation stages close to the feature that owns them, typically under its `_components` directory. Do not place feature-specific implementations in `src/components` merely because they are React components.
- Make components with genuine cross-module applications generic and place them in the appropriate shared component directory. Inspect consumers and responsibilities; do not force unrelated features into a shared abstraction.
- Organize files by responsibility, not merely by where they are consumed. For example, an SSE listener used by a context provider but not implementing context belongs in the API/listener layer rather than the context directory.
- Keep constants, helpers, types, and fixtures in their appropriate locations according to repository conventions. Avoid mixing unrelated responsibilities.
- Flatten unnecessary single-file directories: prefer `offering.tsx` over `offering/index.tsx` when the directory serves no other purpose. Preserve required route and module entry points; use `index.tsx` where the project expects it.
- Check filename spelling, imports, exports, and corresponding test paths when moving or renaming files.

### Reuse and code simplicity

- Search for existing components, hooks, helpers, types, and patterns before creating new ones. Reuse compatible implementations, such as existing dropdowns, success modals, and text utilities.
- Extend a reusable component when the new behavior belongs to its responsibility (for example, single-property sorting in `CustomSort`). Check existing consumers and regression coverage.
- When complete components differ in design or API requirements, extract the genuinely shared subcomponent or helper instead of duplicating common logic or forcing both features into an overly configurable component.
- Remove wrappers that only forward arguments or props without meaningful responsibility. Preserve justified boundaries, such as required providers, adaptation, or stable public interfaces.
- Identify redundant invocations of the same function, hook, helper, or computation within a component or execution path, including calls with different arguments. Reuse a single result when inputs are unchanged, or consolidate calls when the existing API supports multiple inputs in one invocation (for example, `useTranslation` with multiple namespaces). Inspect the function contract and consumers before recommending consolidation; preserve return values, ordering, side effects, independent state, hook rules, and distinct configurations. Do not merge calls merely because they use the same function name, and do not introduce unnecessary memoization.
- Simplify duplicated branches and logically redundant conditions only after verifying the underlying data invariants.
- Use named handlers for complex or multi-step actions; reserve inline callbacks for simple actions. Prefer clear `if/else` statements for conditional side effects and strict equality over loose equality.
- Remove unused implementations, imports, variables, obsolete production mocks, dead branches, and unnecessary console statements. Preserve intentional test fixtures and approved logging or monitoring.
- Avoid `cn()` around a purely static class string; check for invalid or stray utility classes.

### TypeScript and data contracts

- Place reusable API payload and response types in the appropriate `src/types` module. Search for compatible existing types before declaring duplicates.
- Derive related types using appropriate TypeScript utilities such as `Partial`, `Pick`, or `Omit` when their semantics match the requirement. Do not reuse a type solely because its current shape happens to match an unrelated domain.
- Verify optional fields, nullability, identifiers, and response shapes against actual contracts. Do not substitute empty strings for required identifiers to silence type errors.
- Ensure action availability and payload construction account for missing required data. Check each action's own prerequisites, not just a related action's conditions.
- Name types, discriminated-union variants, flags, and derived predicates according to their **specific domain meaning**, not a broad category that also describes other variants. Check how names read at call sites and in conditions; e.g. if both client/lead and sales workflows are pipelines, prefer `"client" | "sales"` over `"pipeline" | "sales"` so `variant === "client"` is unambiguous. Preserve API-defined values and persisted contracts; do not rename public or serialized discriminants without checking compatibility and migration needs.

### Frontend API integration and error handling

- Follow the project's existing request helpers, query keys, service conventions, and mutation metadata. Keep URL construction separate from query parameter serialization where appropriate.
- Prefer Axios `params` and the configured serializer over manual query-string construction. Preserve the backend's required array format, encoding, omission rules, and pagination contract.
- Avoid service-level `try/catch` blocks that only log and rethrow errors when TanStack Query already handles them. Keep meaningful recovery, cleanup, or error adaptation where necessary; do not swallow errors.
- Apply the TanStack Query integration skill when reviewing query or mutation implementations, if available. In particular, use `select` rather than `useMemo` solely for response transformation; verify response and pagination metadata before changing the selected shape.
- Distinguish initial loading and initial errors from background refetch and pagination failures. Preserve already loaded data on subsequent-page errors and provide appropriate inline retry behavior using the installed version's query flags (for v5, `isFetchNextPageError`).
- For mutable paginated collections, investigate duplicate or missing records caused by shifting offsets and concurrent updates. Verify whether existing invalidation, pagination strategy, or deduplication addresses the risk before recommending changes.

### React correctness and state synchronization

- Verify context consumers are descendants of the intended provider. A provider returned by the same component cannot supply context to a hook called in that component.
- Inspect effect dependencies, stale closures, cleanup, and repeated execution. Prefer stable derived primitives over unstable object or array dependencies only when they capture all relevant changes; do not omit required dependencies to suppress execution.
- Trace mutations and real-time events through affected query caches, counters, stores, and UI consumers. Consider other tabs, reconnects, and concurrent operations where relevant.
- Avoid redundant invalidations when SSE or another established mechanism already guarantees the required synchronization. Verify event delivery, affected query keys, and fallback behavior before removing invalidations. Centralize cross-cutting synchronization when justified instead of scattering it across unrelated services.
- Reuse the application's coordinated token-refresh mechanism so concurrent callers share refresh work. Inspect auth-store updates and effect reruns before adding separate reconnect or token state.
- Prevent duplicate submissions and repeated consequential actions while requests are pending. Verify that the pending state reaches all relevant action surfaces and that rapid interactions cannot bypass the protection.

### Performance and dependencies

- Lazy-load on-demand modals and other suitable deferred UI according to the project's existing lazy-loading and Suspense patterns. Verify loading and error behavior; do not defer essential initial UI without justification.
- Investigate third-party dependencies for maintenance, compatibility, reliability, security, and suitability for the workload. Verify suspected implementation defects against the actual installed version or source rather than relying on package popularity alone.
- For long-lived streams, inspect per-connection state isolation, memory growth, chunk decoding, event parsing, cancellation, retries, reconnection, and event replay where applicable.
- Avoid speculative optimization and unnecessary memoization. Identify the actual repeated work or lifecycle problem and recommend a proportionate fix.

### Design system and user experience

- Check `tailwind.config`, CSS variables (including `index.css`), and existing component variants before introducing hardcoded colors or other design values. Use the established token when one exists; do not invent token names or class prefixes.
- Compare new UI against established examples of the same pattern, especially modal headers, action buttons, spacing, borders, and component variants. Use consistent icons and terminology for the same action across surfaces.
- Localize user-facing strings, including status labels, through the project's existing i18n conventions. Verify translation keys and namespaces.
- When a component uses multiple i18next namespaces with the same translation configuration, prefer a single `useTranslation(["sendup", "common"])` call over separate hooks and aliased translation functions. Use explicit namespace-prefixed keys (for example, `t("sendup:active-text")` and `t("common:displayed-text")`) to make the source unambiguous. Preserve existing keys and verify namespace loading, typing, and hook options before consolidating; retain separate calls when distinct configurations require them.
- Preserve confirmation dialogs for consequential actions such as granting access, transferring ownership, or deleting data unless an approved requirement changes that behavior.
- Ensure appropriate success and error feedback. Avoid redundant success toasts when the UI already clearly communicates the outcome and the project supports suppressing them. Do not remove necessary feedback from consequential actions.

### Functional completeness and regression prevention

- Trace visible actions to working handlers and the intended data source. Explicitly flag unfinished features, nonfunctional buttons, no-op callbacks, placeholder recipients, dummy production data, and incomplete workflows. Distinguish intentional test fixtures, stories, and approved placeholders from production gaps.
- Preserve established product behavior unless the change is required or approved. For example, do not hide inactive options when the existing requirement is to display them disabled.
- Check changes to required-field indicators, validation, default values, form resets, modal transitions, visibility, navigation, and scrolling against the intended behavior. Verify multi-step forms retain required values while showing the correct fields.
- Consider relevant failure paths, missing data, slow requests, repeated interactions, and state transitions. Establish a concrete reproduction or code path before labeling a suspected issue a bug.
- Draw attention to incomplete functionality even when discovered outside the immediate diff. Separate pre-existing or out-of-scope issues from regressions introduced by the change; recommend a follow-up PR where appropriate rather than expanding scope silently.

### Testing

- Follow the repository's testing standards and existing test patterns. Inspect `API_TESTING_STANDARDS` before recommending additional API tests; if unavailable, state that limitation.
- Require meaningful tests for new functionality and changed behavior. Check relevant loading, empty, disabled, pending, error, success, confirmation, and regression scenarios rather than demanding every state for every component.
- Test the actual user flow, not only a mocked mutation or optimistic cache update. Exercise relevant interactions and assert observable outcomes using appropriate mocks at the boundaries.
- Do not duplicate tests for shared components in every consumer. A justified wrapper with no additional logic generally needs only a rendering/wiring test, with a short comment explaining the limited coverage when appropriate.
- Avoid testing default library behavior without application-specific logic or a contract that needs protection. Ordinary GET parameter forwarding does not automatically require a dedicated test; custom serialization and filtering behavior may warrant coverage according to project standards.
- Prefer real interaction behavior where practical, such as hovering to display a tooltip. Use stable selectors consistent with project conventions, including supported test IDs when useful; do not mock a component merely to obtain a test ID.
- Ensure test descriptions clearly state the behavior and condition being tested. Match source and test filenames and directory structure according to the repository's convention; update them when source files move.
- Treat missing required behavioral coverage as a review finding even if existing tests pass. Do not claim tests passed unless they were actually executed successfully.

## Quick Snippets

These examples illustrate review principles, not prescribed project APIs.

### Repeated computation

```ts
// Avoid
const soleFieldId = Object.keys(sortFields).length === 1
  ? Object.keys(sortFields)[0]
  : undefined;

// Prefer
const fieldIds = Object.keys(sortFields);
const soleFieldId = fieldIds.length === 1 ? fieldIds[0] : undefined;
```

### Complex inline action

```tsx
const handleReconnect = (account: Account) => {
  // Perform the required validation and state transitions.
};

<AccountCard onReconnect={handleReconnect} />
```

### Provider scope

```tsx
const SharedDeal = () => (
  <SharedClientProvider>
    <SharedDealContent />
  </SharedClientProvider>
);

const SharedDealContent = () => {
  const sharedClient = useSharedClientContext();
  // Consume the context here.
};
```

### Consolidating repeated hook calls (translation example)

```tsx
// Avoid
const { t } = useTranslation("sendup");
const { t: tCommon } = useTranslation("common");

t("active-text");
tCommon("displayed-text");

// Prefer
const { t } = useTranslation(["sendup", "common"]);

t("sendup:active-text");
t("common:displayed-text");
```

Use actual translation keys from the project; these keys are illustrative.

### Pending action protection

```tsx
<Button
  disabled={!onSendNow || !accountId || isSendingNow}
  onClick={handleSendNow}
/>
```

Verify the actual identifier fallback and pending-state scope before applying this pattern.

## Review Output Format

Present findings in descending severity. Group multiple occurrences of the same underlying issue when a single finding is clearer, but identify the affected locations.

For each finding, provide:

- **Severity:** Blocking, Major, Minor, or Nit.
- **Location:** Actual file path and line or line range when available.
- **Issue:** What is wrong, supported by the code or established project convention.
- **Impact:** The concrete failure, regression, maintenance cost, or convention violation.
- **Recommendation:** A focused correction, with a small example when useful.

Use Blocking for issues that make the change unsafe or unacceptable to merge, Major for substantial correctness or required-standard violations, Minor for smaller actionable issues, and Nit for low-impact cleanup. Calibrate severity to actual impact and team requirements.

Keep unresolved questions separate from confirmed findings. List pre-existing or out-of-scope issues separately as follow-up work, especially unfinished functionality. Report checks performed, their results, and any review limitations. If no actionable findings remain, say so; do not manufacture issues or claim merge readiness when required checks are unavailable.

## Resources

- Repository instructions and architecture conventions.
- Project testing documentation, especially `API_TESTING_STANDARDS` where present.
- Existing shared components, types, helpers, design tokens, translations, and representative tests.
- Companion `tanstack-query-integration` skill (or the repository's equivalent API integration skill), when available.
- Official React, TypeScript, TanStack Query, Axios, and relevant dependency documentation for the installed versions when behavior requires verification.
