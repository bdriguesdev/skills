# Security review checklist

> Team-owned. Add your stack's specifics at the end.

## Input and injection

- User input is validated (type, size, format) on the server, not only on the client.
- SQL and NoSQL queries are parameterized; no query strings built from input.
- No command injection (shell calls with input), path traversal (file paths from input), or SSRF (server fetching URLs from input).
- Output is encoded for its context; no XSS through raw HTML rendering (`innerHTML`, `dangerouslySetInnerHTML`, `v-html`) of untrusted data.
- No `eval` or dynamic code from input; deserialization is safe.

## Authentication and access control

- Every new endpoint or action requires authentication unless it is explicitly public.
- Authorization is checked on the server for every resource access (no IDOR: users can only reach objects they are allowed to).
- Role and permission checks are consistent with existing ones; no client-side-only checks.
- Session and token handling stays correct: expiry, rotation, invalidation.

## Data protection

- No secrets, keys, or credentials in code, committed config, or logs.
- Sensitive data (personal data, tokens, passwords) is not logged, not exposed in API responses or error messages, and not sent to analytics.
- Sensitive data is encrypted in transit and at rest where required.

## Web

- CSRF protection on state-changing requests when using cookie-based auth.
- CORS is not widened without need.
- File uploads: type and size validated, stored safely, never executed or served with dangerous content types.
- No open redirects to user-controlled URLs.

## Dependencies and configuration

- New dependencies are necessary, maintained, and free of known vulnerabilities; the lockfile is updated.
- No debug mode, verbose errors, or permissive settings enabled in production.
- Rate limiting on sensitive or expensive endpoints (login, password reset, search, exports).

## Project-specific — FILL IN

- ...
