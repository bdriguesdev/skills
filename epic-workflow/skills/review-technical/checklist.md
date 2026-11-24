# Technical review checklist

> Team-owned. Add what your team cares about and delete what doesn't apply.

## i18n

- No hardcoded user-facing strings; all copy goes through the i18n layer.
- New keys exist in every supported locale, or follow the project's fallback process.
- Dates, numbers, and currencies use locale-aware formatting.
- Sentences are not built by concatenating strings; use interpolation and plural rules.
- Layout tolerates longer translations (and right-to-left languages, if supported).

## Performance

- No N+1 queries; queries use indexes; unbounded lists are paginated.
- No expensive work in hot paths or render loops; memoization where it matters, not everywhere.
- No unnecessary network calls; caches are used and invalidated correctly.
- No large payloads or bundle bloat; lazy loading where appropriate.

## Best practices

- Follows the existing patterns and architecture of the codebase.
- Clear names; functions with one responsibility; no dead or commented-out code.
- No duplicated logic that already exists in a shared helper.
- Errors handled at the right level, with useful messages; no swallowed errors.
- Logs and metrics for important operations and failures, without sensitive data.
- Public APIs and contracts stay backward compatible or are versioned; migrations are reversible.
- Feature flags used as planned; no leftover debug code; no TODOs without a ticket.

## Accessibility (UI changes)

- Semantic elements; labels on inputs and icon buttons.
- Keyboard navigation and visible focus.
- Sufficient color contrast; information not conveyed by color alone.

## Project-specific — FILL IN

- ...
