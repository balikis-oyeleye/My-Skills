---
name: nextjs-app-router-review
description: Review Next.js App Router code for Server and Client Component boundaries, route UX, caching, revalidation, Server Actions, Route Handlers, Proxy, metadata, and redirects. Use for App Router reviews or as a companion to the code-review skill.
---

# Next.js App Router Review

Inspect `package.json`, `next.config.*`, and the route tree before applying version-sensitive rules. Apply Next.js 16-specific Cache Components and Proxy guidance only when the installed project uses those capabilities.

When used from `code-review`, return findings to that skill's output format. Review only unless implementation is explicitly requested.

## Server and Client Boundaries

- Pages and layouts are Server Components by default. Flag browser APIs, state, Effects, or event handlers in Server Components.
- Keep server-only data and secrets out of Client Components and serialized props.
- Avoid broad `"use client"` boundaries that expand the client bundle without an interactivity requirement.
- Do not recommend client hooks in Server Components or move an entire tree client-side to solve a narrow interaction.

## Route UX and Errors

- Render expected request and form errors explicitly.
- Use `notFound()` and `not-found.tsx` for absent route resources, and route or global error boundaries for uncaught exceptions.
- Add meaningful `loading.tsx` or close Suspense boundaries for streamed work when the route needs them.

## Cache Components and Revalidation

When `cacheComponents: true` is enabled:

- Keep uncached async or request-time work inside a close Suspense boundary.
- Use `'use cache'` only for cacheable work; read `cookies()`, `headers()`, and other request-time values outside cached scopes.
- Apply `cacheLife` and `cacheTag` when lifetime or invalidation matters.

Verify the intended semantics before recommending cache APIs:

- `updateTag()` provides immediate read-your-own-writes behavior in Server Actions.
- `revalidateTag(tag, "max")` provides stale-while-revalidate behavior.
- `revalidatePath()` invalidates a route path or layout, not every consumer of shared data.
- `refresh()` refreshes the client router but does not invalidate tagged data.

## Actions, Route Handlers, and Proxy

- Treat Server Functions and Route Handlers as public endpoints: authenticate, authorize, validate client-controlled input, and return appropriate status behavior.
- Show pending and expected-error states for Server Actions, invalidate before `redirect()`, and remember that `redirect()` uses framework control flow.
- Preserve progressive enhancement when a normal form action fits.
- In Next.js 16, use Proxy for request-time rewrites, redirects, and optimistic checks, not slow fetching or sole authorization enforcement.

## Metadata and Routing

- For public routes, verify route-aware title, description, canonical URL, Open Graph data, robots behavior, sitemap entries, and locale alternatives where relevant.
- Prefer server redirects for crawler-important routes and check locale redirects for loops.
