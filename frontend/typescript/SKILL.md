---
name: typescript
description: Review frontend TypeScript modeling, narrowing, nullability, assertions, generics, exported contracts, schema alignment, and compiler-option claims. Use for targeted TypeScript reviews or as a companion to the code-review skill.
---

# Frontend TypeScript Review

Use types to make invalid states harder to represent, not merely to silence errors. Base findings on the actual API, runtime boundary, compiler configuration, and call sites.

When used from `code-review`, return findings to that skill's output format. Review only unless implementation is explicitly requested.

## Contracts and Boundaries

- Reuse compatible contracts before introducing duplicates. Put reusable API types in the repository's established type module.
- Type HTTP responses at the request boundary rather than asserting the shape at the return statement. Validate `unknown` external data when runtime trust is not justified.
- Model optional and nullable values accurately and verify the UI handles their absence.
- Keep runtime validation schemas and form types aligned, deriving types from schemas when the project convention supports it.
- Preserve API-defined and serialized discriminants unless compatibility and migration are addressed.

## Narrowing and State Modeling

- Avoid unnecessary `any` and unsafe assertions. Narrow unknown errors before reading their properties.
- Use discriminated unions when distinct modes would otherwise permit impossible boolean combinations.
- Check exhaustiveness with `never` when an omitted variant would break behavior.
- Use `satisfies` when an object must conform to a contract without losing useful narrow inference.
- Name variants, flags, and predicates for their specific domain meaning.

## Props, Utilities, and Generics

- Give component props explicit contracts and preserve useful callback parameter types.
- Use generics only when behavior is genuinely reusable across types, and preserve the generic relationship in the return type.
- Prefer explicit return types for exported utilities when they form part of the module's public API.
- Derive related types with `Pick`, `Omit`, `Partial`, or similar utilities only when their semantics match.

## Compiler Context

Inspect `tsconfig.json` before claiming the compiler catches an issue. Treat enabling `strict`, `noUncheckedIndexedAccess`, or `exactOptionalPropertyTypes` as context-dependent improvements rather than automatic blockers.
