---
name: performance-seo
description: Review frontend performance and SEO, including bundles, rendering, network waterfalls, Core Web Vitals, images, metadata, canonicals, indexing, redirects, and Open Graph data. Use for targeted audits or as a companion to the code-review skill.
---

# Frontend Performance and SEO Review

Treat performance as user experience and SEO as route-specific product behavior. Report measurable or code-supported risks rather than speculative micro-optimizations.

When used from `code-review`, return findings to that skill's output format. Review only unless implementation is explicitly requested.

## Performance

- Look for unnecessary client boundaries, heavy dependencies in broad client components, expensive render work, duplicate requests, serial waterfalls, over-fetching, and overly broad invalidation.
- Use pagination, infinite loading, or virtualization when verified collection size and interaction requirements justify it.
- Investigate LCP delays, long interaction handlers affecting INP, and layout movement affecting CLS.
- Ensure important above-the-fold images are discoverable early, not lazily loaded, and use suitable dimensions, responsive sizing, and selective fetch priority.
- Keep loading fallbacks structurally stable to avoid layout shift.
- Recommend memoization or deferral only when semantics, profiling, or demonstrated repeated work justify it.

## Metadata and Indexing

- For public pages, verify route-specific title, description, canonical URL, Open Graph title, description and image, robots behavior, and structured data when relevant.
- Avoid broad canonical URLs in shared layouts that make child routes appear to duplicate a parent.
- Private account, dashboard, admin, messaging, verification, and similar routes should normally be `noindex`; public routes must not inherit it accidentally.
- Prefer server-side redirects for crawler-important routes so no-JavaScript clients and crawlers do not receive a spinner-only page.

## Localized and Shareable Pages

- Keep sitemap URLs, canonicals, and language alternatives consistent.
- Canonicals should identify the current locale equivalent; language alternatives should map equivalent localized pages.
- Unsupported locales must not silently emit fallback content under an indexable locale URL without an intentional strategy.
- Public shareable pages should expose a stable URL, title, description, image, and locale when relevant. Do not generate Open Graph images on the client.
