---
name: e2e-tests
description: Project conventions for writing end-to-end tests. Use whenever writing, updating, or fixing e2e tests.
user-invocable: false
---

# Writing e2e tests in this project

> TODO (team): fill in the sections marked FILL IN. Keep this file short and concrete.

## Tooling — FILL IN

- Framework: <e.g., Playwright or Cypress>
- Location: <e.g., `e2e/specs/`>
- Run all: `<command>` · Run one file: `<command>`
- Environment: <how to start the app and services for e2e, seed data, environment variables>

## Principles

- Test user-visible behavior of critical flows, not implementation details. Anything a unit or integration test can cover belongs there instead.
- One scenario per test. Name tests after the behavior, e.g. `user can reset password with a valid link`.
- Select elements by role, label, or test ID, never by CSS class or DOM structure.
- No fixed sleeps. Wait for visible states or network responses.
- Each test creates and cleans up its own data. Tests must pass in any order and in parallel.
- Cover the main path and the most important failure path of the acceptance criteria.
- Run new tests several times locally to make sure they are not flaky before finishing.

## Project patterns — FILL IN

- Page objects and helpers: <where they live; an example>
- Logging in during tests: <the fast way to authenticate>
- Test data factories: <where and how>
- A good example test to copy: <path>
