---
name: ui
description: Review frontend UI consistency, accessibility, responsive behavior, design-system usage, Tailwind and CSS tokens, interaction feedback, confirmation flows, and visible behavior. Use for targeted UI reviews or as a companion to the code-review skill.
---

# Frontend UI Review

Review visible frontend behavior against product requirements, the repository design system, and established examples of the same interaction. Distinguish demonstrable inconsistencies from personal design preference.

When used from `code-review`, return findings to that skill's severity and output format. Review only unless implementation is explicitly requested.

## Design System

- Inspect Tailwind configuration, CSS variables, global styles, and component variants before accepting hardcoded colors, spacing, typography, or invented token names.
- Compare new UI with established examples of the same pattern, especially modal headers, action buttons, borders, spacing, icons, terminology, empty states, and loading states.
- Reuse an existing compatible component rather than rebuilding its interaction and styling.
- Keep overflow ownership explicit. For virtual or infinite lists, the element observed by the list logic must be the actual scrolling container.
- Ensure bounded modal empty and error states fit their container; confine scrolling to content that can grow.
- On dense data pages, verify search and filter discoverability, useful sticky controls, sorting, pagination or infinite loading, row behavior, bulk actions, and destructive-action clarity.
- Recommend charts only when users need to compare, see trends, track progress, or detect anomalies; do not add charts as decoration.

## State Communication

- Distinguish initial empty collections, filtered or searched empty results, and request failures. Empty copy should name the active subset and offer the next useful action when one exists.
- Verify default tabs and filters match requirements; initial state, URL state, query parameters, selected styling, accessible state, counts, labels, and actions must agree.
- Preserve user input during recoverable errors and provide a human-readable message and retry or recovery action.
- Avoid full-page spinners when only one section is loading; match skeletons to the content being replaced.

## Interaction and Feedback

- Preserve confirmation for consequential actions such as deletion, ownership transfer, or access changes unless requirements explicitly remove it.
- Ensure success and error feedback is appropriate to the consequence and context. Avoid redundant success toasts when the interface already makes the outcome clear and the project supports suppressing them.
- Use semantic interactive elements and ensure pending or disabled behavior is visible and prevents repeated actions.
- Verify loading, empty, filtered-empty, error, retry, success, and pagination states relevant to the changed interface.
- Check that required controls remain reachable and that scroll behavior does not hide actions or produce blank space.
- Verify mobile, tablet, and desktop behavior, including horizontal overflow, sticky regions, sheets and dialogs, wrapping, touch targets, and table alternatives.
- Check hover, focus-visible, active, disabled, selected, dark-mode, and reduced-motion states when relevant.

## Accessibility

- Prefer native interactive elements. ARIA does not add keyboard behavior or semantics automatically.
- Give icon-only controls an accessible action name, hide decorative icons, and add a keyboard-accessible tooltip when sighted users need help discovering the meaning.
- Ensure visible focus, keyboard activation, suitable touch targets, and announced pending or disabled states.
- For dialogs, verify focus entry, trapping while modal, Escape behavior, a visible close or cancel control, title and description association, and focus restoration.
- For menus, comboboxes, tabs, grids, and custom controls, verify the expected keyboard model and prefer maintained accessible primitives for non-trivial behavior.
- Connect form labels, descriptions, and errors; expose invalid state and map server validation errors to the relevant fields.
- Inspect copied shadcn/ui components before assuming Radix- or Base UI-specific accessibility behavior.

## Placeholder Controls

- Trace every visible control to an intentional handler, navigation target, menu, or observable result. Report empty handlers, TODO-only actions, placeholder routes, and enabled controls with no user-visible effect.
- Remove or intentionally disable unfinished controls. If a disabled control needs an explanation, account for native disabled elements not receiving focus or pointer events; align appearance, accessible state, and behavior.
- Tests should cover the control's observable result, not merely that its icon renders.

## Review Boundaries

Report a finding when the implementation violates a stated design, an established reusable pattern, accessibility or semantic behavior, or a concrete interaction requirement. Keep optional polish and subjective alternatives out of actionable findings unless the project explicitly requires them.
