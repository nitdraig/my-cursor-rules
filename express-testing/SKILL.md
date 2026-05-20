---
name: express-testing
description: Write and review Express.js API tests with supertest — routes, middleware, validation, authentication, error handling, and Mongoose service mocks. Use when adding Express tests, improving API coverage, or debugging failing backend tests.
---

# Express Testing

Write and fix tests for Express.js APIs (TypeScript or JavaScript). Use `writing-tests` for generic unit tests; use this skill when the work is HTTP routes, supertest, middleware, or Express integration tests.

## When to use

- “Add tests for this Express route” or “cover the users API”
- Debug failing supertest or middleware tests
- Set up API test harness for a new `server/` package
- Review test quality on a backend PR

## Prerequisites

| Requirement | Reason |
|-------------|--------|
| `createApp()` factory | Supertest uses `request(app)` without `listen()` |
| Jest or Vitest + supertest | Standard stack for HTTP assertions |
| Mock services / DB | Keep tests fast and isolated |
| English test names | `returns 404 when user does not exist` |

Default project layout (from `.cursorrules`):

```
server/src/
  __tests__/          # or routes/__tests__, colocated *.test.ts
  app.ts              # createApp()
  routes/
  controllers/
  services/
  middleware/
```

## What to test

| Layer | Tool | Focus |
|-------|------|--------|
| HTTP integration | supertest + `createApp()` | Status, JSON body, headers |
| Middleware | supertest or mock `req`/`res`/`next` | 401, 400 validation, logging |
| Services | Unit tests | Business rules with mocked models |
| Error handler | supertest | `AppError` and 500 behavior |

## App setup for tests

```typescript
// app.ts — export factory, no listen()
export const createApp = (): Express => { /* middleware + routes */ return app; };
```

```typescript
// users.routes.test.ts
import request from 'supertest';
import { createApp } from '../app';
import * as userService from '../services/userService';

jest.mock('../services/userService');

describe('POST /api/users', () => {
  const app = createApp();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('returns 201 when payload is valid', async () => {
    jest.mocked(userService.create).mockResolvedValue({
      id: '1',
      name: 'Jane',
      email: 'jane@example.com',
    });

    const res = await request(app)
      .post('/api/users')
      .send({ name: 'Jane', email: 'jane@example.com', password: 'securePass1' })
      .expect(201);

    expect(res.body).toMatchObject({ name: 'Jane', email: 'jane@example.com' });
    expect(res.body).not.toHaveProperty('password');
  });
});
```

## Test categories

### Happy paths and HTTP errors

- Status codes: 200, 201, 204, 400, 401, 403, 404, 409, 422, 500 as designed
- Response shape matches the API contract
- Invalid `:id` → 400
- Malformed JSON → 400

```typescript
it('returns 400 for malformed JSON', async () => {
  await request(app)
    .post('/api/users')
    .set('Content-Type', 'application/json')
    .send('{ invalid')
    .expect(400);
});
```

### Validation middleware

```typescript
it('returns 400 when email is invalid', async () => {
  const res = await request(app)
    .post('/api/users')
    .send({ name: 'Jane', email: 'not-an-email', password: 'securePass1' })
    .expect(400);

  expect(res.body.error).toBeDefined();
});
```

### Authentication

```typescript
it('returns 401 without Authorization header', async () => {
  await request(app).get('/api/users/me').expect(401);
});

it('returns 200 with valid Bearer token', async () => {
  const token = 'valid-test-token';
  jest.mocked(authService.verifyToken).mockResolvedValue({ id: '1', role: 'user' });

  await request(app)
    .get('/api/users/me')
    .set('Authorization', `Bearer ${token}`)
    .expect(200);
});
```

Mock `verifyToken` or the auth service — do not call real IdPs in unit tests.

### Not found and conflicts

```typescript
it('returns 404 when user does not exist', async () => {
  jest.mocked(userService.findById).mockResolvedValue(null);
  await request(app).get('/api/users/999').expect(404);
});
```

### Error handler

- Service throws `NotFoundError` → 404 with `{ error, message }`
- Unexpected error → 500; no stack trace in production responses

```typescript
it('maps NotFoundError to 404', async () => {
  jest.mocked(userService.findById).mockRejectedValue(new NotFoundError('User'));
  const res = await request(app).get('/api/users/1').expect(404);
  expect(res.body.error).toBe('NOT_FOUND');
});
```

### Middleware in isolation (optional)

```typescript
import type { Request, Response, NextFunction } from 'express';
import { authenticate } from '../middleware/auth';

it('calls next when token is valid', async () => {
  const req = { headers: { authorization: 'Bearer ok' } } as Request;
  const res = { status: jest.fn().mockReturnThis(), json: jest.fn() } as unknown as Response;
  const next = jest.fn();

  await authenticate(req, res, next);
  expect(next).toHaveBeenCalled();
});
```

## Vitest

Same patterns; replace `jest.mock` / `jest.mocked` with `vi.mock` / `vi.mocked` and use `vitest` imports.

## Best practices

- One behavior per test; name describes the assertion
- Independent tests — no shared DB rows; reset mocks in `beforeEach`
- Always `await` supertest chains
- Close MongoDB connections in `afterAll` when using a real test database
- Prefer supertest over mocking `req`/`res` for route-level tests
- Assert security: sensitive fields not in JSON responses

## Anti-patterns

- Calling `listen()` and hitting a real port in tests
- Testing middleware order instead of observable behavior
- Mocking Express so routes and the error handler never run
- Only testing 200 responses
- Shared mutable in-memory stores without reset between tests
- Skipping or disabling failing tests

## CI integration

- Run `pnpm test` / `npm test` on every PR
- Default job: mocked DB; optional job: test MongoDB with `mongodb-memory-server` if the project uses it
- Keep API tests under a few seconds per file when possible

## Checklist

```
- [ ] createApp() exported; listen() only in server.ts
- [ ] Happy path per new or changed route
- [ ] Validation errors (400)
- [ ] Auth errors (401 / 403) where required
- [ ] Not found (404) and conflict (409) if applicable
- [ ] Malformed JSON (400)
- [ ] Error handler and AppError mapping
- [ ] Services mocked; no live third-party calls
- [ ] Tests deterministic in CI
```

## Related skills

| Skill | Use when |
|-------|----------|
| `express-api-review` | Reviewing API design before writing tests |
| `writing-tests` | Non-HTTP units (pure functions, utilities) |

## Notes

- Match the repo’s test runner and folder conventions.
- Run `pnpm run lint` or `npm run lint` after moving files if the project defines it.
