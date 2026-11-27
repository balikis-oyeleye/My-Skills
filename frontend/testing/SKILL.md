---
name: testing
description: Review frontend tests and behavioral coverage for React and TypeScript changes, including user interactions, regressions, routing, async states, and repository test conventions. Use for targeted test reviews or as a companion to the code-review skill.
---

# Frontend Testing Review

Review whether tests protect the changed production behavior and follow the repository's established test utilities and standards. Passing tests do not compensate for missing coverage of a required behavior.

When used from `code-review`, return findings to that skill's severity and output format. Review only unless implementation is explicitly requested.

## Establish the Standard

- Inspect repository test instructions and `API_TESTING_STANDARDS` when present before requiring a particular test shape.
- Reuse shared renderers, providers, fixtures, mock helpers, and stable selectors instead of creating local substitutes.
- Match source and test filenames and directory structure according to repository convention, including after file moves.

## Meaningful Coverage

- Test observable user behavior, not implementation details or only whether a mocked function was called.
- Exercise relevant interactions and assert the resulting UI, navigation, cache, request, or state outcome at the appropriate boundary.
- Require regression coverage when routing changes affect browser history, query-driven tabs, deep links, or scrolling.
- Consider loading, confirmed empty, disabled, pending, initial error, background error, pagination error, retry, success, and confirmation states only where relevant to the changed behavior.
- Preserve usable cached content during recoverable refetch or pagination errors when that is part of the production contract.
- Do not demand tests of default library behavior unless application-specific logic or a contract needs protection.

## Test Quality

- Prefer realistic user interaction utilities and stable project-supported selectors. Do not mock a component only to obtain a test ID.
- Keep test descriptions specific about the behavior and condition.
- Avoid duplicating a shared component's complete test suite in each consumer. A wrapper with no additional behavior usually needs only focused rendering or wiring coverage.
- Verify hoisted module mocks follow the test runner's initialization rules and are cleared between tests.
- Ensure query-backed tests use the repository's shared query provider and expose or observe the cache through established utilities.

## Reporting

Treat missing required behavioral coverage as a finding even when existing tests pass. State which checks actually ran and distinguish execution failures from environment limitations. Never claim a test passed unless it completed successfully.
