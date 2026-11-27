---
name: security
description: Review frontend and Next.js security boundaries, including unsafe HTML and URL sinks, authorization, secret exposure, CSRF, SSR serialization, and Content Security Policy. Use for targeted security reviews or as a companion to the code-review skill.
---

# Frontend Security Review

Report security findings only when a concrete trust boundary, data flow, or unsafe sink supports the risk. Treat client-side visibility as UX, never as authorization enforcement.

When used from `code-review`, return findings to that skill's output format. Review only unless implementation is explicitly requested.

## XSS and Unsafe Sinks

- React escapes text by default; scrutinize `dangerouslySetInnerHTML`, DOM HTML assignment APIs, `document.write`, and untrusted executable URL schemes.
- When authored HTML is required, use a maintained sanitizer before the final unsafe sink and do not mutate sanitized content afterward.
- Trace user-controlled content through libraries and transformations rather than assuming earlier sanitization remains effective.

## Server Boundaries and Secrets

- Treat Server Actions, Server Functions, and Route Handlers as public endpoints. Verify authentication, authorization, and validation server-side.
- Do not accept hidden controls, client route guards, or disabled buttons as enforcement.
- Expose only required data to Client Components and keep secrets out of public environment variables and client bundles.

## Cookies, Serialization, and CSP

- For cookie-authenticated mutations, inspect the project's CSRF strategy. Do not assume CORS, a framework, or a client wrapper provides complete protection.
- Ensure custom SSR or dehydrated-state serialization safely escapes untrusted data before embedding it in HTML; raw `JSON.stringify` is not sufficient by itself.
- Treat Content Security Policy as defense in depth, not a replacement for escaping, sanitization, authorization, or validation. Inspect nonce and third-party script handling when CSP is present.
