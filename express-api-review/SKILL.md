---
name: express-api-review
description: Review Express.js TypeScript APIs for REST design, architecture, code quality, API performance, and security. Use for Express code review, API hardening, performance tuning, security audits, or reviewing routes, middleware, controllers, and Mongoose services.
---

# Express API Review

Review Express.js + TypeScript backends for structure, REST correctness, maintainability, **performance**, and **security**. Use this skill for Express-only work; use `reviewing-code` for full-stack PRs that include frontend changes.

## When to use

- Code review or PR feedback on Express routes, controllers, or middleware
- “Audit this API”, “harden security”, “check performance”, “review REST design”
- Before production deploy of new endpoints or auth changes
- After major refactors to `server/src` or `routes/`

## Steps

1. **Understand the change** — read routes, controllers, services, models, middleware, and env config. Read `.cursorrules` when present (default stack: Express, TypeScript, MongoDB/Mongoose, pnpm).
2. **Architecture & REST design** — checklist below.
3. **Code quality & TypeScript** — checklist below.
4. **Performance audit** — API-specific bottlenecks.
5. **Security audit** — OWASP-oriented backend checks.
6. **Report** using the output format at the end.

Detailed code patterns: [references/patterns.md](./references/patterns.md).

## Architecture & REST design

### Expected layout

Align with feature-based backend structure when the repo uses it:

```
server/src/
  routes/           # Feature routers
  controllers/      # Thin HTTP handlers
  services/         # Business logic
  models/           # Mongoose schemas
  middleware/       # auth, validate, errorHandler, logger
  types/            # Shared types, express.d.ts
  config/           # Env and clients
  app.ts            # createApp() factory
  server.ts         # listen() only here
```

### REST status conventions

| Method | Path | Success | Client error | Not found |
|--------|------|---------|--------------|-----------|
| GET | `/resources` | 200 | — | — |
| GET | `/resources/:id` | 200 | 400 invalid id | 404 |
| POST | `/resources` | 201 | 400 validation | — |
| PUT/PATCH | `/resources/:id` | 200 | 400 | 404 |
| DELETE | `/resources/:id` | 200 or 204 | 400 | 404 |

### Architecture checklist

- [ ] Routes only map HTTP to controllers; no business logic in route files
- [ ] Controllers are thin; services own domain logic and Mongoose access
- [ ] Single global error handler registered **after** all routes
- [ ] Consistent JSON errors: `{ error, message?, details? }`
- [ ] Invalid JSON body returns 400 (handle `entity.parse.failed`)
- [ ] Reusable validation middleware (Zod or shared validators)
- [ ] Routers grouped by feature; auth/validation on the correct router level
- [ ] Middleware order: security → body parsers → logging → routes → error handler
- [ ] `createApp()` exported for tests; `listen()` only in `server.ts`

## Code quality & TypeScript

- [ ] Explicit types on public handlers; no unjustified `any`
- [ ] Async handlers use `try/catch` and `next(error)` (or equivalent)
- [ ] Typed errors (`AppError`, `NotFoundError`) with stable `code` fields
- [ ] Secrets and URLs from environment variables
- [ ] No duplicated auth/validation per route
- [ ] SOLID, KISS, YAGNI — no premature abstraction
- [ ] English identifiers, comments, and API error messages

## Performance audit

API-focused (use `auditing-performance` for frontend bundles and Core Web Vitals).

- [ ] No blocking sync work on the hot path (CPU, filesystem)
- [ ] No N+1 Mongoose queries in list endpoints (`populate` loops, per-item `findById`)
- [ ] Indexes on fields used in `find`, `sort`, and common filters
- [ ] Pagination with max page size on list endpoints
- [ ] Projections / lean queries when full documents are not needed
- [ ] Reasonable middleware chain (no duplicate `json()` or auth)
- [ ] MongoDB connection pooling configured for production
- [ ] Cache headers or server cache for expensive read-only endpoints
- [ ] Rate limits on auth, write-heavy, or public routes
- [ ] `compression` for large JSON responses when appropriate
- [ ] Aggregation pipelines for heavy reports instead of loading full collections

Rank fixes: **High / Medium / Low** impact and effort.

## Security audit

Backend-focused (use `auditing-security` for full-repo scans including frontend).

- [ ] No secrets in source; `.env` in `.gitignore`
- [ ] `helmet` or equivalent security headers
- [ ] CORS: explicit origins in production (not `*`)
- [ ] Rate limiting on login and sensitive writes
- [ ] Auth middleware runs before protected handlers
- [ ] Authorization in services (ownership, roles), not only in UI
- [ ] All `body`, `query`, and `params` validated; strip or reject unknown fields when appropriate
- [ ] No NoSQL injection via unvalidated objects in Mongoose queries
- [ ] Passwords hashed with bcrypt or argon2; never returned or logged
- [ ] Production errors do not expose stack traces
- [ ] `npm audit` / known CVEs on dependencies flagged
- [ ] Logs exclude tokens, passwords, and unnecessary PII
- [ ] JWT/session cookies: `httpOnly`, `secure`, sensible expiry when cookies are used

Severity: **Critical / High / Medium / Low**.

## MongoDB / Mongoose (when applicable)

- [ ] Schema validation at model level where it adds value
- [ ] Indexes documented or evident for hot queries
- [ ] Transactions for multi-document updates that must be atomic
- [ ] Errors from DB layer mapped to `AppError`, not raw driver messages to clients

## Related skills

| Skill | Use when |
|-------|----------|
| `express-testing` | Adding or reviewing supertest coverage after fixes |
| `reviewing-code` | Full-stack PR with Next.js + Express |
| `auditing-performance` | Frontend, bundles, CWV |
| `auditing-security` | Repo-wide secret scan, dependencies, OWASP |
| `database-design` | New collections, indexes, relationships |

## Output format

```markdown
# Express API Review: [scope]

## Summary
[1–2 sentences on risk and overall quality]

## What works well
- [Observation]

## Architecture & REST
| Status | Finding | Suggested fix |
|--------|---------|---------------|
| PASS / WARN / FAIL | ... | ... |

## Performance
| Impact | File | Issue | Suggested fix |
|--------|------|-------|---------------|
| High / Med / Low | ... | ... | ... |

## Security
| Severity | File | Issue | Suggested fix |
|----------|------|-------|---------------|
| Critical / High / Medium / Low | ... | ... | ... |

## Code quality
### Must fix
| File | Line | Issue | Suggested fix |
|------|------|-------|---------------|

### Should fix
...

### Nit
...
```

## Notes

- Explain *why* each finding matters.
- `.cursorrules` and existing repo patterns override generic advice.
- Recommend `npm audit`, Snyk, or Trivy for automated dependency scans; this skill is a structured code review, not penetration testing.
