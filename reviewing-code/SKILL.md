---
name: reviewing-code
description: Perform a thorough code review focused on correctness, maintainability, performance, architecture alignment, and team conventions (TypeScript, React Query hooks, unit/component tests). Use when the user asks for a code review, feedback on their code, PR review, or to check code quality.
---

# Code Review

Use this skill when the user asks for a code review, feedback on their code, or to check code quality.

## Steps

1. **Understand the change** — read the files or diff to understand what the code is supposed to do. Identify the scope (new feature, bug fix, refactor). Read `.cursorrules`, `DESIGN.md`, or similar project docs when present.

2. **Check correctness**
   - Does the code handle edge cases (empty input, null, zero, negative numbers)?
   - Are error states handled (try/catch, error boundaries, fallback UI)?
   - Does async code handle race conditions, cancellation, and timeouts?
   - Are there off-by-one errors in loops or array access?

3. **Check maintainability and design principles**
   - Are functions focused on a single responsibility?
   - Are variable and function names descriptive?
   - Is there unnecessary duplication that should be extracted?
   - Are magic numbers replaced with named constants?
   - Is the code complexity reasonable (deeply nested conditionals, long functions)?
   - Does the change follow **SOLID**, **KISS**, **YAGNI**, and **DRY**?
   - Are there **premature abstractions** (helpers, wrappers, or indirection without a clear second use case)?

4. **Check architecture alignment**
   - Does new code follow the **existing feature-based layout** (e.g. `domain/{Feature}/`, `shared/`, `server/src/{routes|services|controllers}`)?
   - Are responsibilities in the right layer (thin controllers, logic in services, UI in components)?
   - Before adding patterns, **find similar code in the repo** and match naming, file placement, and structure.

5. **Check performance**
   - Are there N+1 query patterns in database access?
   - Are expensive computations or API calls happening in render loops?
   - Are large lists missing virtualization or pagination?
   - Are there missing indexes for common database queries?
   - Is memoization used appropriately (not over-applied)?

6. **Check type safety** (TypeScript projects)
   - **Avoid `any`** — prefer existing types, inference, or narrow unions; flag every unjustified `any` as **Must fix**.
   - Are function return types explicit for public APIs?
   - Are union types handled exhaustively?

7. **Check frontend data layer** (React Query / Next.js projects)
   - Are **`useQuery` and `useMutation` only in `/hooks`** (feature `domain/{Feature}/hooks/` or `shared/hooks/`), not inline in components?
   - Do hooks follow the **same structure as existing hooks** in the repo (read 1–2 reference hooks before judging)?
   - Are **query keys** imported from `api/query-keys` (never ad-hoc string arrays)?
   - Are **URLs** taken from `api/endpoints` constants (no hardcoded paths)?

8. **Check testing**
   - Are there **unit tests** for new/changed logic on **both frontend and backend** when behavior was added or modified?
   - Do tests cover the happy path **and** error cases?
   - Are tests isolated (no shared mutable state)?
   - Prefer **real component tests** over shallow or simulated-only tests; flag mocks that replace testing real UI behavior when a component test is appropriate.

9. **Provide feedback** — organize findings by severity:
   - **Must fix**: bugs, security issues, data loss risks, `any` abuse, missing tests for new behavior, architecture violations (hooks/keys/endpoints)
   - **Should fix**: performance issues, maintainability concerns, deviation from existing patterns
   - **Nit**: style preferences, minor suggestions

   For each finding, include the file, line, the issue, and a suggested fix.

## Notes

- Be constructive — explain *why* something is a problem, not just that it is.
- Acknowledge what's done well, not just what needs fixing.
- Don't bikeshed on style issues that a linter/formatter should handle.
- When project conventions conflict with generic advice, **project conventions win**.

## Output format

```markdown
# Code Review: [brief scope]

## Summary
[1–2 sentences on overall quality and risk]

## What works well
- [Positive observation]

## Findings

### Must fix
| File | Line | Issue | Suggested fix |
|------|------|-------|---------------|
| ... | ... | ... | ... |

### Should fix
| File | Line | Issue | Suggested fix |
|------|------|------|---------------|
| ... | ... | ... | ... |

### Nit
| File | Line | Issue | Suggested fix |
|------|------|-------|---------------|
| ... | ... | ... | ... |
```
