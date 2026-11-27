---
name: react-correctness-review
description: Review React runtime correctness, hooks, context, state synchronization, routing, browser history, asynchronous actions, real-time updates, functional completeness, and performance. Use for targeted React correctness reviews or as a companion to the code-review skill.
---

# React Correctness Review

Trace runtime behavior through components, hooks, providers, stores, routes, asynchronous actions, and caches. Confirm a concrete failure path or regression before reporting a bug.

When used from `code-review`, return findings to that skill's severity and output format. Review only unless implementation is explicitly requested.

## Hooks, Context, and State

- Verify context consumers render below the intended provider. A hook called in the component creating a provider cannot consume that provider's value.
- Inspect effect dependencies, stale closures, cleanup, and repeated execution. Replace unstable dependencies with stable primitives only when those primitives represent every relevant change.
- After refactors, trace state and context consumers before retaining or deleting state, setters, and reset logic. A setter used only by reset code does not prove that the state remains useful.
- Avoid unnecessary global state or mutation for page-local lifecycle behavior.
- Verify derived state cannot drift from its source of truth and that resets cover identifier, modal, route, and account changes where applicable.
- Remember that state is tied to component position, type, and `key`; inspect conditional trees and key changes for unexpected resets or stale forms.
- Keep event-specific logic in event handlers or mutation callbacks. Use Effects to synchronize with external systems rather than to derive render data.
- Check hook call order and reject hooks invoked conditionally, in loops, or inside nested functions.

## Hydration, Forms, and External Stores

- Treat server and client markup mismatches as bugs. Check browser-only reads during initial render, timestamps, randomness, locale-sensitive formatting, invalid nesting, and differing initial auth or theme markup.
- Use `suppressHydrationWarning` only for unavoidable one-level differences, not to hide structural mismatches.
- Ensure controlled inputs update synchronously and do not switch between controlled and uncontrolled modes. Checkboxes and radios use `checked` for selected state.
- Preserve user input through pending, validation, and failure states.
- Use transitions or deferred values only for expensive non-urgent rendering; they do not debounce requests.
- For external subscriptions, prefer `useSyncExternalStore` with stable snapshots and an SSR snapshot when server rendering requires one.

## Asynchronous and Synchronized Behavior

- Prevent duplicate submissions and repeated consequential actions while requests are pending. Ensure the pending state reaches every action surface that can trigger the operation.
- Keep callbacks synchronous when they do not await completion; preserve promise-returning behavior when callers depend on settlement.
- Trace mutations and real-time events through relevant caches, counters, stores, tabs, reconnects, and concurrent operations.
- Avoid redundant invalidation only when an established synchronization mechanism guarantees delivery and updates all affected query keys.
- Reuse coordinated authentication and token-refresh mechanisms so concurrent consumers share refresh work.
- For long-lived streams, inspect connection-specific state, memory growth, chunk decoding, event parsing, cancellation, retry, reconnection, and event replay.
- Check cleanup for subscriptions, listeners, timers, observers, and abortable work. When supported by the installed React version, an Effect Event may read the latest value without reconnecting an external system.

## Routing and Browser Behavior

- When state moves between pathnames and search parameters, verify `push` versus `replace`, deep links, back and forward navigation, and preserved prior behavior.
- Inspect effects, route patterns, tests, and scroll logic that still depend on the old pathname or navigation model.
- Distinguish page navigation, which commonly belongs in browser history, from sheet- or modal-local state that may not.

## Functional Completeness

- Trace buttons, menus, forms, and links to working handlers and intended data sources.
- Flag no-op callbacks, placeholder recipients, dummy production data, unfinished workflows, and actions missing required identifiers.
- Verify required indicators, validation, defaults, resets, multi-step state retention, modal transitions, visibility, navigation, and scrolling.
- Check missing data, slow requests, failure paths, repeated interactions, and transitions between pending, success, and failure.
- Preserve established behavior unless an approved requirement changes it. Separate regressions from pre-existing gaps.

## Performance

- Lazy-load suitable on-demand UI according to existing Suspense and error patterns, but do not defer essential initial UI without evidence.
- Look for actual repeated work, excess subscriptions, unstable dependencies, unnecessary renders, or unbounded memory growth before recommending optimization.
- Do not recommend memoization speculatively.
