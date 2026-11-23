---
name: tanstack-query-integration
description: Implement, review, or improve TanStack Query integrations in React and TypeScript applications. Use when asked to integrate useQuery, useInfiniteQuery, or useMutation into components, handle loading, success, error, empty, and pagination states, or review existing query integrations for correctness, edge cases, and consistency with project conventions.
---

# TanStack Query Integration

## Overview

Use this skill to implement or review TanStack Query integrations in React and TypeScript applications.

Prioritize correct query state handling, readable conditional rendering, reusable UI components, and consistency with the existing codebase.

Every applicable UI state should be rendered dynamically with a descriptive JSX comment.

Always inspect the existing implementation before making changes. **Ask questions when requirements are unclear. Never assume API behavior, business logic, or UI requirements.**

Verify the installed TanStack Query version and consult the corresponding official documentation when necessary.

## Core Guidelines

### Inspect before implementing

- Verify the installed TanStack Query version.
- Inspect existing API hooks and response types.
- Reuse existing components, utilities, and custom hooks.
- Follow established naming, styling, and localization conventions.
- Avoid unnecessary dependencies or abstractions.

### Ask questions — never assume

- Ask when API contracts, business logic, UI behavior, or architectural decisions are unclear.
- Inspect the codebase before asking questions.
- Group related questions to avoid unnecessary back-and-forth.
- Do not ask about information already established by the codebase.
- Do not invent fallback behavior or missing requirements.
- Wait for clarification when a critical decision remains unresolved.
- Distinguish recommendations from confirmed requirements.

### Organize rendering states

Use descriptive JSX comments for each applicable state.

Preferred structure:

```tsx
return (
  <>
    {/* Initial loading state */}

    {/* Success state */}

    {/* Loading more items */}

    {/* Next-page error state */}

    {/* Background refetch error state */}

    {/* Initial error state */}

    {/* Empty state */}
  </>
);
```

- Keep rendering conditions explicit and readable.
- Avoid unnecessary nested ternaries.
- Include only states relevant to the query.
- Adjust rendering order to match the component layout.
- Do not create unnecessary components solely to separate states.

### Handle loading correctly

- Use `isLoading` for an active initial fetch.
- Use `isPending` when handling queries that have not produced a result but may not be actively fetching.
- Reuse existing skeleton components.
- Match skeleton layouts to actual content.
- Do not display initial skeletons during background refetching.
- Consider disabled and paused queries when applicable.

### Preserve successful data

- Render usable data even when a subsequent request fails.
- Do not rely exclusively on `isSuccess` when displaying cached data.
- Use stable keys for mapped elements.
- Follow existing response transformation conventions.
- Use `useMemo` only when justified.

### Handle errors correctly

- Use `isLoadingError` to identify initial query failures.
- Use `isRefetchError` for background refetch failures.
- Use `isFetchNextPageError` for next-page failures.
- Use `isFetchPreviousPageError` for previous-page failures when applicable.
- Never use `isError` alone to decide whether to display a full-page error.
- Preserve usable data during recoverable failures.
- Provide appropriate retry actions.
- Reuse existing error components and error-handling utilities.

### Handle empty states correctly

- Display empty states only when a successful result is genuinely empty.
- Do not interpret missing data during loading as an empty result.
- Do not show an empty state after a failed initial request.
- Account for filtered or searched results when applicable.
- Reuse existing empty-state components.

### Handle infinite queries correctly

- Flatten pages according to the actual API response structure.
- Keep previously loaded pages visible while fetching additional pages.
- Use `isFetchingNextPage` for next-page loading.
- Use `isFetchNextPageError` for pagination errors.
- Check `hasNextPage` before fetching.
- Avoid unintended overlapping requests.
- Prevent intersection observers from repeatedly retrying failed pages.
- Stop pagination when no additional pages exist.
- Handle previous-page states when bidirectional pagination is supported.

### Handle mutations correctly

- Use `isPending` for mutation loading indicators.
- Prevent unintended duplicate submissions.
- Follow existing success and error notification conventions.
- Update or invalidate relevant queries after successful mutations.
- Preserve confirmation modal behavior.
- Do not introduce optimistic updates without clear requirements.
- Implement rollback or reconciliation for optimistic updates when applicable.

## Review Checklist

- **Version:** Installed TanStack Query version verified.
- **Architecture:** Existing hooks, types, and components reused.
- **Clarification:** Ambiguous requirements investigated and clarified.
- **Loading:** Initial loading handled without blocking background refetches.
- **Success:** Usable data rendered and preserved appropriately.
- **Error:** Initial, refetch, and pagination errors distinguished.
- **Empty:** Empty states shown only for confirmed empty results.
- **Pagination:** Next-page loading, errors, retries, and completion handled.
- **Concurrency:** Unintended overlapping fetches prevented.
- **Mutations:** Pending, success, error, and cache updates handled.
- **Comments:** Meaningful UI states identified with JSX comments.
- **Accessibility:** Loading indicators and retry actions accessible.
- **Localization:** User-facing messages follow existing translation conventions.
- **Code quality:** No unnecessary dependencies, abstractions, or unrelated changes.

## Implementation Checklist

- Inspect the relevant codebase files.
- Verify the installed TanStack Query version.
- Identify the query type and response structure.
- Identify all applicable UI states.
- Ask questions about unresolved requirements.
- Reuse existing skeletons, error components, and empty-state components.
- Implement initial loading.
- Implement success rendering.
- Implement initial error handling.
- Implement empty-state handling.
- Preserve cached data during background refetching.
- Handle refetch errors when applicable.
- Implement pagination loading and error handling for infinite queries.
- Prevent automatic pagination retry loops.
- Implement mutation states and cache updates when applicable.
- Add descriptive JSX comments.
- Verify TypeScript types and rendering conditions.
- Run relevant linting, type checks, and tests.

## Quick Snippets

Use these patterns as references. Adapt response types, component names, error messages, and rendering conditions to the existing project.

### 1) Standard query integration

```tsx
const { data, isLoading, isLoadingError, isRefetchError, isSuccess, refetch } =
  useGetItems();

const items = data?.data?.items ?? [];

return (
  <>
    {/* Initial loading state */}
    {isLoading && <ItemSkeleton />}

    {/* Success state */}
    {items.length > 0 &&
      items.map((item) => <Item key={item.id} {...item} />)}

    {/* Background refetch error state */}
    {isRefetchError && (
      <InlineError onRetry={() => void refetch()} />
    )}

    {/* Initial error state */}
    {isLoadingError && !data && (
      <ErrorState onRetry={() => void refetch()} />
    )}

    {/* Empty state */}
    {isSuccess && items.length === 0 && <EmptyState />}
  </>
);
```

### 2) Infinite query data extraction

```tsx
const items = useMemo(
  () =>
    infiniteQuery.data?.pages.flatMap(
      (page) => page.data.data.items ?? []
    ) ?? [],
  [infiniteQuery.data]
);
```

### 3) Infinite scroll integration

```tsx
const { loadMoreRef } = useInfiniteScroll({
  hasNextPage: infiniteQuery.hasNextPage,
  isFetchingNextPage: infiniteQuery.isFetchingNextPage,
  fetchNextPage: () => {
    if (
      !infiniteQuery.hasNextPage ||
      infiniteQuery.isFetching
    ) {
      return;
    }

    void infiniteQuery.fetchNextPage();
  }
});
```

Ensure the existing infinite-scroll hook does not automatically retry failed pages indefinitely.

### 4) Infinite query rendering

```tsx
return (
  <>
    {/* Initial loading state */}
    {infiniteQuery.isLoading && <ItemSkeleton />}

    {/* Success state */}
    {items.length > 0 &&
      items.map((item) => <Item key={item.id} {...item} />)}

    {/* Loading more items */}
    {infiniteQuery.isFetchingNextPage && <ItemSkeleton />}

    {/* Next-page error state */}
    {infiniteQuery.isFetchNextPageError && (
      <InlineError
        message="Unable to load more items."
        onRetry={() => void infiniteQuery.fetchNextPage()}
      />
    )}

    {/* Background refetch error state */}
    {infiniteQuery.isRefetchError && (
      <InlineError
        onRetry={() => void infiniteQuery.refetch()}
      />
    )}

    {/* Initial error state */}
    {infiniteQuery.isLoadingError && items.length === 0 && (
      <ErrorState
        onRetry={() => void infiniteQuery.refetch()}
      />
    )}

    {/* Empty state */}
    {infiniteQuery.isSuccess && items.length === 0 && (
      <EmptyState />
    )}
  </>
);
```

### 5) Mutation integration

```tsx
const { mutate: deleteItem, isPending: isDeleting } = useDeleteItem();

const handleDelete = (id: string) => {
  deleteItem(
    { id },
    {
      onSuccess: () => {
        // Follow existing success and cache-update conventions.
      },
      onError: () => {
        // Follow existing error-handling conventions.
      }
    }
  );
};
```

## Important Edge Cases

### Cached data with an error

A query may have cached data while reporting an error after a failed refetch.

Preserve usable content and display an appropriate non-blocking error.

### Infinite query next-page failure

A failed next-page request may set `isError` while previous pages remain available.

Use `isFetchNextPageError` to handle the pagination failure independently.

### Background refetching

A query may be fetching while displaying valid cached data.

Do not replace existing content with initial loading skeletons.

### Disabled queries

A disabled query may be pending without fetching.

Do not assume `isPending` always means an active request.

### Placeholder data

Placeholder data may be displayed while the actual query result is loading.

Use `isPlaceholderData` when the distinction matters.

### Automatic retries

TanStack Query may automatically retry failed requests.

Respect the configured retry behavior and avoid uncontrolled retry loops.

### Query parameter changes

Filters, search terms, or identifiers may change while previous results remain visible.

Follow the established placeholder-data and caching strategy.

### Query cancellation

Respect the existing query function's abort-signal handling and avoid introducing stale side effects.

## Resources

- Overview: https://tanstack.com/query/latest/docs/framework/react/overview
- Queries: https://tanstack.com/query/latest/docs/framework/react/guides/queries
- `useQuery`: https://tanstack.com/query/latest/docs/framework/react/reference/useQuery
- Infinite queries: https://tanstack.com/query/latest/docs/framework/react/guides/infinite-queries
- `useInfiniteQuery`: https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery
- Mutations: https://tanstack.com/query/latest/docs/framework/react/guides/mutations
- Query retries: https://tanstack.com/query/latest/docs/framework/react/guides/query-retries
- Query cancellation: https://tanstack.com/query/latest/docs/framework/react/guides/query-cancellation
- Placeholder data: https://tanstack.com/query/latest/docs/framework/react/guides/placeholder-query-data

Prefer official TanStack Query documentation for version-specific API behavior.

**Primary principle:** Implement every applicable query state correctly, preserve usable data during recoverable failures, follow existing project conventions, and ask questions rather than making assumptions.
