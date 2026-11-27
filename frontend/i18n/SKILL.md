---
name: i18n
description: Review frontend internationalization, locale key integrity, namespaces, fallbacks, interpolation, plurals, locale routing, and localized SEO metadata. Use for targeted i18n reviews or as a companion to the code-review skill.
---

# Frontend Internationalization Review

Verify that translated UI remains complete and that fallback behavior does not hide missing locale work. Follow the repository's localization library and namespace conventions.

When used from `code-review`, return findings to that skill's output format. Review only unless implementation is explicitly requested.

## Translation Integrity

- Compare locale key sets and namespace structure for the changed feature.
- Check missing interpolation values, plural forms, variable forms, and fallback behavior.
- Report fallback text when the product is expected to ship the reviewed locale; do not treat a deliberate fallback strategy as a defect without evidence.
- Route user-facing labels, statuses, errors, empty states, and action text through established translation conventions.
- When multiple i18next namespaces use the same options, prefer one hook call with explicit namespace-prefixed keys. Preserve separate calls when their configurations differ.

## Routing and Localized SEO

- Ensure unsupported locale routes do not silently render fallback content unless that is the documented strategy.
- Verify canonical URLs and language alternatives point to equivalent pages in the current locale set.
- Check `x-default` against the project's default-locale strategy and prevent locale redirect loops.
- Keep sitemap URLs, canonical URLs, Open Graph locale values, and language alternatives consistent when applicable.
