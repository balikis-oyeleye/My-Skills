---
name: api-integration
description: Implement, review, or improve TanStack Query integrations in React and TypeScript. Use when integrating queries or mutations, transforming API responses with select, refactoring deprecated response transformations, or handling loading, success, error, empty, refetching, and pagination states.
---

# TanStack Query Integration

## Overview

Build readable, type-safe query integrations that follow existing project conventions. Use explicit, commented JSX rendering states and preserve usable data during recoverable failures.

## Workflow

- Inspect the installed TanStack Query version, API hooks, response types, query keys, and similar components.
- For new integrations, identify applicable states and response transformations before implementing.
- For existing integrations, correct state handling and migrate response-only `useMemo` transformations in files being modified to `select`.
- Reuse existing skeletons, error and empty-state components, pagination hooks, translations, and notification utilities. Avoid unrelated changes, new dependencies, and unnecessary abstractions.
- Ask specific, grouped questions when API contracts, business logic, UI behavior, or architectural decisions remain unclear after inspection. Never invent requirements or fallback behavior. Wait for clarification on blocking decisions; distinguish recommendations from confirmed requirements.

## Core Guidelines

### Response transformation and normalization

- Use TanStack Query's `select` option for extracting, flattening, mapping, filtering, or normalizing query responses. This applies to `useQuery` and `useInfiniteQuery`.
- Under the team's convention, `useMemo` used solely to transform query response data is deprecated. Do not introduce it in new code; migrate existing instances when modifying their containing file. This does not prohibit `useMemo` for unrelated computations.
- Place shared transformations in the query hook or reusable query options; use consumer-specific selectors when different consumers require different shapes. Inspect existing consumers before changing a shared hook's return type.
- Prefer stable, typed selector functions defined outside components; use `useCallback` when a selector depends on reactive values. Selectors rerun when their reference or input data changes.
- Keep selectors pure and avoid mutating response data. Handle request failures and response validation in the query function, not in `select`.
- `select` transforms the observer's returned data, not the underlying cached query-function result. Cache updates must use the original cached shape. For infinite queries, retain `pages` and `pageParams` in cache updates and initial/placeholder data; selected output may expose a different shape when supported by the installed version and correctly typed.
- Normalize into IDs and entities only when useful. Do not silently deduplicate or change ordering; clarify duplicate handling when it affects business behavior.
- Do not turn malformed responses into empty results through arbitrary fallbacks.

### Rendering states

Use separate, descriptive JSX comments for applicable states. Prefer readable conditional rendering over nested ternaries. Match skeletons to the content layout and use stable item keys.

| State | Behavior |
| --- | --- |
| `isLoading` | Initial active fetch; render skeletons. |
| Usable data | Render content even if a later request fails. |
| `isLoadingError` | Show a blocking error and `refetch()` retry when no usable result exists. |
| Confirmed empty result | Show the appropriate empty or filtered-empty state, not a loading or error state. |
| `isRefetching` | Preserve content; optionally show a subtle refresh indicator. |
| `isRefetchError` | Preserve cached results and offer a non-blocking retry. |
| `isFetchingNextPage` | Append loading skeletons without replacing existing pages. |
| `isFetchNextPageError` | Preserve loaded pages and offer a `fetchNextPage()` retry. |
| Previous-page states | Apply equivalent behavior using `isFetchingPreviousPage`, `isFetchPreviousPageError`, and `fetchPreviousPage()` when supported. |

Never use `isError` alone to decide whether to replace the entire UI. A cached empty result can also coexist with a refetch failure; retain the appropriate empty-result presentation with a non-blocking error when required by the UX.

In v5, `isLoading` is `isPending && isFetching`. Disabled queries can be pending but idle; paused queries are not necessarily failures. Handle these states explicitly when relevant. Use `isPlaceholderData` to distinguish placeholder results from confirmed results, especially during filter or identifier changes. Respect configured retries and cancellation; do not introduce stale side effects.

### Infinite pagination

- Derive page parameters from the actual API contract and stop fetching when `hasNextPage` is false.
- Reuse the existing infinite-scroll hook. Guard automatic pagination against active fetches, placeholder data where relevant, and next-page errors. Do not create automatic retry loops.
- Keep manual retry separate from automatic pagination so a failed page can be retried deliberately. Prevent overlapping requests unless explicitly intended.
- Use the corresponding previous-page flags and controls for bidirectional pagination.

### Mutations

- Use `isPending` for mutation-specific loading; prevent unintended duplicate submissions and scope loading to the affected item when needed.
- Follow existing confirmation, notification, and error-handling conventions. Close confirmation dialogs after success unless optimistic behavior is explicitly intended.
- Update or invalidate the appropriate query keys using the original cache shape. Do not assume invalidation scope.
- Introduce optimistic updates only when required, with rollback or reconciliation.

## Quick Snippets

Examples assume TanStack Query v5 and illustrative API types and UI components. Adapt them to verified project contracts; do not invent components or response fields.

### Standard query selector

```tsx
const selectUsers = (response: UsersResponse) => response.data.users;

export const useUsers = () =>
  useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
    select: selectUsers
  });
```

### Infinite query selector

```tsx
const selectUsers = (data: InfiniteData<UsersPage>) => ({
  ...data,
  users: data.pages.flatMap((page) => page.items)
});

export const useInfiniteUsers = () =>
  useInfiniteQuery({
    queryKey: ["users", "infinite"],
    queryFn: ({ pageParam }) => fetchUsersPage(pageParam),
    initialPageParam: null as string | null,
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    select: selectUsers
  });
```

### Normalized infinite query selector

```tsx
const selectNormalizedUsers = (data: InfiniteData<UsersPage>) => {
  const entities: Record<string, User> = Object.create(null);
  const ids: string[] = [];

  for (const page of data.pages) {
    for (const user of page.items) {
      if (!Object.hasOwn(entities, user.id)) {
        ids.push(user.id);
      }
      entities[user.id] = user;
    }
  }

  return { ...data, ids, entities };
};
```

This example preserves first-seen ID order and uses the last encountered entity for duplicate IDs. Use that policy only when appropriate. Use `select: selectNormalizedUsers` instead of the flattening selector when normalization is required.

### Infinite query rendering and retry

```tsx
const query = useInfiniteUsers();
const users = query.data?.users ?? [];
const canLoadMore = query.hasNextPage && !query.isFetching &&
  !query.isPaused && !query.isPlaceholderData;

const handleLoadMore = () => {
  if (canLoadMore && !query.isFetchNextPageError) {
    void query.fetchNextPage({ cancelRefetch: false });
  }
};

const { loadMoreRef } = useInfiniteScroll({
  hasNextPage: canLoadMore && !query.isFetchNextPageError,
  isFetchingNextPage: query.isFetchingNextPage,
  fetchNextPage: handleLoadMore
});

return (
  <>
    {/* Initial loading state */}
    {query.isLoading && <UserSkeleton />}

    {/* Success state */}
    {users.map((user) => <UserItem key={user.id} {...user} />)}

    {/* Loading more items */}
    {query.isFetchingNextPage && <UserSkeleton />}

    {/* Infinite scroll trigger */}
    {canLoadMore && !query.isFetchNextPageError && (
      <div ref={loadMoreRef} />
    )}

    {/* Next-page error state */}
    {query.isFetchNextPageError && !query.isFetching && (
      <InlineError onRetry={() => {
        if (canLoadMore) void query.fetchNextPage({ cancelRefetch: false });
      }} />
    )}

    {/* Background refetch error state */}
    {query.isRefetchError && !query.isFetching && (
      <InlineError onRetry={() => void query.refetch()} />
    )}

    {/* Initial error state */}
    {query.isLoadingError && !query.isFetching && (
      <ErrorState onRetry={() => void query.refetch()} />
    )}

    {/* Empty state */}
    {query.data !== undefined && !query.isPlaceholderData &&
      users.length === 0 && !query.hasNextPage && <EmptyState />}
  </>
);
```

For standard queries, use the same rendering principles without pagination-specific sections. Add disabled, paused, or refresh indicators when the requirements call for them. Verify the custom infinite-scroll hook's contract before using the example.

## Validation Checklist

- Verify response types, selector stability, and compatibility with existing consumers and cache updates.
- Exercise initial loading, success, confirmed empty results, initial failure, and retry.
- Exercise cached data and cached empty results during refetch failures, plus pagination failures and manual retry where applicable.
- Verify disabled, paused, placeholder, and parameter-change behavior when relevant.
- Check mutation behavior, localization, accessible loading and retry controls, and meaningful JSX comments.
- Run relevant tests, linting, and TypeScript checks. Report unverified behavior, blockers, and unresolved decisions.

## Resources

Consult official documentation for the installed version:

- [useQuery](https://tanstack.com/query/latest/docs/framework/react/reference/useQuery)
- [useInfiniteQuery](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery)
- [Render optimizations and select](https://tanstack.com/query/latest/docs/framework/react/guides/render-optimizations)
- [Infinite queries](https://tanstack.com/query/latest/docs/framework/react/guides/infinite-queries)
- [Mutations](https://tanstack.com/query/latest/docs/framework/react/guides/mutations)
- [Query cancellation](https://tanstack.com/query/latest/docs/framework/react/guides/query-cancellation)
