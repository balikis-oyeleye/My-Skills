---
name: pr-description-generator
description: Generate concise GitHub pull request descriptions using the user's fixed template. Use when asked to create, write, or generate a PR description from supplied details or repository evidence.
---

# PR Description Generator

Write a concise professional PR description from supported information: what changed, why, issue references, change types, dependencies, screenshots or videos, and reviewer context.

## Evidence Rules

- Support every factual statement with information the user supplied or repository evidence. Never invent issues, related PRs, media, implementation details, motivations, or test results.
- Inspect the actual branch history and diff against the feature's correct base when repository evidence is requested or needed.
- Explain what changed, the problem solved, and motivation when known.
- Record consequential implementation or architecture decisions and their rationale.
- When claiming consistency with the codebase, name the exact verified page, component, hook, feature, or file and explain the behavior being aligned.
- Describe state and navigation precisely, distinguishing route parameters from query parameters.
- Name API integrations by exact HTTP method and endpoint, preserving route parameters such as `GET /resource/:id`.
- When known, explain how the response is transformed, whether it is a source of truth, and which UI behavior it drives.
- Mention deliberate scope differences, resilience behavior, optimistic updates, rollback, retained data, empty-filter behavior, read-only behavior, and failure handling when supported by evidence.

## Content Rules

- Keep `Changes Made` concrete and ordered from integration or data flow to user-visible behavior. Avoid low-value file-by-file narration.
- Check every applicable change type. If the type cannot be determined, leave all options unchecked.
- Preserve supplied issue and PR identifiers exactly. Use `N/A` when Issues, Related PRs, Screenshots/Videos, or Additional Notes have no supplied information.
- Do not delay generation to collect optional information. Leave other unknown optional content empty or retain its instruction where appropriate.

## Output Contract

Return only raw GitHub-flavored Markdown without an introduction, surrounding fence, or closing explanation. Preserve every heading, level, label, and order in this template. Remove unused placeholder bullets and keep blank lines after headings and before lists.

```markdown
## Description

Please include a summary of the change and which issue is fixed. Please also include relevant motivation and context.

## Issues

Related issue numbers:

- #
- #

## Type of Change

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Performance improvement
- [ ] Refactoring (no functional changes)
- [ ] Documentation update
- [ ] UI/UX improvement

## Changes Made

-
-
-

## Screenshots/Videos

If applicable, add screenshots or videos to help explain your changes.

### Related PRs

If this PR depends on other PRs, please list them here:

-
-

## Additional Notes

Any additional information that reviewers should know:
```

The blank bullets and issue markers are template slots, not finished content. Fill or remove them according to the rules above.

## Final Check

Verify that every factual statement is supported, consequential decisions and rationale are included, consistency claims name a verified precedent, route and query parameters are described accurately, applicable change types are checked, no identifiers are invented, all headings remain unchanged, and the result is concise.
