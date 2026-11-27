# Code Review Output Format

Use the format that matches the review type unless the user requests another format. If the user asks for findings only, omit summaries, patches, and final recommendations.

## PR and Branch-Diff Reviews

Mirror the changed-file view. List every reviewed changed file in diff order as a clickable heading, followed by its findings. Write `No issue found.` when a reviewed changed file has no finding.

Keep a cross-file issue under the changed file that introduces or activates it, and link adjacent evidence where useful. Do not repeat the same root issue under several files.

```md
### [`src/path/file.tsx`](/absolute/path/src/path/file.tsx:42)

#### Major: Clear issue title

**Issue:** What is wrong and the evidence supporting it.

**Impact:** The production, user, security, accessibility, or maintenance consequence.

**Recommendation:** The smallest focused correction.

### [`src/path/unchanged-result.ts`](/absolute/path/src/path/unchanged-result.ts:1)

No issue found.
```

Use Blocking for a change that is unsafe to merge, Major for substantial correctness or required-standard violations, Minor for smaller actionable problems, and Nit for low-impact cleanup. Calibrate severity to demonstrated impact.

## Broad Audits and Snippet Reviews

Group findings in descending severity. Each finding must identify its location, issue, impact, and focused recommendation. For repository reviews, use clickable file and line links. For snippets, reference the component, function, or expression.

For an isolated snippet, separate verified findings from `## Context-dependent considerations`. Conditional concerns must not affect the merge recommendation.

## Summary and Recommendation

When the user requests production readiness or merge advice, end with a short judgment and exactly one of:

- `Safe to merge.`
- `Safe after fixes.`
- `Not safe to merge yet.`
- `Safe to merge based on the supplied snippet.`

Do not claim merge readiness when required checks could not run. Provide a focused patch only when useful and requested by the review context; do not rewrite unrelated code.

## Tone

Be direct and evidence-based. Describe the failure condition rather than criticizing the author. Keep unresolved questions and pre-existing issues separate from confirmed findings introduced by the change.
