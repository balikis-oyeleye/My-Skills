# Frontend Review Routing Checklist

Use this only for broad reviews to decide which companion skills apply. The companion skills own the detailed rules.

- **Correctness:** Runtime failures, null access, stale state or closures, hook rules, races, duplicate actions, cleanup, form behavior, dates, route state, environment variables, hydration, and unexpected state resets.
- **Architecture:** Ownership, colocation, reuse, abstractions, dead code, dependencies, imports, exports, and public boundaries.
- **TypeScript:** `any`, unsafe assertions, nullability, narrowing, duplicated contracts, generics, exported return types, discriminated unions, and indexed access.
- **Next.js:** Server and Client boundaries, route UX, metadata, caching, revalidation, Server Actions, Route Handlers, Proxy, environment placement, and locale routing.
- **API and server state:** Query keys, `enabled`, freshness, retries, transformations, mutation state, invalidation, optimistic rollback, cancellation, token refresh, waterfalls, pagination, and SSR dehydration.
- **Security:** Unsafe HTML or URL sinks, client-only authorization, secret exposure, CSRF, unsafe serialization, and CSP assumptions.
- **UI and accessibility:** Hierarchy, action clarity, responsive overflow, loading, empty and error recovery, native semantics, accessible names, focus, dialogs, keyboard behavior, touch targets, and reduced motion.
- **Performance and SEO:** Client bundle size, render work, network waterfalls, large lists, images, LCP, INP, CLS, metadata, canonical URLs, robots, locale alternatives, Open Graph, sitemaps, and redirects.
- **i18n:** Locale key parity, namespaces, fallbacks, interpolation, plurals, localized routing, canonical URLs, and redirect loops.
- **Testing:** Observable user behavior, changed-state coverage, repository utilities, stable selectors, route/history regression coverage, query cache behavior, and environment limitations.

For a scoped review, skip this checklist and load only the companions implicated by the changed code.
